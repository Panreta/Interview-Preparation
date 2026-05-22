#### 1. Flash Attention

**Problem it solves:**

```
Standard Attention:
Q, K, V matrices → write full N×N matrix to HBM (slow GPU memory)
                 → read it back for softmax
                 → write again
                 → read again for output

For N=1000 tokens: 1000×1000 = 1M elements written/read
MEMORY BANDWIDTH is the bottleneck, not compute
```

**Flash Attention solution:**

```
Instead of writing N×N to HBM:

Split Q,K,V into TILES (blocks)
Process one tile at a time entirely in SRAM (fast)
Never materialize full N×N matrix in HBM

HBM reads/writes: O(N²) → O(N)
Speed: 2-4x faster
Memory: O(N²) → O(N)
```

![[Pasted image 20260521153311.png]]
```
Standard Attention:          Flash Attention:
                             
HBM (slow)                  SRAM (fast, on-chip)
┌──────────┐                ┌────┐
│ N×N      │                │tile│← compute entire
│ attention│                │    │  softmax here
│ matrix   │                └────┘
│ written  │                never write N×N!
│ /read    │                
│ 4 times  │                
└──────────┘
```

Math behind: find the max in a block to be the estimation for now, safe softmax
find the max from block.

### Generalizing: The Recurrence
After processing block $k$:

$$m^{(k)} = \max\left(m^{(k-1)},\ \max_{j \in \text{block}_k} x_j\right)$$

$$d^{(k)} = d^{(k-1)} \cdot e^{m^{(k-1)} - m^{(k)}} + \sum_{j \in \text{block}_k} e^{x_j - m^{(k)}}$$

After all $K$ blocks, $d^{(K)}$ is the exact softmax denominator $\displaystyle\sum_j e^{x_j - m^{(K)}}$.

**This is mathematically exact** — not an approximation.

**One-line answer for interview:**

> _"Flash Attention avoids materializing the full attention matrix in HBM by tiling computations in SRAM — trades recomputation for memory bandwidth."_

---

#### 2. Continuous Batching

**Problem:**

```
Naive batching (static):
Batch = [req1(100 tokens), req2(10 tokens), req3(80 tokens)]

req2 finishes at step 10 ──→ GPU slot sits IDLE
                              waiting for req1, req3
                              WASTED compute ❌
```

**Continuous batching:**

```
Step 10: req2 finishes
         IMMEDIATELY insert req4 into that slot
         no waiting, no idle GPU

Timeline:
[req1    req1    req1  ...req1  done]
[req2 done][req4    req4   req4    ]
[req3       req3   req3       done ]
         ↑
     slot reused instantly
```

**Why Meshy cares:** serving 5M users → maximizing GPU utilization directly = cost savings.

---

#### 3. Speculative Decoding

**Problem:** LLM generates one token at a time — sequential, slow.

```
Normal decoding:
[The] → [cat] → [sat] → [on] → [the] → [mat]
 one token per forward pass, can't parallelize
```

**Speculative decoding:**

```
Small draft model (fast):
Guesses next 5 tokens speculatively:
[The cat sat on the]  ← cheap, fast

Large target model (slow but accurate):
Verifies all 5 in ONE forward pass:
✅ The ✅ cat ✅ sat ✅ on ❌ the
                           ↑
                     rejects from here
                     
Accept 4 tokens in cost of ~1 forward pass!
Speedup: 2-3x
```

---

#### 4. PagedAttention (vLLM) — You Already Know This

Connect it to KV cache:

```
Problem:
Each request's KV cache = variable size
Pre-allocating max size = wasteful

PagedAttention:
KV cache stored in fixed-size PAGES
like OS virtual memory

Request A: pages [1, 4, 7]     (non-contiguous ok!)
Request B: pages [2, 3, 8]
Request C: pages [5, 6, 9]

No fragmentation, no waste
Enables 24x more throughput than HuggingFace naive serving
```

---

#### 5. Quantization (You Actually Know This)

```
FP32:  32 bits per weight  ← training
FP16:  16 bits             ← standard inference  
INT8:   8 bits             ← 2x memory saving
INT4:   4 bits             ← 4x memory saving (your Qwen project!)

Tradeoff curve:
Precision  ████████████████  FP32
           ████████          FP16  ← sweet spot
           ████              INT8
           ██                INT4  ← your benchmark
Speed      ░░░░░░░░██████████
```

**Key insight to mention:**

> _"Quantization moves the bottleneck from memory bandwidth back to compute — which is why 4-bit gives superlinear speedup over FP16 on memory-bound hardware like Apple Silicon."_

---

#### 6. Tensor Parallelism vs Pipeline Parallelism

```
Model too big for 1 GPU → split it

Tensor Parallelism (TP):
Split individual weight MATRICES across GPUs

Linear layer W (4096×4096):
GPU1 gets W[:, :2048]   ← left half
GPU2 gets W[:, 2048:]   ← right half
Both compute in parallel, all-reduce at end

Good for: reducing latency
Bad for:  high communication overhead


Pipeline Parallelism (PP):
Split model LAYERS across GPUs

GPU1: layers 1-10   ← runs, passes activations →
GPU2: layers 11-20  ← runs, passes activations →
GPU3: layers 21-32

Good for: large models, low communication
Bad for:  pipeline bubbles (idle GPU time)
```

---

#### 7. MoE — Mixture of Experts

Powers GPT-4, Mixtral, Deepseek:

```
Dense model (GPT-3 style):
Every token → ALL parameters activated
175B params → all 175B used every forward pass

MoE model:
Every token → router picks TOP-2 experts
              only those 2 activate

8 experts, 2 active:
[E1][E2][E3][E4][E5][E6][E7][E8]
         ↑               ↑
    token routes to E3 and E6 only
    E1,2,4,5,7,8 = idle this step

Result: 8x parameters, ~2x compute cost
        Better quality, same inference speed
```