Great. Let's start **Phase 1: Streamlit Prototype**. The goal here is to quickly get a functional web interface running for your LLM agent using **Streamlit**, which allows for fast iteration and testing. We'll also integrate **Git** from the very beginning to ensure proper version control.

-----

### Part 1: Setting up the Prototype & Git Integration

The first step is to set up your project environment. Streamlit is a fantastic tool because it turns simple Python scripts into interactive web apps, making it perfect for rapid prototyping.

#### **Hands-on Session: Setting up the Project & Making the First Commit** 🚀

1.  **Create Your Project Directory:** Open your terminal or command prompt. Navigate to where you want to store your project and create a new folder.

    ```bash
    mkdir my-llm-app
    cd my-llm-app
    ```

2.  **Initialize Git:** Start tracking your project's changes with Git. This is crucial for collaboration and for keeping a history of your code.

    ```bash
    git init
    ```

3.  **Set up the Python Environment:** It's best practice to use a virtual environment to manage your project's dependencies.

    ```bash
    # Create the virtual environment
    python -m venv venv

    # Activate the environment
    # On macOS/Linux:
    source venv/bin/activate
    # On Windows:
    # .\venv\Scripts\activate
    ```

4.  **Install Required Libraries:** Now, install Streamlit and any other libraries your LLM agent needs (e.g., `openai`, `fastapi`, `sentence-transformers`, `numpy`).

    ```bash
    pip install streamlit openai sentence-transformers numpy
    ```

    *Note: We're installing `fastapi` here in anticipation of Phase 2.*

5.  **Create the Streamlit App:** Create a new file named `app.py` in your project directory. This will be the heart of your application. Add the following basic "Hello, World" code.

    ```python
    # app.py
    import streamlit as st

    st.title("My First LLM App Prototype")
    st.write("Welcome to the app!")
    ```

6.  **Run the App:** Run the following command in your terminal to see your app in action.

    ```bash
    streamlit run app.py
    ```

    This will open a new browser tab with your app. You'll see the title and text you just wrote.

7.  **Make the First Commit:** Now that you have a working starting point, let's save this state in Git. First, create a `.gitignore` file to tell Git to ignore your virtual environment folder. This prevents unnecessary files from being tracked.

    ```bash
    # .gitignore
    venv/
    __pycache__/
    ```

    Then, add and commit your changes.

    ```bash
    git add .
    git commit -m "Initial Streamlit app setup"
    ```

    Congratulations, you've successfully created your project, set up version control, and made your first commit\!

-----

### Part 2: Integrating the Agent

Now that the basic UI is working, let's integrate the advanced LLM agent you built in Week 3. We'll move the core agent logic into a single function that the Streamlit app can call.

#### **Hands-on Session: Connecting the UI and the Agent** 🔗

1.  **Refactor Your Agent Code:** Open the `week3_hour8_ultimate_agent.py` script from last week. Copy the entire file content and paste it into your `app.py` file, replacing the "Hello, World" code. Now, refactor the agentic loop into a function.

    ```python
    # app.py (complete content)

    # All your imports from week3_hour8_ultimate_agent.py
    # e.g., import streamlit as st, import openai, import json, etc.

    # Paste your get_llm_response_robust function
    # Paste your knowledge_base, embedding model, and vector store setup
    # Paste your knowledge_base_search, summarize_text, and other tool functions
    # Paste your AGENT_PROMPT and TOOLS dictionary

    def run_agent(user_query):
        """Runs the ultimate LLM agent with a given query."""
        messages = [{"role": "user", "content": AGENT_PROMPT + f"\nUser Question: {user_query}"}]
        # Add system message for OpenAI models if needed
        if 'OpenAI' in globals():
             messages.insert(0, {"role": "system", "content": "You are a helpful assistant with access to tools."})
        
        try:
            # ... (the entire agentic loop from Hour 8 goes here) ...
            # You will need to indent this code to be inside the function.
            # Replace the `while True` loop and `input()` calls
            # with the logic that takes user_query and returns the final answer.

            # Simplified logic for this example:
            raw_response = get_llm_response_robust(messages, LLM_MODEL, temp=0.0)
            
            # Simplified parsing logic for this example:
            if "<tool_call>" in raw_response:
                # ... (parse and call the tool as in Hour 8) ...
                tool_result = "Result from tool call" # Simplified placeholder
                
                # ... (call LLM a second time with tool result) ...
                final_answer = "Final answer from LLM" # Simplified placeholder
                return final_answer
            else:
                return raw_response
        except Exception as e:
            return f"An error occurred: {e}"

    # Now, add the Streamlit UI to call this function
    st.title("My RAG-Powered Agent")
    user_input = st.text_input("Ask me a question:")

    if st.button("Get Answer"):
        if user_input:
            st.write("Thinking...")
            final_response = run_agent(user_input)
            st.write(final_response)
    ```

2.  **Run the App and Test:** Stop and restart your Streamlit app in the terminal (`Ctrl+C` then `streamlit run app.py`). Your web page will now have an input box and a button. Try asking a question from your knowledge base (e.g., "What is our return policy?").

3.  **Commit Your Progress:** You've just made a massive leap. Let's save this version of your code.

    ```bash
    git add .
    git commit -m "Integrated the LLM agent into the Streamlit UI"
    ```

-----

### Part 3: Enhancing the Prototype with a Chat Interface

The current UI is functional but basic. Let's make it feel more like a real chatbot by adding a conversation history and better visual feedback.

#### **Hands-on Session: Polishing the User Experience** ✨

1.  **Use Session State for History:** Streamlit's `st.session_state` is a powerful way to manage state across user interactions. We'll use it to store our chat history.

    ```python
    # app.py (modified)
    import streamlit as st
    # ... (all your agent code from Part 2) ...

    st.title("My RAG-Powered Agent")

    # Initialize chat history in session state
    if "messages" not in st.session_state:
        st.session_state.messages = []

    # Display chat messages from history on app rerun
    for message in st.session_state.messages:
        with st.chat_message(message["role"]):
            st.markdown(message["content"])

    # Accept user input
    if prompt := st.chat_input("Ask me a question..."):
        # Add user message to chat history
        st.session_state.messages.append({"role": "user", "content": prompt})
        
        # Display user message in chat message container
        with st.chat_message("user"):
            st.markdown(prompt)

        # Display assistant response in chat message container
        with st.chat_message("assistant"):
            with st.spinner("Thinking..."):
                response = run_agent(prompt)
            st.markdown(response)
        
        # Add assistant response to chat history
        st.session_state.messages.append({"role": "assistant", "content": response})
    ```

    *Note: This code replaces the old `st.text_input` and `st.button` logic.*

2.  **Run and Test:** Restart your app. You'll now have a much cleaner chat interface. Notice how the conversation history is maintained as you ask questions. The spinner provides valuable visual feedback that the LLM is working.

3.  **Finalize Phase 1:** You've built a fully functional and polished prototype. Let's make one last commit to mark the completion of this phase.

    ```bash
    git add .
    git commit -m "Completed Streamlit prototype with a chat UI"
    ```

You've successfully completed the first phase of the project\! You now have a solid foundation to build upon as we move to a full-stack architecture in the next phase.