# Table of Contents 

<!-- TOC start (generated with https://github.com/derlin/bitdowntoc) -->

  * [Building apps for Microsoft Teams](#building-apps-for-microsoft-teams)
    + [What makes up a Microsoft Teams app?](#what-makes-up-a-microsoft-teams-app)
    + [Where can the Teams client be extended?](#where-can-the-teams-client-be-extended)
    + [Teams, channels, and group chats](#teams-channels-and-group-chats)
    + [Personal apps](#personal-apps)
    + [Messages](#messages)
    + [Writing messages](#writing-messages)
    + [Meeting apps](#meeting-apps)
  * [User interface (UI) elements](#user-interface-ui-elements)
    + [Cards and card actions](#cards-and-card-actions)
    + [Task modules (dialogs)](#task-modules-dialogs)
    + [Deep links](#deep-links)
    + [Web content pages](#web-content-pages)
  * [What are messaging extensions?](#what-are-messaging-extensions)
  * [What is a Microsoft Teams tab?](#what-is-a-microsoft-teams-tab)
  * [How are bots in Microsoft Teams unique?](#how-are-bots-in-microsoft-teams-unique)
  * [Teams App modules](#teams-app-modules)

<!-- TOC end -->

# Contents: DevOps Microsoft 365 Developer

1. [Introduction to building apps for Microsoft Teams](https://learn.microsoft.com/en-us/training/modules/intro-microsoft-teams-apps)
1. [Task-oriented interactions in Microsoft Teams with messaging extensions](https://learn.microsoft.com/en-us/training/modules/msteams-messaging-extensions)
1. [Create embedded web experiences with tabs for Microsoft Teams](https://learn.microsoft.com/en-us/training/modules/embedded-web-experiences)
1. [Create interactive conversational bots for Microsoft Teams](https://learn.microsoft.com/en-us/training/modules/msteams-conversation-bots)
1. [Collect Input in Microsoft Teams with Task Modules](https://learn.microsoft.com/en-us/training/modules/msteams-task-modules)
1. [Connect web services to Microsoft Teams with webhooks and Office 365 Connectors](https://learn.microsoft.com/en-us/training/modules/msteams-webhooks-connectors)
1. [Microsoft Teams - Use the teamwork Microsoft Graph endpoint](https://learn.microsoft.com/en-us/training/modules/msteams-teamwork-endpoint)
1. [Microsoft Teams - Authentication and Single Sign-on](https://learn.microsoft.com/en-us/training/modules/msteams-sso)
1. [Create interactive meeting apps for Microsoft Teams](https://learn.microsoft.com/en-us/training/modules/msteams-meetings-apps)
1. [Get started building apps for Microsoft Teams by using Teams Toolkit for Visual Studio Code](https://learn.microsoft.com/en-us/training/modules/teams-toolkit-vsc-introduction)
1. [Build a bot by using Teams Toolkit for Visual Studio Code](https://learn.microsoft.com/en-us/training/modules/teams-toolkit-vsc-create-bot)
1. [Build a Microsoft Teams tab app by using Teams Toolkit for Visual Studio Code](https://learn.microsoft.com/en-us/training/modules/teams-toolkit-vsc-tab-app)
1. [Add chat to a Microsoft Teams app by using the Teams JavaScript client library](https://learn.microsoft.com/en-us/training/modules/teams-toolkit-vsc-integrate-chat)
1. [Deploy a Microsoft Teams app to Azure by using Teams Toolkit for Visual Studio Code](https://learn.microsoft.com/en-us/training/modules/teams-toolkit-vsc-deploy-apps)
1. [Show a user's emails in an ASP.NET Core app with Microsoft Graph](https://learn.microsoft.com/en-us/training/modules/msgraph-dotnet-core-show-user-emails)
1. [Access a user's calendar events in an ASP.NET Core app with Microsoft Graph](https://learn.microsoft.com/en-us/training/modules/msgraph-dotnet-core-access-user-events)
1. [Download and upload files in an ASP.NET Core app with Microsoft Graph](https://learn.microsoft.com/en-us/training/modules/msgraph-dotnet-core-manage-files)
1. [Show a user's emails in a JavaScript app with Microsoft Graph](https://learn.microsoft.com/en-us/training/modules/msgraph-show-user-emails)
1. [Access a user's calendar events in a JavaScript app with Microsoft Graph](https://learn.microsoft.com/en-us/training/modules/msgraph-access-user-events)
1. [Download and upload files in a JavaScript app with Microsoft Graph](https://learn.microsoft.com/en-us/training/modules/msgraph-manage-files)

## Building apps for Microsoft Teams

### What makes up a Microsoft Teams app?
Apps built on the Microsoft Teams Platform consist of three primary components:

 - The Microsoft Teams client provides the extensions points and UI elements your app will use to engage your users.
 - Your Teams app package is installed in Microsoft Teams. It contains a small icon, a large icon, and a manifest JSON file. The manifest file contains the metadata for your app (like the name of the app, the developer's name, and so on), which extensibility points your app uses (like tabs and messaging extensions), and pointers to your web services that power your app (like the ID for your bot, or your tab's URL).
 - Your web services that you host provide the APIs and logic that power your app.

It's important to keep in mind that the Microsoft Teams Platform isn't a hosting service; the web services powering your app must be hosted by you and accessible by HTTPS over the internet.

### Where can the Teams client be extended?

There are multiple places where the Microsoft Teams client can be extended to allow users to interact with your app. Depending on your scenario, you may choose to focus on a single extension point (like a personal conversational bot), or combine multiple extension points.

### Teams, channels, and group chats

Teams, channels, and group chats allow multiple people to collaborate. Apps that extend context make themselves available to all members of the group or conversation, typically focusing on enabling other collaborative workflows or unlocking new social interactions. Your app will have access to APIs allowing it to get information about the members in the conversation, the channels in a team, and metadata about the team or conversation.

They can be expanded with:
- Conversational, call, and online media bots interacting with members of the conversation through chat, and responding to events (like a new member being added, or a channel being renamed). All conversations with a bot in this context are visible to all members of the channel or group, so you'll need to ensure the conversation is relevant to everyone.
- Channel and group chat tabs providing a full-screen embedded web experience configured for the channel or group chat it's installed in. All members will interact on the same shared web-app, so a stateless single page app experience is typical.
- Webhooks and connectors enabling external services to post messages to the conversation. You can take advantage of cards and card actions to create rich, actionable messages. Webhooks provide a simple, unauthenticated, one-way method to post messages to a channel, while Connectors provide a slightly more robust back-and-forth experience.

### Personal apps
Personal apps are the portion of your Teams app focusing on interactions with a single user. The experience is unique to each individual user. This portion of your app can be pinned to the left-navigation rail - enabling one-click access for your users.

They can contain:
 - Conversational, call, and online media bots having a one-to-one conversation with the user. Because this is a private conversation, if your app needs to have a multi-turn conversation with a user, or provide a notification relevant only to a single user, it's typically best to have that interaction in a personal app.
 - Personal Tabs providing a full-screen embedded web experience.

### Messages
Messages are the heart of collaboration in Teams. With a messaging extension action command, your app can allow users to invoke your app's API from a message, sending the contents of the message to your app for processing or action. Your app can respond by presenting a form (a task module) to the user to collect more information, send a reply to the original message, or send a message directly to the user.

### Writing messages
Your app can help users craft more effective messages by enabling them to search, or take action, in an external system, and insert the results in a rich, structured format complete with actionable buttons.
There are three ways your app can help users create better messages:
 - Messaging Extension - action commands present your user with a modal form (a task module), submit the results of the form to your app, then either insert a message into the conversation directly, or create part of a message the user can edit before sending to the conversation.
 - Messaging Extension - search commands allowing them to quickly search an external system, preview the results of that search, then insert the result into the chat as a rich card.
 - Messaging Extension - link unfurling allows your app to monitor web domains you're interested in. When a URL containing that domain is pasted into the compose message box, your app's API will be invoked, allowing you to add a rich card to the message with additional information about the item being linked to.

### Meeting apps
Meetings enable collaboration, partnership, informed communication, and shared feedback. The meeting app can deliver a user experience for each stage of the meeting lifecycle.
Meeting lifecycle includes pre-meeting, in-meeting, and post-meeting app experience, depending on the attendee's status.

## User interface (UI) elements
Also, to extensibility points, the Microsoft Teams Platform provides flexible UI elements for apps to take advantage of. These elements allow you to create rich experiences that feel native to the Teams client.

### Cards and card actions
Cards are user-interface containers defined by schematized JSON, that can contain multiple properties and attachments. They can contain formatted text, media, controls (like drop-down boxes and radio buttons), and buttons that trigger card actions. Card actions can send payloads to your app's API, open a link, start authentication flows, or send messages to conversations. The Microsoft Teams Platform supports multiple types of cards including Adaptive Cards, Hero Cards, Thumbnail Cards and more. They can be combined into Card Collections and displayed in a list or carousel.

### Task modules (dialogs)
Task modules allow you to create modal popup experiences in your Teams application. Inside the popup you can run your own custom HTML/JavaScript code, show an &lt;iframe&gt; such as a YouTube or Microsoft Stream video or display an Adaptive card. They're especially useful for starting and completing tasks or displaying rich information like videos or Power BI dashboards. A popup experience is often more natural for users starting and completing tasks compared to a tab or a conversation-based bot experience.

### Deep links
Your app can create URL deep links to help navigate your user through your app, and the Teams client. You can create a deep link for most entities within Teams, and some (like a new meeting request) allow you to pre-populate information using query strings in the URL. For example, your conversational bot could send a message to a channel with a deep link to a task module that results in a card being sent as a one-to-one message to a user, that in turn contains a deep link to create a new meeting with a specific user at a certain date/time. Use deep links to connect across the various extension points available to your app, keeping your user in the correct context.

### Web content pages
A web content page is a webpage you host that can be embedded in a tab or a task module. To enable your webpage to be embedded in a Microsoft Teams client, it must:
 - Be hosted on an HTTPS endpoint.
 - Support being embedded in an &lt;iframe&gt; by the Teams client.
 - Include the Microsoft Teams JavaScript client SDK, and invoke the SDK's initialize() method on page load.

## What are messaging extensions?
Messaging extensions allow users to interact with your web service through buttons and forms in the Microsoft Teams client. They can search, or start actions, in an external system from the compose message area, the command box, or directly from a message. You can then send the results of that interaction back to the Microsoft Teams client, typically in the form of a richly formatted card.

<img src="notes/02-messaging-extension-invoke-locations.png" alt="package" width="450"/>

## What is a Microsoft Teams tab?
By using a Microsoft Teams tab, you can display rich interactive web content within Microsoft Teams clients. There are two approaches to creating a custom tab. You can take an existing web-app experience and adapt it to a custom tab. You also can build a custom tab from scratch.

Microsoft Teams tabs are web applications that are hosted by the provider or developer of the application. A tab in Microsoft Teams displays the specified web app within an `<iframe>` in the Microsoft Teams client.

- Tab Scope

    <img src="notes/02-04.png" alt="package" width="450"/>

    1. Team scope: Tabs in channels can be used by teams to interact with your shared experience. These tabs are referred to as channel tabs and were previously known as configurable tabs. When these tabs are added to a team, a user configures the content of your tab experience when the tab is first added to a channel.

    1. Group chat: Channel tabs also can be used in group chats. These are conversations between two or more users.

    1. Personal scope: With personal tabs, users can interact with your experience privately. The content in a personal tab is only relevant to individual users.

- With the [Developer Portal for Microsoft Teams](https://learn.microsoft.com/en-us/microsoftteams/platform/concepts/build-and-test/teams-developer-portal), you can create or integrate your own Microsoft Teams apps, whether you develop custom apps for your enterprise or SaaS applications for teams around the world by streamlining the creation of the manifest and package for your app and providing useful tools like the Card Editor and a React control library. 

- You have two options to create the project and resources necessary to implement Microsoft Teams tabs: Node.js or .NET.

    1. The Teams Toolkit makes it simple to get started with app development for Microsoft Teams using Visual Studio and Visual Studio Code. You can do the following things with the Teams Toolkit:

    1. The Microsoft Teams Yeoman Generator is a community-managed solution that developers can use to create Node.js-based projects. 

## How are bots in Microsoft Teams unique?

- Bots created using the Microsoft Bot Framework are diverse and can be used in multiple platforms. Bots developed for Microsoft Teams contain some differences from other platforms. The primary difference in bots developed for Microsoft Teams is in how activities are handled.

- The Microsoft Teams activity handler derives from the Bot Framework's activity handler to route all Teams activities before allowing any non-Teams-specific activities to be handled.

- Bots for Microsoft Teams are built on the Microsoft Bot Framework. Microsoft recommends you use either C# or Node.js to take advantage of our SDKs. These packages extend the basic Bot Builder SDK classes and methods:

    - Team Member Events
        - teamMemberAdded
        - teamMemberRemoved
    - Channel Events
        - channelCreated
        - channelRenamed
        - channelDeleted
    - Team Events
        - teamRenamed

## Teams App modules

- Task modules are modals that you can populate with either an Adaptive Card or an embedded web/content page for use in your custom Microsoft Teams app's user experience for workflows that require data input. Task modules allow you to gather information in a Teams-aware popup.

- Webhooks and connectors are a simple way to connect your web services to channels and teams inside Microsoft Teams. Outgoing webhooks allow your users to send text messages from a channel to your web services. Connectors allow users to subscribe to receive notifications and messages from your web services. There are two types of connectors available in Microsoft Teams: incoming webhooks and Office 365 connectors.

- Microsoft Teams is the ultimate hub for teamwork and intelligent communications. Microsoft Teams delivers chat-based collaboration, meetings, calling, and enterprise voice features. Developers can tap into the power of Microsoft Teams with Microsoft Graph to integrate your custom applications

- In Microsoft Teams, there are two different authentication flows for the app. Perform `a traditional web-based authentication flow` in a content page embedded in a tab, a configuration page, or a task module. If the app contains a conversational bot, `use the OAuthPrompt flow`, and optionally the Azure Bot Framework's token service to authenticate a user as part of a conversation. 

- Microsoft Teams meetings apps are based on the following concepts:

    1. Meeting lifecycle has different stages: pre-meeting, in-meeting, and post-meeting.
    1. There are three distinct participant roles in a meeting: organizer, presenter, and attendee.
    1. There are various user types in a meeting: in-tenant, guest, federated, and anonymous users.

- Teams Toolkit for Visual Studio Code helps you build capabilities for Microsoft Teams, such as: Bots, Tabs, Message extensions

- Teams Toolkit for Visual Studio Code makes it easy for you to build bots for Microsoft Teams. By using scenario-based app templates, you can create and run a bot in minutes without doing any manual configuration.

- Bots are highly flexible and can be installed in many locations in Microsoft Teams, such as:

    1. Chats
    1. Channels
    1. Meetings
    1. Personal apps

- Tabs in Microsoft Teams allow you to display content like webpages, documents, or custom app features directly in your personal workspace, group chats, or team channels.

- The Microsoft Teams JavaScript client library is a collection of APIs that are accessed through JavaScript functions. The library can help you integrate native and custom Teams features into Teams applications. By using the library's ready-made functions, you can:

    1. Access Teams APIs to create or manage teams, channels, and other objects within the Teams environment.
    1. Allow users to interact with third-party services.
    1. Allow users to access your app securely by using the authentication API.

    ```js
    yarn add @microsoft/teams-js

    <script src="https://res.cdn.office.net/teams-js/2.7.1/js/MicrosoftTeams.min.js "></script>
    ```

- This exercise makes it easy for you to create a basic web app. To get the initial starting app code that you'll use, browse to `https://github.com/microsoftdocs/mslearn-m365-microsoftgraph-dotnetcorerazor` and choose from one of the following options: