## Foundry Tools Study Notes

Microsoft Learn now presents Cognitive Services as **Foundry Tools**. This note preserves the 2023 study path while identifying current terminology and services that should not be selected for new development. See [Foundry Tools overview](https://learn.microsoft.com/en-us/azure/ai-services/what-are-ai-services).

## Current service guidance

- Use **Azure AI Document Intelligence** for document and form extraction, not Form Recognizer.
- Use **Azure AI Search** for full-text, vector, hybrid, and multimodal search, not Azure Cognitive Search.
- Use Microsoft Entra authentication and managed identities where the selected tool supports them; use private networking, least privilege, and key rotation as required.
- QnA Maker, LUIS, Metrics Advisor, Personalizer, Content Moderator, and Anomaly Detector are retired or retiring. Do not use them for new solutions. Anomaly Detector retires on **October 1, 2026** and custom question answering retires on March 31, 2029. Use [Content Safety](https://learn.microsoft.com/en-us/azure/ai-services/content-safety/overview) instead of Content Moderator, and direct new knowledge-grounded applications to Microsoft Foundry. Validate a replacement through current Microsoft Learn guidance before migration.
- The `azure-ai-inference` package retired on May 30, 2026. For current Foundry model inference, use the `openai` package against a v1 endpoint and Microsoft Entra authentication where supported. See [migration guidance](https://learn.microsoft.com/en-us/azure/foundry/how-to/navigate-from-classic).
- Model, API, container, and regional availability changes frequently. Confirm the product page and version-support policy before implementation.

## Contents: Foundry Tools (formerly Cognitive Services)

1. [Get started with AI on Azure](https://learn.microsoft.com/en-us/training/modules/get-started-ai-fundamentals)
1. [Introduction to GitHub Copilot](https://learn.microsoft.com/en-us/training/modules/introduction-to-github-copilot)
1. [Create and consume Foundry Tools](https://learn.microsoft.com/en-us/training/modules/create-manage-cognitive-services)
1. [Secure Foundry Tools](https://learn.microsoft.com/en-us/training/modules/secure-cognitive-services)
1. [Monitor Foundry Tools](https://learn.microsoft.com/en-us/training/modules/monitor-cognitive-services)
1. [Deploy supported Foundry Tools in containers](https://learn.microsoft.com/en-us/training/modules/investigate-container-for-use-cognitive-services)
1. [Introduction to Azure OpenAI Service](https://learn.microsoft.com/en-us/training/modules/explore-azure-openai)
1. [Historical: Build a Language Understanding model](https://learn.microsoft.com/en-us/training/modules/build-language-understanding-model)
1. [Historical: Publish and use a Language Understanding app](https://learn.microsoft.com/en-us/training/modules/publish-use-language-understand-app)
1. [Create speech-enabled apps with the Speech service](https://learn.microsoft.com/en-us/training/modules/transcribe-speech-input-text)
1. [Translate speech with the speech service](https://learn.microsoft.com/en-us/training/modules/translate-speech-speech-service)
1. [Extract insights from text with the Language service](https://learn.microsoft.com/en-us/training/modules/extract-insights-text-with-text-analytics-service)
1. [Translate text with the Translator service](https://learn.microsoft.com/en-us/training/modules/translate-text-with-translator-service)
1. [Explore Vision capabilities](https://learn.microsoft.com/en-us/training/modules/explore-cognitive-services-vision)
1. [Analyze images](https://learn.microsoft.com/en-us/training/modules/analyze-images)
1. [Analyze video](https://learn.microsoft.com/en-us/training/modules/analyze-video)
1. [Classify images](https://learn.microsoft.com/en-us/training/modules/classify-images)
1. [Detect objects in images](https://learn.microsoft.com/en-us/training/modules/detect-objects-images)
1. [Detect, analyze, and recognize faces](https://learn.microsoft.com/en-us/training/modules/detect-analyze-recognize-faces)

## Provisioning and resource types

You can access Foundry Tools (Azure AI services) through two resource types:

- **Single-service resource**: a dedicated resource for one service (Speech, Translator, Language, Vision, etc.). Use when you need isolated billing, a service-specific SKU, or a service unavailable in a multi-service resource.
- **Multi-service resource** (Azure AI services): a single resource with one key and endpoint covering Speech, Translator, Language, Vision, Content Safety, and other services. Simplifies management and billing; use when one application needs several services.

After provisioning, retrieve the key and endpoint from the Azure portal or via the Azure CLI. Store the key in Azure Key Vault; do not embed it in source code.

## Authentication

**Key-based**: pass the subscription key as the `Ocp-Apim-Subscription-Key` request header or the `api-key` query parameter (service-dependent). Keys should rotate on a defined schedule and be retrieved from Key Vault at runtime.

**Microsoft Entra token-based**: supported by many Foundry Tools. Configure a managed identity for the hosting resource (App Service, Azure Functions, AKS, Container Apps, and so on), assign the minimum data-plane role such as `Cognitive Services User`, and obtain a bearer token at runtime. Microsoft Entra authentication requires a custom subdomain for the resource; where it is supported, prefer it over key-based access and disable local authentication after migration.

## Monitoring with Azure Monitor

Enable **Diagnostic settings** on the AI resource to route logs and metrics to a Log Analytics workspace:

- **Request logs**: every API call, HTTP status code, latency, and consumed transactions or tokens.
- **Metric alerts**: alerts on request count, error rate (4xx/5xx), and throttling. Use quota monitoring to detect approaching service limits.
- **Resource health**: check service health in the Azure portal to distinguish application errors from service-side incidents.

## Container deployment

Selected Foundry Tools can be run in a Docker container on-premises or in an edge environment. The containerized version runs the model locally but still requires a metered billing connection to Azure:

- Pull the official container image from Microsoft Container Registry (MCR).
- Provide `ApiKey` and `Billing` endpoint as container environment variables.
- Images must be updated when the service version advances. Not all services or model versions support containers.
- Check the current [supported-containers list](https://learn.microsoft.com/en-us/azure/ai-services/cognitive-services-container-support) before planning an air-gapped deployment.

## Key Foundry Tool capabilities

| Tool | Common use cases | Current naming note |
| --- | --- | --- |
| **Language** | NER, key-phrase extraction, sentiment analysis, summarization, language detection, PII detection | Replaces the older Text Analytics and LUIS APIs; LUIS is retired |
| **Speech** | Speech-to-text, text-to-speech, speaker recognition, custom speech models | Custom models improve accuracy for domain-specific vocabulary and noise |
| **Translator** | Text translation, language detection, transliteration, custom glossaries | Supports bulk translation and document translation endpoints |
| **Vision (Image Analysis v4)** | Image captioning, object detection, dense captioning, and smart crops | API versions differ in feature set; check the migration guide. The Image Analysis background-removal service retired March 31, 2025. |
| **Azure AI Document Intelligence** | Form field extraction, tables, key-value pairs, prebuilt invoices, receipts, IDs, contracts | Current: v4.0 API; v2.1 and v3.0 end-of-life dates apply — migrate proactively |
| **Azure AI Search** | Full-text, vector, hybrid, and multimodal retrieval; semantic reranking | Replaces Azure Cognitive Search name; supports RAG patterns with Azure OpenAI |
| **Content Safety** | Hate, violence, self-harm, and sexual content detection; prompt shields | Required for any application processing user-generated or LLM-generated content |
| **Microsoft Foundry models / Azure OpenAI** | Responses, chat, embeddings, image generation, and speech | Prefer v1 Responses API for new supported workloads; model and regional availability varies. |

## Implementation checklist

1. Define the user outcome, data classification, accuracy measure, latency target, and error-handling path before selecting a tool.
1. Test with representative data including failure cases and require human review for consequential decisions.
1. Use managed identities, Azure Key Vault, network controls, and Azure Monitor according to the selected tool's support matrix.
1. Record the API version, model or feature version, region, quota, and lifecycle status in deployment documentation.
1. Confirm that retired or retiring services (LUIS, QnA Maker, Anomaly Detector, etc.) are not selected for new development. Validate replacements through current Microsoft Learn guidance before migration.
