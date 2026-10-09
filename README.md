# Task 1: Automate Code Deployment Using CI/CD Pipeline (GitHub Actions)

This repository contains a complete Continuous Integration and Continuous Deployment (CI/CD) pipeline built with **GitHub Actions**, **Node.js**, and **Docker**. The pipeline automatically tests the application code, builds a Docker container image, and pushes it to **Docker Hub** upon every push to the `main` branch.

---

## 🚀 Project Overview

The objective of this project is to automate the software delivery lifecycle using GitHub Actions workflows. By leveraging Pipeline-as-Code (`.github/workflows/main.yml`), every code change pushed to the main branch is automatically validated and containerized without manual intervention.

### Pipeline Workflow Steps
1. **Test:** Installs Node.js dependencies and executes automated unit tests.
2. **Build:** Compiles and builds the Docker container image.
3. **Push:** Authenticates with Docker Hub and publishes the versioned Docker image.

---

## 🛠 Tech Stack & Tools

- **Version Control & CI/CD:** [GitHub](https://github.com/) / [GitHub Actions](https://github.com/features/actions)
- **Runtime & Language:** [Node.js](https://nodejs.org/) (v18)
- **Containerization:** [Docker](https://www.docker.com/)
- **Container Registry:** [Docker Hub](https://hub.docker.com/)

---

## 📁 Repository Structure

```text
.
├── .github/
│   └── workflows/
│       └── main.yml        # GitHub Actions CI/CD workflow definition
├── src/
│   └── index.js            # Node.js application entry point
├── test/
│   └── app.test.js         # Automated unit tests
├── Dockerfile              # Instructions to build the application container
├── package.json            # Node.js dependencies and scripts
└── README.md               # Project documentation



⚙️ Setup & Prerequisites
Before triggering the pipeline, set up your secrets in your GitHub repository:

1. Docker Hub Credentials
Log in to Docker Hub.

Navigate to Account Settings > Personal Access Tokens and create a new token.

In your GitHub Repository, go to Settings > Secrets and variables > Actions.

Add the following repository secrets:

DOCKERHUB_USERNAME: Your Docker Hub username.

DOCKERHUB_TOKEN: The Docker Hub Personal Access Token generated above.

📊 CI/CD Workflow (.github/workflows/main.yml)
The workflow is defined in .github/workflows/main.yml:

YAML
name: CI/CD Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  test:
    name: Run Unit Tests
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Source Code
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'

      - name: Install Dependencies
        run: npm ci

      - name: Run Tests
        run: npm test

  build-and-push:
    name: Build & Push Docker Image
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'

    steps:
      - name: Checkout Source Code
        uses: actions/checkout@v3

      - name: Log in to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Build and Push Docker Image
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: |
            ${{ secrets.DOCKERHUB_USERNAME }}/node-ci-cd-app:latest
            ${{ secrets.DOCKERHUB_USERNAME }}/node-ci-cd-app:${{ github.sha }}
💻 Local Running & Testing
1. Run Node.js Application Locally
Bash
# Install dependencies
npm install

# Run unit tests
npm test

# Start local server
npm start
2. Build and Run Docker Image Locally
Bash
# Build image
docker build -t node-ci-cd-app:local .

# Run container
docker run -p 3000:3000 node-ci-cd-app:local
Access the running application at http://localhost:3000.

🧪 Verifying the Pipeline
Make a change in the repository (e.g., update src/index.js or README.md).

Commit and push the changes to main:

Bash
git add .
git commit -m "feat: trigger CI/CD pipeline"
git push origin main
Open the Actions tab in your GitHub repository to track the workflow execution in real time.

Verify that the new image tag appears in your Docker Hub repository upon pipeline completion.
