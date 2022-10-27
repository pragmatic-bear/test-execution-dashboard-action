# test-execution-dashboard-action
GitHub Action to import data for the [Test Execution Dashboard](https://github.com/pragmatic-bear/test-execution-dashboard)

## Parameters
| Parameter         | Required     | Notes |
|--------------|-----------|------------|
| source-alias | no      | Alias of the authenticated org from which to download test results. Default: use default org (must be previously set using ```sfdx force:config:set --setdefaultusername```)       |
| target-alias      | no  | Alias of the authenticated org with installed dashbaord package to which to import test results. Default: use default org (must be previously set using ```sfdx force:config:set --setdefaultusername```)        |
| test-run-id  | no | Job Id of the Test Run to download/import (AsynbApexJobId). Default: if left blank then all 'Completed' Test Runs will be processed |
| package-namespace | no | Namespace of the intstalled package (or created Objects/Fields). Will be pre-pended to the field names for import. Default: PragBear |


## Pre-requisites
### Org
- The [Test Execution Dashboard](https://github.com/pragmatic-bear/test-execution-dashboard) package must be installed (or the same objects/fields created) in the target org. The namespace does not matter.

### GitHub Workflow/Job
- Salesforce CLI must be installed
- Source SF Org must be authenticated
- Target SF Org must be authenticated (can be the same one)
- bash must be supported

## Example YAML
```
- name: 'Run Tests'
    id: run-tests-step
    run: echo "TEST-JOB-ID=$(sfdx force:apex:test:run --testlevel=RunLocalTests --resultformat=json --targetusername=test-org --wait=99 --outputdir=./tests/apex | jq -r '.result.summary.testRunId')" >> $GITHUB_OUTPUT

- name: Move Tests
    uses: pragmatic-bear/test-execution-dashboard-action@main
    with:
        source-alias: 'test-org'
        target-alias: 'test-org'
        test-run-id: '${{ steps.run-tests-step.outputs.TEST-JOB-ID }}'
        package-namespace: '${{ github.event.inputs.namespace }}'
```
