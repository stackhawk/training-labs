# Best Practices

A few habits make agentic runtime testing work well day to day. None of them are complicated, and most of them fall out naturally once your agent is driving the loop.

## Scan early, in development

The best time to catch an issue is while you are still writing the code that causes it. Run scans in your development environment before you merge, not weeks later. This is where the agentic loop shines, because the agent can find an issue, fix it, and verify the fix in the same session, while the code is fresh in your head. Start simple:

```text
Scan my app for vulnerabilities before I open this pull request.
```

If you want it to happen on its own, tell the agent to fold scanning into your workflow:

```text
From now on, scan after each change you make and fix what it finds.
```

If your agent runs the loop automatically after a change, you get this for free. Every meaningful change gets scanned, and problems get fixed before they ever reach a pull request.

## Scan a real environment with real data

A scanner can only test the paths it can reach, and many paths only do something once there is data behind them. An empty database means a shallow scan. Run your app against a seed or fixture dataset that exercises the main flows, with enough data to reach the functional paths and no more. A small, realistic seed beats a huge one, and your agent can generate checked-in seed data so the scan lands on populated paths. Start simple:

```text
Generate checked-in seed data so the scan reaches the main flows of my app.
```

If the important paths need specific records, spell them out:

```text
The main flows need a logged-in user with at least one order on file. Seed that.
```

## Avoid running active scans against production

Runtime testing is active. It sends real attack payloads, and that can create, change, or delete data in whatever it is pointed at. That is exactly what you want against a development or test environment, and something you'll want to avoid against production.

Keep your scanning on development, test, and staging environments where writing data is safe. The whole agentic model is built around scanning and fixing locally as you build, so this lines up naturally. Point the loop at your local app rather than your live one. This is where the real payoff is: finding and fixing an issue in a lower environment means you ship more secure code to production, instead of shipping the vulnerability out into the wild where someone could exploit it.

## Keep your scan config in source control

Treat `stackhawk.yml` like the rest of your code and commit it to your repository. It travels with the app, it is easy to review, and your agent can read and update it in place. Start simple:

```text
Commit my stackhawk.yml so the scan config lives with the app.
```

If you scan more than one environment, tell the agent how you want that handled:

```text
We scan dev and staging. Use environment variables for the host rather than editing one file back and forth.
```

## The short version

Scan early in development, give the scanner realistic data, avoid active scans on production, and keep your config in source control. Do that and the agentic loop stays fast, safe, and honest.

## Where to go next

[![The Agentic Loop](https://img.shields.io/badge/The%20Agentic%20Loop-Back%20to%20the%20lab-00d7d2?style=for-the-badge)](agentic-loop.md)
[![Getting the Most From Your Scans](https://img.shields.io/badge/Getting%20the%20Most%20From%20Your%20Scans-Read-00d7d2?style=for-the-badge)](depth-of-config.md)
[![What the Skills Can Do](https://img.shields.io/badge/What%20the%20Skills%20Can%20Do-Read-00d7d2?style=for-the-badge)](what-the-skills-can-do.md)

---

If you run into any issues or have questions along the way, reach out to our support team at support@stackhawk.com. We're happy to help.
