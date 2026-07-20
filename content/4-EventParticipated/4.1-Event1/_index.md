---
title: "Event 1"
date: 2026
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# Report on “Automated Prompt Engineering: Enhancing LLM Output Quality”

### Event Purpose

- Introduce a browser extension that optimizes prompt engineering workflows.
- Enable users to interact with AI anywhere across the web while automatically optimizing their input commands.

### Speaker List

- **Nguyen Tuan Thinh** - DevOps/Cloud Engineer 
- **...**

### Key Highlights

#### Prompt Engineering 

- **The Problem:** General users often write overly short and vague prompts, leading to inaccurate or hallucinated AI responses, wasted token budget, and decreased productivity.
- **Structured Solution:** The presentation introduced a standardized formula to optimize a prompt consisting of 7 core components. This shifts the mindset from raw, instinctual AI interactions toward a structured, engineering-based workflow.

#### Advanced AI Reasoning Techniques

The core academic focus centered on methodologies that stimulate the reasoning capabilities of Large Language Models (LLMs):

- **Chain-of-Thought (CoT):** Compels the AI to think and explain its reasoning step-by-step before delivering the final answer, significantly improving accuracy.
- **Tree-of-Thoughts (ToT):** Allows the AI to explore and evaluate multiple solution paths (like branches of a tree) to self-discover the most optimal outcome.
- **Token Economics:** Highlights the operational costs within an enterprise context—understanding how tokens are calculated across different languages and the price disparity between input and output tokens to design highly cost-effective prompts.

#### Agile Serverless Architecture - AWS Solution Architecture

- **Seamless User Experience:** The integration between the Chrome Extension and Amazon Cognito enables automatic synchronization of user login states and secure authentication tokens.
- **Performance & Cost Optimization:** The entire backend runs on AWS Lambda and Amazon API Gateway. The system operates and incurs costs strictly on-demand based on user requests, maintaining the ability to auto-scale infinitely during traffic spikes without server management overhead.
- **Direct AI Integration via Amazon Bedrock:** Instead of spending significant time and capital training custom models, the project utilizes Amazon Bedrock to directly invoke world-class foundational LLMs (such as Claude and GPT), ensuring the prompt optimization engine runs smoothly and flexibly.

### Key Takeaways

#### Design Thinking

- **Deep Understanding of Prompts:** Learned that a prompt is not just a basic question, but a well-engineered structure composed of multiple elements (Role, Context, Instruction, Constraints, Few-shot) designed to shape the AI's reasoning framework.
- **Token Economics Mindset:** Realized that every character sent and received incurs financial costs. Developed an understanding of how to write concise, high-density prompts using clear delimiters to maximize efficiency while conserving system resources.

#### Cloud Architecture 

- **The Power of Serverless Architecture:** Understood how to design a modern, auto-scaling system entirely powered by AWS Serverless services like AWS Lambda and API Gateway. This eliminates traditional physical or virtual server management, drastically optimizing operational costs (pay-only-per-request).
- **Deploying Generative AI in Production:** Gained insights into leveraging Amazon Bedrock to connect securely and rapidly with leading LLMs, ensuring data privacy without requiring heavy up-front hardware investments to retrain models from scratch.

### Event Experience

- **Professional Learning Environment:** The event delivered a high-energy tech atmosphere, effectively bridging academic theory with production-grade enterprise applications.
- **Practical Project Insights:** Listening to first-hand experiences from Mr. Nguyen Tuan Thinh (DevOps/Cloud Engineer) bridged the gap between university knowledge and actual market demands. The open QA environment highly encouraged active discussion and critical thinking.

#### Event Gallery
* No images available to display.

> Overall, the event not only provided deep technical insights but also reshaped my mindset regarding modern application design, cloud native systems, and effective cross-functional team collaboration.