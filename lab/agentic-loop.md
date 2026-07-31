# The Agentic Loop

This is the core of the training. You point your AI coding agent at a running application and watch it run the find-fix-verify loop: it configures the scan, runs StackHawk's runtime testing, interprets the findings, fixes them in code, and rescans to confirm the fixes, all from plain-language instructions.

This guide is the same no matter which operating system or AI agent you set up. Before you start, finish **Part 1** (the `hawk` CLI, installed and authenticated) and **Part 2** (the StackHawk skills, installed in your agent).

_If anything does not go the way this guide describes, the [Troubleshooting](troubleshooting.md) page covers the common snags._

## With and without an agent

Runtime testing exercises your running application the way an attacker would and reports the real, exploitable issues it finds. It tests your running app rather than reading your source, so what changes with an agentic workflow is who does the work around the scan. Configuring the scan, running it, reading the results, fixing what turns up, and checking the fix are all steps a developer can do by hand, and plenty of teams do. With an agent, those steps happen through a conversation instead: your agent already understands your application, so it writes the scan configuration, runs the scan, connects each runtime finding back to the code that caused it, fixes it in your source, and rescans to confirm, with you directing along the way.

One of the most useful things the agent handles for you is writing the `stackhawk.yml` scan configuration, including working out how your app authenticates so the scan can reach the pages behind your login. You will see it do this in a moment.

## Step 1: _Starting Your App_

You will scan your own application, so open its source code in your AI agent's workspace. That way the agent can both scan the app and fix what it finds. Then get it running in a development or test environment. Avoid scanning production, since runtime testing sends real attack payloads.

Start the app the way you normally do. If you would rather watch the agent do the work, you can ask it to start the app for you, which is a nice first look at the agent driving your project:

```text
Start my app for me.
```

Keep it running, and note the URL it is serving on, such as `http://localhost:8080`. You will point the agent at that URL in the next step.

## Step 2: _Asking Your Agent to Scan_

In your AI agent, give it a single, plain-language instruction. You do not have to hand it any scanner configuration, which is the nice part: the agent works it out from your app.

```text
Set up security scanning for my app running at <your app's URL> and scan it for vulnerabilities.
```

<details>
<summary>What the agent is doing under the hood</summary>

Exact behavior varies by agent and model, but driven by the StackHawk skills, the agent typically:

* Reads your source to understand the app, and picks up an API definition if your project has one (such as an OpenAPI or Swagger spec) to map the API
* Notices if the app has a login and works out how to authenticate, so the scan can reach the pages behind it
* Creates or reuses an application on the StackHawk platform
* Generates a `stackhawk.yml` for you, including any authentication settings, and validates it
* Runs the scan and reads the results

You did not have to write any of that configuration yourself. When it finishes, open the generated `stackhawk.yml` in your project to see exactly what it produced.
</details>

> [!NOTE]
> If the agent ever misses something specific to your app, you can just tell it. For example, "the app authenticates with a JWT in an Authorization header, please configure the scan for that," or "there is an admin area under /admin you didn't reach." Giving the agent a little more context is all it takes for it to adjust the configuration and scan again.

## Step 3: _Reviewing the Findings_

When the scan finishes, the agent reports what it found, grouped by severity. What turns up depends entirely on your app, so there is no set list to expect. To give you a sense of the kinds of things HawkScan surfaces at each severity:

* **High**, the exploitable issues that need attention first, such as SQL Injection, Cross-Site Scripting, Path Traversal, or Remote OS Command Injection
* **Medium**, often configuration and policy gaps, such as a cross-domain (CORS) misconfiguration, a missing Content Security Policy, a missing anti-clickjacking header, or missing anti-CSRF tokens
* **Low**, hygiene and information-leak issues, such as a leaked `X-Powered-By` header, a missing `X-Content-Type-Options` header, or cookie flag issues

This is the find half of the loop. Take a moment to read what the agent surfaced. Anything it reports is a real issue in the running app, confirmed by testing it, not a guess from reading the source. If the scan comes back clean, that is not automatically an all-clear. It can also mean the scan did not reach very far into your app yet, which is what [Getting the Most From Your Scans](depth-of-config.md) helps you improve.

## Step 4: _Fixing and Verifying_

If the scan surfaced findings, this is where the agent closes them out. Ask it to fix what it found and confirm the fixes:

```text
Fix all of these security findings and rescan to confirm they're resolved.
```

The fixing here is done by your AI agent, not by the scanner. StackHawk surfaces the evidence for each finding, and your agent uses that evidence to go into the source code and remediate each issue the way your codebase would, whether that is the query behind a SQL injection, the output behind a cross-site scripting finding, or the headers behind a configuration finding.

When the agent has made its changes, it rescans to verify. The rescan reruns just the specific checks that fired the first time, so it is quick and focused on the exact issues you found, and anything genuinely resolved drops out of the results. A rescan is how you confirm a fix actually worked, and if something is not fully resolved the agent can keep refining and rescanning until it is. Just make sure your app is running your latest code before each rescan, whether that means a live reload or a quick restart.

> [!NOTE]
> The StackHawk skill can also run this loop on its own right after you make a code change, without being asked. Some teams work that way so security testing just happens as they build. For this training we drive it step by step so you can see each stage.

That is the whole loop. Scan, fix, rescan, verified, driven entirely by plain language.

## Recap: with and without an agent

| Step | Without an agent | With an agent |
|------|------------------|---------------|
| Configure | You write `stackhawk.yml` by hand | The agent generates the config and validates its syntax, so it is right the first time |
| Authenticate | You work out the auth flow and write the config by hand | The agent reads your app to work out the login and configures it, no auth expertise needed |
| Scan | You run the scan | The agent runs it for you and reads the raw output |
| Triage | You read through the findings and rank them | The agent parses them, groups by severity, and explains what each one means |
| Fix | You research and edit each issue yourself | The agent remediates the source from the scanner's evidence, using its knowledge of your codebase |
| Verify | You rerun the scan and compare | The agent rescans the checks that fired and confirms what is resolved |

In one conversation you configured, scanned, fixed, and verified your own app, without writing a scan config or triaging a finding yourself. That is agentic runtime testing.

## Where to go next

[![Getting the Most From Your Scans](https://img.shields.io/badge/Getting%20the%20Most%20From%20Your%20Scans-Read-00d7d2?style=for-the-badge)](depth-of-config.md)
[![What the Skills Can Do](https://img.shields.io/badge/What%20the%20Skills%20Can%20Do-Read-00d7d2?style=for-the-badge)](what-the-skills-can-do.md)
[![Best Practices](https://img.shields.io/badge/Best%20Practices-Read-00d7d2?style=for-the-badge)](best-practices.md)

If you run into any issues or have questions along the way, reach out to our support team at support@stackhawk.com. We're happy to help.
