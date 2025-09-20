
## 🛠️ Part 2: Advanced Conversation & Tool Integration

**Objective:** Day two is where the magic happens. We'll introduce **Function Calling**, a powerful feature that allows the LLM to call a predefined Python function to perform a specific action, like checking for table availability. This moves the bot from a passive conversationalist to an active, goal-oriented agent.

**Instructions:**

1.  **Refine the LLM's role:** Update the system prompt to explicitly tell the LLM about the new function it can use.
2.  **Define the tool:** We'll create a new function `check_availability` and define its schema so the LLM knows what arguments it needs to call it.
3.  **Update the main loop:** The loop will now need to check the LLM's response for a `tool_calls` object. If present, it will execute the function and pass the result back to the LLM for the final response.

<!-- end list -->

```python
import os
import openai
import json
from datetime import datetime

# --- Configuration ---
openai.api_key = os.getenv("OPENAI_API_KEY")

# --- Database Mock-up ---
# This dictionary simulates a database of available times.
AVAILABLE_TABLES = {
    "2025-12-24": ["7:00 PM", "8:00 PM"],
    "2025-12-25": ["6:00 PM", "7:30 PM", "9:00 PM"],
    "2025-12-26": ["6:30 PM", "8:30 PM"]
}

def check_availability(date, time):
    """
    Checks if a given date and time are available in our mock database.
    
    Args:
        date (str): The date in YYYY-MM-DD format.
        time (str): The time in HH:MM AM/PM format.

    Returns:
        bool: True if available, False otherwise.
    """
    print(f"\n[Tool Called]: Checking availability for {date} at {time}")
    if date in AVAILABLE_TABLES and time in AVAILABLE_TABLES[date]:
        return True
    return False

# Function to execute tool calls
def execute_tool_call(tool_call):
    """
    Executes a function based on the LLM's tool call.
    """
    function_name = tool_call.function.name
    function_args = json.loads(tool_call.function.arguments)
    
    if function_name == "check_availability":
        return check_availability(date=function_args.get("date"), time=function_args.get("time"))
    else:
        return {"error": "Function not found"}

def main():
    print("✨ Hello! Welcome to the Royal Tavern. I can help you with your dinner reservation.")

    chat_history = [
        {"role": "system", "content": "You are a friendly and helpful restaurant booking assistant named Royal Tavern AI. Your primary goal is to collect the user's party size, desired date, and time for a reservation. If the user provides all necessary details, use the `check_availability` tool to verify if a table is available. Once all information is gathered and the time slot is confirmed, tell the user the reservation is booked, and provide a confirmation number. Do not make up a confirmation number, instead respond with 'A-123-BOOKING'. Be concise and direct in your responses. Never assume the date, always ask for the full date and time. Do not make up dates or times."}
    ]

    # Tool definition for the LLM
    tools = [
        {
            "type": "function",
            "function": {
                "name": "check_availability",
                "description": "Checks the availability of a table for a specific date and time.",
                "parameters": {
                    "type": "object",
                    "properties": {
                        "date": {
                            "type": "string",
                            "description": "The date of the reservation in YYYY-MM-DD format, e.g., 2025-12-25."
                        },
                        "time": {
                            "type": "string",
                            "description": "The time of the reservation, e.g., '7:00 PM'."
                        }
                    },
                    "required": ["date", "time"]
                }
            }
        }
    ]

    while True:
        user_input = input("You: ")
        chat_history.append({"role": "user", "content": user_input})
        
        # Make the LLM call with the defined tools
        response = openai.chat.completions.create(
            model="gpt-4",
            messages=chat_history,
            tools=tools,
            tool_choice="auto"
        )
        
        message = response.choices[0].message
        
        # Check if the LLM wants to call a tool
        if message.tool_calls:
            tool_call = message.tool_calls[0]
            function_response = execute_tool_call(tool_call)
            
            # Append the tool's response to the conversation
            chat_history.append(message)
            chat_history.append({
                "role": "tool",
                "tool_call_id": tool_call.id,
                "name": tool_call.function.name,
                "content": json.dumps({"is_available": function_response})
            })
            
            # Get the final response from the LLM based on the tool's output
            final_response = openai.chat.completions.create(
                model="gpt-4",
                messages=chat_history
            ).choices[0].message.content
            
            print(f"Royal Tavern AI: {final_response}")
        else:
            # If no tool is called, just print the LLM's response
            print(f"Royal Tavern AI: {message.content}")
        
        if "thank you" in user_input.lower():
            break

if __name__ == "__main__":
    main()
```

**What the code does:**

  * A `tools` list is defined, containing a JSON schema for our `check_availability` function. This schema describes the function's purpose and its required parameters (`date`, `time`).
  * The `openai.chat.completions.create` call now includes the `tools` argument and `tool_choice="auto"`, which lets the LLM decide if and when to call the function.
  * The code checks `message.tool_calls`. If the LLM decides to call the function, the `execute_tool_call` function is triggered.
  * The result of the tool call is sent back to the LLM (as a `"role": "tool"` message) for it to generate the final, natural-language response.

**Outcome:** Your bot can now intelligently ask for the right information (date and time) and then use a "backend" function to verify availability. The user experience is much smoother and more powerful.

-----
