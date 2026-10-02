---
paperclip_version: v2026.1023.0
seo_title: Slack Connector
seo_description: Connect a Slack bot so people can work with an agent from Slack: guided setup, account linking, Slack tools, approvals, scheduled messages, and fixes.
---

# Slack

Slack gives your team a way to work with an agent without leaving the conversation. Someone mentions the agent in a channel, ThinkingMach turns that into a task, and the reply lands back in the thread. The same Slack bot also lets that agent read the discussion, post updates, and send you messages from its ordinary tasks and routines.

There are two separate Slack connections in ThinkingMach, with separate credentials. For most teams, **Chat with an agent** is the one you want.

> **Warning:** Slack is available, but it is not yet fully functional. Follow-up fixes are planned. Review the agent-tool limitations below before relying on it for production work; the separate chat-channel route is experimental.

## Which do you want?

| If you want | Set up | What it gives you |
| --- | --- | --- |
| People to start and continue work by talking to an agent in Slack, and that agent to read and post in Slack while it works | **Chat with an agent** | A Slack bot for one agent: mentions and DMs become ThinkingMach tasks, and the agent gets governed Slack tools |
| An agent to use Slack's own hosted MCP server with a personal Slack authorization | **Use this connection as an agent tool** | Slack's MCP actions, governed by action permissions |

Connecting one does not connect the other. When **Chat connectors** is on and you pick **Slack** under **Connectors**, ThinkingMach asks **Choose how to connect** and offers both options.

---

## Slack as a channel

People mention the agent in a channel or send it a direct message, and ThinkingMach creates a task. Replies come back in the thread.

### Before you connect

- **Chat connectors** must be switched on for the instance. It is an experimental setting, off by default, enabled by an instance administrator under experimental settings.
- A publicly reachable HTTPS address for the instance. Slack delivers events by calling ThinkingMach; this route does not use Slack's socket mode. ThinkingMach Cloud already has one. If you self-host, a private address (for example, a Tailscale Serve URL only your tailnet can reach) is not enough. The wizard shows **Public HTTPS URL required** with a **Learn how to set up HTTPS** link when this is missing.
- Permission to create and install a Slack app in the workspace. If your workspace requires admin approval for new apps, you'll need that approval too.
- A ThinkingMach account that is a member of the company, so you can link your own Slack account at the end.

### Connect it

Open **Connectors**, select **Slack**, then **Chat with an agent**. The first screen asks **Which agent do you want to chat with?** Pick the agent people will talk to. This agent is permanent for the connection; to represent a different agent, connect another Slack app.

Every agent gets its own Slack app, so it shows up in Slack with its own name and avatar. There are two ways to create that app:

| Setup | What you do |
| --- | --- |
| **Automatic setup** (the default) | Paste a temporary Slack token. ThinkingMach creates the app, and you approve the install. |
| **Manual setup** | Under **Advanced** on the token step, choose **Create manually** or **Use an existing app**, then create the app from ThinkingMach's manifest yourself and paste its credentials. |

On the agent step, **Advanced** shows the **Slack app name**, **Bot display name**, and **Slash command**. ThinkingMach fills them in from the agent's name, and every later instruction uses whatever you save here.

You can leave any setup with **Save & exit** and come back through **Finish setup** on the connection's row. Your progress is kept.

#### Automatic setup

Automatic setup has four steps: **Choose agent**, **App configuration access token**, **Install Slack app**, and **Send a message**.

1. **App configuration access token.** Click **Get your App configuration access token**. In Slack app settings, find **Your App Configuration Tokens**, choose **Generate Token**, and pick the workspace for your bot. Copy the **Access Token** (not the refresh token) and paste it into the field. Click **Create Slack app**. ThinkingMach uses this token once to create and configure the app, then throws it away.
2. **Install Slack app.** ThinkingMach opens Slack so you can approve the install. If it doesn't open, or you closed it, click **Install in Slack**. Use your own Slack account: ThinkingMach links the account that approves the install to you, and the bot sends you a welcome DM ("Your Slack account is connected to ThinkingMach").
3. **Send a message to your agent.** Click **Open Slack** and send the bot a message. ThinkingMach checks for it automatically and shows **Success** when it arrives. Click **Done**. If it keeps waiting, open **Didn't work?** for the **Event Subscriptions** and **Request URL** steps.

