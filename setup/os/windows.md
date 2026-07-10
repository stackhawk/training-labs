# Agentic StackHawk Setup: Windows

This guide installs the `hawk` CLI and authenticates it with StackHawk. Your AI agent uses the `hawk` CLI behind the scenes to run the runtime testing against your app. When you finish here, move on to installing the skills for your AI agent.

_For the full install reference, see StackHawk's [Install & Run HawkScan](https://docs.stackhawk.com/getting-started/install-and-run/) documentation._

## Step 1: _Installing the hawk CLI_

Install HawkScan with the Windows installer. Paste this into **PowerShell**; it downloads the current version's MSI and runs it:

```
$version = Invoke-RestMethod https://api.stackhawk.com/hawkscan/version
msiexec.exe /i "https://download.stackhawk.com/hawk/$version/windows-x64/hawk.msi" /passive
```

> [!NOTE]
> The installer is for 64-bit Windows.

## Step 2: _Verifying the Install_

This step confirms `hawk` is installed and on your PATH, so your agent can call it. Open a **new** PowerShell window so it picks up the PATH set by the installer, then run:

```
hawk version
```

If it prints a version, you are ready to move on.

> [!NOTE]
> If PowerShell reports `command not found`, the window you are in has not picked up the updated PATH yet. Open a fresh PowerShell window and try again.

## Step 3: _Authenticating To StackHawk_

Now authenticate the CLI with the StackHawk platform so your scans tie back to your account. The browser flow provisions and saves an API key for you:

**_This training uses `hawk init --browser`, which requires hawk 6.0.0 or later. HawkScan 6.0 bundles its own Java runtime, so there is nothing else to install._**

```
hawk init --browser
```

This opens your browser to approve access and saves your key. When it works, you get an **Authenticated!** response.

<details>
<summary>Prefer to set the key manually?</summary>

Log into StackHawk at [**app.stackhawk.com**](https://app.stackhawk.com), go to **Settings > API Keys > Create New API Key**, then run:

```
hawk init
```

Paste your key when prompted.

> [!WARNING]
> You will only be shown your **full** API Key once. Make sure you copy it and save it somewhere for later.
</details>

## Next: Install your agent's skills

Your CLI is ready. Now head to **Part 2** and install the StackHawk skills for your AI coding agent:

[![Claude Code](https://img.shields.io/badge/Claude%20Code-Set%20up-00d7d2?style=for-the-badge&logo=anthropic&logoColor=white)](../agents/claude-code.md)
[![Cursor](https://img.shields.io/badge/Cursor-Set%20up-00d7d2?style=for-the-badge)](../agents/cursor.md)
[![Codex](https://img.shields.io/badge/Codex-Set%20up-00d7d2?style=for-the-badge&logo=openai&logoColor=white)](../agents/codex.md)
[![GitHub Copilot](https://img.shields.io/badge/GitHub%20Copilot-Set%20up-00d7d2?style=for-the-badge&logo=githubcopilot&logoColor=white)](../agents/copilot.md)
[![Antigravity](https://img.shields.io/badge/Antigravity-Set%20up-00d7d2?style=for-the-badge&logo=google&logoColor=white)](../agents/antigravity.md)

---

If you run into any issues or have questions along the way, reach out to our support team at support@stackhawk.com. We're happy to help.
