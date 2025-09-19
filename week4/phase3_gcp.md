Starting with GCP is an excellent choice. We will use a modern, serverless, and cost-effective approach by deploying your backend and frontend to different services. The setup will also include Git integration for a robust continuous deployment (CD) pipeline.

Here is the step-by-step plan for deploying your full-stack application to Google Cloud Platform.

### Phase 3: Cloud Deployment - GCP

### Part 1: Deploying the FastAPI Backend to Google App Engine

Google App Engine is a great starting point for a Python backend. It's a fully managed serverless platform that automatically handles scaling, so you don't have to worry about servers.

#### **Hands-on Session: Getting the Backend Live**

1.  **Set up your GCP Project:**

      * If you don't have one, create a Google Cloud account and a new project in the [GCP Console](https://console.cloud.google.com/).
      * Enable billing for your project.
      * Install the `gcloud` CLI tools from the [Google Cloud SDK](https://cloud.google.com/sdk/docs/install).
      * Authenticate the CLI by running `gcloud auth login` and set your project with `gcloud config set project [YOUR_PROJECT_ID]`.

2.  **Prepare the Backend for Deployment:**

      * Navigate to your `backend` directory.
      * Create a new file named `app.yaml`. This file tells App Engine how to run your application.

    <!-- end list -->

    ```yaml
    # backend/app.yaml
    runtime: python39  # Use a modern Python version

    entrypoint: uvicorn main:app --host 0.0.0.0 --port 8080

    env_variables:
      # Add any environment variables your agent needs here
      OPENAI_API_KEY: 'your-api-key-here'
      # It's best practice to use Secret Manager for secrets in production

    # You can add scaling settings here for more control
    # automatic_scaling:
    #   max_instances: 5
    ```

      * Create a `requirements.txt` file in the same `backend` directory. List all your Python dependencies.

    <!-- end list -->

    ```
    # backend/requirements.txt
    fastapi
    uvicorn
    # ... any other libraries for your agent like openai, sentence-transformers, etc.
    ```

      * **Final Check:** Ensure your `main.py`, `agent_service.py`, `requirements.txt`, and `app.yaml` are all in the `backend` directory.

3.  **Deploy the Backend:**

      * Make sure you are in the `backend` directory in your terminal.
      * Run the deployment command:

    <!-- end list -->

    ```bash
    gcloud app deploy
    ```

      * Follow the prompts to select a region for your application. This command will create and deploy your backend service. It might take a few minutes.
      * Once complete, it will give you a public URL for your application (e.g., `https://[YOUR_PROJECT_ID].appspot.com`).

4.  **Test the Live API:**

      * Go to your public URL followed by `/docs` (e.g., `https://[YOUR_PROJECT_ID].appspot.com/docs`).
      * You should see your FastAPI interactive documentation, and you can now test the `/chat` endpoint live.

### Part 2: Deploying the Next.js Frontend to Google Cloud Storage

Next.js provides a static build that is perfect for deployment to a simple storage service, which keeps costs down.

#### **Hands-on Session: Getting the Frontend Live**

1.  **Build the Frontend for Production:**

      * Navigate to your `frontend` directory.
      * Change the build output in your `next.config.js` to static.

    <!-- end list -->

    ```javascript
    // frontend/next.config.js
    /** @type {import('next').NextConfig} */
    const nextConfig = {
      output: 'export',
      // Optional: Add other configurations
    };

    module.exports = nextConfig;
    ```

      * Create the production build.

    <!-- end list -->

    ```bash
    npm run build
    ```

      * This will create a `out` folder in your `frontend` directory containing all the static HTML, CSS, and JavaScript files.

2.  **Create a Cloud Storage Bucket:**

      * In the [GCP Console](https://console.cloud.google.com/storage/browser), go to Cloud Storage and click "Create Bucket".
      * Give it a unique, globally accessible name (e.g., `your-llm-app-frontend-bucket`).
      * Choose a region and click "Create".
      * **Set Permissions:** Once created, click on the bucket, go to the "Permissions" tab, and add a new principal `allUsers`. Give this principal the `Storage Object Viewer` role. This makes your website's files publicly accessible.

3.  **Deploy the Frontend Files:**

      * Using the `gcloud` CLI, upload the contents of your `out` directory to the bucket.

    <!-- end list -->

    ```bash
    # Make sure you are in the `frontend` directory
    gcloud storage cp --recursive out/* gs://your-llm-app-frontend-bucket/
    ```

4.  **Configure the Bucket for Website Hosting:**

      * In the GCP Console, go to your bucket's settings.
      * Under the "Static Website" tab, set `index.html` as the index file. Save the changes.
      * Your website is now live at `https://storage.googleapis.com/[YOUR_BUCKET_NAME]/index.html`.

-----

### Part 3: Setting up CI/CD with Git Integration

This is where the magic happens. We will automate the deployment process so that every time you push code to GitHub, it automatically updates your live application.

1.  **Create a Git Repository:** If you haven't already, create a new repository on GitHub and push your local code.

      * `git remote add origin https://github.com/[YOUR_USERNAME]/[YOUR_REPO_NAME].git`
      * `git push -u origin main`

2.  **Connect GCP to GitHub:**

      * Go to the [Cloud Build Console](https://console.cloud.google.com/cloud-build/triggers).
      * Click "Create trigger".
      * Connect your GitHub repository to GCP following the on-screen instructions.

3.  **Create a Cloud Build Trigger:**

      * For the **FastAPI Backend**, set the trigger to:

          * **Source:** Your GitHub repository.
          * **Branch:** `main` (or the branch you want to deploy from).
          * **Build Configuration:** Select "Autodetected" and ensure the location is `/backend` to tell Cloud Build to run the deployment command from that directory.
          * **Action:** When the trigger runs, it will execute `gcloud app deploy`.

      * For the **Next.js Frontend**, create a second trigger:

          * **Source:** Your GitHub repository.
          * **Branch:** `main`.
          * **Build Configuration:** Create a `cloudbuild.yaml` file in the root of your project:

        <!-- end list -->

        ```yaml
        # cloudbuild.yaml
        steps:
        # Build the Next.js app
        - name: 'node'
          entrypoint: 'npm'
          args: ['run', 'build']
          dir: 'frontend'

        # Deploy the static files to GCS
        - name: 'gcr.io/cloud-builders/gsutil'
          args: ['-m', 'rsync', '-r', 'frontend/out/', 'gs://your-llm-app-frontend-bucket/']
        ```

          * Your Cloud Build trigger will now automatically build and upload your static website files on every push to the `main` branch.

You have now successfully deployed your full-stack LLM app to GCP with a robust CI/CD pipeline. The next time you push code to your repository, your application will automatically update itself\!

Ready for the next one? Let's move on to **AWS** when you are.