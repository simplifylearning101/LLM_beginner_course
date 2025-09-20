### AI Restaurant Support Executive Project Roadmap 🗓️

This 3-part project will guide you through building a sophisticated AI assistant capable of handling restaurant table bookings with a **human-like conversational flow**. We'll move from foundational logic to advanced capabilities, ensuring the bot is both effective and engaging.

***

### Part 1: Conversational Core & State Management 🤖

This part focus is on the **foundation**. A human-like conversation isn't a single Q&A—it's a multi-step process where information is collected sequentially. We'll build a system that can track this process and use an LLM to generate responses that fit the current state.

* **Objective:** Create the core logic for the conversational loop and a state machine that tracks the booking information (date, time, party size, name).
* **Key Concepts:**
    * **Conversational State:** A dictionary or object that holds the booking details collected so far.
    * **System Prompt:** A clear set of instructions for the LLM that defines its role and the information it needs to collect.
    * **Mock Database:** A simple Python dictionary to simulate a booking system's availability. This avoids external dependencies and lets us focus on the core logic.
* **Hands-on:** You'll write a Python script that asks for one piece of information at a time and updates the state dictionary. The LLM will be prompted to generate the next question.
* **Link:** [Week5_Bonus](week5_Bonus/part1.md)
***

### Part 2: Advanced Conversation & Tool Integration 🛠️

Now that the foundation is set, we'll make the bot more intelligent and robust. Real-world conversations aren't linear; they're full of ambiguity and unexpected user behavior.

* **Objective:** Teach the bot to handle ambiguity, gracefully manage errors, and use a tool to check for booking availability.
* **Key Concepts:**
    * **Information Extraction:** Using the LLM to extract multiple pieces of information from a single user message (e.g., "I'd like a table for two tonight at 7 PM").
    * **Function Calling:** The bot will need to "check the database" to see if a table is available. We'll use **LLM function calling** to let the model decide when to execute a predefined Python function (`check_availability`).
    * **Dynamic Responses:** If a slot isn't available, the bot shouldn't just say "No." It should suggest alternatives or ask for a different time.
* **Hands-on:** You'll refactor your Day 1 script to include the `check_availability` function and update the LLM prompt to enable tool use. You'll add logic to handle successful and failed booking attempts. 
* **Link:** [Week5_Bonus](week5_Bonus/part2.md)
***

### Part 3: The Human Touch & Finalization ✨

The final day is all about refining the experience. The difference between a good bot and a great one is in the details. We'll add a layer of personality and polish the final output.

* **Objective:** Implement a human-like persona, add conversational flair, and finalize the booking process with a clear confirmation.
* **Key Concepts:**
    * **Persona Engineering:** Refining the system prompt to give the bot a friendly, professional tone. It should sound like a restaurant host, not a robot.
    * **Natural Language:** Using the LLM to generate conversational fillers like "Just a moment while I check..." and personalized greetings.
    * **Structured Output:** The final booking confirmation should be clear and well-formatted. You'll use the LLM to generate a final summary and a mock booking ID.
* **Hands-on:** You'll make the final adjustments to your code. The conversation should now feel like a natural dialogue from start to finish, culminating in a successful booking confirmation. We'll also briefly discuss how to expand this project to a real-world application.
* **Link:** [Week5_Bonus](week5_Bonus/part3.md)