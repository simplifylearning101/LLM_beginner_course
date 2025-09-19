Of course. We have a robust backend ready to go. Now, let's build the user-facing part of the application.

-----

### Part 2: Building the Frontend with Next.js

In this part, we'll build a basic frontend using **Next.js**, a powerful framework for building React applications. The goal is to create a simple form that sends data to your FastAPI backend and displays the response.

#### **Hands-on Session: Creating the Next.js Client** ⚛️

1.  **Set up the Frontend Directory:** Navigate to the root directory of your project (`my-llm-app`). We will create a new Next.js app in a subdirectory to keep our project organized.

    ```bash
    mkdir frontend
    cd frontend
    ```

2.  **Initialize the Next.js App:** Use `create-next-app` to set up a new project. You can accept the default options by pressing Enter for most of the prompts.

    ```bash
    npx create-next-app@latest .
    ```

      * When prompted "Would you like to use TypeScript?", select **No**.
      * When prompted "Would you like to use Tailwind CSS?", select **No**.
      * When prompted "Would you like to use the `src/` directory?", select **No**.
      * When prompted "Would you like to use App Router?", select **Yes**.
      * When prompted "Would you like to customize the default import alias?", select **No**.

3.  **Run the App:** Now, start the Next.js development server to see the default application.

    ```bash
    npm run dev
    ```

    Open your browser and go to `http://localhost:3000`. You should see the Next.js welcome page.

4.  **Simplify the Default Page:** Open the `frontend/app/page.js` file. This file represents the home page of your application. Replace its content with a clean slate for our simple UI.

    ```javascript
    // frontend/app/page.js
    'use client'; // This is a required directive for client-side components in Next.js

    import { useState } from 'react';

    export default function HomePage() {
      const [query, setQuery] = useState('');
      const [response, setResponse] = useState('');
      const [loading, setLoading] = useState(false);

      const handleQuerySubmit = async () => {
        if (!query) return;

        setLoading(true);
        setResponse('Thinking...');

        try {
          const res = await fetch('http://localhost:8000/chat', {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json',
            },
            body: JSON.stringify({ text: query }),
          });
          const data = await res.json();
          setResponse(data.response);
        } catch (error) {
          console.error('Error fetching data:', error);
          setResponse('Error: Could not connect to the backend.');
        } finally {
          setLoading(false);
        }
      };

      return (
        <main style={{ padding: '20px' }}>
          <h1>LLM Chat Client</h1>
          <div>
            <input
              type="text"
              value={query}
              onChange={(e) => setQuery(e.target.value)}
              placeholder="Enter your query here..."
              style={{ width: '300px', padding: '10px' }}
            />
            <button
              onClick={handleQuerySubmit}
              disabled={loading}
              style={{ marginLeft: '10px', padding: '10px' }}
            >
              {loading ? 'Sending...' : 'Send'}
            </button>
          </div>
          <div style={{ marginTop: '20px' }}>
            <h3>Agent's Response:</h3>
            <p>{response}</p>
          </div>
        </main>
      );
    }
    ```

      * The `use client` directive is important for using client-side features like `useState` and `useEffect`.
      * The `handleQuerySubmit` function sends a `POST` request to your FastAPI endpoint at `http://localhost:8000/chat`.
      * We use `useState` to manage the user's query, the agent's response, and a loading state.

5.  **Test the Full Stack:** Make sure your FastAPI server is running in a separate terminal window (`cd backend` and `uvicorn main:app --reload`). Then, in your frontend terminal, run `npm run dev` again.

      * Go to `http://localhost:3000`.
      * Type a query into the input box and click "Send".
      * Your Next.js frontend will communicate with your FastAPI backend, which in turn will run your LLM agent and return the response to the frontend.

6.  **Commit Your Progress:** You've now connected your frontend to your backend. This is a massive accomplishment\!

    ```bash
    # Go back to the root directory
    cd ..
    git add .
    git commit -m "Created Next.js frontend and connected it to the FastAPI API"
    ```