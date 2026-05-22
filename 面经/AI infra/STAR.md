1. time is urge
   "In my Cloud-Native Microservices project, we had to fix the submitting deadline. But the problem of deploying to AWS kept surfacing new integration bugs that threatened to eat all our remaining time.
   
   Based on the understanding of the project, instead of debugging everything in the cloud simultaneously, I imposed a local-first rule: I broke the remaining work into daily milestones: containerization first, then RabbitMQ integration, then load balancing, then auto-scaling. And set local test for each decoupled services first. We generated the local test code. Then we found the bug like using the wrong Rabbit MQ, inconsistent port for certain service, obsolete ecollipes version.
   
   Finally, we had a hard deadline for dealing with cloud computing. And we also saved time for plot the statistic pictures for our project for better illustration.
   
   In hindsight, I would have set the local-first rule at kickoff rather than mid-project.

   
2. different personality
	   On my influence maximization research project, I worked with a teammate who was very strict on presentation first while I tend to generate the whole project out first. Early on this created friction around whether I should spare some time on creating PPT each day for pre. Instead of letting it fester, I suggested we align on the actual priority — delivering the project on time — and proposed showing the teacher algorithmic results directly rather than spending daily time on slides. What helped most was realizing we actually had the same goal — we just approached it differently. The project delivered on time and paper was also published in **arXiv**, and I came away with a better sense of how to work with people who think differently than me.
   
   3. weakness and strength
      Stubborn:
      
      I'm strong at end-to-end ownership — I'm comfortable moving from problem definition through implementation to evaluation. For example, in my stochastic graph simulation project, instead of using optimizer library, I wrote the solution of optimization myself only to make it faster and fully interpretable.
      
      Thoroughness:
      My strength is that I see things through — from problem definition to simulation. In my ai agent project, I learnt RAG and word embedding methods like TF-IDF and RoPE from scratch, and also generate the code out to see what exact problem I am gonna face(file path handling and class initialization order)
      
      
   4. Experience to work with ai to solve a hard problem
      When benchmarking Qwen2.5, I hit an unexpected memory bottleneck on unified architecture. I used Claude to help me reason through the quantization trade-offs — specifically whether 4-bit would degrade output quality enough to matter for my use case. But I didn't just take the answer — I designed the perplexity evaluation myself to verify the claim empirically. The result was 1.8x throughput improvement with 50% memory reduction while maintaining acceptable quality.
      
      
      
   5. why Meshy
      -  Fancy about the concept to generate 3D model by 2D picture.
      - I watched Whynot TV from Ethan Hu
      - In Bay area
