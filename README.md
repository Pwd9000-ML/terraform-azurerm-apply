# Terraform Apply - GitHub Action (terraform-azurerm-apply)

GitHub Action - Download a Terraform plan workspace artifact created by `Pwd9000-ML/terraform-azurerm-plan` and apply with AzureRM backend.  

See my [detailed tutorial]() for more usage details.  

**NOTE:** This plan is a **Child**-Action of **Parent**-Action: **[Pwd9000-ML/terraform-azurerm]()**. Can be used independently as well with **Child**-Action: **[Pwd9000-ML/terraform-azurerm-plan](https://github.com/Pwd9000-ML/terraform-azurerm-plan)**.  

## Usage

```yaml
name: "TF-Deployment-01"
on:
  workflow_dispatch:
  pull_request:
    branches:
      - master
jobs:
  Plan_Dev:
    runs-on: ubuntu-latest
    environment: null #(Optional) If using GitHub Environments          
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Dev TF Plan
        uses: Pwd9000-ML/terraform-azurerm-plan@v1.0.0
        with:
          path: "path-to-TFmodule"                 ## (Optional) Specify path TF module relevant to repo root. Default="."
          az_resource_group: "resource-group-name" ## (Required) AZ backend - AZURE Resource Group hosting terraform backend storage acc 
          az_storage_acc: "storage-account-name"   ## (Required) AZ backend - AZURE terraform backend storage acc 
          az_container_name: "container-name"      ## (Required) AZ backend - AZURE storage container hosting state files 
          tf_key: "state-file-name"                ## (Required) AZ backend - Specifies name that will be given to terraform state file 
          tf_vars_file: "tfvars-file-name"         ## (Required) Specifies Terraform TFVARS file name inside module path
          enable_TFSEC: true                       ## (Optional)  Enable TFSEC IaC scans
          arm_client_id: ${{ secrets.ARM_CLIENT_ID }}             ## (Required) ARM Client ID 
          arm_client_secret: ${{ secrets.ARM_CLIENT_SECRET }}     ## (Required)ARM Client Secret
          arm_subscription_id: ${{ secrets.ARM_SUBSCRIPTION_ID }} ## (Required) ARM Subscription ID
          arm_tenant_id: ${{ secrets.ARM_TENANT_ID }}             ## (Required) ARM Tenant ID

  Apply_Dev:
    needs: Plan_Dev
    runs-on: ubuntu-latest
    environment: Development #(Optional) If using GitHub Environments      
    steps:
      - name: Dev TF Deploy
        uses: Pwd9000-ML/terraform-azurerm-apply@v1.0.0
        with:
          az_resource_group: "resource-group-name" ## (Required) AZ backend - AZURE Resource Group hosting terraform backend storage acc 
          az_storage_acc: "storage-account-name"   ## (Required) AZ backend - AZURE terraform backend storage acc 
          az_container_name: "container-name"      ## (Required) AZ backend - AZURE storage container hosting state files 
          tf_key: "state-file-name"                ## (Required) AZ backend - Specifies name that will be given to terraform state file 
          arm_client_id: ${{ secrets.ARM_CLIENT_ID }}             ## (Required) ARM Client ID 
          arm_client_secret: ${{ secrets.ARM_CLIENT_SECRET }}     ## (Required)ARM Client Secret
          arm_subscription_id: ${{ secrets.ARM_SUBSCRIPTION_ID }} ## (Required) ARM Subscription ID
          arm_tenant_id: ${{ secrets.ARM_TENANT_ID }}             ## (Required) ARM Tenant ID
```

## Inputs

| Input | Required |Description |Default |
| ----- | -------- | ---------- | ------ |
| `tf_version` | FALSE | Specifies the Terraform version to use. | "latest" |
| `az_resource_group` | TRUE | AZ backend - AZURE Resource Group name hosting terraform backend storage account | N/A |
| `az_storage_acc` | TRUE | AZ backend - AZURE terraform backend storage account name | N/A |
| `az_container_name` | TRUE | AZ backend - AZURE storage container hosting state files  | N/A |
| `tf_key` | TRUE | AZ backend - Specifies name that will be given to terraform state file | N/A |
| `arm_client_id` | TRUE | The Azure Service Principal Client ID | N/A |
| `arm_client_secret` | TRUE | The Azure Service Principal Secret | N/A |
| `arm_subscription_id` | TRUE | The Azure Subscription ID | N/A |
| `arm_tenant_id` | TRUE | The Azure Service Principal Tenant ID | N/A |

## Outputs

None.  

* Plan artifact is downloaded from workspace artifacts. (Plan can be created using `Pwd9000-ML/terraform-azurerm-plan` Action.)

## Versions of runner that can be used

At the moment only linux runners are supported. Support for all runner types will be released soon.

| Environment | YAML Label |
| --------------------|---------------------|
| Ubuntu 20.04 | `ubuntu-latest` or `ubuntu-20-04` |
| Ubuntu 18.04 | `ubuntu-18.04` |

## License

The associated scripts and documentation in this project are released under the [MIT License](LICENSE).
