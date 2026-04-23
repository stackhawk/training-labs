# StackHawk SME Training: HawkScan Deep Dive

Welcome to the StackHawk SME Training lab. This session goes beyond the basics of HawkScan and into a deeper understanding of YAML configuration, authenticated scanning, API spec-driven discovery, and best practices for long-term success with the tool.

**Please complete all prerequisites below before the live session.** This allows us to skip the setup phase and spend our time on the hands-on configuration and scanning work that makes this session valuable.

---

## Pre-Session Prerequisites

### 1. Sign Into StackHawk

Log into your StackHawk account at [app.stackhawk.com](https://app.stackhawk.com). If you do not yet have access, reach out to your team lead or StackHawk contact before the session.

### 2. Verify Java

You will need Java JDK 11 or higher. Run the following to check:

```bash
java -version
```

If Java is not installed or below version 11, download and install the latest JDK from [adoptium.net](https://adoptium.net) before continuing.

### 3. Install HawkScan

If HawkScan is not already installed, expand the section for your operating system below.

<details>
<summary><strong>macOS</strong></summary>

```bash
curl -v https://download.stackhawk.com/hawk/pkg/hawk-5.5.0.pkg -o hawk-5.5.0.pkg &&\
sudo installer -pkg hawk-5.5.0.pkg -target /Applications &&\
rm hawk-5.5.0.pkg
```

</details>

<details>
<summary><strong>Windows (PowerShell)</strong></summary>

```powershell
msiexec.exe /i https://download.stackhawk.com/hawk/msi/hawk-5.5.0.msi /passive
```

</details>

Verify the installation on either platform with:

```bash
hawk version
```

> v5.5.0 is the latest release

### 4. Create a Training Directory and Download the Required Files

Create a folder for the training and download both required files into it.

<details>
<summary><strong>macOS</strong></summary>

```bash
mkdir ~/stackhawk-training && cd ~/stackhawk-training
```

```bash
curl -Ls https://github.com/kaakaww/javaspringvulny/releases/download/0.2.0/java-spring-vuly-0.2.0.jar -o ./java-spring-vuly-0.2.0.jar
```

```bash
curl -Ls https://raw.githubusercontent.com/stackhawk/training-labs/sme-training/stackhawk-training.yml -o ./stackhawk-training.yml
```

</details>

<details>
<summary><strong>Windows (PowerShell)</strong></summary>

```powershell
mkdir stackhawk-training; cd stackhawk-training
```

```powershell
Invoke-WebRequest -Uri "https://github.com/kaakaww/javaspringvulny/releases/download/0.2.0/java-spring-vuly-0.2.0.jar" -OutFile "java-spring-vuly-0.2.0.jar"
```

```powershell
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/stackhawk/training-labs/sme-training/stackhawk-training.yml" -OutFile "stackhawk-training.yml"
```

</details>

---

Once your prerequisites are complete, select the guide for your operating system below. The guide will walk you through the full training session step by step.

| Operating System | Guide |
|---|---|
| macOS | [SME-MacOSTrainingGuide.md](SME-MacOSTrainingGuide.md) |
| Windows | [SME-WindowsTrainingGuide.md](SME-WindowsTrainingGuide.md) |

---

If you run into any issues getting set up before the session, reach out to your StackHawk contact or email [support@stackhawk.com](mailto:support@stackhawk.com) — we are happy to help ahead of time.
