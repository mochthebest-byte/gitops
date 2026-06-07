# GitOps — Kubernetes Manifests for voting-app

This repository contains the desired state for the voting-app microservices, deployed via **ArgoCD**.

## How It Works

1. **CI pipeline** builds a new Docker image and pushes it to ECR
2. **CI pipeline** commits the new image tag to this repo (e.g., `apps/vote/values.yaml`)
3. **ArgoCD** detects the change and syncs the new version to the EKS cluster

## Repository Structure

```
apps/
├── vote/values.yaml        # Vote service (Python/Flask)
├── result/values.yaml      # Result service (Node.js)
└── worker/values.yaml      # Worker service (.NET)
```

## What Gets Updated

CI updates the `tag` field in each `values.yaml`:

```yaml
image:
  repository: 428156589409.dkr.ecr.us-east-1.amazonaws.com/my-app/vote
  tag: a1b2c3d4e5f6...    # ← CI updates this to the git SHA
```

## ArgoCD Apps

Apps are defined in `mochthebest-byte/my-devops-project/argocd-apps/`:
- `voting-app.yaml` → apps/vote
- `result.yaml` → apps/result
- `worker.yaml` → apps/worker

## Manual Trigger

To trigger a redeploy manually, push to this repo or use the workflow in `my-devops-project`:

```bash
gh workflow run deploy-voting-app.yml -f service=vote -f environment=dev
```
