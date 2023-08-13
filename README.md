# Salesforce Deployment GitHub Actions

Build the `package.xml` and deploy to Salesforce using this GitHub Action

## Inputs

```yml
DRY_RUN:
description: "Validate deploy and run Apex tests but don't save to the org."
type: boolean
TEST_LEVEL:
description: "Deployment Apex testing level."
type: choice
default: 'RunLocalTests'
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
    default: 'force-app'
SFDX_AUTH_URL:
description: "The auth url tied to your deployment environment"
type: string
required: true
```
## Getting the SFDX Auth URL


## Usage

You can see how these work by checking my [write-up]() or [YouTube video]().