Excellent. We've successfully set up the backend and frontend as separate services. Now for the final step of this phase: bringing it all together into a clean, integrated chat application. We'll replicate the user experience from the Streamlit prototype, but within the Next.js framework.

-----

### Part 3: Building the Full-Stack Chat Interface

In this part, we'll implement a full-fledged chat interface using React's state management, allowing for a fluid, conversational experience. We'll use the API endpoint we created in Part 1 to power our chat.

#### **Hands-on Session: Finalizing the UI** ✨

1.  **Modify the Frontend Code:** Open the `frontend/app/page.js` file again. We'll replace the simple input/button with a full chat history display, similar to what you saw with Streamlit's `st.chat_message`. The key is to manage an array of messages in your React state.

    ```javascript
    // frontend/app/page.js
    'use client';

    import { useState, useRef, useEffect } from 'react';

    export default function HomePage() {
      // State to hold the conversation history
      const [messages, setMessages] = useState([]);
      const [inputQuery, setInputQuery] = useState('');
      const [loading, setLoading] = useState(false);
      const chatContainerRef = useRef(null);

      // Scroll to the bottom of the chat container when messages are updated
      useEffect(() => {
        if (chatContainerRef.current) {
          chatContainerRef.current.scrollTop = chatContainerRef.current.scrollHeight;
        }
      }, [messages]);

      const handleQuerySubmit = async (e) => {
        e.preventDefault();
        const userQuery = inputQuery.trim();
        if (!userQuery) return;

        // Add user message to the conversation history
        setMessages((prevMessages) => [...prevMessages, { role: 'user', text: userQuery }]);
        setInputQuery(''); // Clear the input field
        setLoading(true);

        try {
          const res = await fetch('http://localhost:8000/chat', {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json',
            },
            body: JSON.stringify({ text: userQuery }),
          });
          const data = await res.json();
          // Add the agent's response to the conversation history
          setMessages((prevMessages) => [...prevMessages, { role: 'assistant', text: data.response }]);
        } catch (error) {
          console.error('Error fetching data:', error);
          setMessages((prevMessages) => [...prevMessages, { role: 'assistant', text: 'Error: Could not connect to the backend.' }]);
        } finally {
          setLoading(false);
        }
      };

      return (
        <main style={{ padding: '20px', display: 'flex', flexDirection: 'column', height: '100vh', boxSizing: 'border-box' }}>
          <h1>LLM Full-Stack Chat App</h1>

          {/* Chat container with a scrollbar */}
          <div
            ref={chatContainerRef}
            style={{
              flex: 1,
              border: '1px solid #ccc',
              padding: '10px',
              overflowY: 'auto',
              marginBottom: '10px',
              borderRadius: '8px',
            }}
          >
            {messages.map((msg, index) => (
              <div key={index} style={{ marginBottom: '10px' }}>
                <p>
                  <strong style={{ color: msg.role === 'user' ? 'blue' : 'green' }}>{msg.role === 'user' ? 'You' : 'Agent'}:</strong> {msg.text}
                </p>
              </div>
            ))}
            {loading && <p style={{ color: '#888' }}>Agent is thinking...</p>}
          </div>

          {/* Input form */}
          <form onSubmit={handleQuerySubmit} style={{ display: 'flex' }}>
            <input
              type="text"
              value={inputQuery}
              onChange={(e) => setInputQuery(e.target.value)}
              placeholder="Ask me a question..."
              disabled={loading}
              style={{ flex: 1, padding: '10px', borderRadius: '8px', border: '1px solid #ccc' }}
            />
            <button
              type="submit"
              disabled={loading}
              style={{ marginLeft: '10px', padding: '10px 20px', borderRadius: '8px', border: 'none', backgroundColor: '#0070f3', color: 'white', cursor: 'pointer' }}
            >
              Send
            </button>
          </form>
        </main>
      );
    }
    ```

2.  **Test the Full Chat App:**

      * Ensure your FastAPI server is still running in its terminal (`cd backend` and `uvicorn main:app --reload`).
      * In a separate terminal, ensure your Next.js app is also running (`cd frontend` and `npm run dev`).
      * Open your browser to `http://localhost:3000`. You now have a complete chat interface. Ask a series of questions and watch the conversation history build. You'll see the "Agent is thinking..." message while the backend is processing your query.

3.  **Commit the Final Prototype:** This marks the completion of the full-stack prototype. You've successfully migrated from a simple single-file app to a more scalable and organized client-server architecture.

    ```bash
    # Go back to the root directory
    cd ..
    git add .
    git commit -m "Implemented full chat interface in Next.js frontend"
    ```

You have now completed **Phase 2: Full-Stack Development**. You have a working, professional-grade LLM application running locally. The next and final step in this week's project is to deploy this application to a cloud provider so it can be used by anyone.