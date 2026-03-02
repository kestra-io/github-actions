# Kestra GitHub Actions

Official GitHub Actions to validate and deploy Kestra [Flows](https://kestra.io/docs/workflow-components/flow) and [Namespace Files](https://kestra.io/docs/concepts/namespace-files) as part of your [CI/CD pipeline](https://kestra.io/docs/version-control-cicd/cicd/github-action).

## Available actions
- `kestra-io/github-actions/validate-flows` – validate flows in a folder before deploying.
- `kestra-io/github-actions/deploy-flows` – deploy flows (optionally force a namespace or override existing flows).
- `kestra-io/github-actions/deploy-namespace-files` – upload namespace files or folders.

## Quick start workflow
Use on `main` (or your release branch) to validate then deploy:

```yaml
name: Kestra CI/CD
on: [push]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: kestra-io/github-actions/validate-flows@main
        with:
          directory: ./kestra/flows
          server: ${{ secrets.KESTRA_HOSTNAME }}
          # apiToken: ${{ secrets.KESTRA_API_TOKEN }}   # EE optional

  deploy:
    runs-on: ubuntu-latest
    needs: validate
    steps:
      - uses: actions/checkout@v4
      - uses: kestra-io/github-actions/deploy-flows@main
        with:
          directory: ./kestra/flows
          server: ${{ secrets.KESTRA_HOSTNAME }}
          override: true

  upload-ns-files:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: kestra-io/github-actions/deploy-namespace-files@main
        with:
          localPath: ./config
          namespace: engineering
          namespacePath: config
          server: ${{ secrets.KESTRA_HOSTNAME }}
```

Store credentials as GitHub Secrets. For Enterprise Edition features, supply `apiToken` and `tenant`.

## Key inputs
**Validate flows**
- `directory` (default `./`)
- `server` (required)
- `tenant` (default `main`)
- `apiToken`, `user`, `password` (optional)

**Deploy flows**
- `directory` (default `./`)
- `namespace` (optional; keep authored flow namespaces when omitted)
- `override` (`false` by default)
- `server` (required)
- `tenant` (default `main`)
- `apiToken`, `user`, `password` (optional)

**Deploy namespace files**
- `localPath` (default `./`)
- `namespacePath` (required destination path)
- `namespace` (required)
- `override` (`false` by default)
- `server` (required)
- `tenant` (default `main`)
- `apiToken`, `user`, `password` (optional)

## Tips
- Runners must reach your Kestra server (public endpoint or self-hosted runner).
- To keep production flows immutable, add `labels: { system.readOnly: true }` to CI-managed flows.
- For offline validation without a Kestra instance, use the legacy marketplace action: `kestra-validate-action`.

## Links
- Docs: https://kestra.io/docs/how-to-guides/github-actions
- Validate action: `kestra-io/github-actions/validate-flows`
- Deploy flows: `kestra-io/github-actions/deploy-flows`
- Deploy namespace files: `kestra-io/github-actions/deploy-namespace-files`
