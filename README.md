# Myelos plugins for Claude

A plugin marketplace for Claude (claude.ai, Cowork and Claude Code).

## servicelog

[servicelog.cloud](https://servicelog.cloud) keeps your motorcycle's maintenance record: service log, open issues, service intervals, reference sheet and manual. This plugin connects Claude to it and adds four skills.

You need a servicelog.cloud account to use it. Accounts are currently by invitation only; there is no public sign-up.

| Skill | What it does |
| :- | :- |
| `log-maintenance` | Asks a few focused questions about work you did, shows you the entry, and records it once you say yes. |
| `whats-due` | Lists overdue and upcoming service intervals on a bike, soonest first. |
| `report-problem` | Works through a fault using the bike's record and manual, then offers to open an issue. |
| `overview` | Shows the overview card of your bikes, with a short summary of what needs attention. |

Claude also picks the right skill on its own when your message matches one. Nothing is written to your record without your confirmation.

### Install on claude.ai

1. Go to **Customize > Plugins > Add > Add marketplace** and enter `Myelos/claude-plugins`.

2. Install **Servicelog** from the marketplace.

3. Open the plugin's **Connectors** tab. If you already have servicelog.cloud connected under **Customize > Connectors**, check that the plugin's tab shows it as **Connected** and skip adding it. Only if it shows **Not added**, add it: it is added as a custom connector, and you sign in with your servicelog.cloud account the first time it connects.

4. In a chat, type `/` and then `servicelog` to see the skills.

The skills load even before the connector is connected, but they need it to read or write your record.

### Install in Claude Code

```
/plugin marketplace add Myelos/claude-plugins
/plugin install servicelog@myelos
```

Then run `/mcp`, select the servicelog server and sign in to servicelog.cloud. The skills are available as `/servicelog:log-maintenance`, `/servicelog:whats-due`, `/servicelog:report-problem` and `/servicelog:overview`.

If you also have servicelog.cloud as a claude.ai connector, Claude Code uses the plugin's server and hides the connector, so you get one set of tools, not two. The plugin's server needs its own sign-in, and its tools are named `mcp__plugin_servicelog_servicelog__*`, so update any permission rules that name the connector's tools.

A plugin installed on claude.ai also appears in Claude Code when you sign in with the same claude.ai account, so you only need to install it in one place. Because the plugin's server then takes over from your claude.ai connector, run `/mcp` once in Claude Code, select the servicelog server and sign in to servicelog.cloud there too.
