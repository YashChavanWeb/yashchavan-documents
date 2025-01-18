# Deploying a MERN Stack Application for FREE - Complete Guide

Document by Yash Chavan  
[LinkedIn](https://www.linkedin.com/in/YashChavanWeb/) | [GitHub](https://github.com/YashChavanWeb/)

## Contents

1. [Step 1: Configure the Frontend](#step-1-configure-the-frontend)
2. [Step 2: Configure the Backend](#step-2-configure-the-backend)
3. [Step 3: Update the Root `package.json` File](#step-3-update-the-root-packagejson-file)
4. [Step 4: Update the Environment Variables](#step-4-update-the-environment-variables)
5. [Step 5: Deploy the Application](#step-5-deploy-the-application)
   - [Step 5.1: Upload the Application to GitHub](#step-51-upload-the-application-to-github)
   - [Step 5.2: Deploy the Application on Render](#step-52-deploy-the-application-on-render)
6. [Step 6: Access the Application](#step-6-access-the-application)
7. [Conclusion](#conclusion)

---

## Introduction

This guide walks you through the process of deploying a MERN (MongoDB, Express, React, Node) stack application under a single domain. We'll go through configuring both the frontend and backend, and deploying the application online for public access.

---

## The Current State of the Application

The application consists of two parts:

- **Frontend (client-side)**: Runs on `http://localhost:5173` (in development)
- **Backend (server-side)**: Runs on `http://localhost:3000` (in development)

In development, these two parts are hosted separately, but for deployment, we aim to serve both on a single domain.

---

## Goal

1. Deploy both the frontend and backend on a single domain (e.g., `http://localhost:3000`), so that visiting the backend URL will also serve the frontend.
2. Deploy the application online and make it accessible via a public URL (e.g., `https://website.service.com`).

---

## Configuration Changes

### Step 1: Configure the Frontend

1. Navigate to the **frontend** directory:

   ```bash
   cd ./frontend
   ```

2. Build the production version of the frontend:

   ```bash
   npm run build
   ```

   This generates a `dist` folder containing production-ready files.

   _(Note: You can delete the `dist` folder for now; we will rebuild it later.)_

---

### Step 2: Configure the Backend

1. Navigate to the **backend** directory:

   ```bash
   cd ../backend
   ```

2. Add the following environment variables to your `.env` file (keep sensitive data out of your code):

   ```env
   MONGO_URI=mongodb+srv://your_url
   PORT=your_port
   ```

   _(Add any other environment variables as needed, such as API keys or third-party service configurations.)_

3. Update your backend entry file (e.g., `index.js`, `app.js`, or `server.js`) to serve the frontend files in production:

   - Import the `path` module:

     ```js
     import path from "path";
     ```

   - Initialize `__dirname`:

     ```js
     const __dirname = path.resolve();
     ```

   - Modify your entry file to serve static files from the `frontend/dist` directory in production:

     ```js
     if (process.env.NODE_ENV === "production") {
       app.use(express.static(path.join(__dirname, "/frontend/dist")));
       app.get("*", (req, res) => {
         res.sendFile(
           path.resolve(__dirname, "frontend", "dist", "index.html")
         );
       });
     }
     ```

   This ensures that all requests to the backend will return the React app's `index.html`, while the backend's API routes remain functional.

---

### Step 3: Update the Root `package.json` File

Navigate to the **root** directory where both frontend and backend are located:

```bash
cd ..
```

In the root `package.json`, update the scripts to manage both frontend and backend:

```json
"scripts": {
    "dev": "nodemon backend/server.js",
    "build": "npm install && npm install --prefix frontend && npm run build --prefix frontend",
    "start": "node backend/server.js"
}
```

These scripts will:

- Run the backend in development mode (`npm run dev`).
- Install dependencies and build the frontend (`npm run build`).
- Start the backend in production mode (`npm run start`).

---

### Step 4: Update the Environment Variables

Modify the `start` and `dev` scripts to set the correct environment mode (`development` or `production`):

```json
"scripts": {
    "dev": "NODE_ENV=development nodemon backend/server.js",
    "build": "npm install && npm install --prefix frontend && npm run build --prefix frontend",
    "start": "NODE_ENV=production node backend/server.js"
}
```

To test the application locally, run the following in the root directory:

```bash
npm run dev
```

This will start the application at, eg: `http://localhost:3000`, where both the backend and frontend will be accessible.

---

### Step 5: Deploy the Application

#### Step 5.1: Upload the Application to GitHub

1. Create a `.gitignore` file in the root directory with the following content to exclude unnecessary files:

   ```gitignore
   # Logs
   logs
   *.log
   npm-debug.log*
   yarn-debug.log*
   yarn-error.log*
   pnpm-debug.log*
   lerna-debug.log*

   # Node Modules
   node_modules
   dist
   dist-ssr
   *.local

   # Editor directories and files
   .vscode/
   !.vscode/extensions.json
   .idea
   .DS_Store
   *.suo
   *.ntvs*
   *.njsproj
   *.sln
   *.sw?
   .env
   ```

2. Initialize a Git repository and push the code to GitHub:

   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M master
   git remote add origin <your-github-repo-url>
   git push -u origin master
   ```

---

#### Step 5.2: Deploy the Application on Render

1. Sign up or log in to [Render](https://render.com) using your GitHub account.

2. Click on **"New Web Service"** and select the repository you just pushed to GitHub.

3. Configure the deployment settings:

   - Set the **Build Command** to: `npm run build`
   - Set the **Start Command** to: `npm run start`

4. Choose the **Free Plan** for deployment (Render offers 750 free hours per month).

   _(Note: Free services on Render may spin down after 15 minutes of inactivity, and it may take a few minutes to restart.)_

5. Add your environment variables to Render:

   - **MONGO_URI**: Your MongoDB URI
   - **PORT**: The port the backend will listen on

6. Click on **"Deploy Web Service"** to start the deployment process.

---

### Step 6: Access the Application

After deployment completes, your application will be live and accessible through the URL provided by Render (eg. `https://website.onrender.com`). Both the frontend and backend will be served under the same domain, offering a smooth user experience.

---

## Conclusion

Congratulations! You’ve successfully deployed a MERN stack application with the frontend and backend running on a single domain. This setup ensures better integration between the client and server, providing a seamless deployment process and a unified user experience.
