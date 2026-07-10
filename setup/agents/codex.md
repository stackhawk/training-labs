# Agentic StackHawk Setup: Codex

This guide installs the StackHawk skills into **Codex**. The skills teach Codex how to run the scanner, interpret results, and reason about code-level fixes. Make sure you have already completed **Part 1** (installing and authenticating the `hawk` CLI) for your operating system.

_For the full reference, see StackHawk's [Codex](https://docs.stackhawk.com/ai-security/agent-skills/codex/) documentation._

## Step 1: _Add the StackHawk marketplace_

```
codex plugin marketplace add stackhawk/agent-skills-marketplace
```

## Step 2: _Install the skills_

Then install the **`wingman`** umbrella plugin, which pulls in the full skill set in one install:

```
codex plugin add wingman@stackhawk
```

<details>
<summary>If your Codex version doesn't support the umbrella plugin</summary>

Install the skills individually instead:

```
codex plugin add hawkscan@stackhawk
codex plugin add stackhawk-api@stackhawk
codex plugin add hawkscan-ci@stackhawk
codex plugin add stackhawk-data-seed@stackhawk
codex plugin add stackhawk-optimize@stackhawk
```
</details>

## Step 3: _Verify the install_

Confirm the skills are active by asking Codex:

```text
What StackHawk skills do you have?
```

It should list the StackHawk skills, including HawkScan scanning and API reporting.

> [!NOTE]
> The skills work best with a current flagship model, which handles the multi-step scan, fix, and rescan workflow well.

## Next: Run the lab

Your agent is ready. Head to **Part 3** to run the find-fix-verify loop:

[![The Agentic Loop](https://img.shields.io/badge/The%20Agentic%20Loop-Start%20the%20lab-00d7d2?style=for-the-badge)](../../lab/agentic-loop.md)

---

If you run into any issues or have questions along the way, reach out to our support team at support@stackhawk.com. We're happy to help.
