# Lambda CI/CD with GitHub Actions

A project that demonstrates a CI/CD pipeline using GitHub Actions to automatically validate, test, and deploy AWS infrastructure and a Lambda function.

## Purpose / Learning

This project is a hands-on reference for building a real CI/CD pipeline with GitHub Actions and AWS. Key concepts covered:

- How to trigger workflows on specific events (`push` to `main`, `pull_request`) and specific file paths (`lambda/**`, `cloudformation/**`)
- How to securely authenticate to AWS from GitHub Actions using repository secrets (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`)
- How to validate a CloudFormation template before merging using `aws cloudformation validate-template`
- How to automatically deploy a temporary test CloudFormation stack on a PR and clean it up when the PR is merged
- How to post automated comments on a PR using `actions/github-script`
- How to package and deploy a Python Lambda function by zipping dependencies and using `aws lambda update-function-code`
- How to use CloudFormation `Parameters` to make templates reusable across environments (test, staging, production)

## Project Structure

```
lambda-cicd/
├── .github/workflows/
│   ├── cfn-validate-pr.yml   # Validates CloudFormation and deploys a test stack on PRs
│   └── lambda-deploy.yml     # Deploys the Lambda function on push to main
├── cloudformation/
│   └── s3_bucket.yml         # CloudFormation template to create an S3 bucket
├── lambda/
│   ├── lamba_function.py     # Lambda function handler
│   └── requirements.txt      # Python dependencies (currently empty)
└── README.md
```

## Workflows

### `lambda-deploy.yml` — Deploy Lambda on Push
- Triggers on push to `main` when files in `lambda/` change
- Sets up Python 3.12, installs dependencies from `requirements.txt`
- Zips the `lambda/` folder and deploys it to the Lambda function `my-test-cicd-lambda`

### `cfn-validate-pr.yml` — Validate CloudFormation on PR
- Triggers on pull requests that modify files in `cloudformation/`
- Validates the CloudFormation template syntax
- Deploys a temporary test stack named `pr-test-stack-<PR number>`
- Posts a comment on the PR confirming the test stack was deployed
- Cleans up (deletes) the test stack automatically when the PR is merged

## ⚠️ Hardcoded Values — Must Change Before Using

| File | Value | What to Change |
|------|-------|----------------|
| `cfn-validate-pr.yml` | `aws-region: eu-west-2` | Change to your target AWS region |
| `lambda-deploy.yml` | `aws-region: eu-west-2` | Change to your target AWS region |
| `lambda-deploy.yml` | `--function-name my-test-cicd-lambda` | Replace with your actual Lambda function name |

## Required GitHub Secrets

Before running the workflows, add the following secrets to your GitHub repository under **Settings > Secrets and variables > Actions**:

| Secret | Description |
|--------|-------------|
| `AWS_ACCESS_KEY_ID` | AWS IAM user access key |
| `AWS_SECRET_ACCESS_KEY` | AWS IAM user secret access key |
