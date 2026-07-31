# What the Skills Can Do

The lab focuses on the core find-fix-verify loop, but the StackHawk skills give your agent more than that. Exactly how much any given agent can do depends on its capabilities, but once the skills are installed you can generally ask for the things below in plain language. This page is a quick tour so you know what is there when you need it.

## Report on your security posture

Beyond running a scan, your agent can pull results back from the StackHawk platform and summarize them for you. It is a read-only way to see where things stand without leaving your editor. Start simple:

```text
What's my current security posture across my apps?
```

If you want to narrow it down, add the context:

```text
Show me the findings from my latest scan and what changed since the last one.
```

The agent can fetch the data, group it by severity, and point you at what needs attention first.

## Wire scanning into your CI pipeline

Once you have a scan working locally, your agent can add it to your continuous integration pipeline so every change gets scanned automatically. The skills can detect common CI providers (GitHub Actions, GitLab, Jenkins, CircleCI, and others) and write the workflow for you. Start simple:

```text
Add a HawkScan step to my CI pipeline that scans on every pull request.
```

If it reaches for the wrong provider or file, point it at the right one:

```text
We use GitLab CI and the pipeline lives in .gitlab-ci.yml. Add the scan there.
```

The agent creates or updates the pipeline file and tells you what to set on your side, such as your API key as a CI secret.

## Seed data so scans reach more of your app

A scan can only test what the running app actually exposes, and some routes only do something when there is data behind them. If your app starts with an empty database, your agent can generate checked-in seed data so authenticated scans reach real, populated paths. Start simple:

```text
Set up seed data so the scanner can reach the pages that need existing records.
```

If it needs to know your data layer, tell it:

```text
The app uses Postgres through Prisma. Seed the data using the scripts in ./prisma.
```

This is especially useful the first time you onboard an app for scanning.

## Tune and optimize a scan

If a scan is noisier or slower than you would like, or it is not reaching enough of your app, your agent can tune the configuration: enabling the right crawler for a single-page app, wiring in an API spec, narrowing what gets scanned, and turning off checks that do not apply to your stack. Start simple:

```text
My scan is finding very few routes. Can you tune the config to reach more of the app?
```

If you already know the cause, hand it the details:

```text
It's a React single-page app talking to a REST API. Turn on the AJAX crawler and use the OpenAPI spec at ./api/openapi.yaml.
```

## Where to go next

[![The Agentic Loop](https://img.shields.io/badge/The%20Agentic%20Loop-Start%20the%20lab-00d7d2?style=for-the-badge)](agentic-loop.md)
[![Getting the Most From Your Scans](https://img.shields.io/badge/Getting%20the%20Most%20From%20Your%20Scans-Read-00d7d2?style=for-the-badge)](depth-of-config.md)
[![Best Practices](https://img.shields.io/badge/Best%20Practices-Read-00d7d2?style=for-the-badge)](best-practices.md)

---

If you run into any issues or have questions along the way, reach out to our support team at support@stackhawk.com. We're happy to help.
