## Azure Machine Learning Study Notes

Azure Machine Learning supports the full machine-learning lifecycle: data and asset management, training jobs, reusable components and pipelines, model registration, deployment, and monitoring. For current development, use Azure Machine Learning CLI v2 or the Python SDK v2 (`azure-ai-ml`). CLI v1 is unsupported and SDK v1 support ended on June 30, 2026; migrate remaining v1 automation before changing a production workload. See [Azure Machine Learning CLI and SDK v2](https://learn.microsoft.com/en-us/azure/machine-learning/concept-v2).

## Contents: Machine Learning

1. [Get started with AI on Azure](https://learn.microsoft.com/en-us/training/modules/get-started-ai-fundamentals)
1. [Introduction to GitHub Copilot](https://learn.microsoft.com/en-us/training/modules/introduction-to-github-copilot)
1. [Introduction to machine learning](https://learn.microsoft.com/en-us/training/modules/introduction-to-machine-learning)
1. [Use Automated Machine Learning in Azure Machine Learning](https://learn.microsoft.com/en-us/training/modules/use-automated-machine-learning)
1. [Create a regression model with Azure Machine Learning designer](https://learn.microsoft.com/en-us/training/modules/create-regression-model-azure-machine-learning-designer)
1. [Create a classification model with Azure Machine Learning designer](https://learn.microsoft.com/en-us/training/modules/create-classification-model-azure-machine-learning-designer)
1. [Create a clustering model with Azure Machine Learning designer](https://learn.microsoft.com/en-us/training/modules/create-clustering-model-azure-machine-learning-designer)
1. [Explore and analyze data with Python](https://learn.microsoft.com/en-us/training/modules/explore-analyze-data-with-python)
1. [Train and evaluate regression models](https://learn.microsoft.com/en-us/training/modules/train-evaluate-regression-models)
1. [Train and evaluate classification models](https://learn.microsoft.com/en-us/training/modules/train-evaluate-classification-models)
1. [Train and evaluate clustering models](https://learn.microsoft.com/en-us/training/modules/train-evaluate-cluster-models)
1. [Train and evaluate deep learning models](https://learn.microsoft.com/en-us/training/modules/train-evaluate-deep-learn-models)
1. [Refine and test machine learning models](https://learn.microsoft.com/en-us/training/modules/test-machine-learning-models)

## Azure Machine Learning workspace

The **workspace** is the top-level resource for all Azure Machine Learning work. It manages compute, data, environments, experiments, models, and deployments in a single governed boundary.

| Asset / Resource | Purpose |
| --- | --- |
| **Compute instance** | Personal managed VM for interactive notebooks and experiments |
| **Compute cluster** | On-demand, scalable cluster for training jobs; scales to zero when idle |
| **Data asset** | Versioned reference to a file, folder, or tabular dataset in a registered datastore |
| **Datastore** | Named registration of a storage account (Blob, ADLS Gen2, SQL, etc.) |
| **Environment** | Versioned Docker image + conda/pip dependencies for training and inference |
| **Component** | Versioned, reusable pipeline step with a defined interface (inputs, outputs, parameters) |
| **Pipeline** | Directed acyclic graph (DAG) of components |
| **Registered model** | Versioned trained-model artifact with metadata and tags |
| **Online endpoint** | Managed real-time inference endpoint (auto-scaling, monitoring, blue/green traffic) |
| **Batch endpoint** | Managed batch inference endpoint for large-scale asynchronous scoring |

## Automated machine learning (AutoML)

AutoML automates model algorithm selection, feature engineering, and hyperparameter tuning. Provide a labeled training dataset and a target column; AutoML iterates over algorithms and configurations to find the best-performing model for the task type.

**AutoML task types:**

- **Classification**: predicts a categorical label (binary or multi-class)
- **Regression**: predicts a continuous numeric value
- **Time-series forecasting**: predicts future values using historical time-series data
- **NLP**: text classification using pretrained transformer models
- **Computer vision**: image classification, object detection, image segmentation

AutoML produces a leaderboard of runs with metrics. Review feature importance, data guardrails, and cross-validation results before registering the best model. The best model can be registered from the studio UI or via the SDK/CLI.

## Azure Machine Learning designer

The designer provides a visual drag-and-drop interface for building training pipelines without code. Use module palette items to:

- Import data from a registered dataset
- Apply transformations (normalize, select features, split)
- Select and configure an algorithm (regression, classification, clustering)
- Score and evaluate the model
- Compare runs by evaluation metrics

Designer pipelines can be converted to inference pipelines and deployed to a web endpoint. For production workloads with version-controlled code, prefer SDK v2 components over the designer.

## Training jobs

A **command job** runs a script on a compute cluster or compute instance:

```python
from azure.ai.ml import Input, MLClient, command
from azure.identity import DefaultAzureCredential

ml_client = MLClient(
    DefaultAzureCredential(),
    subscription_id="<SUBSCRIPTION_ID>",
    resource_group_name="<RESOURCE_GROUP>",
    workspace_name="<WORKSPACE_NAME>",
)

job = command(
    code="./src",
    command="python train.py --data ${{inputs.training_data}} --epochs 10",
    inputs={"training_data": Input(type="uri_folder", path="azureml:my-dataset:3")},
    environment="azureml:my-env:7",
    compute="gpu-cluster"
)
returned_job = ml_client.jobs.create_or_update(job)
print(returned_job.studio_url)
```

Pin data and environment asset versions for reproducibility; use `@latest` only for deliberate exploratory work. Use `mlflow.log_metric()` from the training script to record metrics, then track runs in Azure Machine Learning studio or via the CLI.

## Model evaluation metrics

Choose and report the metric that matches the business cost of different error types.

**Regression:**
- **MAE** (Mean Absolute Error): average absolute difference; same units as the target
- **RMSE** (Root Mean Squared Error): penalizes large errors more heavily than MAE
- **R²** (Coefficient of Determination): proportion of variance explained; closer to 1 is better

**Classification:**
- **Accuracy**: correct predictions / total predictions; misleading with imbalanced classes
- **Precision**: of predicted positives, how many are actually positive
- **Recall (Sensitivity)**: of actual positives, how many were correctly predicted
- **F1 Score**: harmonic mean of precision and recall; balances both when classes are imbalanced
- **AUC-ROC**: area under the ROC curve; measures discrimination across all decision thresholds

**Clustering:**
- Average distance to cluster center: tightness within each cluster
- Average distance to other centers: separation between clusters

Always evaluate on data withheld from training (hold-out set or cross-validation). Report metrics together with dataset characteristics and the split strategy.

## Current implementation notes

- Start with a well-defined prediction target and success metric. Split data to prevent leakage, evaluate on a representative holdout set, and record the dataset, code, environment, model, and evaluation result for each run.
- Use **components** for reusable steps and **pipelines** to orchestrate data preparation, training, evaluation, and deployment. Pipelines standardize MLOps, enable collaboration, and can reuse unchanged step outputs.
- Register versioned models and environments before production deployment to ensure reproducibility and rollback capability.
- For real-time inference, managed online endpoints provide managed serving, scaling, security, and monitoring. Use Microsoft Entra token authentication where supported, test locally first, and use staged traffic shifts for production rollouts. Register the evaluation data and metrics used for a promotion decision, then monitor latency, errors, throughput, and prediction quality after deployment.
- For generative workloads, record prompt, model, retrieval, safety, and evaluation configuration with the deployment. Use Microsoft Foundry when the workload needs the Responses API, agent tooling, or Foundry-native evaluations; use Azure Machine Learning when managed endpoints, training jobs, registries, and broader MLOps controls are the primary need.
