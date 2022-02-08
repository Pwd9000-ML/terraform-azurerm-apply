# Terraform Apply - GitHub Action (terraform-azurerm-apply)

Download a Terraform plan workflow artifact created by `Pwd9000-ML/terraform-azurerm-plan` and apply with AzureRM backend.  

See my [detailed tutorial](https://dev.to/pwd9000/multi-environment-azure-deployments-with-terraform-and-github-part-2-pdl) for more usage details.  

**NOTE:** Can be used independently with Action: **[Pwd9000-ML/terraform-azurerm-plan](https://github.com/marketplace/actions/terraform-plan-for-azure)**.  

## Installation

```yaml
steps:
  - name: Dev TF Deploy
    uses: Pwd9000-ML/terraform-azurerm-apply@v1.1.0
    with:
      plan_mode: "deploy"                      ## (Optional) Specify plan mode. Valid options are "deploy" or "destroy". Default="deploy"
      tf_version: "latest"                     ## (Optional) Specifies version of Terraform to use. e.g: 1.1.0 Default="latest"
      tf_key: "state-file-name"                ## (Required) Specifies name of the terraform state file and plan artifact to download
      az_resource_group: "resource-group-name" ## (Required) AZ backend - AZURE Resource Group hosting terraform backend storage acc 
      az_storage_acc: "storage-account-name"   ## (Required) AZ backend - AZURE terraform backend storage acc 
      az_container_name: "container-name"      ## (Required) AZ backend - AZURE storage container hosting state files 
      arm_client_id: ${{ secrets.ARM_CLIENT_ID }}             ## (Required) ARM Client ID 
      arm_client_secret: ${{ secrets.ARM_CLIENT_SECRET }}     ## (Required)ARM Client Secret
      arm_subscription_id: ${{ secrets.ARM_SUBSCRIPTION_ID }} ## (Required) ARM Subscription ID
      arm_tenant_id: ${{ secrets.ARM_TENANT_ID }}             ## (Required) ARM Tenant ID
```


## Examples

See the following [GitHub repository](https://github.com/Pwd9000-ML/Azure-Terraform-Deployments) for a full working demo of the usage of this action.

### Usage Example 1 - Deploy Plan and Apply (BUILD)

Usage example of a terraform deploy plan with applying the deploy (creating resources).

```yaml
name: "TF-Deploy"
on:
  workflow_dispatch:
  pull_request:
    branches:
      - master

jobs:
  Plan_Dev_Deploy:
    runs-on: ubuntu-latest
    environment: null #(Optional) If using GitHub Environments          
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Dev TF Plan Deploy
        uses: Pwd9000-ML/terraform-azurerm-plan@v1.1.0
        with:
          path: "path-to-TFmodule"                 ## (Optional) Specify path TF module relevant to repo root. Default="."
          plan_mode: "deploy"                      ## (Optional) Specify plan mode. Valid options are "deploy" or "destroy". Default="deploy"
          tf_version: "latest"                     ## (Optional) Specifies version of Terraform to use. e.g: 1.1.0 Default="latest"
          tf_vars_file: "tfvars-file-name"         ## (Required) Specifies Terraform TFVARS file name inside module path
          tf_key: "state-file-name"                ## (Required) AZ backend - Specifies name that will be given to terraform state file and plan artifact
          enable_TFSEC: true                       ## (Optional) Enable TFSEC IaC scans (Private repo requires GitHub enterprise). Default=false
          az_resource_group: "resource-group-name" ## (Required) AZ backend - AZURE Resource Group hosting terraform backend storage account
          az_storage_acc: "storage-account-name"   ## (Required) AZ backend - AZURE terraform backend storage account
          az_container_name: "container-name"      ## (Required) AZ backend - AZURE storage container hosting state files 
          arm_client_id: ${{ secrets.ARM_CLIENT_ID }}             ## (Required) ARM Client ID 
          arm_client_secret: ${{ secrets.ARM_CLIENT_SECRET }}     ## (Required)ARM Client Secret
          arm_subscription_id: ${{ secrets.ARM_SUBSCRIPTION_ID }} ## (Required) ARM Subscription ID
          arm_tenant_id: ${{ secrets.ARM_TENANT_ID }}             ## (Required) ARM Tenant ID
          github_token: ${{ secrets.GITHUB_TOKEN }} ## (Required) Needed to comment output on PR's. ${{ secrets.GITHUB_TOKEN }} already has permissions.

  Apply_Dev_Deploy:
    needs: Plan_Dev_Deploy
    runs-on: ubuntu-latest
    environment: Development #(Optional) If using GitHub Environments      
    steps:
      - name: Dev TF Deploy
        uses: Pwd9000-ML/terraform-azurerm-apply@v1.1.0
        with:
          plan_mode: "deploy"                      ## (Optional) Specify plan mode. Valid options are "deploy" or "destroy". Default="deploy"
          tf_version: "latest"                     ## (Optional) Specifies version of Terraform to use. e.g: 1.1.0 Default="latest"
          tf_key: "state-file-name"                ## (Required) Specifies name of the terraform state file and plan artifact to download
          az_resource_group: "resource-group-name" ## (Required) AZ backend - AZURE Resource Group hosting terraform backend storage acc 
          az_storage_acc: "storage-account-name"   ## (Required) AZ backend - AZURE terraform backend storage acc 
          az_container_name: "container-name"      ## (Required) AZ backend - AZURE storage container hosting state files 
          arm_client_id: ${{ secrets.ARM_CLIENT_ID }}             ## (Required) ARM Client ID 
          arm_client_secret: ${{ secrets.ARM_CLIENT_SECRET }}     ## (Required)ARM Client Secret
          arm_subscription_id: ${{ secrets.ARM_SUBSCRIPTION_ID }} ## (Required) ARM Subscription ID
          arm_tenant_id: ${{ secrets.ARM_TENANT_ID }}             ## (Required) ARM Tenant ID
```

## Usage Example 2 - Destroy Plan and Apply (DESTROY)

Usage example of a terraform destroy plan with applying the destroy (removing resources).

```yaml
name: "TF-Destroy"
on:
  workflow_dispatch:
  pull_request:
    branches:
      - master

jobs:
  Plan_Dev_Destroy:
    runs-on: ubuntu-latest
    environment: null #(Optional) If using GitHub Environments          
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Dev TF Plan Destroy
        uses: Pwd9000-ML/terraform-azurerm-plan@v1.1.0
        with:
          path: "path-to-TFmodule"                 ## (Optional) Specify path TF module relevant to repo root. Default="."
          plan_mode: "destroy"                     ## (Optional) Specify plan mode. Valid options are "deploy" or "destroy". Default="deploy"
          tf_version: "latest"                     ## (Optional) Specifies version of Terraform to use. e.g: 1.1.0 Default="latest"
          tf_vars_file: "tfvars-file-name"         ## (Required) Specifies Terraform TFVARS file name inside module path
          tf_key: "state-file-name"                ## (Required) AZ backend - Specifies name that will be given to terraform state file and plan artifact
          enable_TFSEC: true                       ## (Optional) Enable TFSEC IaC scans (Private repo requires GitHub enterprise). Default=false
          az_resource_group: "resource-group-name" ## (Required) AZ backend - AZURE Resource Group hosting terraform backend storage account
          az_storage_acc: "storage-account-name"   ## (Required) AZ backend - AZURE terraform backend storage account
          az_container_name: "container-name"      ## (Required) AZ backend - AZURE storage container hosting state files 
          arm_client_id: ${{ secrets.ARM_CLIENT_ID }}             ## (Required) ARM Client ID 
          arm_client_secret: ${{ secrets.ARM_CLIENT_SECRET }}     ## (Required)ARM Client Secret
          arm_subscription_id: ${{ secrets.ARM_SUBSCRIPTION_ID }} ## (Required) ARM Subscription ID
          arm_tenant_id: ${{ secrets.ARM_TENANT_ID }}             ## (Required) ARM Tenant ID
          github_token: ${{ secrets.GITHUB_TOKEN }} ## (Required) Needed to comment output on PR's. ${{ secrets.GITHUB_TOKEN }} already has permissions.

  Apply_Dev_Destroy:
    needs: Plan_Dev_Destroy
    runs-on: ubuntu-latest
    environment: Development #(Optional) If using GitHub Environments      
    steps:
      - name: Dev TF Destroy
        uses: Pwd9000-ML/terraform-azurerm-apply@v1.1.0
        with:
          plan_mode: "destroy"                     ## (Optional) Specify plan mode. Valid options are "deploy" or "destroy". Default="deploy"
          tf_version: "latest"                     ## (Optional) Specifies version of Terraform to use. e.g: 1.1.0 Default="latest"
          tf_key: "state-file-name"                ## (Required) Specifies name of the terraform state file and plan artifact to download
          az_resource_group: "resource-group-name" ## (Required) AZ backend - AZURE Resource Group hosting terraform backend storage acc 
          az_storage_acc: "storage-account-name"   ## (Required) AZ backend - AZURE terraform backend storage acc 
          az_container_name: "container-name"      ## (Required) AZ backend - AZURE storage container hosting state files 
          arm_client_id: ${{ secrets.ARM_CLIENT_ID }}             ## (Required) ARM Client ID 
          arm_client_secret: ${{ secrets.ARM_CLIENT_SECRET }}     ## (Required)ARM Client Secret
          arm_subscription_id: ${{ secrets.ARM_SUBSCRIPTION_ID }} ## (Required) ARM Subscription ID
          arm_tenant_id: ${{ secrets.ARM_TENANT_ID }}             ## (Required) ARM Tenant ID
```

In both examples the terraform plan will be created and is compressed and published to the workflow as an artifact using the same name of the input `tf_key`:  

![image.png](https://raw.githubusercontent.com/Pwd9000-ML/terraform-azurerm-plan/master/assets/artifact.png)  

The artifact will either contain a deployment plan called `deploy_plan.tfplan` if `plan_mode: "deploy"` is used, or a destroy plan called `destroy_plan.tfplan` if `plan_mode: "destroy"` is used.  

The terraform apply action will download and apply the plan inside of the artifact created by the plan action using the same `tf_key` and will start a deploy or destroy action based on the `plan_mode`.  

**NOTE:** If `enable_TFSEC` is set to `true` on plan stage, Terraform IaC will be scanned using TFSEC and results are published to the GitHub Project `Security` tab:  

![image.png](https://raw.githubusercontent.com/Pwd9000-ML/terraform-azurerm-plan/master/assets/tfsec.png)  

If using a private repository, GitHub enterprise is needed when enabling TFSEC. However if a public repository is used, code analysis is included and TFSEC can be enabled on public repositories without the need for a GitHub enterprise account.

## Inputs

| Input | Required |Description |Default |
| ----- | -------- | ---------- | ------ |
| `plan_mode` | FALSE | Specify plan mode. Valid options are `deploy` or `destroy`. | "deploy" |
| `tf_version` | FALSE | Specifies the Terraform version to use. | "latest" |
| `tf_key` | TRUE | Specifies name of the terraform state file and plan artifact to download | N/A |
| `az_resource_group` | TRUE | AZ backend - AZURE Resource Group name hosting terraform backend storage account | N/A |
| `az_storage_acc` | TRUE | AZ backend - AZURE terraform backend storage account name | N/A |
| `az_container_name` | TRUE | AZ backend - AZURE storage container hosting state files  | N/A |
| `arm_client_id` | TRUE | The Azure Service Principal Client ID | N/A |
| `arm_client_secret` | TRUE | The Azure Service Principal Secret | N/A |
| `arm_subscription_id` | TRUE | The Azure Subscription ID | N/A |
| `arm_tenant_id` | TRUE | The Azure Service Principal Tenant ID | N/A |

## Outputs

None.  

* Plan artifact is downloaded from workflow artifacts. (Plan can be created using `Pwd9000-ML/terraform-azurerm-plan` Action.)

## Versions of runner that can be used

At the moment only linux runners are supported. Support for all runner types will be released soon.

| Environment | YAML Label |
| --------------------|---------------------|
| Ubuntu 20.04 | `ubuntu-latest` or `ubuntu-20-04` |
| Ubuntu 18.04 | `ubuntu-18.04` |

## License

The associated scripts and documentation in this project are released under the [MIT License](LICENSE).
