## Microsoft Foundry Study Notes

[Microsoft Foundry](https://learn.microsoft.com/en-us/azure/foundry/what-is-foundry) provides a development platform for models, agents, tools, evaluations, tracing, and operational controls used in AI applications. An effective solution begins with a user task, a measurable quality bar, clear data boundaries, and a failure-handling design, not with a model choice alone.

## Contents: Microsoft Foundry

1. [Get started with AI applications and agents on Azure](https://learn.microsoft.com/en-us/training/paths/get-started-ai-apps-agents/)
1. [Develop AI agents on Azure](https://learn.microsoft.com/en-us/training/paths/develop-ai-agents-azure/)
1. [Develop a generative AI chat app with Microsoft Foundry](https://learn.microsoft.com/en-us/training/modules/foundry-sdk/)
1. [Develop your first agent with Microsoft Foundry](https://learn.microsoft.com/en-us/training/modules/develop-first-agent/)
1. [Evaluate generative AI applications](https://learn.microsoft.com/en-us/training/paths/evaluate-generative-ai-apps/)
1. [Evaluate generative AI performance in Microsoft Foundry portal](https://learn.microsoft.com/en-us/training/modules/evaluate-models-azure-ai-studio/)
1. [Analyze and debug your generative AI app with tracing](https://learn.microsoft.com/en-us/training/modules/tracing-generative-ai-app/)
1. [Automate AI evaluations with Microsoft Foundry and GitHub Actions](https://learn.microsoft.com/en-us/training/modules/automated-evaluation-genaiops/)
1. [Operationalize AI responsibly with Azure AI Foundry](https://learn.microsoft.com/en-us/training/paths/operationalize-ai-responsibly/)
1. [Configure and manage guardrails in Microsoft Foundry](https://learn.microsoft.com/en-us/training/modules/azure-ai-foundry-configure-manage-guardrails/)
1. [Implement security for AI](https://learn.microsoft.com/en-us/training/paths/implement-ai-security/)

## Choosing the right workload

Use conventional application logic when an outcome can be produced deterministically. Use a generative model when the task requires language, reasoning over supplied context, classification with flexible input, or content generation. Use an agent when the model must select tools, retrieve information, or coordinate a bounded multi-step workflow.

Before selecting a model, define representative tasks and measure quality, latency, throughput, cost, regional availability, safety behavior, and data-handling requirements. Keep an evaluation set separate from prompt examples. The evaluation set should include normal requests, ambiguous requests, adversarial inputs, and failure cases relevant to the intended users.

### Select the platform boundary

| Primary need | Starting point | Why |
| --- | --- | --- |
| Model inference, a managed agent, tools, retrieval, tracing, and generative-AI evaluations | Microsoft Foundry | Provides an integrated AI application platform and managed agent capabilities. |
| Speech, language, vision, document extraction, search, or content-safety APIs | [Foundry Tools](foundry-tools.md) | Use a focused managed capability when a generative agent is not required. |
| Training jobs, model registry, managed endpoints, and conventional ML operations | [Azure Machine Learning](azure-machine-learning.md) | Provides broader machine-learning training and MLOps lifecycle capabilities. |
| Low-code business automation and maker-focused conversational experiences | [AI Builder and Copilot Studio](ai-builder.md) | Fits governed Power Platform environments, connectors, and Power Automate actions. |
| Microsoft 365 Copilot or Teams extension surfaces | [Microsoft 365 Copilot development](../microsoft-365/copilot-dev.md) | Fits agents and apps grounded in Microsoft 365 data and collaboration experiences. |

These products can work together, but select one system of record for authorization, business state, prompts, evaluation results, and deployment ownership. Do not use an agent merely to reimplement deterministic validation, authorization, or transaction logic.

## Projects, models, and application boundaries

Start new work with Foundry projects in the current portal. A project groups the resources and assets needed for an AI application. Separate environments so development experimentation does not share credentials, data, or deployment controls with production. Prefer prompt agents for managed, configuration-first agents; use hosted agents when custom agent code and orchestration are required; or call the Responses API from an existing application when a managed agent runtime is not needed.

Treat system instructions, tool schemas, retrieval configuration, model version, safety settings, and evaluation criteria as versioned application configuration. A prompt change can materially change application behavior, so review and test it like a code change.

When an application uses retrieval, design the data pipeline as carefully as the prompt. Identify authoritative sources, chunking and metadata rules, access trimming, freshness expectations, citation behavior, and evaluation criteria for groundedness and relevance. Retrieval does not remove the need for output validation or user-facing uncertainty handling.

### Model and deployment selection

Select a model by measured fitness for the specific task rather than by a general ranking. Compare a small number of candidate models with the same prompts, tools, retrieval context, safety policy, and evaluation dataset. Record the selected model deployment, version, region, quota, throughput configuration, and fallback behavior as release configuration.

- Use a smaller or specialized model when it meets the quality target at lower latency or cost.
- Use structured outputs or validated schemas when downstream code needs reliable fields. Treat parsing failures as an expected error path.
- Set bounded input, output, tool-call, retry, and execution limits. An agent must fail predictably rather than loop indefinitely or consume an unbounded budget.
- Test model changes as compatibility changes. A newer version can affect safety behavior, tool calling, JSON conformance, latency, and output style even when prompts are unchanged.

## Retrieval and knowledge grounding

Retrieval-augmented generation (RAG) adds relevant, authorized source material to a model request. It improves grounding only when the corpus, retrieval, and instructions are designed and evaluated together.

1. Identify the authoritative sources and content owners. Remove obsolete, duplicate, or unapproved content before indexing.
1. Define document boundaries, chunk size, overlap, metadata, source URL, effective date, and sensitivity classification. Metadata should support filtering, access trimming, citations, and freshness decisions.
1. Apply authorization before a chunk reaches the model. Never assume that a citation or a user interface filter provides sufficient security.
1. Require the application to cite source material where users must verify an answer, and specify how the agent should respond when evidence is absent or conflicting.
1. Evaluate retrieval separately from generation: measure whether the right evidence was retrieved, whether it was authorized and current, and whether the final answer accurately used it.

Foundry IQ can provide a shared knowledge layer for agent scenarios. It does not replace data governance, source access control, content lifecycle ownership, or application-level output validation.

## Agents and tools

An agent should have a specific responsibility, an explicit tool set, and limits on what it can access or change. Define each tool with narrow inputs, predictable outputs, authorization checks, error handling, timeout behavior, and idempotency requirements. Never let a model bypass the application's authorization or data-access layer.

Build confirmation steps for consequential actions such as sending messages, changing data, approving transactions, or invoking external systems. Record the user intent, tool arguments, outcome, and correlation identifiers needed to investigate errors. Provide a safe fallback when a tool is unavailable or a response is uncertain.

### Tool and agent design checklist

| Design concern | Required decision |
| --- | --- |
| Responsibility | State the one user outcome the agent owns and explicitly exclude unrelated work. |
| Authorization | Enforce user and service authorization in the tool or backend, never in prompt instructions alone. |
| Input and output | Use narrow schemas, validate values, and return predictable structured errors. |
| Side effects | Require confirmation for irreversible or externally visible operations; make retries idempotent where possible. |
| Data handling | Limit tool results to the minimum data needed for the next step and do not expose secret-bearing diagnostics to the model. |
| Resilience | Set timeouts, retry only safe transient failures, and provide a human or deterministic fallback. |
| Observability | Record request, approved tool arguments, response status, latency, and correlation IDs while protecting sensitive content. |

MCP can make external tools discoverable to an agent. Connect only trusted, governed MCP servers, review every tool's capabilities and data egress, and scope credentials and network access independently of the model's instructions.

## Evaluation, tracing, and operations

Evaluate an application continuously as models, prompts, tools, and source data change. Combine automated evaluations with human review for scenarios where correctness, tone, safety, or business impact cannot be measured reliably by an automated score. Make evaluation datasets, evaluator versions, pass thresholds, and human-review criteria versioned release inputs rather than an ad hoc preproduction activity.

Tracing connects model calls, tool calls, retrieval, and application activity within a request. Use it to diagnose latency, failed tool calls, unexpected context, and quality regressions. Monitor production for availability, token use, latency, errors, safety signals, tool failures, and user feedback. Establish rollback criteria before deploying a material model or prompt change.

### Evaluation design

Build an evaluation set from representative production-like tasks, support cases, known failures, and approved synthetic variations. Label the expected outcome or a review rubric before comparing candidate versions. Keep development examples separate from test cases to avoid evaluating on data the application has effectively memorized.

| Evaluation area | Questions to answer |
| --- | --- |
| Task quality | Did the result solve the user's task and follow the required format? |
| Groundedness | Are statements supported by retrieved, authorized, current sources? |
| Tool use | Did the agent select an appropriate tool, send valid arguments, and interpret the result correctly? |
| Safety | Does the application resist prompt injection, avoid prohibited output, and decline unsafe actions? |
| Reliability | How often do timeouts, malformed outputs, retrieval misses, or tool failures occur? |
| Efficiency | What are the latency percentiles, token consumption, tool-call count, and cost per successful task? |

Set release thresholds for the measures that matter. A change that improves an aggregate score but regresses a high-risk scenario should not automatically promote. Use human review for nuanced, sensitive, or high-impact outcomes and capture reviewer agreement so that the rubric itself can improve.

### Trace and production-operating practices

- Propagate one correlation ID from the user request through application code, retrieval, agent calls, tools, and downstream services.
- Record configuration versions with each trace: prompt/instruction revision, model deployment, tools, retrieval corpus/index revision, guardrail policy, and application build.
- Sample and protect content-bearing logs according to data classification and retention policy. Operational observability must not become an uncontrolled data store.
- Alert on failed actions, elevated latency, unexpected safety events, retrieval errors, high costs, and sudden changes in user feedback or quality scores.
- Establish a rollback playbook that can disable a tool, revert a prompt/model configuration, restrict an affected data source, or route the workflow to a human.

## Responsible AI and security

Classify data before sending it to a model or tool. Apply least privilege, network controls, content-safety measures, logging safeguards, retention rules, and secret-management practices appropriate to the data sensitivity. Test for prompt injection, indirect prompt injection through retrieved content, data leakage, unsafe tool use, and harmful or inaccurate output.

Human oversight must be designed into high-impact workflows. Users need a way to understand when an AI-generated result is uncertain, contest an outcome, and reach an accountable human or process where appropriate.

### Threat-model AI-specific paths

Threat model the system end to end, including user input, retrieved content, model output, tools, identities, network paths, and operational logs. Test direct and indirect prompt injection, data exfiltration attempts, malicious tool arguments, over-broad identity permissions, cross-tenant access, unsafe output, and denial-of-service or cost-amplification behavior. Mitigations can include trusted-content boundaries, input and output validation, Content Safety guardrails, least-privilege managed identities, private networking, rate limits, human confirmation, and incident response procedures.

## Release and lifecycle management

Treat an AI application release as a versioned bundle rather than a single model change. The bundle should include application code, instructions, model deployment, tool definitions, retrieval/index configuration, evaluator and dataset versions, safety settings, infrastructure, and operating runbooks.

1. Develop and test in an isolated environment with nonproduction identities and approved test data.
1. Run automated tests, evaluation suites, security checks, and human review against the proposed release bundle.
1. Deploy through a controlled promotion path with a defined owner, approval criteria, monitoring window, and rollback plan.
1. Observe production traces and feedback, compare them with the baseline, and investigate regressions before expanding traffic or scope.
1. Review model, API, SDK, and service lifecycle announcements regularly. Retest migration candidates before support deadlines, not during an incident.

## Modernization and lifecycle notes

Microsoft Foundry is the current name after Azure AI Studio and Azure AI Foundry. For new agent work, use Foundry Agent Service and the v1 Responses API where the target region supports them. The Assistants API sunsets on **August 26, 2026**; migrate agent code and test the new conversation, item, response, and agent-version model before that date. The `azure-ai-inference` package retired on **May 30, 2026**; use the `openai` package and, for current projects, `azure-ai-projects` 2.x. See [migrate from the classic portal](https://learn.microsoft.com/en-us/azure/foundry/how-to/navigate-from-classic).

## Reference architecture and learning resources

For a typical production agent, a user request enters an application that authenticates the caller, applies authorization, creates a trace, and calls the model or agent. Retrieval and tools operate through separately authorized services. Guardrails and output validation run before a response or side effect reaches the user. Telemetry, evaluation results, and feedback feed the release process without becoming a substitute for human accountability.

- [Foundry Agents service overview](https://learn.microsoft.com/en-us/azure/foundry/agents/overview)
- [Build knowledge-enhanced AI agents with Foundry IQ](https://learn.microsoft.com/en-us/training/modules/introduction-foundry-iq/)
- [Evaluate generative AI applications](https://learn.microsoft.com/en-us/azure/foundry/how-to/evaluate-generative-ai-app)
- [Analyze and debug your generative AI app with tracing](https://learn.microsoft.com/en-us/training/modules/tracing-generative-ai-app/)
- [Migrate from the Foundry classic portal](https://learn.microsoft.com/en-us/azure/foundry/how-to/navigate-from-classic)