ThinkingMach uploads the agent's avatar as the app icon. If the upload fails, setup carries on, and you can upload an icon in Slack later.

#### Manual setup or an existing app

On the token step, open **Advanced** and choose **Create manually** to build the app yourself from ThinkingMach's manifest, or **Use an existing app** to connect an app you already have. The manual path has six steps:

1. **Choose agent.**
2. **Create Slack app.** Click **Create Slack app**: Slack opens in a new tab with ThinkingMach's manifest already filled in. Pick your workspace, create the app, and install it. If you made the app earlier, use **I already created the app** instead. To inspect the generated configuration, open **View Slack App Manifest**.
3. **Add credentials.** The two secrets live on different Slack screens, and the wizard gives you directions next to each field:
   - **Bot User OAuth Token**, from **OAuth & Permissions**. It starts with `xoxb-`.
   - **Signing Secret**, from **Basic Information**. It has no token prefix. An `xapp-` app token is not the signing secret.

   Click **Connect Slack app**.
4. **Verify Slack connection.** In your Slack app, choose **Event Subscriptions**. Beside the prefilled **Request URL**, click **Retry** if it isn't verified yet, and save if Slack asks. ThinkingMach moves on by itself once Slack has verified the connection.
5. **Connect your Slack account.** Copy the command ThinkingMach shows (your slash command followed by `connect`, for example `/yourbot connect`) and send it in your Slack workspace. It only identifies you; it does not start any agent work. When your Slack account appears in ThinkingMach, click **This is my Slack account**. Only confirm an account that belongs to you. You'll see **Linked to you**, then **Continue to message test**.
6. **Try it** (optional). Open a channel, invite the bot if needed, and send the suggested message, for example `@yourbot you there?`. Pick the bot from Slack's @mention suggestions so it is really notified. Continue the conversation in the thread. ThinkingMach checks for your message automatically; finish with **I've sent the test message**.

On the manual path, use the generated manifest rather than configuring scopes by hand. It is the supported configuration, and hand-picked scopes are the most common reason a setup half-works. Upload the app icon yourself in Slack under **Basic Information** → **Display Information**.

> **Danger:** The bot token, signing secret, and configuration token are full credentials. Paste them only into ThinkingMach. If one leaks, rotate it in Slack and reconnect.

#### If setup is interrupted

| What you see | What to do |
| --- | --- |
| The configuration token expired or was rejected | Generate a new token and try again. Your draft is kept. |
| ThinkingMach isn't sure whether Slack created the app | Click **Open Slack app settings** and check. If no app exists, tick **I checked Slack and no app was created. Create a new app.** and try again. |
| Your Slack app is saved, but its event settings still need to be applied | Paste a fresh configuration token and click **Retry app configuration**. ThinkingMach reuses the same app. |
| Your credentials are saved but the connection check failed | Click **Retry connecting**. If Slack access changed, click **Authorize in Slack again**. |

### How a conversation becomes work

| In Slack | In ThinkingMach |
| --- | --- |
| A linked person mentions the agent in a channel | One task per new mentioned thread |
| A linked person sends the agent a direct message | The agent replies in the DM (when **Allow direct messages** is on). With **Require at-mention** on, the DM must @mention the bot too. |
| The conversation continues in the thread | It continues on the same task. With **Require at-mention** on, channel replies must @mention the bot. |
| The agent has answered and nothing is left to do | The task shows as **Idle** |

ThinkingMach acknowledges with a reaction so people can see a message was picked up before the agent has finished thinking.

**Require at-mention** is on for new Slack connections. It keeps ordinary chatter from starting agent work: the agent only responds to messages that @mention it, including replies in a thread it's already in. The setting applies to direct messages as well as channels. Turn it off in **Settings** if you want any reply in a followed thread to reach the agent. Connections made before this setting existed keep their previous behavior.

If you stop the agent's run from the Slack thread, the current response ends. Your next message in the thread starts a fresh turn on the same task.

**Idle** means the agent answered and is waiting for the next message. It is not a request for review, so an answered Slack thread does not clutter your review queue or active work counts. You can still find it under the connection's **Conversations** tab, in search, and in recent tasks. A new message in the thread, or one you send from ThinkingMach, picks the task straight back up. If delivery failed, a decision is pending, or more work is queued, the task stays active instead.

### Reply from ThinkingMach

