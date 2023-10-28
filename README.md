# Salesforce Deployment GitHub Actions

Use this action to build the `package.xml` and deploy to a specified Salesforce environment.

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
  description: "Path to the local source files to deploy."
  type: string
  default: "force-app"
SFDX_AUTH_URL:
  description: "The auth url tied to your deployment environment"
  type: string
  required: true
```

## Getting the SFDX Auth URL

To get the `SFDX_AUTH_URL`, enter the following command in your terminal:

```bash
sf org display --verbose --json -o <MY_TARGET_ORG_ALIAS>
```

Copy down the value of `sfdxAuthUrl` for later.

## Usage

You can see how these work by checking my [write-up](https://www.1sync.co/build-salesforce-deployment-pipeline-with-github-actions) or [YouTube video](https://youtu.be/R31DWnkiYpY).
