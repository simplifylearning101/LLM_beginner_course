Let's get your app running on Azure. We'll use **Azure App Service**, a powerful and versatile platform that simplifies the process of deploying and scaling both your Python and Node.js applications. For CI/CD, we'll use a direct integration with GitHub.

-----

### Phase 3: Cloud Deployment - Azure

### Part 1: Deploying the FastAPI Backend to Azure App Service

We will deploy your FastAPI backend to a dedicated App Service instance. This is a common and robust approach for hosting Python web APIs on Azure.

#### **Hands-on Session: Getting the Backend Live** 🖥️

1.  **Set up your Azure Account and CLI:**

      * If you don't have one, create an Azure account and a new subscription.
      * Install the **Azure CLI** and authenticate by running `az login` in your terminal.

2.  **Prepare the Backend for Deployment:**

      * Navigate to your `backend` directory.
      * Ensure your `requirements.txt` file is up-to-date with all your Python dependencies. Azure will use this to install everything needed to run your app.
      * Azure App Service requires a **startup command** to tell it how to run your FastAPI application. We'll set this during the deployment process.

3.  **Create the Azure App Service:**

      * Create a **resource group** to organize your resources.

    <!-- end list -->

    ```bash
    az group create --name my-llm-app-rg --location eastus
    ```

      * Create an **App Service plan**. This defines the underlying compute resources for your app.

    <!-- end list -->

    ```bash
    az appservice plan create --name my-llm-app-plan --resource-group my-llm-app-rg --is-linux --sku B1
    ```

      * Create the **FastAPI web app**.

    <!-- end list -->

    ```bash
    az webapp create --resource-group my-llm-app-rg --plan my-llm-app-plan --name your-fastapi-app-name --runtime 'PYTHON|3.9'
    ```

      * **Note:** `your-fastapi-app-name` must be a globally unique name.

4.  **Configure and Deploy the Backend:**

      * Set the startup command for your app. The command tells Azure to use Gunicorn, a production-level server, to run your `main:app` instance.

    <!-- end list -->

    ```bash
    az webapp config set --resource-group my-llm-app-rg --name your-fastapi-app-name --startup-file "gunicorn --bind 0.0.0.0 --workers 4 main:app"
    ```

      * **Set your API Key:** Configure your environment variables in the Azure portal for your FastAPI app under **Settings \> Configuration**. Add a new application setting with the name `OPENAI_API_KEY` and your actual API key as the value.

-----

### Part 2: Deploying the Next.js Frontend to Azure App Service

We'll use another App Service instance to host the Node.js server that serves your Next.js application.

#### **Hands-on Session: Getting the Frontend Live** 🌐

1.  **Prepare the Frontend:**

      * Navigate to your `frontend` directory.
      * Update the `fetch` call in `frontend/app/page.js` to point to your live Azure backend URL: `https://your-fastapi-app-name.azurewebsites.net/chat`.
      * In your `frontend` directory, create a simple `server.js` file to allow Azure to run your Next.js app.

    <!-- end list -->

    ```javascript
    // frontend/server.js
    const { createServer } = require('http');
    const next = require('next');

    const port = process.env.PORT || 3000;
    const app = next({ dev: false });
    const handle = app.getRequestHandler();

    app.prepare().then(() => {
      createServer(handle).listen(port, (err) => {
        if (err) throw err;
        console.log(`> Ready on http://localhost:${port}`);
      });
    });
    ```

2.  **Create a separate App Service for the frontend:**

    ```bash
    az webapp create --resource-group my-llm-app-rg --plan my-llm-app-plan --name your-nextjs-app-name --runtime 'NODE|18-lts'
    ```

3.  **Set the startup command** for your Next.js app.

    ```bash
    az webapp config set --resource-group my-llm-app-rg --name your-nextjs-app-name --startup-file "npm run start"
    ```

      * You will also need to add a `start` script to your `package.json` file in your `frontend` directory: `"start": "node server.js"`.

-----

### Part 3: Setting up CI/CD with Git Integration

Azure App Service's **Deployment Center** makes connecting your GitHub repository and setting up a continuous deployment pipeline a breeze.

1.  **Connect your GitHub Repository:**

      * In the Azure Portal, navigate to your **FastAPI** App Service instance.
      * On the left-hand menu, select **Deployment Center** under "Deployment".
      * Choose **GitHub** as your source and authorize access.
      * Select your repository and the `backend` folder as the source for this deployment. Azure will automatically generate a GitHub Actions workflow file for you.
      * Repeat this process for your **Next.js** App Service instance, selecting the same repository but pointing to the `frontend` folder.

2.  **Automated Deployment:**

      * Azure automatically creates a `.github/workflows` folder in your repository with YAML files for your pipelines.
      * Now, every time you push new code to your `main` branch, GitHub Actions will automatically build and deploy your backend and frontend to their respective App Service instances.

Congratulations, you have now successfully deployed your full-stack LLM application to all three major cloud providers\! You have a powerful, flexible, and fully automated setup for your project.

To help you get started with the Azure App Service deployment, here is a helpful video.

  * [Integrate GitHub as Source Code in Azure App Service and Automate Deployment with GitHub Actions](https://www.youtube.com/watch?v=lCdK_QP9SFc)
