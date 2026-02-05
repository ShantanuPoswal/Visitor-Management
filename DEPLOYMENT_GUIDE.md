# Deployment Guide for Visitor Management System

This guide will help you deploy your Visitor Management System to **Render.com**.
The project consists of two parts: a **Backend** (Node.js) and a **Frontend** (React). We will deploy them as two separate services.

## Prerequisites
1.  **GitHub Account**: Your code must be pushed to a GitHub repository.
2.  **Render Account**: Create one at [render.com](https://render.com).
3.  **MongoDB Atlas**: A cloud-hosted MongoDB database (since `localhost` won't work on the cloud).

---

## Step 1: Push Code to GitHub
1.  Create a new repository on GitHub.
2.  Push your code to it:
    ```bash
    git add .
    git commit -m "Ready for deployment"
    git remote add origin <your-github-repo-url>
    git push -u origin main
    ```

## Step 2: Database Setup (MongoDB Atlas)
1.  Go to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas).
2.  Create a free cluster.
3.  Create a generic user (e.g., `dbUser`) and password.
4.  Allow access from "Anywhere" (`0.0.0.0/0`) in IP Access List.
5.  Get your connection string. It will look like:
    `mongodb+srv://<username>:<password>@cluster0.xyz.mongodb.net/?retryWrites=true&w=majority`

## Step 3: Deploy Backend (Web Service)
1.  On Render, click **New +** and select **Web Service**.
2.  Connect your GitHub repository.
3.  Configure the service:
    *   **Name**: `visitor-backend` (or similar)
    *   **Root Directory**: `Backend` (Important!)
    *   **Runtime**: Node
    *   **Build Command**: `npm install`
    *   **Start Command**: `node server.js`
4.  **Environment Variables** (Scroll down to "Advanced"):
    *   Add `MONGO_URI`: (Paste your MongoDB Atlas connection string here)
    *   Add `JWT_SECRET`: (Enter a secure secret key, e.g., `mySuperSecretKey123`)
    *   Add `PORT`: `10000` (Render creates this automatically, but good to know)
    *   Add `FRONTEND_URL`: (Leave this blank for now, we will fill it after deploying the frontend)
5.  Click **Create Web Service**.
6.  Wait for it to deploy. Copy the **Service URL** (e.g., `https://visitor-backend.onrender.com`).

## Step 4: Deploy Frontend (Static Site)
1.  On Render, click **New +** and select **Static Site**.
2.  Connect the same GitHub repository.
3.  Configure the service:
    *   **Name**: `visitor-frontend`
    *   **Root Directory**: `frontend` (Important!)
    *   **Build Command**: `npm install && npm run build`
    *   **Publish Directory**: `build`
4.  **Environment Variables**:
    *   Add `REACT_APP_API_URL`: Paste your **Backend URL** from Step 3, **and append `/api`** at the end.
        *   Example: `https://visitor-backend.onrender.com/api`
5.  Click **Create Static Site**.
6.  Wait for it to deploy. Copy the **Site URL** (e.g., `https://visitor-frontend.onrender.com`).

## Step 5: Connect Frontend to Backend
1.  Go back to your **Backend Service** dashboard on Render.
2.  Go to **Environment**.
3.  Edit `FRONTEND_URL` and paste your **Frontend URL** (from Step 4).
    *   Example: `https://visitor-frontend.onrender.com` (no trailing slash is best, but code handles it)
4.  **Save Changes**. Render will automatically restart the backend.

## Troubleshooting
*   **CORS Errors**: If you see CORS errors in the browser console, ensure the `FRONTEND_URL` in the Backend Environment Variables matches your actual Frontend URL exactly.
*   **Database Connection**: If the backend fails to start, check your `MONGO_URI` in the Backend logs. Ensure you replaced `<password>` with your actual password in the connection string.
