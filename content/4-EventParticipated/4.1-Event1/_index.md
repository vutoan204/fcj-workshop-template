---
title: "AWS Community Day Vietnam 2026"
date: 2026-05-23
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---



# Harvest Report: AWS Community Day Vietnam 2026

### Event Information
* **Event Name:** AWS Community Day Vietnam 2026
* **Date & Time:** May 23, 2026 (08:00 - 17:00)
* **Location:** Ho Chi Minh City, Vietnam
* **Role:** Attendee

---

### Event Objectives
AWS Community Day is a large-scale technology conference organized by the local AWS User Group Vietnam. The main goals of the event are:
* Share the latest technology trends in the AWS cloud ecosystem, particularly Generative AI (GenAI), Serverless architectures, and modern application designs.
* Connect software engineers, Solution Architects (SAs), AWS Community Builders, and the tech business community in Vietnam.
* Share practical best practices, digital transformation case studies, and cloud cost optimization tactics from leading enterprises (GoTymeX, VPBank, Cloud Kinetics, etc.).

---

### Key Presentation Highlights

#### 1. Context Is Everything - Making AI Actually Work for You
* **Speaker:** Tinh Truong (Platform Engineer, GoTymeX)
* **Key Topics:**
  * Identified the core reason why AI applications frequently hallucinate is not necessarily weak LLMs, but rather a lack of specific, relevant Context.
  * Introduced the "Prompt to Memory Pipeline" architecture – showing how to build dynamic context injection pipelines and long-term memory solutions for AI.
  * Shared insights on working productively alongside AI assistants and recommended skill development paths for students/junior developers in the AI era.

#### 2. Friendly AI Assistant w/ Amazon Quick
* **Speaker:** Pham Ngoc Hai Anh (G-AsiaPacific Vietnam, AWS Community Builder)
* **Key Topics:**
  * Analyzed enterprise paint points: Business users waste substantial time looking up scattered, fragmented data from multiple systems.
  * Introduced "Amazon Quick", an AI assistant built on Amazon Bedrock, integrating 40+ secure database connectors and intelligent web searching capability.
  * Real-world Case Study: A Project Manager assistant that automates Minutes of Meeting (MoM) generation, drafts emails, and tracks developers' schedules.

#### 3. From Edge to Origin: CloudFront as Your Foundation
* **Speaker:** Nguyen Tuan Thinh (DevOps Engineer, First Cloud AI Journey)
* **Key Topics:**
  * Addressed cost unpredictability in traditional pay-as-you-go CDN pricing models.
  * Proposed a fixed-price CDN model utilizing Amazon CloudFront, AWS WAF, Route 53, and S3 storage resources for predictable monthly expenses.
  * Detailed advanced configurations: Edge security (AWS Shield, WAF, Mutual TLS, geo-blocking), Origin Failover setups, HTTP/3 (QUIC) support, and edge logic customizations using CloudFront Functions or Lambda@Edge.

#### 4. 36 hrs with LotusHacks: Building UTMorpho from Idea to Reality
* **Presenters:** UTMorpho Team (LotusHacks 2026 Participants)
* **Key Topics:**
  * Shared their journey designing and building "UTMorpho", a Generative Design application, in a 36-hour hackathon.
  * System Architecture: React SPA frontend on CloudFront + S3; API Gateway + Lambda backend writing to S3 and DynamoDB; Amazon Bedrock GenAI engine parsing image sketches, generating UI layouts, and streaming live React previews.
  * Lessons on handling AI overgeneration, token limits, and working under extreme pressure.

#### 5. Non-Determinism of 'Deterministic' LLM Settings
* **Speaker:** Duc Dao (Solution Architect, Cloud Kinetics)
* **Key Topics:**
  * Explained that despite configuring `temperature = 0` on commercial LLM APIs, output generation remains non-deterministic.
  * Outlined technical causes: Non-associative GPU floating-point calculations during parallel processing, and dynamic request batching on multi-tenant inference platforms.
  * Shared mitigation strategies: Majority Voting ensembles, self-hosting models with deterministic runtime limits, and structural output constraints (JSON Mode, Regex Grammar).

#### 6. Enterprise-Grade Multi-Agent System: The Case of Startup Credit Scoring
* **Speaker:** Vy Lam (Senior Business Systems Analyst, VPBank)
* **Key Topics:**
  * Addressed the lack of traditional financial data when evaluating credit scores for startup businesses.
  * Highlighted single-agent limitations: Context bottlenecks, low auditability, and higher failure rates in complex decisions.
  * Proposed a virtual committee framework using a Multi-Agent system (specialized agents for finance, risk, market analysis) to achieve consensus.
  * Business outcomes: Accelerated credit processing from weeks to hours (95% faster), cut manual analyst hours by 95%, and projected high multi-year ROI.

---

### Key Takeaways & Lessons Learned

#### 1. Architectural Design Mindset
* **Edge Security and Optimization:** CloudFront is not just for caching assets but serves as a crucial edge defense line. Setting up S3 Gateway Endpoints and implementing optimized CDN packages can dramatically save network egress fees.
* **Business-First Approach:** Tech implementations, particularly AI integrations, must align directly with the enterprise's operational needs to yield real business value and ROI, rather than just chasing hype.

#### 2. Advanced GenAI Concepts
* **Handling LLM Constraints:** Understanding non-determinism helps backend developers build fail-safes and structured grammars, avoiding absolute trust in raw LLM outputs.
* **Multi-Agent Systems:** Transitioning from single-prompt chatbots to collaborative Multi-Agent systems is the key to solving complex business problems.

---

### Applying to NodeJ2Car Project
The knowledge gained from the event was applied to the design and implementation of the **NodeJ2Car** backend system:
1. **Edge Security & CDN:** Deployed the React frontend to S3 and distributed it via CloudFront with ACM SSL/TLS certificates and AWS WAF edge protection as completed in Week 10.
2. **Decoupled Asynchronous Processing:** Implemented Amazon SQS messaging queues at Week 8 to buffer and consume invoice webhooks, protecting the primary Express servers from spikes in traffic.
3. **Optimizing AI Scan Image API:** Applied filename/metadata sanitization and structured regex validation bounds to the AI parts scanning endpoint at Week 6 to reduce AI misclassification errors.

---

### Event Photos
![AWS Community Day Vietnam 2026](/images/4-EventParticipated/4.1-Event1/event.jpg)

---

### Personal Reflections
> AWS Community Day Vietnam 2026 offered an energetic, tech-driven atmosphere. Learning from top industry speakers broadened my technical horizons and created great networking opportunities. The event provided great inspiration throughout my internship.
