## Machine Learning Operations (MLOps) Study Notes

MLOps applies DevOps practices to machine-learning systems so that data, code, environments, models, deployments, and monitoring are versioned and operated as one lifecycle. Use Azure Machine Learning CLI v2 or Python SDK v2 for new automation. CLI v1 is unsupported and SDK v1 support ended on June 30, 2026. See [Azure Machine Learning CLI and SDK v2](https://learn.microsoft.com/en-us/azure/machine-learning/concept-v2).

## Contents: Machine Learning Operations (MLOps)

1. [Get started with AI on Azure](https://learn.microsoft.com/en-us/training/modules/get-started-ai-fundamentals)
1. [Introduction to GitHub Copilot](https://learn.microsoft.com/en-us/training/modules/introduction-to-github-copilot)
1. [Introduction to DevOps principles for machine learning](https://learn.microsoft.com/en-us/training/modules/introduction-development-operations-principles-for-machine-learn)
1. [Explore Azure Machine Learning workspace resources and assets](https://learn.microsoft.com/en-us/training/modules/explore-azure-machine-learning-workspace-resources-assets)
1. [Explore developer tools for workspace interaction](https://learn.microsoft.com/en-us/training/modules/explore-developer-tools-for-workspace-interaction)
1. [Design a data ingestion strategy for machine learning projects](https://learn.microsoft.com/en-us/training/modules/design-data-ingestion-strategy-for-machine-learning-projects)
1. [Design a machine learning model training solution](https://learn.microsoft.com/en-us/training/modules/design-machine-learning-model-training-solution)
1. [Design a model deployment solution](https://learn.microsoft.com/en-us/training/modules/design-model-deployment-solution)
1. [Design a machine learning operations solution](https://learn.microsoft.com/en-us/training/modules/design-machine-learning-operations-solution)
1. [Use an Azure Machine Learning job for automation](https://learn.microsoft.com/en-us/training/modules/use-azure-machine-learn-job-for-automation)
1. [Trigger Azure Machine Learning jobs with GitHub Actions](https://learn.microsoft.com/en-us/training/modules/trigger-azure-machine-learn-jobs-github-actions)
1. [Trigger GitHub Actions with feature-based development](https://learn.microsoft.com/en-us/training/modules/trigger-github-actions-trunk-based-development)
1. [Work with linting and unit testing in GitHub Actions](https://learn.microsoft.com/en-us/training/modules/work-linting-unit-test-github-actions)
1. [Work with environments in GitHub Actions](https://learn.microsoft.com/en-us/training/modules/work-environments-github-actions)
1. [Deploy a model with GitHub Actions](https://learn.microsoft.com/en-us/training/modules/deploy-model-github-actions)

## Workspace resources and assets

An MLOps pipeline manages workspace assets across environments. Key assets to version and track:

| Asset | Versioning practice | Why it matters |
| --- | --- | --- |
| **Data assets** | Version-pinned references, not raw paths | Reproducible training; auditable input lineage |
| **Environments** | Docker image + conda/pip lock file | Consistent runtime across runs; reproducible reruns |
| **Components** | YAML interface + versioned implementation script | Reusable pipeline steps; enables parallel development |
| **Models** | Registered with metadata, tags, and evaluation metrics | Promotion tracking; rollback capability |
| **Endpoints** | Deployment YAML versioned with code | Controlled promotion; blue/green and canary patterns |

Register data as versioned data assets rather than embedding paths in training scripts. Pin environments by exact version in production jobs to prevent silent runtime drift.

## Data ingestion design

A sound data ingestion design addresses:

- **Source registration**: register each distinct source as a named datastore. Avoid connection strings in training scripts.
- **Snapshot vs. live reference**: snapshot the training data at job submission time (most reproducible) or reference a live data asset (simpler, but training data can vary between runs).
- **Feature engineering**: transformations that must be consistent between training and inference should be encapsulated in a versioned component, not in ad-hoc notebook code.
- **Data validation**: check schema, null rates, value ranges, and expected distributions before training begins. Log validation results as job metrics so failures block pipeline progression.
- **Sensitive data**: apply sensitivity labels, data masking, or row-level access controls before data enters any training job.

## Training solution design

Design each training job to be reproducible, parameterized, and observable:

- **Parameterize** learning rate, epochs, data path, model type, output path, and split ratios. Use job YAML inputs or `ArgumentParser`; avoid hard-coded values.
- **Log metrics** with `mlflow.log_metric()` for every metric used in a promotion decision.
- **Log artifacts**: save the trained model, feature importance, confusion matrix, and evaluation report as run artifacts.
- **Component vs. script**: for steps that will be reused or shared, wrap them in versioned components with typed YAML interfaces. For one-off exploration, a command job on a compute instance is sufficient.
- **Distributed training**: for large models or datasets, use `PyTorch DistributedDataParallel`, `TensorFlow MirroredStrategy`, or Azure ML's `TorchDistributed` and `MpiDistributed` configurations on multi-node compute clusters.

## Deployment solution design

**Real-time inference (online endpoint)**:

- Each online endpoint hosts one or more named deployments. Each deployment runs a specific model version.
- Traffic splitting enables blue/green (100%/0% → 0%/100%) and canary (95%/5%) patterns.
- Use `az ml online-endpoint invoke --local` or the Python SDK to test scoring logic locally before cloud deployment.
- Monitor latency, request count, error rate, CPU, and memory in Azure Monitor. Set alert rules for elevated 5xx error rates and high latency.

**Batch inference (batch endpoint)**:

- Accepts files or data asset references; returns results asynchronously to a storage output path.
- Use for large-scale scoring where millisecond latency is not required.

## CI/CD with GitHub Actions

A three-workflow structure covers training, promotion, and deployment:

**CI (on pull request):**
1. Lint and unit-test training code
2. Validate data asset existence and schema
3. Submit a small smoke-test training job
4. Fail the PR if training metrics fall below a threshold

**CD (on merge to main):**
1. Run the full training pipeline on the approved dataset
2. Compare the newly registered model against the currently deployed model on the holdout test set
3. If the new model meets the promotion criteria, update the online endpoint deployment
4. Apply a staged traffic shift (e.g., 10% for 15 minutes, then 100%)
5. Notify the team and record the deployment

**Key GitHub Actions configuration:**

```yaml
name: Train Azure Machine Learning model

on:
  workflow_dispatch:

permissions:
  contents: read
  id-token: write

jobs:
  train:
    runs-on: ubuntu-latest
    steps:
      - name: Login to Azure
        uses: azure/login@532459ea530d8321f2fb9bb10d1e0bcf23869a43 # v3.0.0
        with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}

      - name: Submit training job
        run: |
          az ml job create \
            --file jobs/train.yaml \
            --workspace-name ${{ vars.AML_WORKSPACE }} \
            --resource-group ${{ vars.RESOURCE_GROUP }}
```

Use OpenID Connect (OIDC) federated credentials rather than long-lived service principal secrets for Azure authentication. The `id-token: write` permission is required to request the OIDC token; it does not grant Azure access on its own. Assign the minimum required RBAC roles: **AzureML Data Scientist** for job submission, plus **Reader** on the resource group.

## Operating notes

- Version all training code, component definitions, environment specs, data references, model registrations, and endpoint configuration. A reproducible release must identify every input needed to recreate it.
- Separate data preparation, training, evaluation, and deployment into distinct pipeline steps. Pipelines manage step dependencies and can reuse unchanged outputs to reduce cost and time.
- In CI, run code quality checks and unit tests. In CD, validate model metrics and deployment configuration before promoting a registered model.
- For managed online endpoints, deploy and debug locally, inspect Azure Monitor logs and metrics, and use staged traffic allocation for production changes. Apply Microsoft Entra authentication and least-privilege RBAC.
- For generative AI, log the prompt and tool configuration, model version, retrieval sources, safety outcomes, latency percentiles, token consumption, and user feedback. Use tracing and evaluations to link regressions to a model, prompt, tool, or data change, and define a rollback threshold before release.
