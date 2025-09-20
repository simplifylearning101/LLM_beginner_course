## 🍽️ Part 1: Conversational Core & State Management

**Objective:** On day one, you'll build the foundation of the AI assistant. We'll focus on creating a simple conversational loop and a state management system that allows the bot to "remember" information throughout the conversation. The key is to get the basic prompt-response cycle working and to store crucial details like the guest's name, party size, date, and time.

**Instructions:**

1.  **Set up your environment:** Make sure you have the `openai` Python library installed.
    ```bash
    pip install openai
    ```
2.  **Get your API Key:** Obtain an API key from the OpenAI Platform and set it as an environment variable or directly in your script.
3.  **Code the conversational core:** Create a Python file (e.g., `booking_bot.py`). The script below uses a `user_data` dictionary to manage the state. This dictionary will hold all the information we collect from the user. We'll also use a simple `chat_history` list to maintain the conversational context.

<!-- end list -->

```python
import os
import openai
import json
from datetime import datetime

# --- Configuration ---
# Set your OpenAI API key
openai.api_key = os.getenv("OPENAI_API_KEY")

# --- Database Mock-up ---
# In a real application, this would be a database call.
# For this project, we'll use a simple dictionary to mock available tables.
AVAILABLE_TABLES = {
    "2025-12-24": ["7:00 PM", "8:00 PM"],
    "2025-12-25": ["6:00 PM", "7:30 PM", "9:00 PM"],
    "2025-12-26": ["6:30 PM", "8:30 PM"]
}

def get_llm_response(messages, model="gpt-4"):
    """
    Sends messages to the OpenAI LLM and returns the response.
    """
    try:
        response = openai.chat.completions.create(
            model=model,
            messages=messages
        )
        return response.choices[0].message.content
    except openai.OpenAIError as e:
        print(f"An error occurred: {e}")
        return "I'm sorry, I am experiencing a technical issue. Please try again later."

def main():
    """
    Main function to run the conversational bot.
    """
    print("✨ Hello! Welcome to the Royal Tavern. I can help you with your dinner reservation.")

    # State management dictionary
    user_data = {
        "name": None,
        "party_size": None,
        "date": None,
        "time": None,
        "reservation_confirmed": False
    }

    # Conversation history for context
    chat_history = [
        {"role": "system", "content": "You are a friendly and helpful restaurant booking assistant named Royal Tavern AI. Your primary goal is to collect the user's name, party size, desired date, and time for a reservation. Do not book a reservation unless all four pieces of information are provided."}
    ]

    while not user_data["reservation_confirmed"]:
        user_input = input("You: ")
        
        # Add user's message to the conversation history
        chat_history.append({"role": "user", "content": user_input})
        
        # Get LLM response
        llm_response = get_llm_response(chat_history)
        
        # Add LLM's response to the history
        chat_history.append({"role": "assistant", "content": llm_response})
        
        # Simple logic to update state (will be improved in Day 2)
        if "name" in user_input.lower():
            # A more robust solution would use a parser or function calling
            user_data["name"] = user_input.split()[-1] 
        
        print(f"Royal Tavern AI: {llm_response}")

        # Check for exit condition (will be replaced by a final booking step)
        if "thank you" in user_input.lower():
            break

if __name__ == "__main__":
    main()
```

**What the code does:**

  * The `main` function initializes a `user_data` dictionary to track the reservation details.
  * `chat_history` stores the entire conversation, which is sent to the LLM on each turn. This allows the model to remember the context.
  * The `while` loop runs until the reservation is confirmed.
  * The `get_llm_response` function handles the communication with the OpenAI API.
  * A very basic `if "name"` check demonstrates how you might start to extract information, but this will be refined significantly on Day 2.

**Outcome:** At the end of part 1, you should have a script that can hold a coherent, multi-turn conversation with a user about making a reservation. It won't yet be able to confirm a booking or check a mock database, but it will have the foundational state management in place.

-----
