Moving to AWS is a great way to expand your cloud skills. We'll use two key AWS services to get your full-stack app live. We'll use **AWS Elastic Beanstalk** for your backend and **AWS Amplify** for your frontend. This combination provides a powerful, managed, and highly integrated deployment experience with built-in CI/CD.

-----

### Phase 3: Cloud Deployment - AWS

### Part 1: Deploying the FastAPI Backend to AWS Elastic Beanstalk

AWS Elastic Beanstalk simplifies the deployment of web applications by handling all the provisioning of servers, load balancers, and scaling. It's a perfect match for a Python backend.

#### **Hands-on Session: Getting the Backend Live** 🛠️

1.  **Set up your AWS Account and CLI:**

      * If you haven't already, create an AWS account.
      * Install the AWS CLI and configure it by running `aws configure`.
      * Install the **Elastic Beanstalk CLI (EB CLI)** with `pip install awsebcli --user`.

2.  **Prepare the Backend for Deployment:**

      * Navigate to your `backend` directory.
      * Create a new file named `.ebextensions/python.config` inside your `backend` folder. This file configures the Python environment for Elastic Beanstalk.

    <!-- end list -->

    ```yaml
    # backend/.ebextensions/python.config
    option_settings:
      aws:elasticbeanstalk:container:python:
        WSGIPath: main:app
    ```

      * The `WSGIPath` tells Elastic Beanstalk to run your FastAPI application from the `app` instance in the `main.py` file.

      * Create a file named `Procfile` in the `backend` directory to specify the command to run your server.

    <!-- end list -->

    ```
    # backend/Procfile
    web: uvicorn main:app --host 0.0.0.0 --port 5000
    ```

      * **Final Check:** Ensure your `main.py`, `agent_service.py`, `requirements.txt`, `.ebextensions/python.config`, and `Procfile` are all in the `backend` directory.

3.  **Initialize Elastic Beanstalk:**

      * In your terminal, from the `backend` directory, run the EB CLI initialization command.

    <!-- end list -->

    ```bash
    eb init
    ```

      * Follow the prompts:
          * Select a region.
          * Choose "Create New Application".
          * The EB CLI should automatically detect your environment as **Python**. Confirm this.
          * Select the latest Python version.
          * Say "Yes" to setting up SSH if you want to be able to access the server for debugging.

4.  **Create the Environment and Deploy:**

      * Now, create the Elastic Beanstalk environment.

    <!-- end list -->

    ```bash
    eb create your-llm-app-backend-env
    ```

      * The deployment process will take a few minutes as AWS provisions the necessary infrastructure.
      * Once it's healthy, you can open your live application in a browser with `eb open`.
      * Copy the URL of your live backend. You will need this for the frontend.

-----

### Part 2: Deploying the Next.js Frontend to AWS Amplify

AWS Amplify is an incredibly simple service for deploying modern front-end applications with built-in CI/CD.

#### **Hands-on Session: Getting the Frontend Live** 🚀

1.  **Prepare the Frontend:**

      * Navigate to your `frontend` directory.
      * Since Amplify uses a build process, you'll need to define your API URL as a build-time environment variable. You can't use a relative path.
      * **Modify `page.js`:** Update the API endpoint URL in your `fetch` call in `frontend/app/page.js` to point to your live Elastic Beanstalk backend URL.

    <!-- end list -->

    ```javascript
    // frontend/app/page.js (modified fetch call)
    // ...
    try {
        const res = await fetch('http://your-llm-app-backend-env.us-east-1.elasticbeanstalk.com/chat', {
            method: 'POST',
            // ...
        });
        // ...
    } catch (error) {
        // ...
    }
    // ...
    ```

      * **Create `amplify.yml`:** In the root of your project (`my-llm-app`), create an `amplify.yml` file. This is a build specification file that tells Amplify how to build and deploy your monorepo project.

    <!-- end list -->

    ```yaml
    # amplify.yml
    version: 1
    frontend:
      phases:
        preBuild:
          commands:
            - npm ci
        build:
          commands:
            - npm run build
      artifacts:
        baseDirectory: frontend/.next # Tell Amplify to look for the build files in the Next.js output directory.
        files:
          - '**/*'
      cache:
        paths:
          - node_modules/**/*
    ```

      * **Update `.gitignore`**: Make sure your `.gitignore` file includes `frontend/.next/`.

2.  **Deploy the Frontend with AWS Amplify:**

      * Go to the [AWS Amplify Console](https://console.aws.amazon.com/amplify/home).
      * Click **Get started** under "Amplify Hosting".
      * Choose **GitHub** as your repository provider and connect your account.
      * Select your project's repository and the `main` branch.
      * In the build settings, Amplify should automatically detect your Next.js framework. Make sure the build settings are correct. You can use the `amplify.yml` file you created.
      * Review the settings and click **Save and deploy**.

3.  **Test the Full Stack:**

      * Amplify will now build and deploy your frontend. Once complete, it will provide you with a public URL (e.g., `https://main.d123.amplifyapp.com`).
      * Navigate to this URL. Your Next.js frontend is now live and talking to your FastAPI backend on Elastic Beanstalk. Congratulations\! 🎉

-----

### Part 3: Setting up CI/CD with Git Integration

You've already set up the continuous deployment pipelines.

  * **AWS Amplify:** By connecting your GitHub repository, you've automatically created a CI/CD pipeline. Every time you push a new commit to the `main` branch, Amplify will automatically build and redeploy your frontend.
  * **AWS Elastic Beanstalk:** You can use **AWS CodePipeline** to create a pipeline that watches your GitHub repository and automatically deploys changes to Elastic Beanstalk. This is a more advanced setup. For now, to deploy new backend code, you can simply run `eb deploy` from your `backend` directory, and it will deploy the latest version of your code from your local machine to the environment.

You have now successfully deployed your full-stack LLM app to AWS. Next up: **Azure**\!