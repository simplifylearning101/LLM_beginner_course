# 10 Week Applied LLM course for beginners
Welcome to "Applied LLM"! I'm thrilled to have you here. Over the next 10 weeks, we're going on a journey to not just learn about Large Language Models, but to master the art of building practical, real-world applications with them. 

We'll start with the absolute basics—what an LLM even is—and quickly move into the hands-on, project-based work that will define this course. Forget dense theory and abstract concepts; our focus is on building, experimenting, and solving problems. 

My goal is to equip you with the skills to confidently develop, evaluate, and deploy your own LLM applications, turning you from a beginner into a skilled practitioner. 

Let's get started.

### **Applied LLM Course Roadmap**

**Course Philosophy:**

* **Applied, not Theoretical:** The focus is on practical application. Theory is provided to understand the "why," but the majority of time is spent on the "how."
* **Simple is Key:** Complex concepts are broken down into simple, digestible pieces. Jargon is avoided unless it is clearly explained.
* **Practice, Practice, Practice:** Learning is an active process. The course includes hands-on exercises, coding challenges, and a final project to solidify understanding.
* **From Zero to Hero:** The curriculum starts with the absolute basics and progressively builds to advanced topics. No prior knowledge of LLMs is assumed.

---

### **List of All Topics**

1.  Introduction to LLMs
2.  Prompt Engineering Fundamentals
3.  Advanced Prompting Techniques
4.  Retrieval-Augmented Generation (RAG)
5.  LLM APIs and Integrations
6.  Building Simple LLM Applications
7.  Agents and Tool Use
8.  Fine-Tuning LLMs
9.  Evaluation and Deployment
10. Capstone Project

---

### **10-Week Course Agenda**

#### **Week 1: The Foundations of LLMs**

* **Theme:** Demystifying LLMs and getting hands-on with our first interactions.
* **Learning Goals:**
    * Understand what a Large Language Model (LLM) is in simple terms.
    * Learn about the key architectural concepts (briefly, like the "Transformer" architecture).
    * Identify common use cases and limitations of LLMs.
    * Interact with a public LLM (like ChatGPT) to understand its conversational nature.
* **Practice Outcome:**
    * Write a one-page summary in simple English explaining LLMs to a non-technical friend.
    * Complete a series of guided prompts using a public LLM to explore its capabilities and boundaries.
* **Homework:** A series of progressively more complex prompting exercises to generate different types of text (e.g., a short story, a poem, a movie script synopsis).

---

#### **Week 2: The Art of Prompt Engineering**

* **Theme:** Mastering the core skill for working with LLMs—how to get the desired output.
* **Learning Goals:**
    * Understand the fundamental principles of prompt engineering: clarity, specificity, and persona.
    * Practice basic prompting patterns: providing instructions, giving examples (**few-shot prompting**), and defining constraints.
    * Explore how to guide the LLM's tone and style.
* **Practice Outcome:**
    * Write a series of prompts for different tasks (e.g., summarizing an article, drafting an email, creating a list of product features).
    * Iteratively refine prompts to improve the quality of the output.
* **Homework:** A collection of 10 prompts to solve real-world problems (e.g., "Help me write a professional apology email," "Draft a social media post for a new coffee shop").

---

#### **Week 3: Advanced Prompting Techniques**

* **Theme:** Taking prompting skills to the next level to handle complex problems and get consistent results.
* **Learning Goals:**
    * Dive into advanced techniques like **Chain-of-Thought (CoT)** and **Tree-of-Thought** prompting.
    * Learn how to use "persona" and "role-play" effectively in prompts.
    * Understand how to break down complex tasks into smaller, manageable steps for the LLM.
    * Introduce the concept of "context" and its influence on the model's response.
* **Practice Outcome:**
    * Apply CoT prompting to solve a complex logical or mathematical reasoning problem.
    * Write a prompt where the LLM acts as a specific character (e.g., a history professor, a senior software engineer).
* **Homework:** Design a step-by-step prompt that generates a complete lesson plan for a specific topic, using the advanced techniques learned.

---

#### **Week 4: Retrieval-Augmented Generation (RAG) - Part 1**

* **Theme:** Overcoming the "knowledge cutoff" of LLMs by giving them access to external data.
* **Learning Goals:**
    * Understand the problem RAG solves: "How do I make the LLM knowledgeable about my own data?"
    * Learn the basic RAG workflow: **Retrieval** of information and **Augmentation** of the prompt.
    * Introduce vector databases and embeddings in a simple, non-mathematical way.
    * Build a simple Python script that uses a document to answer a question.
* **Practice Outcome:**
    * Write a Python program that takes a text file and a user question and answers the question using a basic RAG approach with a simple search.
