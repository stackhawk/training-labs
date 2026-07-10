# Agentic StackHawk Setup: macOS

This guide installs the `hawk` CLI and authenticates it with StackHawk. Your AI agent uses the `hawk` CLI behind the scenes to run the runtime testing against your app. When you finish here, move on to installing the skills for your AI agent.

_For the full install reference, see StackHawk's [Install & Run HawkScan](https://docs.stackhawk.com/getting-started/install-and-run/) documentation._

## Step 1: _Installing the hawk CLI_

### With Homebrew (recommended)

```
brew tap stackhawk/cli
brew trust stackhawk/cli
brew install hawk
```

Homebrew installs the right build for your Mac and puts `hawk` on your PATH automatically.

### Without Homebrew (installer package)

If you don't use Homebrew, download and run the macOS installer. This one-liner grabs the current version and installs it:

```
HAWK_VERSION=$(curl -s https://api.stackhawk.com/hawkscan/version)
curl -L "https://download.stackhawk.com/hawk/${HAWK_VERSION}/darwin-arm64/hawk.pkg" -o hawk.pkg
sudo installer -pkg hawk.pkg -target / && rm hawk.pkg
```

> [!NOTE]
> The installer package is for Apple Silicon Macs. On an Intel Mac, use the Homebrew method above.

## Step 2: _Verifying the Install_

This step confirms `hawk` is installed and on your PATH, so your agent can call it. Run:

```
hawk version
```

If it prints a version, you are ready to move on.

> [!NOTE]
> If your terminal reports `command not found`, open a new terminal window and try again. On a first install, a fresh terminal picks up the newly added command.

## Step 3: _Authenticating To StackHawk_

Now authenticate the CLI with the StackHawk platform so your scans tie back to your account. The browser flow provisions and saves an API key for you:

**_This training uses `hawk init --browser`, which requires hawk 6.0.0 or later. HawkScan 6.0 bundles its own Java runtime, so there is nothing else to install._**

```
hawk init --browser
```

This opens your browser to approve access and saves your key to `~/.hawk/hawk.properties`. When it works, you get an **Authenticated!** response.

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
