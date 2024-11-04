# StackHawk Developer Training: Getting Started With HawkScan - Windows Training Guide

[![en](https://img.shields.io/badge/lang-en-red.svg)](WindowsOSTrainingGuide.md)[![es](https://img.shields.io/badge/lang-es-yellow.svg)](WindowsOSTrainingGuide.es.md)

This guide will be used in tandem with the live training given by the StackHawk Team. It can also be used in a self-paced fashion to walk through the steps of getting started with HawkScan. Throughout the training, we will reference specific commands that must be entered in your terminal. Those commands are outlined below, with detailed descriptions.

## Step 1: _Installing HawkScan_
There are multiple install methods and [downloads](https://docs.stackhawk.com/download.html) of the HawkScan CLI tool. But for the purpose of this training, we will install HawkScan via our MSI Installer. Run the following command in your terminal to download and install the latest version of HawkScan.


```
msiexec.exe /i https://download.stackhawk.com/hawk/msi/hawk-4.3.0.msi /passive
```

You can check to see what version of HawkScan you currently have installed by running,

```
hawk version
```
>v4.3.0 is the latest


## Step 2: _Authenticating To StackHawk_
Now that HawkScan is installed locally, we need to authenticate with the StackHawk platform to tie your scans back to your account.

First, we need to create an API Key. Log into StackHawk through a web browser, and navigate to **Settings> API Keys> Create New API Key**. You can also click [here](https://app.stackhawk.com/settings/apikeys) to get to the API Key creation page.
> [!WARNING]
> You will only be shown your **full** API Key once. Make sure you copy it and save it somewhere for later.

Now, we need to use that API Key to authenticate to StackHawk. Run the following command in your terminal.

```
hawk init
```

After entering this command, HawkScan will prompt you to enter your API Key. Paste your new API Key, which you saved from the previous step, into the terminal and hit enter. If done correctly, you will receive an **Authenticated!** response. 

## Step 3: _Installing & Running Java Spring Vulny_
[Java Spring Vulny](https://github.com/kaakaww/javaspringvulny) is our testing application, purpose-built with vulnerabilities, and it makes a perfect target for testing HawkScan!

To get started, we need to clone the Java Spring Vulny repo to our local computer. To pull down the repository, run the following Git command in your terminal.

```
git clone git@github.com:kaakaww/javaspringvulny.git
```

Alternatively, if you are not set up to clone repositories with Git, you can download Java Spring Vulny as a Zip file [here!](https://github.com/kaakaww/javaspringvulny) It will be located in your downloads folder and needs to be moved to the home directory.

Next, we need to launch Java Spring Vulny so it's alive and running. To keep things easy, we are going to download and run a JAR version of the application. Run the following command to download the application

```
Invoke-WebRequest -Uri "https://github.com/kaakaww/javaspringvulny/releases/download/0.2.0/java-spring-vuly-0.2.0.jar" -OutFile "java-spring-vuly-0.2.0.jar"
```
Then use this command to get the application running locally on your computer.

```
Start-Process java -ArgumentList "-jar","java-spring-vuly-0.2.0.jar","--spring.profiles.active=windows"
```

If executed correctly, we should now be able to access the running Java Spring Vulny application at https://localhost:9000/
> Depending on the browser, you might receive a secure site warning and need to click through to access the application.

Click through the application and observe the different pages.

## Step 4: _Creating a StackHawk Application_

For every application that you scan with StackHawk, you will need to identify that application in the platform. Each application will have an ID associated with it, and scans of that application can be broken up by environment.  
We can create these applications in StackHawk through the CLI. Run the following command in a terminal to create your application.

```
hawk create application --name 'jsv-[USERNAME]-test' -e dev
```

> When running this command, replace [USERNAME] with your name. The StackHawk platform will use this Application name to separate your scans from other applications.
> 
> If you have multiple Orgs associated with your account. Add -o [Org ID] at the end of this command

HawkScan will respond with a **_Kaakaww!!_** to let you know the creation was successful. You will then be provided with an Application ID. Save this ID for future reference.

## Step 5: _Running HawkScan_
The moment we have been working towards. Running HawkScan against our application!
We now need to move from the home directory, into the **JavaSpringVulny** directory within your terminal.
```
cd javaspringvulny
```

This is where we want to execute HawkScan, as it's where our application-specific configuration files are located.

> [!NOTE]
> If you pulled down this repo through a Zip file. The directory will be named **javaspringvulny-main**, and you will need to run `cd javaspringvulny-main`

HawkScan uses YAML files to instruct the scanner on how to interact with your application. These files can include everything from telling the scanner where the running application is located to instructing it on authenticating with your app.

### Basic Scan

The first scan we are going to execute is the basic `stackhawk.yml` file located in the stackhawk.d subdirectory. This file includes the basic necessities required for the scanner to run.

```yaml
app:
  applicationId: 08846d92-5abd-4257-b98e-760ba8c050b9
  env: Development
  host: ${APP_HOST:https://localhost:9000}
```
- **Application ID**: This allows the scanner to know which application you are scanning. (As setup in the last step)

- **Environment**: StackHawk allows you to define an environment on each scan. This then allows you to separate your findings later in the platform by environment type.

- **Host**: This is telling the scanner where it can go out and access the application.

To kick off the scan against JavaSpringVulny using the basic configuration, insert your saved ApplicationID into the following command and run it in your terminal.

```
$env:APP_ID = "XXXXX"; hawk scan /stackhawk.d/stackhawk.yml
```

If successful, you will start to see lines in your terminal about your configuration, the scanner spidering URLs, and then, ultimately, the vulnerabilities found in the application.

### Authenticated Scan
Now that we successfully completed a basic scan of Java Spring Vulny, let's do a more thorough scan that uses authentication.
If you click through the Java Spring Vulny application at https://localhost:9000/ , you can see that there are different kinds of authentication that can be used. For the purpose of this training, we are going to use Form Authentication to get the scanner logged in and then inject a cookie to maintain the session.

To do this, we will use the `stackhawk-auth-form-cookie.yml` that is located in the stackhawk.d directory

```yaml
app:
  env: ${APP_ENV:Form Cookie}
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
      success: ".*200.*"
    loggedInIndicator: "\\QSign Out\\E"
    loggedOutIndicator: ".*Location:.*/login.*"
```
This configuration tells the scanner where and how to authenticate with the application. We tell it how to find the login forms, what username and password to inject, and even what cookie to use. You may notice that this configuration does not include the **ApplicationID**, **Environment**, or **Host**. With HawkScan, we can use multiple YAML files to instruct the scanner.

To kick off this authenticated scan, we will use the same command as last time but add the `stackhawk-auth-form-cookie.yml` to the end of the Hawk Scan command. The command you run should match the following.

```
$env:APP_ID = "XXXXX"; hawk scan stackhawk.d/stackhawk.yml stackhawk.d/stackhawk-auth-form-cookie.yml
```

Just like the first scan, you will see the scanner spidering the application and the vulnerabilities that are found.
If you log into the StackHawk site and navigate to the **Scans** section, you can take a deeper dive into the specifics of each vulnerability.

Similar to the above YAML, the JavaSpringVulny repo contains examples of multiple different forms of authentication that you can run and test against the application!

## Additional Resources
The following are some great resources from our documentation, that can be used to add on to the above training. If you end up running into any issues or have any questions for our team, please don't hesitate to reach out. You can contact us by emailing support@stackhawk.com

- [Getting Started With HawkScan](https://docs.stackhawk.com/getting-started/)

- [HawkScan Authenticated Scanning](https://docs.stackhawk.com/hawkscan/authenticated-scanning/)

- [HawkScan Best Practices](https://docs.stackhawk.com/support/best-practices.html)

- [StackHawk Integrations](https://docs.stackhawk.com/workflow-integrations/)


