# TERRAFORM-APPLY-GH-ACTION

Repository containing Ohpen's Github Action to apply Terraform configuration with Ohpen standard.

- [TERRAFORM-APPLY-GH-ACTION](#TERRAFORM-APPLY-GH-ACTION)
  - [code-of-conduct](#code-of-conduct)
  - [github-action](#github-action)

## code-of-conduct

Go crazy on the pull requests :) ! The only requirements are:

> - Use _conventional-commits_.
> - Include _jira-tickets_ in your commits.
> - Create/Update the documentation of the use case you are creating, improving or fixing. **[Boy scout](https://biratkirat.medium.com/step-8-the-boy-scout-rule-robert-c-martin-uncle-bob-9ac839778385) rules apply**. That means, for example, if you fix an already existing workflow, please include the necessary documentation to help everybody. The rule of thumb is: _leave the place (just a little bit)better than when you came_.

### github-action

This action performs a [_terraform apply_](https://www.terraform.io/cli/commands/apply) on the IAC that is specified. The (required) inputs are:

- _region_: aws region name.
- _access-key_: user access key to be used.
- _secret-key_: user secret key to be used.
- _terraform-folder_: folder where the terraform configuration is.
- _backend-configuration_: path of the tfvars file with backend configuration.
- _terraform-plan-file_: terraform plan previously generated by 'plan' job.
- _terraform-outputs-file_: File where terraform will write down the outputs after applying.

⚠️ Attention! Terraform will try to assume the deployment role in the destination AWS account. Such deployment will fail if the user is not allowed to assume such role.

Here is an example:

```yaml
name: CI
on:
  pull_request:
    branches: ["main"]
jobs:
  apply-team-branch-plan:
    needs: [plan-team-branch-deployment]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/download-artifact@v2
        with:
          name: deployment-folder
          path: deployment-folder
      - uses: actions/download-artifact@v2
        with:
          name: deployment-team-branch-plan
          path: deployment-team-branch-plan
      - uses: actions/download-artifact@v2
        with:
          name: deployment-team-branch-conf
          path: deployment-team-branch-conf
      - uses: ohpensource/terraform-apply-gh-action/action@0.0.0.1
        name: terraform apply
        with:
          region: $REGION
          access-key: $COR_AWS_ACCESS_KEY_ID
          secret-key: $COR_AWS_SECRET_ACCESS_KEY
          terraform-folder: "deployment-folder/terraform"
          backend-configuration: "deployment-team-branch-conf/backend.tfvars"
          terraform-plan-file: "deployment-team-branch-plan/tfplan"
          terraform-outputs-file: "deployment-team-branch-outputs/outputs.json"
```