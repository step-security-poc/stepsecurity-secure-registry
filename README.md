# StepSecurity Secure Registry POC

This repository demonstrates how to set up and use the **StepSecurity Secure Package Registry** in a GitHub Actions CI/CD pipeline, for testing purposes

## What This Does

This repo contains two demo workflows that show Secure Registry in action:

1. **`npm-install.yml`** — Installs an npm package (`@actions/core`) through the StepSecurity registry, so you can see the policy evaluation in the dashboard for a single package. You can start here to see how Secure Registry works for a regular package install. 
2. **`compromised-package-demo.yml`** — Installs a clean version of a test package, then attempts to install a known-compromised version. The compromised version is blocked at install time. For more details, see the [Demo Details here](https://github.com/step-security-poc/stepsecurity-secure-registry/edit/main/README.md#demo-compromised-package-blocking) *this uses a dummy compromised package - this is not a real compromised package*

After each run, you can view the **policy evaluation results** in the [StepSecurity dashboard](https://app.stepsecurity.io).

## Setup

### 1. Fork or copy this repo into your own GitHub organization

This is a demo repo. To try it out, you need your own copy where you can add secrets and run workflows.

- Click **Fork** (top-right of this page) to create a copy under your GitHub account or organization, **or**
- Create a new repo (or an existing repo) in your org and simply copy the `.github/workflows/` files into it.

> **Important:** All of the steps below happen in **your fork / your copy** of this repo - not in this original repo.

### 2. Get your API key

1. Log in to the [StepSecurity dashboard](https://app.stepsecurity.io).
2. Navigate to the **Secure Registry** section and click on **Setup Guide** tab
3. Copy your API key

### 3. Add the secret to your forked repo

1. In **your fork**, go to **Settings → Secrets and variables → Actions**.
2. Click **New repository secret**.
3. Name: `STEPSECURITY_API_KEY`
4. Value: paste your API key from step 2.

### 4. Run the workflows

In **your fork**, go to the **Actions** tab and choose which demo to run:

- **npm install with StepSecurity registry** — basic registry integration demo (also runs automatically on pushes/PRs to `main`)
- **Demo: Compromised package blocked by Secure Registry** — demonstrates a compromised package being blocked (manual trigger only)

### 5. View policy evaluations

After a workflow completes, go to the [StepSecurity dashboard](https://app.stepsecurity.io) and check the **Policy Evaluations** tab to see how each package request was evaluated.

---

## How It Works

The workflows configure npm to use `https://registry.stepsecurity.io/javascript` as the registry, authenticated with your API key. Every `npm install` request flows through Secure Registry, which evaluates it against the controls you have enabled before returning a response.

All packages pass through StepSecurity's security policy layers before being served:

| Policy | Status | What it does |
|---|---|---|
| **Cooldown Period** | Available | Blocks packages published within a configurable number of days (default: 10), giving the community and scanners time to catch compromised releases first. |
| **Compromised Packages** | Available | Blocks packages flagged as compromised or reported as malicious by StepSecurity's SOC and the broader security community. |
| **Typosquat Detection** | Coming soon | Blocks packages that mimic popular names (e.g., `ax1os`, `axois`) to prevent dependency confusion attacks. |

---

## Demo: Compromised Package Blocking

The `compromised-package-demo.yml` workflow demonstrates Secure Registry blocking a known-compromised npm package at install time.

### What it does

The workflow installs two versions of the same test package through the StepSecurity registry:

| Package | Version | Result |
|---|---|---|
| `@harden-runner-canary/basic-npm-package` | `1.4.0` | ✅ Allowed — clean version, installs normally |
| `@harden-runner-canary/basic-npm-package` | `1.5.0` | ❌ Blocked — flagged as compromised, `npm install` fails with `ETARGET` |

When Secure Registry blocks a compromised version, it strips that version from the package metadata entirely. From npm's perspective, the version simply doesn't exist — so the install fails before any code is downloaded or executed.

> **Note:** The "Install compromised version" step is _expected_ to show a failure in the workflow run. That's the security control working as intended. *This uses a dummy compromised package - this is not a real compromised package*

---

## Learn More

- [StepSecurity Secure Registry docs](https://docs.stepsecurity.io/packages/secure-registry)
- [StepSecurity dashboard](https://app.stepsecurity.io)
