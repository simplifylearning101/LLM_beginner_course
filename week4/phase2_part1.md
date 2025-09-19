Alright, let's transition to the full-stack version of the application. The goal of this phase is to build a robust and scalable backend using **FastAPI** that our frontend can communicate with.

-----

### Phase 2: Full-Stack Development

### Part 1: Building the FastAPI Backend

We're moving from a monolithic Streamlit app to a client-server architecture. Your LLM agent will now reside on a backend server and be exposed via a clean API endpoint.

#### **Hands-on Session: Creating the FastAPI Server** 👨‍💻

1.  **Set up the Backend Directory:** In your existing `my-llm-app` folder, create a new directory for your backend code. This helps separate the backend and frontend parts of your project.

    ```bash
    mkdir backend
    cd backend

    # You are already in your project's virtual environment from Phase 1.
    # If not, create and activate it now.
    # python -m venv venv
    # source venv/bin/activate
    ```

2.  **Install FastAPI and Uvicorn:** FastAPI doesn't come with a built-in server. We'll use Uvicorn, a lightning-fast ASGI server, to run our application.

    ```bash
    pip install "fastapi[all]" uvicorn
    ```

    *`[all]` is a shortcut that installs all optional dependencies, including Pydantic for data validation and other tools.*

3.  **Create the Main Application File:** Create a file named `main.py` inside your `backend` directory. This will be the entry point for our server. Add the following code for a basic "Hello, World" endpoint.

    ```python
    # backend/main.py
    from fastapi import FastAPI
    from pydantic import BaseModel

    app = FastAPI()

    # Define a data model for the request body
    class Query(BaseModel):
        text: str

    @app.get("/")
    async def read_root():
        """A simple root endpoint to confirm the server is running."""
        return {"message": "Welcome to the FastAPI LLM Agent API!"}

    # This is where your agent logic will go in the next step
    @app.post("/chat")
    async def chat_with_agent(query: Query):
        """Processes a user query and returns a response from the LLM agent."""
        return {"response": f"You asked: {query.text}"}
    ```

4.  **Run the Server:** From inside the `backend` directory, run the server using Uvicorn. The `--reload` flag is great for development as it automatically restarts the server when you make changes.

    ```bash
    uvicorn main:app --reload
    ```

    *`main` refers to the file (`main.py`) and `app` refers to the FastAPI instance (`app = FastAPI()`).*

5.  **Test the API:** Open your web browser and navigate to `http://127.0.0.1:8000`. You should see the welcome message. Even better, navigate to `http://127.0.0.1:8000/docs`. FastAPI automatically generates interactive documentation for your API using the OpenAPI standard (Swagger UI). You can test your `/chat` endpoint directly from here.

6.  **Commit the Backend Setup:** Let's save this state in your project's Git repository. Navigate back to your root directory (`my-llm-app`).

    ```bash
    git add .
    git commit -m "Set up FastAPI backend with a simple chat endpoint"
    ```

-----

### Part 2: Integrating the Agent into the API

Now that your backend is up and running, let's integrate your powerful agent. We'll move the core logic from your Streamlit file into a separate service module and call it from our new FastAPI endpoint.

#### **Hands-on Session: Connecting the Dots** 🤝

1.  **Create the Agent Service:** Create a new Python file in your `backend` directory called `agent_service.py`. This will contain all the core logic of your LLM agent.

    ```bash
    # Make sure you are in the backend directory
    touch agent_service.py
    ```

2.  **Move Agent Logic:** Copy all of the agent's code—the tool functions, the prompt, and the `run_agent(user_query)` function—from your `app.py` file into this new `agent_service.py` file. This isolates the agent logic from the API logic, which is a key principle of good software design.

3.  **Modify `main.py`:** Now, update your `main.py` file to import and use the agent service. We'll also add a few more things to handle API requests properly.

    ```python
    # backend/main.py
    from fastapi import FastAPI
    from fastapi.middleware.cors import CORSMiddleware
    from pydantic import BaseModel
    from .agent_service import run_agent  # Import your agent function

    app = FastAPI()

    # We need to add CORS middleware to allow the frontend (on a different port)
    # to access this API.
    origins = ["*"]  # In a production app, specify your frontend's URL here

    app.add_middleware(
        CORSMiddleware,
        allow_origins=origins,
        allow_credentials=True,
        allow_methods=["*"],
        allow_headers=["*"],
    )

    class Query(BaseModel):
        text: str

    @app.get("/")
    async def read_root():
        return {"message": "Welcome to the FastAPI LLM Agent API!"}

    @app.post("/chat")
    async def chat_with_agent(query: Query):
        """Processes a user query and returns a response from the LLM agent."""
        print(f"Received query: {query.text}")
        response = run_agent(query.text)
        return {"response": response}
    ```

4.  **Test the Full Agent via the API:** Run your FastAPI server again (`uvicorn main:app --reload`). Go back to `http://127.0.0.1:8000/docs`, find the `/chat` endpoint, click "Try it out," and enter a query that uses one of your agent's tools. You should see a response from your agent.

5.  **Commit Your Progress:** You've successfully separated your agent logic and exposed it via an API. This is a huge milestone.

    ```bash
    # Go back to the root directory
    cd ..
    git add .
    git commit -m "Integrated LLM agent into FastAPI and created chat API endpoint"
    ```