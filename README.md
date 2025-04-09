# az-eu-data-boundary-config
EU Data Boundary Config - Tenant Setup

Setup a new EntraID tenant with EU Data Boundary config before adding Azure Subscriptions
https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/manage-data-boundary?tabs=azure-cli

Consider using the following steps to configure above: 

Step 1:  Use an existing Azure Portal to create new EntraID tenant in EU Country (EU model clause compliant)
 -  EntraID resource provder, Manage Tenants, Create-New
 -  Select a country in EU

Step 2: Create a local user in the new tenant. 
 - Assign them "global administrator"  (may be optional)
 - Find out your new user's ObjectId

Step 3: Assign them "Access Management for all subscriptions" permission 
 - Log in a new Azure Portal session with the new user
 - This may require MFA setup
 - In the EntraID resource provider blade, go to Overview, Properties
 - The set the "Access Management for all subscriptions" permission for your new local user

Step 4:  Install or setup latest Azure CLI
 - Consider installing a new VM in an EU Azure region and install latest AzureCLI to avoid issues with previous versions
 - Using PowerShell v7 may work as well - careful with pre-installed or older v5 version and MS Graph API issues

Step 5:  Configure EU Data Boundary
- Use the CMD in the Windows VM to use the following Azure CLI commands in a CMD shell in the WinVM
    - az login --allow-no-subscriptions  # with the new local user setup above and noted ObjectId
    - DATA_BOUNDARY_TENANT_ADMINISTRATOR_ROLE_ID="d1a38570-4b05-4d70-b8e4-1100bcf76d12"
    - az role assignment create --assignee-object-id "<yournewuserObjectId>" --assignee-principal-type "User" --role "d1a38570-4b05-4d70-b8e4-1100bcf76d12" --scope "/"
    - az data-boundary create --data-boundary EU --default default

The output from the last command for reference was: 
{
  "id": "/providers/Microsoft.Resources/dataBoundaries/9a46a751-f6d9-4edd-8622-eb2c633594df",
  "name": "9a46a751-f6d9-4edd-8622-eb2c633594df",
  "properties": {
    "dataBoundary": "EU",
    "provisioningState": "Succeeded"
  }
}

Step 6:  Add subscriptions

Hope that helps
