# Getting the Most From Your Scans

The agentic loop is only as good as what the scanner can actually reach. A scan that never gets past a login page, or never learns your API's routes, will come back quiet and give you a false sense of safety. The depth of your configuration decides how much a scan really finds. This page covers the three things that move that needle the most.

Everything here applies whether your agent drives the scan or you run it yourself. When your agent sets things up, these are the details it is working out on your behalf, and understanding them helps you steer it.

## Give the scanner your API definition

If your app is an API, the single most valuable thing you can provide is its definition, such as an OpenAPI or Swagger spec, a GraphQL schema, or a gRPC proto. The scanner uses it to map every route and exercise each one. Without a definition, the scanner relies on crawling alone and may miss endpoints, especially ones that are not linked from any page.

If a spec is checked into your source, the agent usually finds it and wires it in for you. Start simple:

```text
Configure the scan to use my API definition so it covers every route.
```

If it does not find the spec on its own, point it at the file:

```text
The OpenAPI spec lives at ./api/openapi.yaml. Use it to configure the scan.
```

Keeping a spec checked into the source is the easiest way to get a thorough scan. If you don't have one, that is worth fixing, both for scanning and for everyone who consumes your API.

## Make sure the scan is authenticated

Most applications keep their sensitive data and most powerful actions behind a login. If the scanner is not signed in, everything behind that login goes untested, and those blind spots are exactly where serious issues tend to hide.

An authenticated scan needs a few pieces of information:

* How the app authenticates, such as a form login, a token, or an OAuth flow
* How the session is carried, such as a cookie or a bearer token
* A path the scanner can request to confirm it is still signed in
* A signal that shows whether the session is active, such as a "Sign Out" link

Your agent can set most of this up from your source and a little guidance from you, as you saw in the lab. Start simple:

```text
Scan my app and make sure the scan is authenticated so it reaches the pages behind the login.
```

If it misses your login, describe how your app signs in:

```text
My app uses a form login at /login. Configure the scan to sign in and test behind it.
```

The takeaway is simple. An unauthenticated scan is only testing the front door. Get the scanner signed in and it can test the whole building.

## Reach the backend of single-page apps

Many modern front ends are single-page apps built with frameworks like React, Vue, or Angular. The pages are drawn by JavaScript in the browser, so a plain crawl of the HTML finds very little. Two things help here.

First, for a JavaScript front end, the scanner can use an AJAX crawler that runs a real headless browser to click through the app and discover the routes that only appear after the page renders.

Second, and more important, the highest-value target is usually the backend API, not the front end. A front-end-only scan mostly surfaces header and configuration issues. The injection, authorization, and business-logic problems live in the API behind it. If your app is a front end talking to a separate backend, point the scanner at that backend, ideally with its API definition, and you will get far more out of every scan. Start simple:

```text
My app is a single-page app. Configure the scan to reach the routes that only appear after it renders.
```

If you know the setup, hand the agent the details:

```text
It's a React front end calling a REST API at http://localhost:4000. Point the scan at that backend and use its OpenAPI spec.
```

## The short version

The scanner can only report on what it can reach. Give it your API definition, get it authenticated, and make sure it can reach your backend. Do those three things and the agentic loop has real depth to work with. And remember it is still an AI agent. If it misses your spec or your auth, a sentence of context is usually all it takes to get it there.

## Where to go next

[![The Agentic Loop](https://img.shields.io/badge/The%20Agentic%20Loop-Back%20to%20the%20lab-00d7d2?style=for-the-badge)](agentic-loop.md)
[![What the Skills Can Do](https://img.shields.io/badge/What%20the%20Skills%20Can%20Do-Read-00d7d2?style=for-the-badge)](what-the-skills-can-do.md)
[![Best Practices](https://img.shields.io/badge/Best%20Practices-Read-00d7d2?style=for-the-badge)](best-practices.md)

---

If you run into any issues or have questions along the way, reach out to our support team at support@stackhawk.com. We're happy to help.
