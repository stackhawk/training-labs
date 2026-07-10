# StackHawk Developer Training: Getting Started With Agentic StackHawk

This training gets you started on your journey with agentic security testing using StackHawk. Instead of running scans by hand and triaging findings on your own, you point your AI coding agent at your running application and let it do the work. It configures the scan, runs StackHawk's runtime testing against your app, interprets the results, and fixes what it finds, then rescans to confirm the fix. This step-by-step training lets you watch that find-fix-verify loop happen firsthand.

You can follow this training on your own at your own pace, or alongside a live session with the StackHawk team.

### **Throughout this training, we will cover the following.**

* How agentic runtime testing works, and how it compares to doing it by hand
* Installing the `hawk` CLI
* Installing the StackHawk skills in your AI coding agent
* Pointing your agent at your own running application
* Running your first agentic scan
* The find-fix-verify loop, where your agent scans, fixes, and rescans
* Why the depth of your configuration drives the quality of your scan

---

# Prerequisites

> [!IMPORTANT]
> **Complete Parts 1 and 2 before a live training session.** The prerequisites below, plus installing the CLI (Part 1) and your agent's skills (Part 2), are setup to finish ahead of time. In a live session we jump straight into **Part 3: Run the Lab** so we can spend our time on the agentic loop itself. Following this on your own? Just work through the parts in order.

The following prerequisites are the same regardless of your operating system or which AI agent you use.

<details>
<summary>StackHawk Account Access</summary>

To participate in and complete this training, you will need access to your organization's StackHawk account with permission to create new applications and API keys, on an active **Secure**, **Scale**, or **Wingman** subscription.

> _If you already have access to a StackHawk account, please do not create an additional organization. Doing so will add extra steps to the training._

You can log in to your StackHawk account at [**app.stackhawk.com**](https://app.stackhawk.com). If you don't have an account yet, you can [**start a StackHawk Wingman trial**](https://app.stackhawk.com/signup) to get set up.
</details>

<details>
<summary>An AI Coding Agent</summary>

You will need one of the supported AI coding agents installed: **Claude Code**, **Cursor**, **Codex**, **GitHub Copilot**, or **Antigravity**. You will install the StackHawk skills into your agent in Part 2.
</details>

<details>
<summary>Your Application</summary>

You will scan your own application, so have one ready to point your agent at. You need:

* A running application in a development or test environment. Avoid scanning production, since runtime testing sends real attack payloads.
* Its source code open in your AI agent's workspace, so the agent can both scan the app and fix what it finds.

If you don't have an app of your own on hand, a deliberately vulnerable test app is available to scan instead.
</details>

---

# The Training Path

This training has three parts. Setup differs depending on your operating system and your AI agent, but the lab itself is the same for everyone. Complete the parts in order.

## Part 1: Install the hawk CLI

Choose the setup guide for your operating system.

[![macOS|154](https://img.shields.io/badge/macOS-Set%20up-00d7d2?style=for-the-badge&logo=apple&logoColor=white)](setup/os/macos.md)
[![Windows](https://img.shields.io/badge/Windows-Set%20up-00d7d2?style=for-the-badge&logo=windows&logoColor=white)](setup/os/windows.md)

---

## Part 2: Install the StackHawk Skills

Choose the setup guide for your AI coding agent.

[![Claude Code](https://img.shields.io/badge/Claude%20Code-Set%20up-00d7d2?style=for-the-badge&logo=anthropic&logoColor=white)](setup/agents/claude-code.md)
[![Cursor](https://img.shields.io/badge/Cursor-Set%20up-00d7d2?style=for-the-badge)](setup/agents/cursor.md)
[![Codex](https://img.shields.io/badge/Codex-Set%20up-00d7d2?style=for-the-badge&logo=openai&logoColor=white)](setup/agents/codex.md)
[![GitHub Copilot](https://img.shields.io/badge/GitHub%20Copilot-Set%20up-00d7d2?style=for-the-badge&logo=githubcopilot&logoColor=white)](setup/agents/copilot.md)
[![Antigravity](https://img.shields.io/badge/Antigravity-Set%20up-00d7d2?style=for-the-badge&logo=google&logoColor=white)](setup/agents/antigravity.md)

---

## Part 3: Run the Lab

[![The Agentic Loop](https://img.shields.io/badge/The%20Agentic%20Loop-Start%20the%20lab-00d7d2?style=for-the-badge)](lab/agentic-loop.md)

---

# Going Further

Once you have run the loop, these guides help you get more out of StackHawk.

[![Getting the Most From Your Scans](https://img.shields.io/badge/Getting%20the%20Most%20From%20Your%20Scans-Read-00d7d2?style=for-the-badge)](lab/depth-of-config.md)
[![What the Skills Can Do](https://img.shields.io/badge/What%20the%20Skills%20Can%20Do-Read-00d7d2?style=for-the-badge)](lab/what-the-skills-can-do.md)
[![Best Practices](https://img.shields.io/badge/Best%20Practices-Read-00d7d2?style=for-the-badge)](lab/best-practices.md)
[![Troubleshooting](https://img.shields.io/badge/Troubleshooting-Read-00d7d2?style=for-the-badge)](lab/troubleshooting.md)

---

If you run into any issues getting the prerequisites set up, or have questions along the way, reach out to our support team at support@stackhawk.com. We're more than happy to help.

---

_Looking for the original training on running HawkScan manually? It now lives in [`legacy-training/`](legacy-training/README.md). It is kept for reference and is no longer maintained._
