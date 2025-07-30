# 🚀 React App with Full CI/CD and Testing Overview

This document provides a complete guide to setting up, testing, and deploying a **React application** to **AWS S3** using a fully automated **CI/CD pipeline**. It covers local development, an overview of common testing frameworks, and a step-by-step tutorial for configuring a pipeline with **AWS CodePipeline**, **CodeBuild**, and **GitHub**.

Every time you push changes to your `main` branch, your app is automatically built and deployed.

---

## 📦 Prerequisites

Make sure the following are ready before starting:

- [Node.js](https://nodejs.org/) and npm installed.
- An AWS account with permissions to create S3 buckets, CodePipeline, and CodeBuild resources.
- A GitHub account and a repository for your project.

---

## 🛠️ Local Setup

### 1. Clone the repository or create a new React app

To work from an existing repository:

git clone https://github.com/your-username/your-repo.git
cd your-repo

---

To create a new React app inside your current empty folder:

The dot (.) initializes the project in the current directory
npx create-react-app .

---

### 2. Install dependencies

npm install

---

### 3. Start the app locally

npm start

---

Visit `http://localhost:3000` in your browser to see the app running.

---

## ⚙️ AWS CI/CD Pipeline Setup

Follow these steps to establish the pipeline that builds and deploys your app to an S3 bucket automatically.

### ✅ Step 1: Create an S3 Bucket

1.  Go to the **AWS S3 Console**.
2.  Click **Create bucket**.
3.  Enter a globally unique name (e.g., `my-react-app-cicd-demo`).
4.  Choose your desired AWS Region.
5.  Click **Create bucket**.

### 📂 Step 2: Prepare Your Code Repository

1.  Push your React code to your GitHub repository.
2.  Add a `buildspec.yml` file at the root of your project. This file tells AWS CodeBuild how to build your app.

    ```
    version: 0.2

    phases:
      install:
        runtime-versions:
          nodejs: 18
        commands:
          - echo Installing dependencies...
          - npm ci
      build:
        commands:
          - echo Building React app...
          - npm run build

    artifacts:
      # Include all files from the 'build' directory
      base-directory: build
      files:
        - '**/*'
    ```

3.  Commit and push the `buildspec.yml` file:
    ```
    git add buildspec.yml
    git commit -m "Add buildspec.yml for AWS CodeBuild"
    git push
    ```

### 🏗️ Step 3: Create a CodePipeline Pipeline

1.  Go to the **AWS CodePipeline Console**.
2.  Click **Create pipeline**.
3.  **Pipeline settings**: Enter a pipeline name (e.g., `react-app-deployment-pipeline`) and let AWS create a new service role.
4.  **Source stage**:
    - **Source provider**: Select **GitHub (Version 2)**.
    - Connect your GitHub account.
    - Select your repository and the branch you want to deploy from (e.g., `main`).
5.  **Build stage**:
    - **Build provider**: Select **AWS CodeBuild**.
    - Click **Create project**. A new window will open.
    - **Project name**: `react-app-build`.
    - **Environment**: Select `Managed image` with `aws/codebuild/standard:7.0` (or newer).
    - **Runtime**: Node.js.
    - **Buildspec**: Select `Use a buildspec file`.
    - Create the build project and return to the CodePipeline setup.
6.  **Deploy stage**:
    - **Deploy provider**: Select **Amazon S3**.
    - **Region**: Choose the region where you created your bucket.
    - **Bucket**: Select the S3 bucket you created in Step 1.
    - **Important**: Check the box for **Extract file before deploy**.
7.  Review all settings and click **Create pipeline**.

### 🌐 Step 4: Enable Static Website Hosting on S3

1.  Navigate to your S3 bucket in the AWS Console.
2.  Go to the **Properties** tab.
3.  Scroll down to **Static website hosting** and click **Edit**.
4.  Select **Enable**.
5.  Set the **Index document** to `index.html`.
6.  Set the **Error document** to `index.html` (this helps with client-side routing).
7.  Click **Save changes**.

### 🔓 Step 5: Configure the S3 Bucket Policy

To make your website publicly accessible, you must attach a bucket policy.

1.  In your S3 bucket, go to the **Permissions** tab.
2.  Under **Bucket Policy**, click **Edit**.
3.  Paste the following JSON, replacing `your-bucket-name` with your actual bucket name.

    ```
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Sid": "PublicReadGetObject",
          "Effect": "Allow",
          "Principal": "*",
          "Action": "s3:GetObject",
          "Resource": "arn:aws:s3:::your-bucket-name/*"
        }
      ]
    }
    ```

    ⚠️ **Warning**: This policy makes all files in the bucket publicly readable. Do not use it for sensitive data.

4.  Click **Save changes**.

---

### 🚀 Step 6: Commit & Push to Trigger Deployment

Your pipeline is now active. To trigger a deployment, simply make a code change and push it to the configured branch (`main`).

## Make a change to your app

1. git add .
2. git commit -m "Trigger CI/CD deployment"
3. git push

---

CodePipeline will automatically:

1.  Detect the new commit.
2.  Start a build with CodeBuild.
3.  Deploy the built artifacts to your S3 bucket.

---

## 🌍 Access Your Live React App

Once the pipeline succeeds, your app will be accessible at the **static website endpoint**. You can find this URL in your S3 bucket under **Properties** → **Static website hosting**.

It will look like: `http://your-bucket-name.s3-website.<region>.amazonaws.com`

---

## ✅ Summary Checklist

| Task                           | Status |
| ------------------------------ | ------ |
| React App Created              | ✅     |
| GitHub Repository Pushed       | ✅     |
| `buildspec.yml` Created        | ✅     |
| S3 Bucket Created              | ✅     |
| Static Website Hosting Enabled | ✅     |
| Bucket Policy Applied          | ✅     |
| CodePipeline Connected         | ✅     |
| Auto Deployments Working       | ✅     |

---

## 🙌 Final Notes

Your React app is now live with a modern, automated AWS CI/CD pipeline! Every push to your `main` branch updates your live application—no manual steps needed.

## 👨‍💻 About Me

Visit my portfolio: [deodeepkunj.dev](https://www.deodeepkunj.dev)

## 📘 Blogs

Read my latest technical write-ups at [Medium](https://medium.com/@deodeepkunj)
