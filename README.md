# StepSecurity Secure Registry POC

This repository demonstrates how to set up and use the **StepSecurity Secure Package Registry** in a GitHub Actions CI/CD pipeline, for testing purposes 

## What This Does

The included workflow installs an npm package (`@actions/core`) through the StepSecurity registry instead of the public npm registry. Every package request is evaluated against your organization's security policies — including **cooldown periods**, **AI-based package analysis**, and **typosquat detection** - before the package is served.

After a successful run, you can view the **policy evaluation results** in the [StepSecurity dashboard](https://app.stepsecurity.io).

## Setup

### 1. Fork or copy this repo into your own GitHub organization
 
This is a demo repo. To try it out, you need your own copy where you can add secrets and run workflows.
 
- Click **Fork** (top-right of this page) to create a copy under your GitHub account or organization, **or**
- Create a new repo in your org and copy the `.github/workflows/npm-install.yml` file into it.

### 2. Get your API key

1. Log in to the [StepSecurity dashboard](https://app.stepsecurity.io).
2. Navigate to the **Secure Registry** section and click on **Setup Guide** tab 
3. Copy your API key

### 3. Add the secret to the repository

1. Go to **Settings → Secrets and variables → Actions** in the GitHub repo.
2. Click **New repository secret**.
3. Name: `STEPSECURITY_API_KEY`
4. Value: paste your API key from step 1.

### 4. Run the workflow

Go to the **Actions** tab → select **npm install with StepSecurity registry** → click **Run workflow**. The workflow also runs automatically on pushes and PRs to `main`

### 5. View policy evaluations

After the workflow completes, go to the StepSecurity dashboard and check the **Policy Evaluations** tab to see how the installed packages were evaluated

## How It Works

The workflow does three things:

1. **Configures npm** to use `https://registry.stepsecurity.io/javascript` as the registry, authenticated with your API key.
2. **Installs `@actions/core`** in a temporary directory to generate a real package request through the registry.
3. **Prints the dependency tree** (`npm ls --all`) so you can verify what was installed.

All packages pass through StepSecurity's security policy layers before being served:

| Policy | What it does |
|---|---|
| **Cooldown Period** | Holds newly published versions for a configurable window (e.g., 24–72 hrs) so the community and scanners can catch compromised releases first. |
| **Compromised Packages** | Blocks packages flagged as compromised or reported as malicious by StepSecurity's SOC and the broader security community |
| **Typosquat Detection** (*coming soon*)| Blocks packages that mimic popular names (e.g., `ax1os`, `axois`) to prevent dependency confusion attacks. |

## Learn More

- [StepSecurity Secure Registry docs](https://docs.stepsecurity.io)
- [StepSecurity dashboard](https://docs.stepsecurity.io/packages/secure-registry)
