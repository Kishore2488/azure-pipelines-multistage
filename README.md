# Azure Pipelines — Multi-Stage CI/CD

![Lint](https://github.com/Kishore2488/azure-pipelines-multistage/actions/workflows/lint.yml/badge.svg)

A reference `azure-pipelines.yml` showing how I structure multi-stage pipelines in Azure DevOps: Build → Test → Deploy to Dev → (manual approval) → Deploy to Prod, built from reusable stage templates instead of one long flat file.

## Why templates

A flat pipeline file gets unreadable past a couple of stages, and copy-pasted steps drift out of sync. Splitting Build/Test/Deploy into `templates/*.yml` means each stage is independently testable and reusable across other pipelines in the same project.

## Structure

```
azure-pipelines.yml        # orchestrates stages, wires variable groups per environment
templates/
  build.yml                 # restore, package, publish artifact
  test.yml                  # run unit tests
  deploy.yml                 # parameterized deploy stage (environment, approval)
```

## How the environments are separated

- `deploy.yml` is parameterized by `environmentName` and `azureSubscription`, so the same template deploys to dev, staging, and prod with different pipeline variables — not three copies of the same YAML.
- Production uses an Azure DevOps **Environment** with a manual approval check, so nothing reaches prod without a human sign-off, while dev deploys automatically on every successful build.

## CI on this repo

Azure Pipelines itself only runs inside an Azure DevOps project, so this repo uses a lightweight GitHub Actions `yamllint` check to catch syntax errors before this ever gets imported into a real pipeline.
