
## 🤝 Day 3: The Human Touch & Finalization

**Objective:** On the final day, we'll polish the user experience and add the final booking confirmation step. We'll make the bot more conversational and ensure it provides a clear, final summary of the reservation.

**Instructions:**

1.  **Enhance the system prompt:** Add more conversational and human-like instructions to the prompt to give the bot personality.
2.  **Add confirmation logic:** Once a booking is successful, we'll introduce a final message that confirms all details and provides a confirmation number.
3.  **Finalize the script:** Combine all the elements into a cohesive and easy-to-run final script.

<!-- end list -->

```python
import os
import openai
import json
from datetime import datetime

# --- Configuration ---
openai.api_key = os.getenv("OPENAI_API_KEY")

# --- Database Mock-up ---
# This is our mock table availability.
AVAILABLE_TABLES = {
    "2025-12-24": ["7:00 PM", "8:00 PM"],
    "2025-12-25": ["6:00 PM", "7:30 PM", "9:00 PM"],
    "2025-12-26": ["6:30 PM", "8:30 PM"]
}

# In a real app, this would be a more complex booking function that updates a real database.
def book_table(name, party_size, date, time):
    """
    Mocks the final table booking. Returns a confirmation number.
    """
    print(f"\n[Booking Confirmed]: Booking for {name}, {party_size} people, on {date} at {time}.")
    # Simulate database update
    if date in AVAILABLE_TABLES and time in AVAILABLE_TABLES[date]:
        AVAILABLE_TABLES[date].remove(time)
    
    return "A-123-BOOKING"

def check_availability(date, time):
    """
    Checks if a given date and time are available.
    """
    print(f"\n[Tool Called]: Checking availability for {date} at {time}")
    if date in AVAILABLE_TABLES and time in AVAILABLE_TABLES[date]:
        return True
    return False

# Function to execute tool calls
def execute_tool_call(tool_call):
    function_name = tool_call.function.name
    function_args = json.loads(tool_call.function.arguments)
    
    if function_name == "check_availability":
        return check_availability(date=function_args.get("date"), time=function_args.get("time"))
    elif function_name == "book_table":
        return book_table(
            name=function_args.get("name"),
            party_size=function_args.get("party_size"),
            date=function_args.get("date"),
            time=function_args.get("time")
        )
    else:
        return {"error": "Function not found"}

def main():
    print("✨ Hello! Welcome to the Royal Tavern. I can help you with your dinner reservation.")

    # State management dictionary
    user_data = {
        "name": None,
        "party_size": None,
        "date": None,
        "time": None
    }

    # Enhanced system prompt with a more conversational tone
    chat_history = [
        {"role": "system", "content": "You are a friendly and helpful restaurant booking assistant named Royal Tavern AI. Your primary goal is to gather the user's name, party size, desired date, and time for a reservation. Once all four pieces of information are provided and verified as available using the `check_availability` tool, use the `book_table` tool to finalize the reservation. If a time is unavailable, be sure to offer alternative times. Be concise and empathetic. Do not book a reservation unless all four pieces of information are provided. Do not make up dates, times, or party sizes. Once the table is booked, provide the user with the confirmation number: 'A-123-BOOKING' and a friendly closing remark. Never make up a confirmation number. If the user thanks you after booking, respond with a polite and friendly closing remark. Always ask for the full date in YYYY-MM-DD format."}
    ]

    # Tool definitions for the LLM
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
        },
        {
            "type": "function",
            "function": {
                "name": "book_table",
                "description": "Books a table for a user once all information is collected and availability is confirmed.",
                "parameters": {
                    "type": "object",
                    "properties": {
                        "name": {
                            "type": "string",
                            "description": "The name of the person making the reservation."
                        },
                        "party_size": {
                            "type": "integer",
                            "description": "The number of people in the party."
                        },
                        "date": {
                            "type": "string",
                            "description": "The date of the reservation in YYYY-MM-DD format."
                        },
                        "time": {
                            "type": "string",
                            "description": "The time of the reservation, e.g., '7:00 PM'."
                        }
                    },
                    "required": ["name", "party_size", "date", "time"]
                }
            }
        }
    ]

    while True:
        user_input = input("You: ")
        
        # Add user's message to the conversation history
        chat_history.append({"role": "user", "content": user_input})
        
        # Get LLM response with tools
        response = openai.chat.completions.create(
            model="gpt-4",
            messages=chat_history,
            tools=tools,
            tool_choice="auto"
        )
        
        message = response.choices[0].message
        
        if message.tool_calls:
            # Handle tool calls
            tool_call = message.tool_calls[0]
            function_name = tool_call.function.name
            function_args = json.loads(tool_call.function.arguments)

            # Store extracted information
            for key in user_data.keys():
                if function_args.get(key) is not None:
                    user_data[key] = function_args[key]

            # Execute the tool and get its response
            function_response = execute_tool_call(tool_call)

            # Append tool's response to the conversation for LLM to see
            chat_history.append(message)
            chat_history.append({
                "role": "tool",
                "tool_call_id": tool_call.id,
                "name": tool_call.function.name,
                "content": json.dumps({"result": function_response})
            })
            
            # Get the final natural language response
            final_response = openai.chat.completions.create(
                model="gpt-4",
                messages=chat_history
            ).choices[0].message.content
            
            print(f"Royal Tavern AI: {final_response}")
            
            # A final check to exit the loop after a successful booking
            if final_response.startswith("Wonderful!"):
                break
        else:
            # If no tool is called, just print the LLM's response
            print(f"Royal Tavern AI: {message.content}")
        
        if "thank you" in user_input.lower():
            print("Royal Tavern AI: You're welcome! Enjoy your visit.")
            break

if __name__ == "__main__":
    main()
```

**What the code does:**

  * A new tool, `book_table`, is added. The LLM's system prompt now tells it to use this tool once all required information is gathered and availability is confirmed.
  * The `execute_tool_call` function is updated to handle calls to both `check_availability` and `book_table`.
  * The `main` loop is now more robust, capable of handling a full conversational flow from greeting to confirmation. The bot's personality is enhanced through the new system prompt, making the interaction more natural and satisfying.