A Slack-linked task shows a **Connected to Slack** banner with an **Open Slack** link. Messages you send on the task, and the agent's replies, are also posted to the Slack thread, labelled with your name. You can also write in the banner's composer and click **Send to channel**: your message is posted to Slack with your name and starts the agent. Your Slack account must be linked to this connection for your messages to go through.

### What the agent can do from Slack

The bot connection gives its agent a set of Slack tools. Ask for things in plain language, such as "summarize this thread and create follow-up tasks for the decisions". The agent can:

- Read channels, threads, messages, files and source links, and search available channel history.
- Send messages and files, react, pin, bookmark, and work with canvases and lists.

Some actions always need approval through ThinkingMach first: creating channels, inviting people, deleting the bot's messages, removing bookmarks, and sharing a list with a channel. A message inside Slack that says "approved" does not count. A channel the bot creates stays switched off for replies until a person turns it on.

The agent works with the permissions of the linked person who asked. Only linked people can direct these tools, and the agent can only read channels that both the bot and that person can see. Private-channel material stays in its source channel or in that person's DM with the bot. The bot cannot join channels on its own, invite itself, or read other people's DMs with it.

Open **Slack tools** at the bottom of the connection's **Settings** tab to check whether the app has every permission the tools need. To change which actions need approval, see [Set action permissions](action-permissions.md).

If you connected Slack before these tools existed, **Slack tools** shows **Add permissions to unlock more tools** with the missing scopes. Your connection keeps working. To unlock the rest, open your app in Slack, go to **OAuth & Permissions**, add those Bot Token Scopes, and reinstall the app to your workspace.

The same tools are available to the agent in its ordinary ThinkingMach tasks and routines, using the bot assigned to that agent and the Slack account linked to the person responsible for the work.

### Send messages later, or on a schedule

