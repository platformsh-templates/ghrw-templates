# Platform.sh Reusable GitHub Workflows for Templates

A collection of reusable GitHub workflows that are used by the Platform.sh collection of templates. Inputs are received 
via [Repository Variables](https://docs.github.com/en/actions/learn-github-actions/variables#creating-configuration-variables-for-a-repository). See workflow description for possible inputs.

## [autopr.yaml](./.github/workflows/autopr.yaml) 
### Description
Creates an auto merging PR when a target branch is updated.
### Inputs
* `CW_JOBID` - *Optional*. The name of the job id in the calling workflow that calls the testprenvironments.yaml 
reusable workflow. Defaults to `TestPrEnv-CW`
* `STATUS_CHECKS` - *Optional*. Comma seperated list of status checks that must pass for the PR to be accepted. Defaults to   
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
## [post-pr-acceptance.yaml](./.github/workflows/post-pr-acceptance.yaml)
### Description
Runs all other workflows and actions that should occur after a PR has been accepted.


### Uses
* [last-updated.yaml](./.github/workflows/last-updated.yaml)
* [tb-sync.yaml](./.github/workflows/tb-sync.yaml)

### Example:
```yaml
name: Runs post merge-acceptance workflows
on:
  push:
    branches:
      - main
      - master

jobs:
  run-reusable-post-pr-acceptance:
    uses: platformsh-templates/ghrw-templates/.github/workflows/post-pr-acceptance.yaml@main
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
## [tb-sync.yaml](./.github/workflows/tb-sync.yaml)
### Description
Keeps Platform.sh configuration files in sync between a template's repository and the
[template-builder](https://github.com/platformsh/template-builder) system. Upon detection of a change to a Platform.sh
configuration file(s), commits those changes to the [template-builder](https://github.com/platformsh/template-builder)
repository for the relevant template, and creates a pull request.
### Uses
* actions/checkout@v3
* tj-actions/changed-files
* actions/checkout@v2
### Example:
```yaml
name: Track and sync tracked files
on:
  push:
    branches:
      - main
      - master

jobs:
  run-reusable-tb-sync:
    uses: platformsh-templates/ghrw-templates/.github/workflows/tb-sync.yaml@main
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
## [testprenvironments.yaml](./.github/workflows/testprenvironment.yaml)
### Description
Runs a series of tests against the Pull Request environment to ensure no regressions have been introduced. See the 
[platformsh/gha-template-pr-tests](https://github.com/platformsh/gha-template-pr-tests) action for more details.

### Inputs
* `DELAY_START` - _Optional_. Delay the start of testing for X seconds after the PR environment is available. Please 
note this is in seconds, not milliseconds. Must be a valid integer.
* `PR_ENV_TIMEOUT` - _Optional_. The number of seconds the action should wait for Platform.sh to report back with a PR 
environment URL. Please note this is in seconds, not milliseconds. Must be a valid integer

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