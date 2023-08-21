# Quarkus Readme

Welcome to the Quarkus project! This repository contains the source code and configuration for the Quarkus application, along with a GitHub Actions workflow for Continuous Integration (CI) and Continuous Deployment (CD).

## CI/CD Workflow

### CI (Continuous Integration)

The CI workflow is triggered on pushes to the `dev` and `main` branches. It performs the following steps:

1. **Checkout**: This step checks out the repository code.
2. **Get Tags**: Using a custom script, the latest tag is determined and stored as an output variable.
3. **Setup Java Environment**: Sets up the Java environment with OpenJDK 17 using the Temurin distribution.
4. **Build and UnitTest**: Updates the project version to the latest tag, then builds and runs unit tests using Maven.
5. **Package**: Packages the application into a legacy JAR and creates a Docker image tagged with the version.
6. **Test**: Creates a Docker network, runs the Dockerized application, and tests its functionality.
7. **Publish**: Logs in to Amazon ECR, tags the Docker image, and pushes it to the ECR repository for later deployment.

### CD (Continuous Deployment)

The CD workflow is triggered when there's a successful CI build on the `main` or `dev` branch. It performs the following steps:

1. **Checkout**: Similar to the CI workflow, this step checks out the code.
2. **Deploy**: Deploys the Docker image from the ECR repository to the target host using SSH. The image is run as a Docker container.
3. **E2E**: Performs an end-to-end test by making an HTTP request to the deployed application.
4. **Clean repo**: Cleans the repository by removing any untracked files.
5. **Create and push tag**: Creates a Git tag for the deployed version and pushes the tag.

### Report

After both the CI and CD workflows have completed, the Report workflow sends a notification to a Slack channel about the build status and includes links to relevant pull requests or commits.

## Configuration

The CI/CD workflow is defined in the `.github/workflows` directory. The workflows utilize GitHub Actions and consist of multiple steps executed on different runners.

### Secrets

Make sure to configure the following secrets in your GitHub repository settings:

- `AWS_ACCESS_KEY_ID`: AWS access key ID for Docker image publishing.
- `AWS_SECRET_ACCESS_KEY`: AWS secret access key for Docker image publishing.
- `AWS_DEFAULT_REGION`: AWS region for Docker image publishing.
- `SLACK_WEBHOOK_URL`: Slack webhook URL for notifications.
- `QUARKUS_USER`: SSH username for CD deployment.
- `SSH_PRIVATE_KEY`: Private SSH key for CD deployment.

### Environment Variables

- `QUARKUS_HOST`: The host where the Quarkus application will be deployed.
- `GITHUB_TOKEN`: GitHub personal access token for creating tags.
