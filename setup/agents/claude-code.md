# Agentic StackHawk Setup: Claude Code

This guide installs the StackHawk skills into **Claude Code**. The skills teach Claude Code how to run the scanner, interpret results, and reason about code-level fixes. Make sure you have already completed **Part 1** (installing and authenticating the `hawk` CLI) for your operating system.

_For the full reference, see StackHawk's [Claude Code](https://docs.stackhawk.com/ai-security/agent-skills/claude-code/) documentation._

## Step 1: _Add the StackHawk marketplace_

From within Claude Code, add the StackHawk marketplace:

```
/plugin marketplace add stackhawk/agent-skills-marketplace
```

## Step 2: _Install the skills_

Then install the **`wingman`** umbrella plugin. A single install of `wingman` pulls in the full skill set (`hawkscan`, `stackhawk-api`, `stackhawk-data-seed`, and `stackhawk-optimize`), so you don't have to install them one by one:

```
/plugin install wingman@stackhawk
```

## Step 3: _Verify the install_

Confirm the skills are active by asking Claude Code:

```text
What StackHawk skills do you have?
```

It should list the StackHawk skills, including HawkScan scanning and API reporting. You can also run `/plugin` to see the installed StackHawk plugins.

> [!NOTE]
> The skills work best with a current flagship model, which handles the multi-step scan, fix, and rescan workflow well.

## Next: Run the lab

Your agent is ready. Head to **Part 3** to run the find-fix-verify loop:

[![The Agentic Loop](https://img.shields.io/badge/The%20Agentic%20Loop-Start%20the%20lab-00d7d2?style=for-the-badge)](../../lab/agentic-loop.md)

---

If you run into any issues or have questions along the way, reach out to our support team at support@stackhawk.com. We're happy to help.