You can ask the agent for a Slack message from any of its tasks: "send me a Slack message when the report is ready". It opens its DM with you (the task's responsible person) and posts there. You need a linked Slack account for this to work.

For something like "every weekday at 10am, post the standup summary in #team", use an ordinary [routine](../guides/projects-workflow/routines.md) assigned to the agent. Put the time, timezone, and destination channel in the routine's instructions. There is no separate Slack scheduler. Each run uses the routine's responsible person and checks their Slack link, channel access, and permissions again when it fires. Routine results only reach Slack when the agent sends them explicitly, so say where the message should go.

> **Note:** Sending DMs needs the `im:write` scope. If your app was created before this was added, reinstall it with the updated manifest.

### Settings and access

The connection's page has **Settings**, **Access**, **Conversations**, and **Activity** tabs.

- **Settings** lists **Allowed Channels** (every channel the bot is in), the **Allow direct messages** and **Require at-mention** toggles, **Additional communication instructions**, and the **Slack tools** summary.
- **Access** holds linked people, **Invite people**, and two optional sections: **Guest access** and **Personal Slack search**.

**Allowed Channels** controls where the agent replies and writes, not what it can read. To add a channel, invite the bot to it in Slack. Channels you invite the bot to start enabled, and the list refreshes by itself while you have it open. If you switch one off, it stays off until someone turns it back on in ThinkingMach; a new mention or invitation does not re-enable it.

By default, the agent replies conversationally in Slack: answer first, short paragraphs or short lists, with bigger deliverables attached or linked. Use **Additional communication instructions** (up to 4,000 characters) to add your own guidance, such as product names or audience. It applies when new tasks start, and does not grant any extra permissions.

### Access

Provider channel access determines where a message can reach the integration; it does not by itself authorize agent work. ThinkingMach also checks the sender's linked identity and company membership. Linked users must be active non-viewer members, and their messages run with their own ThinkingMach permissions.

To let a teammate use the agent, click **Invite people** on the **Access** tab and use **Copy invitation** to share the instructions:

1. They send the connect command (your slash command followed by `connect`) in your Slack workspace.
2. The bot sends them a private link. They open it, sign in to ThinkingMach, and confirm their Slack account. The link expires in 15 minutes and works once.
3. If they aren't a member of the organization yet, they request access, and an admin approves the request before they can link.

Nobody needs to create another Slack app or share credentials. Inviting someone to a Slack channel does not give them ThinkingMach access. The agent knows this flow too: if a teammate asks it in Slack how to connect, it gives them the right command.

**Allow unlinked people**, under **Guest access**, is off for new connections. When on, people without linked accounts can start isolated tasks. They cannot approve actions, spend, or manage access, and their requests are refused when isolation is unavailable.

### Optional: personal Slack search

On the **Access** tab, **Personal Slack search** → **Your Slack search access** lets each linked person authorize private search with **Connect Slack search**. Basic channel reading works without it. A connection manager first sets up the Slack app's **Client ID** and **Client Secret** under **OAuth app configuration for connection managers**, registering the shown redirect URL and the user scopes `search:read.public`, `search:read.private` and `search:read.files` in Slack.

> **Note:** Slack's real-time search is not enabled on current runtimes, even when a personal grant is connected. ThinkingMach shows this limitation on the same screen. Agents use bounded channel-history search instead and report what they covered.

---

## Slack as an agent tool

An agent uses a Slack workspace credential to search messages, read channel lists, and post — on its own initiative, inside its own task.

If what you want is an agent that reads and posts in Slack, the chat connection above already gives its agent governed Slack tools. You don't need this route as well for that.

### Agent-tool compatibility notice

**The Slack agent-tool route is available but has known compatibility limitations in v2026.916.0.** That release configures OAuth endpoints and scopes that differ from Slack's current hosted MCP requirements. A successful bot installation does not verify an MCP connection.

Slack MCP requires a registered internal or marketplace-published app, user-token OAuth endpoints, and the user scopes for the intended tools. See [Slack's MCP authentication requirements](https://docs.slack.dev/ai/slack-mcp-server/). Bot-token setup is for the separate chat-channel path below.

Wait for a release with verified Slack MCP compatibility, or contact [support@thinkingmach.com](mailto:support@thinkingmach.com) before attempting this route. The access notes below explain the intended tool behavior; they are not a record of successful setup on this release.

### Access and actions

Channel reach is Slack's decision: the authorized token sees what its scopes and the workspace allow, and private channels require the authorizing user or bot to be a member. ThinkingMach does not have a channel picker, so narrow access on the Slack side.

Slack's server supplies the action list. Posting is classified as a write, so leave it on **Ask first** unless you want an agent posting to a shared workspace unprompted. Open the connection's **Permissions** tab for the live list. See [Set action permissions](action-permissions.md).

### Try it

```txt
Search Slack for the most recent message mentioning "deploy" and tell me which channel it was in. Do not post anything.
```

A read confirms the credential without putting a message in front of colleagues.

> **Note:** Illustrative task, not a recorded test result.

---

## Slack as a channel

People mention the agent in a channel or send it a direct message, and ThinkingMach creates a task. Replies come back in the thread.

### Before you connect

- **Chat connectors** must be switched on for the instance. It is an experimental setting, off by default, enabled by an instance administrator under experimental settings.
- A publicly reachable HTTPS address for the instance. Slack delivers events by calling ThinkingMach; this route does not use Slack's socket mode.
- Permission to create and install a Slack app in the workspace.
- The agent that will answer.

### Connect it

1. Open **Connectors**, select **Slack**, then **Chat with an agent**.
2. On the **Access** step, choose the identity and which agents may use the connection.
3. ThinkingMach generates a **Slack app manifest** containing exactly the scopes, events, and request URL this route needs. Copy it.
4. At [Slack API apps](https://api.slack.com/apps), create an app **from the manifest** rather than configuring scopes by hand, then install it in the workspace.
5. Back in ThinkingMach, paste the **Bot User OAuth Token** (it starts with `xoxb-`) and the **Signing Secret**.
6. Choose the agent that will answer, and finish.

Use the generated manifest. It is the supported configuration, and hand-picking scopes is the most common reason a channel setup half-works.

> **Danger:** The bot token and signing secret are full credentials for the app. Paste them only into ThinkingMach. If either leaks, rotate it in Slack and reconnect.

### How a conversation becomes work

| In Slack | In ThinkingMach |
| --- | --- |
| Someone mentions the agent in a channel | One task per new mentioned thread |
| Someone sends the agent a direct message | The agent replies in the DM |
| The conversation continues in the thread | It continues on the same task |

ThinkingMach acknowledges with a reaction so people can see a message was picked up before the agent has finished thinking.

### Access

Provider channel or repository access determines where a message can reach the integration; it does not by itself authorize agent work. ThinkingMach also checks the sender's linked identity and company membership. Linked users must be active non-viewer members. Unlinked senders depend on the connection's **Allow unlinked people** setting and any sponsor requirements. Review these controls before inviting people to use the agent.

### Try it

1. Invite the app to a private channel you control.
2. Mention it: `@YourAgent hello, can you confirm you are connected?`
3. Expect a reaction, then a threaded reply, and a matching task in ThinkingMach.

> **Note:** Procedure, not a recorded test result. Use a private channel for the first attempt.

---

## Troubleshooting

| Problem | Likely cause | Fix |
| --- | --- | --- |
| **Chat with an agent** is not offered | **Chat connectors** is off for the instance | Ask an administrator to enable it |
| **Public HTTPS URL required** appears | Slack can't reach the instance | Put the instance behind a public HTTPS address, then continue |
| Slack refuses to install the app | The workspace restricts app installation or requires approval | Ask a workspace administrator, then resume with **Finish setup** |
| The configuration token is rejected | App configuration tokens are short-lived and tied to one workspace | Generate a new token for the right workspace and paste the **Access Token**, not the refresh token |
| ThinkingMach warns about the signing secret | You pasted a bot or app token into **Signing Secret** | Copy **Signing Secret** from **Basic Information** |
| **Verify Slack connection** keeps waiting | Slack hasn't re-checked the Request URL since you saved credentials | In **Event Subscriptions**, click **Retry** beside the Request URL |
| Your account never appears after the connect command | The command went to a different workspace or app | Send your app's exact slash command plus `connect` in the workspace where you installed it |
| A mention in a channel does nothing | The bot isn't in the channel, the channel is off in **Allowed Channels**, or the bot was typed as plain text | Invite the bot, enable the channel, and pick the bot from @mention suggestions |
| A reply in the thread doesn't reach the agent | **Require at-mention** is on, so channel replies need an @mention | @mention the bot in the reply, or turn **Require at-mention** off in **Settings** |
| The agent reads a channel but won't reply there | The channel is off in **Allowed Channels** | Enable it in **Settings** |
| **Settings** shows **Add permissions to unlock more tools** | The app predates the newer Slack tools | Add the listed Bot Token Scopes in Slack and reinstall the app |
| A scheduled or requested DM doesn't arrive | The responsible person has no linked Slack account, or the app lacks `im:write` | Link the account from **Access**, or reinstall with the updated manifest |
| Signature verification failures | The signing secret does not match the installed app | Copy the current signing secret and reconnect |
| Agent-tool route: "Slack MCP access is disabled for this app" | MCP access is switched off in the Slack app's settings; signing in doesn't turn it on | Ask the Slack app owner to enable MCP access, then refresh the connection |
| The tool route asks for a client ID and secret | Expected — Slack requires your own OAuth app on this route | Register the app in Slack's console |
| Slack refuses to install the app | The workspace restricts app installation or requires approval | Ask a workspace administrator |
| The channel app is installed but silent | Slack cannot reach the instance, or the app was not created from ThinkingMach's manifest | Confirm the public HTTPS address, then recreate the app from the manifest |
| A mention in a channel does nothing | The app is not a member of that channel | Invite it to the channel |
| Private channel content is missing on the tool route | The authorizing identity is not a member | Add it to the channel in Slack |
| Signature verification failures | The signing secret does not match the installed app | Copy the current signing secret and reconnect |
| Connecting the channel did not give agents Slack actions | The two routes are independent | Set up the tool connection as well |

## Limitations

- One workspace per connection on either route.
- The chat route needs public ingress and does not support socket mode.
- The bot cannot join channels by itself; people invite it. Private channels require explicit membership.
- Search inside Slack-origin work is a bounded history scan, not workspace-wide search, and doesn't read thread replies unless it fetches them.
- Removing the connection in ThinkingMach does not uninstall the Slack app. The app and its bot stay in your workspace until you remove them in Slack.

## Related guides

- [Discord](discord.md), [Microsoft Teams](microsoft-teams.md), [Telegram](telegram.md) — other conversation channels.
- [GitHub](github.md) — the other mixed-purpose connector, with the same tool-versus-channel split.
- [How connector access works](access-model.md)
- [Set action permissions](action-permissions.md)
- [Routines](../guides/projects-workflow/routines.md) — schedule recurring work, including Slack messages.
- [Wire Slack/Discord notifications](../how-to/wire-slack-discord-notifications.md) — a webhook-based alternative for board alerts.
- [Slack app quickstart](https://api.slack.com/start/quickstart)
