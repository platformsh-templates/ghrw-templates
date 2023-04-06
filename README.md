# Platform.sh Reusable GitHub Workflows for Templates

A collection of reusable GitHub workflows that are used by the Platform.sh collection of templates. Inputs are received 
via Repository Variables. See workflow description for possible inputs.

## [autopr.yaml](./.github/workflows/autopr.yaml) 
### Description
Creates an auto merging PR when a target branch is updated.
### Inputs
* `status-checks` - *Optional*. Comma seperated list of status checks that must pass for the PR to be accepted. Defaults to   
`TestPrEnv-CW / TestPrEnvironment`. Please note that if the required status check is contained in a reusable workflow, the naming convention is `<calling-workflow-job-id> / <reusable-workflow-job-id>`. 

### Uses
* [platformsh/gha-prep-for-autopr](https://github.com/platformsh/gha-prep-for-autopr)
* [platformsh/gha-create-autopr](https://github.com/platformsh/gha-create-autopr)

### Example:
```yaml
name: Trigger Auto PR on push to update branch
on:
  push:
    branches:
      - update
jobs:
  run-reusable-autopr:
    uses: platformsh-templates/ghrw-templates/.github/workflows/autopr.yaml@main
    secrets: inherit
```

***
## [last-updated.yaml](./.github/workflows/last-updated.yaml)
### Description
Updates the ./.platform/last.updated file with the current date
### Uses
* actions/checkout
### Example:
```yaml
name: Update last.updated
on:
  push:
    branches:
      - main
      - master
jobs:
  run-reusable-last-update:
    uses: platformsh-templates/ghrw-templates/.github/workflows/last-updated.yaml@main
    secrets: inherit
```

***
## [sourceops.yaml](./.github/workflows/sourceops.yaml)
### Description
Runs the Source Operations Toolkit
### Uses
* [platformsh/gha-run-sourceops-update](https://github.com/platformsh/gha-run-sourceops-update)
### Example:
```yaml
name: Trigger Source Operations on a Schedule
on:
  schedule:
    # Run at 00:15 after every 19th hour
    - cron: '15 */19 * * *'
  workflow_dispatch:

jobs:
  run-reusable-sop-update:
    uses: platformsh-templates/ghrw-templates/.github/workflows/sourceops.yaml@main
    secrets: inherit
```

***
## [tb-sync.yaml](./.github/workflows/tb-sync.yaml)
### Description
### Uses
* actions/checkout@v3
* tj-actions/changed-files
* actions/checkout@v2


***
## [testprenvironments.yaml](./.github/workflows/testprenvironment.yaml)
### Description
Runs a series of tests against the Pull Request environment to ensure no regressions have been introduced. See the 
[platformsh/gha-template-pr-tests](https://github.com/platformsh/gha-template-pr-tests) action for more details.

### Inputs
* `delay-start` - _Optional_. Delay the start of testing for X seconds after the PR environment is available. Please 
note this is in seconds, not milliseconds. Must be a valid integer.

### Uses
* [platformsh/gha-retrieve-projectid](https://github.com/platformsh/gha-retrieve-projectid)
* [platformsh/gha-retrieve-production-url](https://github.com/platformsh/gha-retrieve-production-url)
* [platformsh/gha-template-pr-tests](https://github.com/platformsh/gha-template-pr-tests)
### Example:
```yaml
name: "TestPullRequestEnv"
run-name: "TestPullRequestEnv"
on:
  workflow_run:
    workflows: [ "platformsh" ]
    types:
      - completed
  pull_request:
    branches:
      - master
      - main
jobs:
  TestPrEnv-CW:
    uses: platformsh-templates/ghrw-templates/.github/workflows/testprenvironment.yaml@main
    secrets: inherit
```