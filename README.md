# TaskForge

**An AI team in Discord that runs the admin side of freelance web work.**
Client email in → plan → execution → review → your approval.

![Switch](https://img.shields.io/badge/Built%20with-Switch-5B5BD6?style=for-the-badge)
![Discord](https://img.shields.io/badge/Discord-5865F2?style=for-the-badge&logo=discord&logoColor=white)
![Gmail](https://img.shields.io/badge/Gmail-EA4335?style=for-the-badge&logo=gmail&logoColor=white)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-34A853?style=for-the-badge&logo=googlesheets&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Hackathon](https://img.shields.io/badge/Flip%20the%20Switch-Hackathon%202026-black?style=for-the-badge)

**Team CodeSync** · Track: Best Business Workflow

[DEMO VIDEO](https://youtu.be/royGJFflGsg?si=T_UdKx4GZVEaGPNh) · [BLOG POST](https://codesync.hashnode.dev/setup-took-longer-than-the-agents-an-ai-team-for-freelancers-built-on-switch)




## The Problem

A freelance web developer's work arrives as email. Every message hides a requirement, a deadline and a priority call. Then comes the admin: break the request into tasks, update a tracker, change the site without wrecking the design, write an invoice, and check the result matches what the client asked for. Freelancers do this alone, and things slip.

## What TaskForge does

Three agents run on [Switch](https://github.com/) inside one Discord server, connected to Gmail and Google Sheets.

| Agent | What it does |
|---|---|
| **Planner** | Reads the client email, extracts the requirement, orders work by deadline and priority, breaks it into smaller tasks, writes an execution plan |
| **Execution** | Follows the plan: updates Google Sheets, makes minimal changes to the website design, generates a professional invoice, reports completion to the reviewer |
| **Reviewer** | Checks the generated website, validates invoice details, tests the automation flow, confirms the requirement is met. Requests a correction, or asks the freelancer when unsure |

**Nothing is final until the reviewer has checked it and the freelancer gives final approval.**

## Architecture
<img width="1273" height="377" alt="architecture" src="https://github.com/user-attachments/assets/ce92b242-abb7-493f-963b-c2cae562e93e" />


Four layers: input (Gmail and Discord), the Switch agent environment, a shared workspace (execution plan, review feedback, generated outputs), and external tools (Google Sheets, website files, invoice documents).

## Workflow
<img width="1273" height="453" alt="workflow" src="https://github.com/user-attachments/assets/cabad480-939b-4fe2-83ec-55af5890d6cc" />


1. A client email arrives. The planner turns it into an execution plan in the shared workspace.
2. A person triggers the execution agent in Discord. It updates the Sheet, edits the website files and generates the invoice.
3. A person triggers the reviewer. Outcomes: **Approved**, **Uncertain** (asks the freelancer), or **Correction required** (feedback goes to the workspace, and the execution agent fixes it).
4. The freelancer gives final approval.

## How we used Switch

- **Agents connected:** planner, execution, reviewer, created under *Your Agents* in Switch Console.
- **Coding agent behind them:** OpenCode (used for testing; planned to move to Antigravity).
- **Channel setup:** one Discord server. Each Discord channel is its own Switch room, and agents are added with `!invite-agent @agent-name`. [ADD: your channel names and which agents are in each]
- **Handoffs:** agents pass work through a shared workspace folder, and a person triggers the next step. Both rooms must use the same working directory. This doubles as an approval gate.
- **Access:** *Who can talk to your agent* is set to Anyone or Custom rules, so teammates can use them.

<img width="1917" height="1065" alt="Screenshot 2026-11-07 202939" src="https://github.com/user-attachments/assets/ffe6b64a-ec59-4900-ac6a-9a2088a5f7c5" />
<img width="1911" height="970" alt="Screenshot 2026-11-07 203001" src="https://github.com/user-attachments/assets/bfc105ac-edf7-4603-baba-875946b720bf" />
<img width="1917" height="1063" alt="Screenshot 2026-11-07 203022" src="https://github.com/user-attachments/assets/995cbfb0-53b6-407c-aa7b-e3ade99d2ce9" />
<img width="1865" height="1052" alt="Screenshot 2026-11-07 203050" src="https://github.com/user-attachments/assets/e4142f95-b547-4f0f-b648-4f4ad4611397" />
<img width="1907" height="1061" alt="Screenshot 2026-11-07 203110" src="https://github.com/user-attachments/assets/84426c73-6adf-4604-a382-456afa0c2acc" />




## Run it yourself

**Requirements:** Windows or macOS, Docker Desktop (WSL 2 backend on Windows), Switch Console, a Discord account, a personal Google account, an account for a coding agent (we used OpenCode).

1. **Install Docker Desktop and Switch Console.** Start Docker first. Sign in to your coding agent.
2. **Start the local server.** Switch Console → *Add a server* → *Run a server on this computer* → *Start*. Wait for "Running locally".
3. **Create a Discord bot.** In the Discord Developer Portal create an application and a bot. Copy the token. Turn on **Server Members Intent** and **Message Content Intent**, then Save Changes.
4. **Invite the bot** with scopes `bot` and `applications.commands`, and permissions: View Channels, Send Messages, Send Messages in Threads, Manage Webhooks, Manage Channels, Manage Roles, Read Message History, Attach Files, Add Reactions.
5. **Copy your Server ID** (Discord Developer Mode → right-click server → Copy Server ID).
6. **Connect Discord to Switch.** Home → *Messaging apps* → *Connect* → *Discord*. Paste the bot token and Server ID.
7. **Create the three agents** under *Your Agents* and set *Who can talk to your agent* to Anyone or Custom rules.
8. **Create the rooms.** Post a message in each channel, then run `!invite-agent @agent-name` for each agent. Check with `!agents-status`.
9. **Connect Google.** In Google Cloud Console enable the APIs you use (Gmail, Sheets). Under Google Auth Platform → Audience set user type to External and add your personal `@gmail.com` as a test user. Create an OAuth client of type **Web application**, leave JavaScript origins blank, and paste the Switch callback URL as the redirect URI. Sign in from Switch (Advanced → *Go to [App] (unsafe)* is expected in testing mode)
10. **Add the agent instructions** from [`/agents`](agents/) [ADD: folder with your three agent prompts], then send a test client email.

## Troubleshooting

| Problem | Fix |
|---|---|
| `switch-core stopped ... CancelledError: Cancelled via cancel scope` | Clear leftover processes, restart Docker and the console, press Start again |
| Discord "bridge is not running", `ClientConnectorDNSError ... getaddrinfo failed` | Not the token. Laptop DNS could not resolve Discord. Use 8.8.8.8 and 1.1.1.1, flush DNS, restart the server |
| `planner-agent isn't in this room` | Run `!invite-agent @planner-agent` |
| `I only take instructions from m…` | Change *Who can talk to your agent* from owner-only |
| `Ineligible accounts not added` (Google test user) | Remove stray spaces, use a personal Gmail, not a college Workspace address or alias |
| Desktop OAuth client hides redirect URIs | Create a Web application client instead |
| Bot offline when the laptop sleeps | Change power settings; production fix is an always-on host |

Harmless: `GATEWAY_PUBLIC_URL ... loopback address` on a single machine, and `heartbeat_lapsed` reconnects within about a second.

## Known limitations

- Runs on one laptop, so it stops when the laptop sleeps.
- Handoffs between rooms are triggered by a person (by design, as an approval gate).
- No per-agent permissions yet: every connected agent gets the same Google access.
- Client emails are untrusted input and could attempt prompt injection. The reviewer and the final human approval exist for this reason.
- Discord thread titles show raw role IDs like `<@&1567…>` (cosmetic).
- [ADD: anything else that is not finished. Say plainly what was demoed on real data and what was simulated.]

## Roadmap

- Always-on hosting
- Per-agent permissions (only the planner reads mail, only execution can send an invoice)
- Read-only Gmail scope by default

## Repository structure

```
.
├── README.md
├── docs/
│   ├── architecture.png
│   └── workflow.png
├── agents/          #  prompts / instructions for planner, execution, reviewer
└── samples/         #  sample client email
```

## Team CodeSync

- [Dhanishka Kumari Sahoo] ([GitHub](https://github.com/Dhanishka26))
- [Asmit Panda] ([GitHub](https://github.com/ShulPaani))

Built for the **Flip the Switch Hackathon** by WeMakeDevs and DevTools Academy.
