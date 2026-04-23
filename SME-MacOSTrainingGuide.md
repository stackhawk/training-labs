# StackHawk SME Training: HawkScan Deep Dive — MacOS Guide

This guide is used in tandem with the live StackHawk SME training session. It goes beyond the fundamentals of HawkScan and into a deeper understanding of YAML configuration, authenticated scanning, API spec-driven discovery, and best practices for long-term success with StackHawk.

> [!NOTE]
> If you have not yet completed the pre-session prerequisites, return to the [pre-session setup page](README.md) before continuing.

---

## Step 1: _Authenticate HawkScan_

With HawkScan installed, we need to wire it up to your StackHawk account. This is how the scanner knows where to send results and verifies it has permission to run.

First, generate an API Key. In the StackHawk UI, navigate to **Settings > API Keys > Create New API Key**, or visit the [API Keys settings page](https://app.stackhawk.com/settings/apikeys).

> [!WARNING]
> You will only be shown your **full** API Key once. Copy it and save it somewhere secure before closing the window.

Now use that key to authenticate HawkScan:

```bash
hawk init
```

HawkScan will prompt you to enter your API Key. Paste the key and press enter. A successful response returns **Authenticated!**

---

## Step 2: _Create Your StackHawk Application_

Every application you scan with StackHawk needs to be registered in the platform so scan results are organized and tied back to the right place. Run the following command to create one:

```bash
hawk create application --name 'jsv-[USERNAME]-test' -e dev
```

> Replace `[USERNAME]` with your name or username so your application is distinct from others in the same organization.

HawkScan will respond with a **Kaakaww!!** to confirm success and return an **Application ID**. Save this ID — it will be used throughout the session.

---

## Step 3: _Start Java Spring Vulny_

Time to get our target application running. Navigate to your training directory and run:

```bash
cd ~/stackhawk-training
java -jar ./java-spring-vuly-0.2.0.jar
```

Once started, the application is accessible at [https://localhost:9000](https://localhost:9000).

Depending on your browser, you may receive a security warning for the self-signed certificate. Click through to access the application.

> [!NOTE]
> This command runs in the foreground and will occupy your current terminal. Open a new terminal tab or window to run the remaining commands.

Spend a moment exploring the app — the login form, the different pages, and the API endpoints. This is what we will be scanning.

---

## Step 4: _YAML Deep Dive — API Spec + Authentication_

This is the heart of today's session. We are going to walk through `stackhawk-training.yml` in detail — a single file that handles authentication and API spec-driven scanning together.

HawkScan uses YAML files as its instruction manual. Every field tells the scanner something: where the app lives, how to authenticate, what endpoints to test, how to interpret responses. Let's break it down piece by piece.

Open `stackhawk-training.yml` in your editor so you can follow along. Here is the full file:

```yaml
app:
  applicationId: ${APP_ID:f5ee2290-3383-415c-96c7-ee0a398d90b9}
  env: ${APP_ENV:dev-training}
  host: ${APP_HOST:https://localhost:9000}
  excludePaths:
    - "/logout"
  antiCsrfParam: "_csrf"
  authentication:
    usernamePassword:
      type: FORM
      loginPath: /login
      loginPagePath: /login
      usernameField: username
      passwordField: password
      scanUsername: "janesmith"
      scanPassword: "password"
    cookieAuthorization:
      cookieNames:
        - "JSESSIONID"
    testPath:
      path: /search
      success: "HTTP.*200.*"
    loggedInIndicator: "\\QSign Out\\E"
    loggedOutIndicator: ".*Location:.*/login.*"
  openApiConf:
    path: /openapi
    includeAllMethods: true
    includedMethods:
      - POST
      - PUT
    customVariables:
      - field: text
        values:
          - "test value one"
          - "test value two"
      - field: searchText
        values:
          - "test search one"
          - "test search two"
```

### Breaking Down the Top-Level Fields

Three fields are required for HawkScan to execute on any scan:

- **applicationId**: The unique identifier for your application in StackHawk. The `${APP_ID:...}` syntax tells HawkScan to read this value from an environment variable named `APP_ID`, falling back to the default if not set. This pattern keeps sensitive and environment-specific values out of files committed to source control — use it everywhere.

- **env**: The environment label for this scan (e.g., `dev`, `staging`, `prod`). StackHawk stores results separately per environment, so you can track what was introduced or fixed at each stage of your pipeline.

- **host**: The base URL of the running application. Same environment variable pattern — the URL can be passed at scan time rather than hardcoded in the file.

These three are the bare minimum required for the scanner to run. Everything else is additional configuration that helps the scanner interact more accurately with your specific application.

- **excludePaths**: Paths the scanner should skip entirely. Excluding `/logout` prevents HawkScan from terminating the session it just authenticated into mid-scan.

- **antiCsrfParam**: The name of the CSRF token field in your application's forms. HawkScan reads this token from the login page and includes it automatically when submitting login requests.

### Breaking Down the Authentication Block

StackHawk supports the vast majority of authentication methods you will encounter — from form-based login to JWT, OAuth, API keys, and more. For today's session, we are using a basic username and password form login. This method is the easiest to follow along with because it mirrors exactly what happens in a browser: fill in the form, submit, get a session cookie. Regardless of which authentication method your application uses, the same two things always need to happen: the scanner needs to **log in**, and it needs to **stay logged in** throughout the entire scan.

```yaml
    usernamePassword:
      type: FORM
      loginPath: /login
      loginPagePath: /login
      usernameField: username
      passwordField: password
      scanUsername: "janesmith"
      scanPassword: "password"
    cookieAuthorization:
      cookieNames:
        - "JSESSIONID"
```

- **type: FORM**: Submits credentials by filling in and posting an HTML login form — the same way a browser would. This is the most common pattern for traditional web applications.
- **loginPath**: The URL the form posts to.
- **loginPagePath**: The URL where the login form lives. HawkScan fetches this first to read the CSRF token before submitting.
- **usernameField / passwordField**: The name attributes of the form fields.
- **scanUsername / scanPassword**: The credentials the scanner uses. In a real environment these should be dedicated test account credentials — not real user accounts.
- **cookieAuthorization**: After logging in, the application issues a session cookie. This tells HawkScan which cookie to carry on all subsequent requests to maintain the session. `JSESSIONID` is the standard Java session cookie — you can verify this in your browser's dev tools after logging in manually.

```yaml
    testPath:
      path: /search
      success: "HTTP.*200.*"
    loggedInIndicator: "\\QSign Out\\E"
    loggedOutIndicator: ".*Location:.*/login.*"
```

- **testPath**: An endpoint that is only accessible when authenticated. After logging in, HawkScan calls this path to confirm the session is actually working before it begins scanning. If the response does not match the `success` pattern, the scan halts rather than proceeding unauthenticated and producing misleading results.
- **loggedInIndicator**: A regular expression that matches something only present in an authenticated response — in this case, the literal text "Sign Out" on the page. HawkScan checks for this pattern on responses throughout the scan to confirm the session is still active.
- **loggedOutIndicator**: A regex matching a response that signals the session has ended — here, a redirect to the login page. Together, these two patterns are HawkScan's ongoing mechanism for detecting session loss and re-authenticating during a scan.

### Breaking Down the OpenAPI Block

By default, HawkScan uses a basic web spider to discover your application's endpoints. It looks at the anchor tags on the host, builds a site tree, and spiders what it can find. Depending on how your application is built — particularly if you have REST API routes, dynamic paths, or endpoints not linked from the UI — the spider alone may not find everything. Providing API documentation gives HawkScan a complete and deterministic map of your application's attack surface so nothing gets left behind.

StackHawk supports OpenAPI and Swagger specs, GraphQL schemas, gRPC definitions, HAR files, and more. Whatever your application exposes, there is likely a way to provide that structure to the scanner.

```yaml
  openApiConf:
    path: /openapi
    includeAllMethods: true
    includedMethods:
      - POST
      - PUT
```

- **path**: Points HawkScan at the running application's OpenAPI endpoint to pull the spec automatically at scan time. Alternatively, if you have the spec as a local file, you can use `filePath: path/to/openapi.yaml` instead — HawkScan supports both.
- **includeAllMethods / includedMethods**: Controls which HTTP methods are tested. Explicitly including `POST` and `PUT` ensures write operations are scanned, not just read endpoints.

```yaml
    customVariables:
      - field: text
        values:
          - "test value one"
          - "test value two"
      - field: searchText
        values:
          - "test search one"
          - "test search two"
```

- **customVariables**: Provides real example values for specific API parameters. When HawkScan crafts test requests from the spec, it substitutes these values for the matching fields. For parameters where an empty or random value would cause the request to fail before any security testing can run, providing realistic values helps the scanner actually reach the code paths it needs to test.

> [!NOTE]
> The combination of API spec-driven scanning and authentication is one of the most powerful configurations in HawkScan. The spec ensures the scanner knows every endpoint that exists. Authentication ensures it can actually reach the protected ones. Together they surface vulnerabilities that an unauthenticated spider-only scan would never find.

---

## Step 5: _Running HawkScan_

With JavaSpringVulny running and the YAML reviewed, let's execute the scan. From your training directory, run the following command and replace `XXXXX` with the Application ID you received in Step 2.

```bash
APP_ID=XXXXX hawk scan stackhawk-training.yml
```

HawkScan will read your configuration, submit the login form to authenticate, verify the session via the test path, and then begin testing endpoints guided by the OpenAPI spec.

As the scan runs you will see output in your terminal including:

- Your configuration being loaded and validated
- Authentication steps and confirmation
- URLs being discovered and tested
- Vulnerabilities found in real time

When the scan completes, HawkScan prints a summary and provides a direct link to your results in the StackHawk platform.

---

## Step 6: _Exploring the StackHawk UI_

With a scan complete, let's walk through the platform and understand how to work with your results. Log into [app.stackhawk.com](https://app.stackhawk.com) and navigate to your application.

### The Scans Page

The Scans page shows a chronological feed of all scans that have occurred for your application. For each scan you can see:

- Date, time, and duration
- Environment label
- Finding counts broken down by severity: Critical, High, Medium, Low, Info
- Scan status

Click into a scan to go deeper.

### Finding Details

Each finding within a scan includes:

- **Vulnerability name and description**: What was found, how it works, and why it matters
- **Severity**: Risk level based on the nature and exploitability of the vulnerability
- **Path**: The specific URL path where the issue was identified
- **Request / Response**: The exact HTTP request HawkScan sent and the response it received — you can inspect and replay this
- **Remediation guidance**: Practical steps to address the vulnerability

### Triage Statuses

Each finding has a triage status that reflects where it stands in your workflow. Use these to communicate progress across your team, avoid duplicate work, and track what needs attention.

### Filtering and Navigation

Use the filters on the Scans and Findings pages to sort and narrow results by severity, path, status, and more. For applications with a large number of findings, filtering is essential for prioritization.

---

## Step 7: _Best Practices_

With a solid understanding of the scan flow and configuration, here are the practices that drive long-term success with StackHawk.

#### Keep the Scanner Close to Your Application

HawkScan makes thousands of requests to your application during a scan. If there is significant network distance or infrastructure between the scanner and the application — firewalls, load balancers, rate limiters, cross-region hops — those requests have to travel farther and wait longer for responses. This degrades scan performance and can introduce false positives, because the scanner ends up assessing the infrastructure in front of your application rather than the application itself. Where possible, run the scanner and the application in the same environment or the same network segment. Pre-production environments that lack production-grade security tooling in front of them are ideal: you get a cleaner signal on the actual application, and the scan runs faster.

#### Use Environment Variables for Sensitive Values

Never hardcode API keys, passwords, or environment-specific values in YAML files committed to source control. The `${VARIABLE_NAME:default}` pattern throughout today's YAML is the right approach — pass sensitive values at scan time via environment variables or your CI/CD secret management.

#### Separate Your Environments

Run scans across environments using distinct `env` labels in your YAML (`dev`, `staging`, `prod`). StackHawk maintains separate finding histories per environment, allowing you to track what is fixed, what regresses, and what is newly introduced at each stage of your pipeline.

#### Invest in Configuration Upfront

Before thinking about automation or integrations, put real time into your YAML configuration. Authentication, API spec coverage, excluded paths, custom variables — getting these right is what determines whether you can trust your scan results. A scan that is not properly authenticated, or that is missing half your application's endpoints, will give you an incomplete picture. The work done upfront to configure the scanner accurately against your application is what makes it possible to eventually automate scanning with confidence. Once the configuration is dialed in, the rinse-and-repeat from there gets much easier.

#### Use Technology Flags and Custom Scan Policies

StackHawk runs technology-specific tests for databases, operating systems, frameworks, and more. By default, many of these are enabled — but if your application does not use a given technology, those tests are just noise. They waste scan time and can generate false positives by testing for vulnerabilities that are irrelevant to your stack. In your application settings, the Technology Flags section lets you check and uncheck the technologies that are actually in use. It is one of the highest-value tuning steps you can take.

Beyond technology flags, StackHawk also supports custom scan policies — configurable collections of tests that control exactly what gets run. You can create policies at the application level or org-wide, enabling tests that matter most for your team's security priorities and disabling ones that do not apply. Together, technology flags and scan policies give you a more efficient scan, cleaner results, and greater confidence that what comes back is actually relevant to your application.

---

## Step 8: _Troubleshooting & Resources_

When things do not behave as expected, HawkScan's built-in logging options make diagnosing issues much faster.

### Logging Flags

Add these flags to your `hawk scan` command for additional visibility:

**Verbose output** — Enables foreground logging so scan activity is printed to the console in real time:

```bash
hawk scan --verbose stackhawk-training.yml
```

**Debug logging** — Maximum log detail for deep troubleshooting:

```bash
hawk scan --debug stackhawk-training.yml
```

**HTTP traffic logging** — Logs the actual HTTP requests and responses between HawkScan and your target:

```bash
hawk scan --log-http stackhawk-training.yml
```

> [!WARNING]
> These flags, especially `--log-http`, can significantly increase the size and volume of logs. HTTP logging records every request and response made during the scan, which can be extensive on larger applications. This additional processing can also slow the scan down. Use these flags for targeted troubleshooting sessions rather than leaving them on by default.

**Running HawkScan in a container?** If you are running HawkScan via Docker rather than the CLI, pass the equivalent environment variables instead of using the flags above:

```bash
docker run -e DEBUG=true -e VERBOSE=true -v $(pwd):/hawk:rw stackhawk/hawkscan
```

For GitHub Actions (or other CI wrappers around the image), set them in the `env` block of your scan step:

```yaml
env:
  DEBUG: true
  VERBOSE: true
```

In addition to what is printed to the console, HawkScan writes a log file to disk when running. The console output is intentionally aggregated — the log file contains the full detail. The log is written to:

```
~/.hawk/sessions/<scan-id>/hawkscan.log
```

The scan ID is printed at the start of every scan, so you can find the exact directory easily. This file is especially useful when combined with `--log-http` to review the full request/response stream after a scan completes.

### Common Issues and First Steps

#### Scanner Cannot Reach the Application

Verify the `host` value in your YAML matches where the application is actually running. Confirm the app is started before the scan begins.

#### Authentication Failures

Use `--log-http` to inspect the authentication requests and responses. Check that `loginPath`, field names, and credentials are correct. Beyond the login itself, verify that all three of your authentication verification settings are accurate:

- **testPath**: Confirm the path is one that actually requires authentication and returns the expected status code when the session is valid. If this check fails, the scan will halt before it starts.
- **loggedInIndicator**: Confirm the regex matches a string that is genuinely present in an authenticated response and absent from an unauthenticated one. Test it against a real response from the application.
- **loggedOutIndicator**: Confirm the regex matches a response that signals the session has ended — typically a redirect to the login page. A misaligned pattern here means HawkScan cannot detect session loss mid-scan and may proceed unauthenticated without realizing it.

#### Network or SSL Errors (VPN, Zscaler, or Corporate Proxy)

If your organization uses a VPN, Zscaler, or any corporate proxy that performs SSL inspection, HawkScan may fail with certificate validation errors like `PKIX path building failed` or `unable to find valid certification path to requested target`. HawkScan is a Java application, so the intercepting proxy's root CA needs to be available to the JVM trust store.

In most cases, your IT team has already deployed the corporate root CA into your machine's system keystore. If that is true, the fix is one flag:

```bash
hawk scan --use-native-truststore stackhawk-training.yml
```

Or as an environment variable:

```bash
export USE_NATIVE_TRUSTSTORE=true
hawk scan stackhawk-training.yml
```

This works with macOS Keychain, the Windows Certificate Store, and Linux system CA bundles.

If that does not resolve it, or you are running via Docker (where the container cannot see your host keystore), reach out to [support@stackhawk.com](mailto:support@stackhawk.com) and we will walk you through mounting a truststore file into the scan. Running the scan off VPN, where permitted, is also a quick way to confirm the proxy is actually the cause.

#### API Key Errors

Confirm your key is valid and that you ran `hawk init` successfully. You can re-run `hawk init` at any time with a new key.

#### Java Issues

Confirm Java JDK 11 or higher is installed and active. Run `java -version` to check.

### Resources

- [StackHawk Documentation](https://docs.stackhawk.com) — Complete coverage of every feature, configuration option, and integration
- [Getting Started With HawkScan](https://docs.stackhawk.com/getting-started/)
- [HawkScan Authenticated Scanning](https://docs.stackhawk.com/hawkscan/authenticated-scanning/)
- [HawkScan Best Practices](https://docs.stackhawk.com/support/best-practices.html)
- [StackHawk Integrations](https://docs.stackhawk.com/workflow-integrations/)
- **Chat Support** — Visit [stackhawk.com](https://stackhawk.com) and use the chat widget to connect with StackHawk's support bot. It handles most troubleshooting questions and configuration help, and if you need to reach a human, that option is always available.
- **Support** — [support@stackhawk.com](mailto:support@stackhawk.com) — reach out any time with technical questions, configuration help, or issues you cannot resolve on your own.

### Additional Exploration

The [JavaSpringVulny repository](https://github.com/kaakaww/javaspringvulny) contains a full library of example YAML configurations in its `stackhawk.d/` directory, covering form auth, cookie auth, JWT, external tokens, custom spiders, and more. It is a great resource for experimenting with different authentication patterns and scan configurations beyond what we covered today.
