## AI Builder Study Notes

AI Builder is a Microsoft Power Platform capability for using prebuilt and custom AI models in Power Apps and Power Automate. Use a prebuilt model when it matches the business scenario; train and evaluate a custom model when business-specific data is required. See [AI Builder overview](https://learn.microsoft.com/en-us/ai-builder/overview).

## Contents: AI Builder

1. [Get started with AI on Azure](https://learn.microsoft.com/en-us/training/modules/get-started-ai-fundamentals)
1. [Process custom documents with AI Builder](https://learn.microsoft.com/en-us/training/modules/get-started-with-form-processing)
1. [Recognize text with AI Builder](https://learn.microsoft.com/en-us/training/modules/get-started-with-ai-builder-text-recognition)
1. [Analyze the sentiment of text with AI Builder](https://learn.microsoft.com/en-us/training/modules/get-started-with-ai-builder-sentiment-analysis)
1. [Extract information from business cards with AI Builder](https://learn.microsoft.com/en-us/training/modules/get-started-with-ai-business-card-reader)
1. [Detect objects with AI Builder](https://learn.microsoft.com/en-us/training/modules/get-started-with-ai-builder-object-detection)
1. [Identify the language of text with AI Builder](https://learn.microsoft.com/en-us/training/modules/get-started-with-ai-builder-language-detection)
1. [Identify key phrases with AI Builder](https://learn.microsoft.com/en-us/training/modules/get-started-with-ai-builder-key-phrase-extraction)
1. [Use AI Builder in Power Automate](https://learn.microsoft.com/en-us/training/modules/ai-builder-power-automate)
1. [Process receipts with AI Builder](https://learn.microsoft.com/en-us/training/modules/ai-builder-receipt-processing)
1. [Get started with AI Builder](https://learn.microsoft.com/en-us/training/modules/get-started-with-ai-builder)
1. [Manage models in AI Builder](https://learn.microsoft.com/en-us/training/modules/manage-models)
1. [Create text with GPT in AI Builder](https://learn.microsoft.com/en-us/training/modules/ai-builder-text-generation)
1. [Get started with Microsoft Copilot Studio](https://learn.microsoft.com/en-us/training/modules/power-virtual-agents-bots)
1. [Manage topics in Microsoft Copilot Studio](https://learn.microsoft.com/en-us/training/modules/manage-power-virtual-agents-topics)
1. [Work with entities and variables in Microsoft Copilot Studio](https://learn.microsoft.com/en-us/training/modules/power-virtual-agents-entities)
1. [Enhance Microsoft Copilot Studio agents](https://learn.microsoft.com/en-us/training/modules/enhance-power-virtual-agents-bots)
1. [Build effective agents with Microsoft Copilot Studio](https://learn.microsoft.com/en-us/training/modules/build-effective-bots)

## AI Builder model types

AI Builder supports two categories of models:

**Prebuilt models** are ready to use without training and work against standard content types:

- **Document processing** (powered by Azure AI Document Intelligence): extracts key-value pairs, tables, and text from forms and invoices. Supports custom layouts for specific form types.
- **Text recognition (OCR)**: extracts printed and handwritten text from images and PDF files.
- **Sentiment analysis**: classifies text as positive, neutral, or negative and returns a confidence score.
- **Language detection**: identifies the dominant language of a text input and returns an ISO language code.
- **Key phrase extraction**: identifies the key talking points from text.
- **Business card reader**: extracts contact details (name, email, phone, organization, address) from a business card image.
- **Receipt processing**: extracts merchant, total, tax, line items, and transaction date from receipt images.
- **Invoice processing**: extracts header and line-item fields from invoices.
- **Object detection** (prebuilt): detects common objects in images.
- **Text generation (GPT)**: generates or transforms text using Azure OpenAI models configured in the Power Platform environment.

**Custom models** require labeled training data provided in the environment:

- **Custom document processing**: train on labeled forms to extract specific fields from a business-specific form layout. Provide at least five examples per field for a viable initial model.
- **Custom object detection**: train on labeled images to detect specific objects (product damage, equipment presence). Requires bounding-box annotations and sufficient image variety.
- **Custom text classification**: train on labeled text samples to assign business-specific categories to input text.

## Model lifecycle in AI Builder

1. **Create**: select the model type and give it a name inside the Power Apps or Power Automate portal.
2. **Configure**: specify fields to extract (document processing) or upload labeled images or text (custom models).
3. **Train**: AI Builder trains the model on the provided data. Training time varies by model type and data volume.
4. **Evaluate**: review the accuracy score and quick-test results. A model should be tested with data withheld from training before automating a consequential decision.
5. **Publish**: publishing a model makes it available in Power Apps controls and Power Automate actions. Models can be re-trained; a new published version replaces the previous one in apps using the latest version.
6. **Use**: add the model to a Power Apps canvas app (AI Builder control) or to a Power Automate cloud flow (AI Builder action step).

Model training and runtime prediction consume AI Builder credits. Credit consumption depends on model type and request volume. Confirm the credit balance in the Power Platform admin center before enabling high-volume automation.

## Using AI Builder in Power Automate

Add an AI Builder action to a cloud flow to process documents, analyze text, or generate content:

1. Trigger the flow on a new file (SharePoint, OneDrive, email attachment, or HTTP).
2. Add an AI Builder action for the relevant model type and map the file content or text to the action's input.
3. Use the output fields (extracted values, classification label, confidence score, generated text) in subsequent flow steps.
4. Branch on confidence: route low-confidence results to a human review step (approval) before automated processing continues.

A common pattern: trigger on new email with attachment → Document Processing → write extracted fields to Dataverse → if confidence < 0.8, create an approval task → on approval, update a SharePoint record.

## Microsoft Copilot Studio agents

Microsoft Copilot Studio (formerly Power Virtual Agents) is the current platform for building conversational agents in the Power Platform.

Use Copilot Studio when a maker-focused, low-code agent needs to work with Power Platform data, connectors, topics, and workflows. For agents that need custom orchestration code, a broad tool catalog, managed hosting, tracing, or formal evaluations, evaluate [Microsoft Foundry Agent Service](https://learn.microsoft.com/en-us/azure/foundry/agents/overview). The two platforms can coexist when each has a clear responsibility and authorization boundary.

- **Topics**: a topic defines a conversation path triggered by specific phrases or system events. Each topic contains trigger phrases, message nodes, question nodes, conditions, actions (Power Automate flow calls), and variable assignments.
- **Entities**: system entities (date, number, email, geography) and custom entities capture specific data types from user input. Variables store entity values and can be passed to and from Power Automate flows.
- **Knowledge sources**: connect agents to SharePoint sites, uploaded files, or external URLs for question answering from curated content.
- **Actions**: agents can call Power Automate cloud flows to interact with data, run AI Builder models, query APIs, or integrate with external services. Actions receive variables from the conversation and return results back to the topic.
- **Channels**: publish agents to Microsoft Teams, a website embed, or other supported endpoints.
- **Test and monitor**: use the test panel to step through conversation paths before publishing. Review conversation analytics to identify low-performing topics and unanswered questions.

Agent quality depends on the breadth of trigger phrases, the coverage of topic branches, the clarity of bot messages, and the handling when the agent cannot recognize the intent (the escalation or fallback path).

## Implementation notes

- Model training quality depends on representative, labeled data and clear evaluation criteria. Test with withheld data before automating a business decision.
- AI Builder credits are consumed for training and predictions. For high-volume or latency-sensitive automation, compare the supported AI Builder model with a direct Foundry Tool or Foundry model API, including licensing, operational ownership, and data-governance requirements.
- Confirm release status and regional availability. Preview features are not intended for production, and administrators control feature availability per environment.
- Keep solutions inside appropriate Power Platform environments. Apply least-privilege permissions and data policies to control which connectors can be combined.
- Model, API, and feature availability changes. Verify the current state in [AI Builder documentation](https://learn.microsoft.com/en-us/ai-builder/overview) before designing a production solution.
