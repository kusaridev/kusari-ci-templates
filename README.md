# Kusari CI/CD Templates

Reusable CI/CD templates for integrating [Kusari Inspector](https://www.kusari.dev/inspector) security scanning into your GitLab and GitHub workflows. These templates automatically scan pull requests and merge requests for security vulnerabilities and post results as comments.

## Features

- **Automated Security Scanning**: Runs on every pull/merge request
- **Auto-commenting**: Posts scan results directly to PRs/MRs when issues are found
- **SARIF Output**: Generates industry-standard SARIF reports
- **Configurable**: Control failure behavior and comment posting
- **Zero Setup**: Just add secrets and include the template

## Available Templates

### GitLab CI/CD Template

Location: [`gitlab/kusari-scan-v1.yml`](gitlab/kusari-scan-v1.yml)

**Usage:**

```yaml
# .gitlab-ci.yml
include:
  - remote: 'https://raw.githubusercontent.com/kusaridev/kusari-ci-templates/v1/gitlab/kusari-scan-v1.yml'
```

**Required Setup:**
1. Add CI/CD variables in GitLab (Settings > CI/CD > Variables):
   - `KUSARI_CLIENT_ID`: Your Kusari client ID
   - `KUSARI_CLIENT_SECRET`: Your Kusari client secret (mark as masked)

2. For MR comments, choose one option:
   - **Option A (Recommended)**: Create a Project Access Token with `api` scope and add as `GITLAB_TOKEN` variable
   - **Option B**: Enable CI job token API access in Settings > CI/CD > Token Access

See [`gitlab/kusari-scan-v1.yml`](gitlab/kusari-scan-v1.yml) for detailed configuration options.

---

### GitHub Actions Reusable Workflow

Location: [`.github/workflows/kusari-scan-v1.yml`](.github/workflows/kusari-scan-v1.yml)

**Usage:**

Create `.github/workflows/kusari-scan.yml` in your repository:

```yaml
name: Kusari Security Scan

on:
  pull_request:
    branches: [main, master]

jobs:
  kusari-scan:
    uses: kusaridev/kusari-ci-templates/.github/workflows/kusari-scan-v1.yml@v1
    permissions:
      contents: read
      pull-requests: write  # Required for PR comments
    secrets:
      KUSARI_CLIENT_ID: ${{ secrets.KUSARI_CLIENT_ID }}
      KUSARI_CLIENT_SECRET: ${{ secrets.KUSARI_CLIENT_SECRET }}
    with:
      fail_on_issues: false  # Optional: fail workflow on security issues
      post_comment: true     # Optional: post results as PR comment
```

**Required Setup:**
1. Add repository secrets (Settings > Secrets and variables > Actions):
   - `KUSARI_CLIENT_ID`: Your Kusari client ID
   - `KUSARI_CLIENT_SECRET`: Your Kusari client secret

2. Ensure the workflow has `pull-requests: write` permission for commenting

See [`.github/workflows/kusari-scan-v1.yml`](.github/workflows/kusari-scan-v1.yml) for detailed configuration options.

## Configuration Options

| Option | GitLab | GitHub | Description | Default |
|--------|--------|--------|-------------|---------|
| Fail on issues | `KUSARI_FAIL_ON_ISSUES` | `fail_on_issues` | Fail pipeline/workflow when security issues found | `false` |
| Post comment | `KUSARI_POST_COMMENT` | `post_comment` | Post scan results as MR/PR comment | `true` |
| CLI image | `KUSARI_CLI_IMAGE` | `kusari_cli_image` | Override default Kusari CLI container image | Latest stable |

## Getting Kusari Credentials

To use these templates, you need Kusari Inspector credentials:

1. Sign up at [https://us.kusari.cloud/signup](https://us.kusari.cloud/signup)
1. Go to API Keys and Create a New Key with all inspector permisions (inspector_bundle_scan, inspector_result_user_read, and inspector_result_workspace_read)
1. Copy the ID and secret into your `KUSARI_CLIENT_ID` and `KUSARI_CLIENT_SECRET` respectively
1. Add them to your CI/CD platform's secrets/variables

## Enterprise / Self-Hosted Setup

For air-gapped or self-hosted environments:

1. Mirror `ghcr.io/kusaridev/kusari-cli` to your internal container registry
2. Copy the template files to your internal Git repository
3. Update the `KUSARI_CLI_IMAGE` / `kusari_cli_image` to point to your internal registry
4. Include/reference from your internal location

## Support

- Documentation: [https://docs.us.kusari.cloud/Inspector/](https://docs.us.kusari.cloud/Inspector/)
- Website: [https://www.kusari.dev/inspector](https://www.kusari.dev/inspector)

## Version Tagging

This repository uses major version tags for easy updates:
- Use `@v1` to always get the latest v1.x.x release
- Use `@v1.0.1` to pin to a specific version
