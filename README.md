# Java Microservices Application deployment to AWS

This repo contains GitHub Actions workflow for building, testing and deploying the vprofile application to AWS EKS.

## Workflow

The workflow consists of 3 main jobs:

### Testing

This job runs on `ubuntu-latest` and executes the following:

- Checks out code from repository
- Runs maven test
- Runs maven checkstyle
- Sets up Java 11
- Sets up SonarQube scanner
- Runs SonarQube analysis
- Checks SonarQube quality gate

### BUILD_AND_PUBLISH

This job depends on successful completion of Testing job. It:

- Checks out code
- Builds Docker image 
- Pushes image to ECR repo: `teerraform-git-repo` in `us-east-1` 

### DeployToEKS

This job depends on successful completion of BUILD_AND_PUBLISH job. It:

- Checks out code
- Configures AWS credentials
- Get kubeconfig for cluster: `terraform-git-eks`
- Login to ECR
- Deploys Helm chart: `vprofilecharts` in namespace `default` with release name `vprofile-stack`

The Helm values specify the image to use from ECR and the tag as GitHub run number.

## Secrets Required

The following secrets need to be configured in the repo for the workflow:

- `AWS_ACCESS_KEY_ID` 
- `AWS_SECRET_ACCESS_KEY`
- `SONAR_URL`
- `SONAR_TOKEN` 
- `SONAR_ORGANIZATION`
- `SONAR_PROJECT_KEY`
- `REGISTRY` - ECR registry URL

## Conclusion

This results in a CI/CD pipeline that builds, tests and deploys the app on every commit to GitHub repository.
