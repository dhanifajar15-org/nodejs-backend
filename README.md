## Technical Test -  CI/CD and Kubernetes

I will outline the Continuous Integration and Continuous Deployment (CI/CD) process for a Node.js backend application using GitHub Actions. This workflow employs GitHub-hosted runners to automate the build, test, dockerization, and deployment process for different branches of the repository. We'll also discuss the branching strategy and unique features of this setup.

This documentation outlines the CI/CD process implemented for a Node.js backend application. GitHub Actions, with its Standard GitHub-hosted runners, is used to automate the entire process. The CI/CD workflow includes linting, testing, dockerization, and deployment to Google Kubernetes Engine (GKE).

### 1. Workflow Overview

The CI/CD process involves the following steps for each branch:

1. **Linting Job**

   - Utilizes ESLint for code linting.
   - Ensures consistent code style and quality.

2. **Testing Job**

   - Executes `yarn test` to run tests and generate code coverage.
   - Utilizes `.env` secrets for configuration.

3. **Dockerize Job**

   - Builds a Docker image for the Node.js backend.

   - Tags the image using the format:

     ```
     dhanifajar15/nodejs-backend:${{ inputs.ENVIRONMENT }}-sha-${{ steps.tags.outputs.sha_short }}
     ```

   - Pushes the image to Docker Hub.

4. **Deployment Job**

   **All Deployment Job except Production **

   - Deploys the Dockerized application to GKE.
   - Credentials stored in ConfigMap and passed as secret variables.
   - Utilizes GitHub Marketplace actions for applying Kubernetes manifests.

   **Production Deployment Job**

   - Triggered by tagging a version (e.g., v1.0) on the `master` branch.
   - Executes linting, testing, Dockerization, and deployment to production.

### 2. Reusable Workflow

The CI/CD workflow is designed to be reusable, reducing duplication. It consists of multiple jobs, which can take inputs and use secrets.

![image-20230821213435490](/assets/image-20230821213435490.png)

### 3. Branching Strategy

The branching strategy consists of the following branches:

- `develop`: Main development branch.
- `staging`: Pre-production branch for testing.
- `master`: Production branch.
- `hotfix`: Temporary branch for critical fixes.
- `feature`: Temporary branch for feature development.

For each branch, a dedicated CI/CD setup is created. This setup includes linting, testing, dockerization, and deployment jobs.

![image-20230821213131407](/assets/image-20230821213131407.png)

### 4. Wildcard DNS

A unique feature is the use of wildcard domains for different branches. This is achieved using `nip.io`. For example, a feature branch named `feature` with commit SHA `34.142.207.149` will have a domain like `feature-${{github.sha}}.34.142.207.149.nip.io`.

![image-20230821212936760](/assets/image-20230821212936760.png)

This wildcard domain setup enables easy access to deployed branches for testing purposes.

### 6. Secret Management

Sensitive information such as Docker Hub credentials, GKE credentials, and other secrets are stored securely as GitHub secrets and accessed within the workflow as environment variables.

