"Hi, I am Tingyu Zhang, I acquired my Bachelor's degree on Applied Mathematics in Shenzhen, and I'm a CS master's student at Northeastern University Seattle Campus. My background is in ML systems and infrastructure — I've done projects on AWS ECS with Docker, deployed LLMs locally, and built ML training pipelines. I'm interested in Meshy LLC because it sits at the intersection of AI and infrastructure, which is exactly what I want to go deeper in."


Machine Learning Engineering Intern

Designed an NLP pipeline to extract material requirements from raw text and classify core needs using ensemble learning combining Random Forest and Logistic Regression; applied NLTK preprocessing for tokenization and TF-IDF vectorization for feature extraction. Indexed high-cardinality fields with B-tree indexes in PostgreSQL to accelerate feature retrieval.

Astronomical Image Classifier: ViT Fine-Tuning & LLM Agent:

Processed 97G raw astronomical data into prompt-completion pairs and light curve labels; fine-tuned a Vision Transformer (ViT) using LoRA (parameter-efficient fine-tuning) on a remote GPU server via SSH for multi-class astronomical image classification; benchmarked against LeNet-5.

---

> I built an autonomous job search agent that runs entirely locally using Ollama with Llama 3.2. The core idea was using RAG — Retrieval Augmented Generation — where I store job postings in a ChromaDB vector store and match them against my resume semantically, not just by keyword.

> On top of that I built a multi-agent system using LangChain — one agent tailors the resume, one generates cover letters, one tracks applications. I used prompt chaining and few-shot learning to make the outputs consistent. That cut my application prep time from about 2 hours down to 15 minutes per job.

> For data collection I built a web scraper with Selenium and BeautifulSoup that pulls listings daily from multiple job sites, stores them in SQLite, and uses Celery for async processing — collecting over 2,000 listings a day.


---

 
> This was a distributed e-commerce system with four independent Spring Boot microservices — Product, Shopping Cart, Warehouse, and Credit Card Authorizer — each running in its own Docker container on AWS ECS. And the part I wrote was Warehouse.

> I provisioned the entire infrastructure with Terraform so it was reproducible and version controlled. ALB handled traffic routing between services, and I configured both CPU and memory based auto-scaling policies.

> For communication I used a hybrid pattern — REST for synchronous operations like inventory reservation where consistency matters, and RabbitMQ with Apache Commons Pool for async fire-and-forget shipment requests where throughput matters more. I also made explicit CAP tradeoff decisions per service — for example the Product service is high-read so I prioritized availability, Shopping Cart is high-write so I needed stronger consistency. Finally I load tested everything with Locust to make sure no single service was the bottleneck."