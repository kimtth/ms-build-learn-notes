## Microsoft 365 Copilot Development Study Notes

The [Microsoft 365 developer platform](https://learn.microsoft.com/en-us/microsoft-365/developer/) supports enterprise agents and apps across Microsoft 365 Copilot, Teams, Outlook, Microsoft Graph, Office, SharePoint, and Power Platform. Start from the user workflow, then choose the smallest supported extension point that provides the required user experience, data access, actions, and deployment scope.

## Contents: Microsoft 365 Copilot Development

- [Extend Microsoft 365 Fundamentals learning path](https://learn.microsoft.com/en-us/training/paths/m365-extend-fundamental/)
- [Microsoft 365 Copilot extensibility documentation and training](https://learn.microsoft.com/en-us/microsoft-365-copilot/extensibility/)
- [Introduction to building apps for Microsoft Teams](https://learn.microsoft.com/en-us/training/modules/intro-microsoft-teams-apps)
- [Task-oriented interactions in Microsoft Teams with messaging extensions](https://learn.microsoft.com/en-us/training/modules/msteams-messaging-extensions)
- [Create embedded web experiences with tabs for Microsoft Teams](https://learn.microsoft.com/en-us/training/modules/embedded-web-experiences)
- [Create interactive conversational bots for Microsoft Teams](https://learn.microsoft.com/en-us/training/modules/msteams-conversation-bots)
- [Collect input in Microsoft Teams with task modules](https://learn.microsoft.com/en-us/training/modules/msteams-task-modules)
- [Connect web services to Microsoft Teams with webhooks and Office 365 Connectors](https://learn.microsoft.com/en-us/training/modules/msteams-webhooks-connectors)
- [Use the teamwork Microsoft Graph endpoint](https://learn.microsoft.com/en-us/training/modules/msteams-teamwork-endpoint)
- [Microsoft Teams authentication and single sign-on](https://learn.microsoft.com/en-us/training/modules/msteams-sso)
- [Create interactive meeting apps for Microsoft Teams](https://learn.microsoft.com/en-us/training/modules/msteams-meetings-apps)
- [Get started building apps for Microsoft Teams by using Teams Toolkit for Visual Studio Code](https://learn.microsoft.com/en-us/training/modules/teams-toolkit-vsc-introduction)
- [Build a bot by using Teams Toolkit for Visual Studio Code](https://learn.microsoft.com/en-us/training/modules/teams-toolkit-vsc-create-bot)
- [Build a Microsoft Teams tab app by using Teams Toolkit for Visual Studio Code](https://learn.microsoft.com/en-us/training/modules/teams-toolkit-vsc-tab-app)
- [Add chat to a Microsoft Teams app by using the Teams JavaScript client library](https://learn.microsoft.com/en-us/training/modules/teams-toolkit-vsc-integrate-chat)
- [Deploy a Microsoft Teams app to Azure by using Teams Toolkit for Visual Studio Code](https://learn.microsoft.com/en-us/training/modules/teams-toolkit-vsc-deploy-apps)
- [Show a user's emails in an ASP.NET Core app with Microsoft Graph](https://learn.microsoft.com/en-us/training/modules/msgraph-dotnet-core-show-user-emails)
- [Access a user's calendar events in an ASP.NET Core app with Microsoft Graph](https://learn.microsoft.com/en-us/training/modules/msgraph-dotnet-core-access-user-events)
- [Download and upload files in an ASP.NET Core app with Microsoft Graph](https://learn.microsoft.com/en-us/training/modules/msgraph-dotnet-core-manage-files)
- [Show a user's emails in a JavaScript app with Microsoft Graph](https://learn.microsoft.com/en-us/training/modules/msgraph-show-user-emails)
- [Access a user's calendar events in a JavaScript app with Microsoft Graph](https://learn.microsoft.com/en-us/training/modules/msgraph-access-user-events)
- [Download and upload files in a JavaScript app with Microsoft Graph](https://learn.microsoft.com/en-us/training/modules/msgraph-manage-files)

> **Current guidance:** Some retained Microsoft Learn module titles refer to Teams Toolkit, task modules, Office 365 Connectors, or Bot Framework-era patterns. Use them for foundational concepts, then apply the current Microsoft 365 Agents Toolkit, TeamsJS v2, Teams SDK, and Microsoft 365 Agents SDK guidance in this note before building a solution.

## Topics covered

- [Choose an extensibility model](#choose-an-extensibility-model)
- [Microsoft 365 platform context](#microsoft-365-platform-context)
- [Microsoft 365 Copilot agents](#microsoft-365-copilot-agents)
- [Copilot connectors and APIs](#copilot-connectors-and-apis)
- [Teams apps and agents](#teams-apps-and-agents)
- [Microsoft Graph and Microsoft Entra ID](#microsoft-graph-and-microsoft-entra-id)
- [Office Add-ins](#office-add-ins)
- [SharePoint Framework](#sharepoint-framework)
- [Development, deployment, and operations](#development-deployment-and-operations)
- [Current references](#current-references)

## Choose an extensibility model

| Goal | Recommended starting point | Use it when | Key constraint |
| --- | --- | --- | --- |
| Focus Copilot on a defined task, knowledge set, and approved actions | Declarative agent | Copilot's model and orchestration meet the requirement | It is user-initiated and operates within Copilot's orchestration model |
| Bring custom orchestration, models, proactive automation, or multichannel behavior | Custom engine agent | The workflow requires tailored reasoning, external integrations, or group collaboration | Plan hosting, cost, evaluation, and responsible-AI controls |
| Make external knowledge searchable and available for Copilot grounding | Copilot connector | Content belongs in an external repository or line-of-business system | Decide whether indexed synchronization or query-time federation is appropriate |
| Provide task-focused collaboration UI or a Teams-native agent | Teams app or Teams SDK agent | Users work in chats, channels, meetings, or a Teams personal app | Validate feature availability across intended Teams clients |
| Read or modify Microsoft 365 data | Microsoft Graph | The task needs Microsoft 365 data, automation, or identity-aware access | Request the minimum permissions and choose delegated or app-only access deliberately |
| Extend Word, Excel, PowerPoint, Outlook, OneNote, or Project | Office Add-in | The workflow is centered on document, workbook, mail, or calendar context | Use supported Office.js requirement sets and a compatible manifest |
| Customize SharePoint, Teams, or Viva Connections pages | SharePoint Framework (SPFx) | The result is a web part, extension, or Adaptive Card Extension | Do not depend on the SharePoint page DOM or undocumented CSS |

## Microsoft 365 platform context

Microsoft 365 combines productivity applications such as Teams, Outlook, Word, Excel, PowerPoint, and OneNote with services such as Exchange Online, SharePoint, OneDrive, Microsoft Entra ID, and Microsoft Graph. Build against documented host capabilities and service APIs rather than relying on a specific client or page implementation.

The [Microsoft 365 Developer Program](https://developer.microsoft.com/en-us/microsoft-365/dev-program) provides development resources for supported scenarios. Keep experimentation isolated from production data and tenants, and confirm the current sandbox, licensing, and eligibility requirements before depending on it for a project.

## Microsoft 365 Copilot agents

Microsoft 365 Copilot agents combine **knowledge**, **actions**, an **orchestrator**, a **foundation model**, and a user experience. Define the boundaries of each element before implementation: which data is allowed, who can invoke each action, what confirmation is required, what the agent must refuse, and how failures reach a person or support process. See [Agents for Microsoft 365 Copilot](https://learn.microsoft.com/en-us/microsoft-365-copilot/extensibility/agents-overview).

### Declarative agents

Use a declarative agent when the scenario can be expressed as Copilot instructions, knowledge, and actions while retaining Copilot's model and orchestration. It inherits the Microsoft 365 Copilot security, compliance, and Responsible AI foundation.

- Define a narrow purpose, clear instructions, conversation starters, and explicit constraints.
- Ground the agent with approved Microsoft 365 content, Copilot connector content, uploaded files, or supported external knowledge.
- Add only the actions needed for the business workflow and require confirmation for meaningful side effects.
- Build with [Agent Builder](https://learn.microsoft.com/en-us/microsoft-365-copilot/extensibility/agent-builder), [Microsoft 365 Agents Toolkit](https://learn.microsoft.com/en-us/microsoftteams/platform/toolkit/overview-agents-toolkit), Copilot Studio, or SharePoint as appropriate.
- Validate Responsible AI requirements before distribution. Government-cloud capabilities vary by cloud and authoring approach; confirm the current support matrix before committing to a deployment model.

See [Declarative agents for Microsoft 365 Copilot](https://learn.microsoft.com/en-us/microsoft-365-copilot/extensibility/overview-declarative-agent).

### Custom engine agents

Use a custom engine agent when the scenario needs custom business logic, an orchestration framework, a specific or fine-tuned model, proactive workflows, group productivity, or availability outside Microsoft 365. It can be built with Copilot Studio, the [Microsoft 365 Agents SDK](https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/), Teams SDK, or Microsoft Foundry.

| Approach | Best fit |
| --- | --- |
| Copilot Studio | A managed, low-code agent with standard business-system integrations and governance |
| Microsoft 365 Agents SDK | A pro-code, multichannel agent with custom orchestration and application control |
| Teams SDK | A Teams-focused, collaborative agent for chats, channels, and meetings |
| Microsoft Foundry | An existing or custom AI solution that needs Foundry model, evaluation, and agent capabilities before publishing to Microsoft 365 |

Custom engine agents require an explicit security and operations design. Protect credentials and external APIs, enforce authorization in the backend, record evaluation results, monitor cost and quality, provide a human-escalation path, and test streaming behavior so that final messages and attachments remain correctly ordered. See [Custom engine agents for Microsoft 365](https://learn.microsoft.com/en-us/microsoft-365-copilot/extensibility/overview-custom-engine-agent).

## Copilot connectors and APIs

### Copilot connectors

Copilot connectors extend Copilot with external line-of-business knowledge. Choose the connector model based on the data-residency, freshness, and retrieval requirements:

| Model | Data flow | Choose it for |
| --- | --- | --- |
| Synced connector | Content is ingested into Microsoft Graph and semantically indexed | Repositories and document stores where indexed search, semantic retrieval, and Microsoft Search integration are desired |
| Federated connector | An MCP server retrieves content at query time; content is not moved into Microsoft Graph | Dynamic, sensitive, or regulated data that must remain in its source system |

For a synced connector, design the `title`, `content`, semantic labels, source URL, security trimming, and update lifecycle together. An AI administrator must register the application and grant the required Microsoft Graph permissions. Do not ingest content that a user should not be able to discover in the source system. See [Microsoft 365 Copilot connectors overview](https://learn.microsoft.com/en-us/microsoft-365-copilot/extensibility/overview-copilot-connector).

### Microsoft 365 Copilot APIs

Use [Microsoft 365 Copilot APIs](https://learn.microsoft.com/en-us/microsoft-365-copilot/extensibility/copilot-apis-overview) when an app or custom engine agent needs AI capabilities grounded in Microsoft 365 data. The Retrieval API provides permission-trimmed grounding without extracting data into a separate RAG store. Other APIs include search, meeting insights, interaction export, usage reports, and agent package management; some APIs are preview, so verify support and limitations before production use.

Copilot APIs are REST endpoints under the Microsoft Graph namespace. The app package ID identifies an app for packaging and distribution, while the Microsoft Entra application (client) ID identifies the OAuth application; they are different identifiers. Acquire user tokens with a supported authentication provider such as MSAL, honor organizational policy, and confirm licensing requirements for each user.

Use Microsoft Graph for data operations such as reading or changing files, messages, and events. Use Copilot APIs when the application needs Microsoft 365 AI reasoning over the user's authorized data.

## Teams apps and agents

The [Teams platform](https://learn.microsoft.com/en-us/microsoftteams/platform/overview) supports both collaborative agents and task-focused apps. Teams apps are packaged with a manifest, icons, and metadata; the web service and application logic are hosted by the developer and must be reachable over HTTPS.

| Surface | Use it for | Design guidance |
| --- | --- | --- |
| Personal app or tab | A user's private workspace, dashboard, or focused workflow | Keep the experience useful when opened independently of a chat or channel |
| Channel or group-chat tab | Shared content and collaborative state | Model membership, permissions, and concurrent changes explicitly |
| Message extension | Search, create, or act on external content without leaving the compose or command experience | Make cards understandable when forwarded or viewed out of context |
| Dialog | Focused input, confirmation, or short-lived workflows | Use the TeamsJS dialog capability rather than new task-module patterns |
| Meeting app | Before-, during-, and after-meeting experiences | Account for recurrence, participant roles, and guest or external users |
| Agent | Conversational, context-aware assistance and actions | Select the Teams SDK for Teams-centric collaboration or the Agents SDK for broader channels |

Use [Adaptive Cards](https://adaptivecards.io/) for concise, structured, actionable content. Provide accessible text, use clear action labels, validate every submitted value on the server, and make action processing idempotent so that retries or duplicate submissions are safe.

For web-hosted Teams content, use the current Teams JavaScript client library, initialize it before calling Teams APIs, and ensure the site supports embedding. TeamsJS v2 uses capability namespaces such as `app`, `pages`, and `dialog`; do not add new code that relies on deprecated global callbacks or the deprecated `settings` namespace.

```typescript
import { app, pages } from "@microsoft/teams-js";

await app.initialize();
const context = await app.getContext();

if (pages.config.isSupported()) {
  pages.config.registerOnSaveHandler((saveEvent) => {
    saveEvent.notifySuccess();
  });
}

console.log(context.app.host.name);
```

For new notification scenarios, prefer Teams Workflows, Power Automate webhook triggers, or proactive messaging through the supported SDK. Microsoft 365 Connectors are nearing deprecation, so do not use them as the foundation of a new integration. Assign co-owners for workflow-based notifications and test private and shared channels.

## Microsoft Graph and Microsoft Entra ID

[Microsoft Graph](https://learn.microsoft.com/en-us/graph/overview) is the unified API surface for Microsoft 365, Microsoft Entra ID, and related Microsoft cloud services. It supports REST APIs and SDKs at `https://graph.microsoft.com`.

Authentication begins with a Microsoft Entra app registration. Configure the redirect URIs and credentials appropriate to the client type, use MSAL or another supported library to obtain tokens, and validate tokens on the server. Never treat a client-side UI check as authorization.

| Access model | Use it when | Security implication |
| --- | --- | --- |
| Delegated permissions | A signed-in user performs an action | Both the app and user must be authorized; the app acts within the user's effective access |
| Application permissions | A daemon, background process, or tenant-wide service runs without a user | The app can access data with its own identity and therefore needs stricter governance and admin consent |

Request the least-privileged Microsoft Graph permissions that meet the scenario. Document consent, separate user consent from tenant-admin consent, protect secrets and certificates, and prefer managed identities or workload identity federation where the hosting platform supports them. See [Microsoft Graph authentication and authorization basics](https://learn.microsoft.com/en-us/graph/auth/auth-concepts).

## Office Add-ins

[Office Add-ins](https://learn.microsoft.com/en-us/office/dev/add-ins/overview/office-add-ins) use web technologies and Office.js to extend Word, Excel, PowerPoint, Outlook, OneNote, and Project across supported Office clients. An add-in contains an app package with a manifest and icons plus a separately hosted web application accessed through HTTPS.

- Use the recommended manifest type for the intended host and capability; the unified manifest is recommended for most current scenarios.
- Use add-in commands, task panes, event handlers, and content add-ins only where the target client supports the required API set.
- Build with Office.js rather than COM or VSTO for new cross-platform experiences. COM and VSTO are Windows-only and aren't supported in new Outlook on Windows.
- Test each host, platform, and requirement set that the application promises to support.
- Package and distribute the add-in through centralized deployment for an organization or Partner Center for Microsoft Marketplace, as appropriate.

## SharePoint Framework

[SharePoint Framework (SPFx)](https://learn.microsoft.com/en-us/sharepoint/dev/spfx/sharepoint-framework-overview) is the recommended model for client-side SharePoint customization and a primary replacement for the SharePoint add-in model. Build web parts and extensions for SharePoint, surface the same solution in Teams where supported, and build Adaptive Card Extensions for Viva Connections.

SPFx uses standard web tooling such as Node.js, npm, TypeScript, and JavaScript frameworks. It runs in the current user's browser context and provides supported lifecycle, HTTP, context, and configuration APIs. Do not target the SharePoint page DOM or CSS as a contract: the page DOM is not an API and those dependencies can break a customization. Check the [SPFx compatibility matrix](https://learn.microsoft.com/en-us/sharepoint/dev/spfx/compatibility) before choosing Node.js, TypeScript, React, or SharePoint targets.

## Development, deployment, and operations

1. Define the user task, data classification, actor, host, and success criteria before selecting a platform.
1. Design the app or agent package, Microsoft Entra registration, identity flow, permissions, knowledge sources, actions, and confirmation boundaries as one solution.
1. Use [Microsoft 365 Agents Toolkit](https://learn.microsoft.com/en-us/microsoftteams/platform/toolkit/overview-agents-toolkit) templates and local testing for supported Teams, Microsoft 365 Copilot, Outlook, and agent scenarios. Use the [Microsoft 365 Agents SDK](https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/) when its multichannel agent model fits the requirement.
1. Test happy paths, denied permissions, missing knowledge, malformed card submits, duplicate actions, token expiration, policy blocking, and host-specific rendering.
1. Apply least privilege, store secrets outside source code, validate all service-side authorization decisions, and include audit and support ownership before release.
1. Manage rollout through tenant distribution or the commercial marketplace as applicable. Record version, configuration, permissions, intended users, support contacts, and rollback steps.
1. Monitor availability, latency, errors, action outcomes, agent quality, cost, and user feedback. Re-evaluate prompts, instructions, grounding, and actions with representative test data after significant changes.

### Current modernization guidance

- **Microsoft 365 Agents Toolkit** is the current product name for Teams Toolkit. **TeamsFx** is deprecated and receives community-only support through September 2026.
- The Bot Framework SDK and Emulator are archived; Microsoft support tickets for them are no longer serviced after December 31, 2025. Build new work with the Teams SDK or Microsoft 365 Agents SDK and plan a migration for legacy bots.
- Use **Microsoft Entra ID**, not Azure AD, in new identity documentation and user-facing guidance.
- Mark retired products as historical rather than recommending new implementations with them. For example, Viva Topics retired on February 22, 2025, and Viva Goals retired on December 31, 2025.

## Current references

- [Extend Microsoft 365 Fundamentals learning path](https://learn.microsoft.com/en-us/training/paths/m365-extend-fundamental/)
- [Microsoft 365 Copilot extensibility documentation and training](https://learn.microsoft.com/en-us/microsoft-365-copilot/extensibility/)
- [Microsoft Teams platform documentation](https://learn.microsoft.com/en-us/microsoftteams/platform/)
- [Microsoft Graph documentation and tutorials](https://learn.microsoft.com/en-us/graph/)
- [Office Add-ins documentation](https://learn.microsoft.com/en-us/office/dev/add-ins/)
- [SharePoint Framework documentation](https://learn.microsoft.com/en-us/sharepoint/dev/spfx/)
- [Microsoft 365 Agents SDK documentation](https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/)