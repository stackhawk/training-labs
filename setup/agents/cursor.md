# Agentic StackHawk Setup: Cursor

This guide installs the StackHawk skills into **Cursor**. Unlike the marketplace-based agents, Cursor installs the skills directly from the `stackhawk/agent-skills` repository using an install script. The skills teach Cursor how to run the scanner, interpret results, and reason about code-level fixes. Make sure you have already completed **Part 1** (installing and authenticating the `hawk` CLI) for your operating system.

_For the full reference, see StackHawk's [Cursor](https://docs.stackhawk.com/ai-security/agent-skills/cursor/) documentation._

## Step 1: _Install the skills_

Run the install script for your operating system. It clones the repository to a temporary location, installs the Cursor skills, and cleans up after itself.

> [!NOTE]
> This install uses `git` to clone the skills repository, so make sure git is installed first.

### macOS

```
git clone https://github.com/stackhawk/agent-skills.git /tmp/stackhawk-skills
bash /tmp/stackhawk-skills/scripts/install.sh --platform cursor --target ~
rm -rf /tmp/stackhawk-skills
```

### Windows (PowerShell)

```
git clone https://github.com/stackhawk/agent-skills.git $env:TEMP\stackhawk-skills
& "$env:TEMP\stackhawk-skills\scripts\install.ps1" -Platform cursor
Remove-Item -Recurse -Force "$env:TEMP\stackhawk-skills"
```

## Step 2: _Verify the install_

Confirm the skills are active by asking Cursor:

```text
What StackHawk rules do you have?
```

It should describe the StackHawk rules. You can also confirm the rule files exist in `~/.cursor/rules/`; Cursor picks them up automatically.

> [!NOTE]
> The skills work best with a current flagship model, which handles the multi-step scan, fix, and rescan workflow well.

## Next: Run the lab

Your agent is ready. Head to **Part 3** to run the find-fix-verify loop:

[![The Agentic Loop](https://img.shields.io/badge/The%20Agentic%20Loop-Start%20the%20lab-00d7d2?style=for-the-badge)](../../lab/agentic-loop.md)

---

If you run into any issues or have questions along the way, reach out to our support team at support@stackhawk.com. We're happy to help.
