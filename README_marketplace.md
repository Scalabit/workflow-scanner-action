# Workflow Security Scanner

AI-powered GitHub Action that automatically scans your workflows for security vulnerabilities and creates pull requests with fixes.

## Features

- **Deep Security Analysis** - Scans GitHub workflows using Zizmor
- **AI-Powered Fixes** - Uses advanced LLMs to generate contextual security fixes
- **Automated PRs** - Creates pull requests with detailed explanations and fixes
- **Self-Contained** - Runs completely in Docker with integrated Dagger engine
- **Binary Scanner** - Uses optimized Go binary for fast processing
- **Detailed Reports** - Comprehensive security analysis and recommendations

## Quick Start

### 1. Get Your API Token

1. Visit [remediator](https://remediator.ai/)
2. Sign in with GitHub OAuth
3. Subscribe to premium
4. Copy your API token

### 2. Add Secrets to Your Repository

Go to your repository **Settings** -> **Secrets and variables** -> **Actions** and add:

| Secret Name | Description | Required |
|-------------|-------------|----------|
| `FS_API_TOKEN` | Your premium API token from remediator | Yes |
| `LLM_API_KEY` | OpenAI, Anthropic, or Gemini API key | Yes |
| `DOCKERHUB_USERNAME` | Docker Hub username | Yes |
| `DOCKERHUB_TOKEN` | Docker Hub token | Yes |  

### 3. Create Workflow

Create `.github/workflows/security-scan.yml`:

```yaml
name: Workflow Security Scan

on:
  pull_request:
    branches: [ main ]

jobs:
  security-scan:
    if: ${{ !contains(github.event.pull_request.title, 'Security Audit & Fixes for GitHub Actions Workflows') }}
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
      issues: write
    
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        
      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
        
      - name: Run workflow scanner
        uses: Scalabit/workflow-scanner-action@main
        with:
          api-token: ${{ secrets.FS_API_TOKEN }}
          github-token: ${{ secrets.GITHUB_TOKEN }}
          llm-api-key: ${{ secrets.LLM_API_KEY }}
```

## Input Parameters

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `api-token` | API token from remediator service | Yes | - |
| `github-token` | GitHub token with repo permissions | No | `${{ github.token }}` |
| `llm-api-key` | LLM API key (OpenAI/Anthropic/Gemini) | Yes | - |
| `repository` | Repository in format `owner/repo` | No | `${{ github.repository }}` |


## Outputs

This action does not produce any outputs. It scans your workflows and creates a pull request if issues are found.

## LLM Providers

Supports OpenAI (`sk-...`), Anthropic (`sk-ant-...`), or Gemini API keys.

## Troubleshooting

- **Invalid API token**: Check secrets and subscription status
- **Permission errors**: Verify GitHub token has repo access
- **Missing secrets**: Ensure all required secrets are set

[Get started at remediator.ai](https://remediator.ai/)

#### Further info also on website
---

**Made with your security in mind by [Scalabit](https://scalabit.dev/)**
