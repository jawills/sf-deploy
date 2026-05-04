# Salesforce Deployment GitHub Actions

Use this action to build a `package.xml` (or use your own) and deploy to a specified Salesforce environment. After deployment, it writes a **job summary** on the Actions run and exposes **outputs** for downstream steps.

## Inputs

```yml
DRY_RUN:
  description: "Validate deploy and run Apex tests but don't save to the org."
  type: boolean
TEST_LEVEL:
  description: "Deployment Apex testing level."
  type: choice
  default: "RunLocalTests"
  options:
    - NoTestRun
    - RunSpecifiedTests
    - RunLocalTests
    - RunAllTestsInOrg
WAIT:
  description: "Number of minutes to wait for command to complete and display results."
  type: number
  default: 30
SOURCE_DIRECTORY:
  description: "Path to the local source files to deploy (used only when MANIFEST_PATH is not set)."
  type: string
  default: "force-app"
MANIFEST_PATH:
  description: "If set, deploy this manifest instead of generating one from SOURCE_DIRECTORY."
  type: string
POST_DESTRUCTIVE_CHANGES:
  description: "If set, passed to sf project deploy start as --post-destructive-changes."
  type: string
SFDX_AUTH_URL:
  description: "The auth url tied to your deployment environment"
  type: string
  required: true
DEPLOYMENT_ID:
  description: "The deployment id from a validation/ dry run."
  type: string
```

## Outputs

| Output           | Description                                        |
| ---------------- | -------------------------------------------------- |
| `deployment_id`  | Salesforce deployment job id.                      |
| `deploy_status`  | Status from the CLI (e.g. `Succeeded`, `Failed`). |

## Delta deploys (e.g. SFDX-Git-Delta)

This action does not run [sfdx-git-delta](https://github.com/scolladon/sfdx-git-delta). In your workflow, install the plugin, run `sf sgd source delta`, then pass the generated paths into this action.

- Use **`MANIFEST_PATH`** for the incremental `package.xml` (for example `sgd-out/package/package.xml`).
- Set **`POST_DESTRUCTIVE_CHANGES`** when you also want post-destructive deletes in the same deploy (for example `sgd-out/destructiveChanges/destructiveChanges.xml`). Omit it when that file does not exist or you are not deleting metadata.
- For comparisons against `origin/main` (or similar), use `actions/checkout` with enough history (for example `fetch-depth: 0`) so git can resolve both refs.
- Avoid deploying an empty `package.xml`; follow [SGD’s guidance](https://github.com/scolladon/sfdx-git-delta) and branch in your workflow when there is nothing to deploy.

Example fragment (same job must have the Salesforce CLI available before SGD):

```yaml
- name: Install Salesforce CLI
  run: npm install -g @salesforce/cli

- name: Install SGD
  run: echo y | sf plugins install sfdx-git-delta

- name: Delta manifest
  run: sf sgd source delta --from "origin/main" --to "HEAD" --output-dir sgd-out --source-dir force-app

- name: Deploy
  uses: your-org/github-actions@main
  with:
    SFDX_AUTH_URL: ${{ secrets.SFDX_AUTH_URL }}
    MANIFEST_PATH: sgd-out/package/package.xml
    POST_DESTRUCTIVE_CHANGES: sgd-out/destructiveChanges/destructiveChanges.xml
```

(Adjust the action reference and wire `POST_DESTRUCTIVE_CHANGES` only when that file exists, for example with a conditional step or `if:` in your workflow.)

## Getting the SFDX Auth URL

To get the `SFDX_AUTH_URL`, enter the following command in your terminal:

```bash
sf org display --verbose --json -o <MY_TARGET_ORG_ALIAS>
```

Copy down the value of `sfdxAuthUrl` for later.

## Usage

You can see how these work by checking my [write-up](https://www.1sync.co/build-salesforce-deployment-pipeline-with-github-actions) or [YouTube video](https://youtu.be/R31DWnkiYpY).