* **Homework:** Extend the program to handle multiple text files and answer questions across them.

---

#### **Week 5: Retrieval-Augmented Generation (RAG) - Part 2**

* **Theme:** Building a more robust RAG system with vector stores and embedding models.
* **Learning Goals:**
    * Understand text **embeddings** and why they are better than keyword search.
    * Learn how to use a basic embedding model (e.g., from `Sentence-Transformers`).
    * Get hands-on with a simple **vector database** (e.g., ChromaDB or FAISS).
    * Build a complete, end-to-end RAG system with a vector database.
* **Practice Outcome:**
    * Write a Python program that embeds chunks of a large PDF and stores them in a vector database.
    * Create a query function that searches the database for relevant chunks and uses them to answer a user's question.
* **Homework:** Build a small Q&A application for a provided set of documents (e.g., a company's FAQs).

---

#### **Week 6: Building LLM Applications with Frameworks**

* **Theme:** Connecting the building blocks to create professional applications using a framework like **LangChain** or **LlamaIndex**.
* **Learning Goals:**
    * Understand the purpose of LLM orchestration frameworks.
    * Learn the core components of a framework like LangChain (Chains, Agents, Tools).
    * Build our first complete application (e.g., a simple chatbot) using a framework.
    * Understand how to manage conversation history and memory.
* **Practice Outcome:**
    * Build a conversational chatbot that can remember previous interactions.
    * Create a simple chain that combines a prompt and an LLM to perform a specific task.
* **Homework:** Build a more advanced chatbot that can answer questions based on a specific context provided at the start of the conversation (e.g., a travel agent bot).

---

#### **Week 7: LLM Agents and Tool Use**

* **Theme:** Giving our LLM applications the ability to "do" things in the real world.
* **Learning Goals:**
    * Understand the concept of LLM **Agents** and how they differ from a simple chatbot.
    * Learn how to define "Tools" that an LLM can use (e.g., a web search tool, a calculator tool).
    * Build a simple agent that can decide which tool to use based on a user's request.
* **Practice Outcome:**
    * Build a simple agent with two tools: a "calculator" and a "web search" tool. The agent should be able to answer questions like "What is 20 divided by 4, and what is the current population of Tokyo?"
* **Homework:** Create an agent that can answer a more complex multi-step question that requires both internal knowledge and external tool use, e.g., "What is the capital of France and how many people live there?"

---

#### **Week 8: Customizing LLMs with Fine-Tuning**

* **Theme:** When RAG isn't enough, we'll explore training a model on our own data for specific behavior or style.
* **Learning Goals:**
    * Understand the difference between RAG and **fine-tuning**.
    * Learn *why* and *when* to fine-tune a model.
    * Get a high-level overview of different fine-tuning techniques (e.g., **LoRA**).
    * Use a cloud platform (like Google Colab) to fine-tune a small open-source model on a custom dataset.
* **Practice Outcome:**
    * Prepare a small dataset for a specific task (e.g., generating technical documentation in a specific style).
    * Run a fine-tuning script to train a pre-trained model.
    * Compare the performance of the fine-tuned model against the base model.
* **Homework:** Fine-tune a model to respond in a specific persona (e.g., a pirate, a formal business leader).

---

#### **Week 9: Evaluation, Deployment, and Best Practices**

* **Theme:** Making our applications production-ready.
* **Learning Goals:**
    * Learn different metrics and methods for **evaluating** the performance of an LLM application.
    * Understand the challenges and best practices for **deploying** LLM applications.
    * Introduce basic concepts of MLOps for LLMs.
    * Review ethical considerations and potential biases.
* **Practice Outcome:**
    * Write a simple evaluation script that compares a model's output against a set of "golden" responses.
    * Deploy a simple RAG application using a framework like Streamlit or Flask and make it accessible via a web browser.
* **Homework:** Create a simple report analyzing the performance of your deployed application and identifying areas for improvement.

---

#### **Week 10: The Capstone Project**

* **Theme:** Bringing everything together to design, build, and present a complete LLM application.
* **Learning Goals:**
    * Synthesize all knowledge from the previous nine weeks.
    * Develop an end-to-end LLM application of their choice.
    * Learn project management and presentation skills.
* **Practice Outcome:**
    * **The Project:**
        * Propose a project idea that solves a real-world problem.
        * Implement the project using a combination of the techniques learned (Prompting, RAG, Agents, etc.).
        * Document the project's architecture, implementation details, and evaluation results.
        * Present the final project and its outcomes.
* **Homework:** The final project is the only homework for this week. It is a chance to showcase their expertise and build a portfolio piece.
