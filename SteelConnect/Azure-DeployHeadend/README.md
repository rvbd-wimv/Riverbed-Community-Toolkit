# Cookbook - Deploy SteelConnect EX Standalone Headend in Azure

## Prerequisites

| Tasks | Description |
| --- | --- |
| 1. Connect to your Azure subscription | Sign-in on [Azure portal](https://portal.azure.com)|
| 2. Create a resource group in the location where you will deploy SteelConnect EX appliances| <ul><li>Resource Group name: **SteelConnect-EX-Images**</li><li>Location: **Target location for appliances**</li></ul> |
| 3. Create a Storage Account resource, and then create a Blob container  | <ul><li>Storage Account Name: **unique name**</li><li>Location: **Target location for appliances**</li><li>Container name: **images**</li></ul> |
| 4. Send a request to [Riverbed Support](https://support.riverbed.com/) with a SAS and connecting string to receive images in your Blob Container: FlexVNF, Director and Analytics | <ul><li>Generate a **Shared Access Signature** for your blob container</li><li>Get the **SAS** and connection string for support </li></ul>|

## Deployment

[1. Import VHD Images](#1-import-vhd-images)
[2. Deploy using Terraform template](#2-deploy-using-terraform-template)

### 1. Import VHD Images

| Tasks | Description |
| --- | --- |
| 1. In the Azure Portal, navigate to the Resource group |Go to [Azure portal](https://portal.azure.com)|
| 2. Add new resource, select Image, and hit Create new||
| 3. Fill parameters for the SteelConnect EX **FlexVNF** image| <ul><li>Name: **steelconnect-ex-flexvnf**</li><li>Location: **Target location** for appliances</li><li>OS disk type: **Linux**</li><li>Storage Blob: url of the **flexvnf vhd** in the storage account blobs container</li></ul>|
| 4. Repeat **step 2.** and fill parameters for the SteelConnect EX **Director** image| <ul><li>Name: **steelconnect-ex-director**</li><li>Location: **Target location** for appliances</li><li>OS disk type: **Linux**</li><li>Storage Blob: url of the **director vhd** in the storage account blobs container</li></ul>|
| 5. Repeat **step 2.** and fill parameters for the SteelConnect EX **Analytics** image| <ul><li>Name: **steelconnect-ex-analytics**</li><li>Location: **Target location** for appliances</li><li>OS disk type: **Linux**</li><li>Storage Blob: url of the **analytics vhd** in the storage account blobs container</li></ul>|

#### Example

When the import is done, the resource group will contain a storage account and an image resource for each appliance. In the Azure portal, it should looks like this:

![resource group](./images/steelconnect-ex-import-vhd-images-resources.png)

### 2. Deploy using Terraform template

1. Open Azure Cloud Shell and select PowerShell console

Try from Azure Portal, or [shell.azure.com](https://shell.azure.com), or by clicking [![Embed launch](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)

2. Get Riverbed Community Toolkit sources (PowerShell)

```PowerShell
# Check the Azure context (subscription and tenant id are correct)
$azureContext = Get-AzContext
$azureContext.Subscription

# Comment our the line below and fill the parameter {your-test-sub} to select a different subscription
# Select-AzSubscription -SubscriptionName "{your-test-sub}"

# Change dir
cd $user

# Copy the sources/scripts from Github
git clone https://github.com/riverbed/Riverbed-Community-Toolkit.git
```

3. Stage variables for Terraform (terraform.vartf)

```PowerShell
Set-Location ./Riverbed-Community-Toolkit
Set-Location ./SteelConnect/Azure-DeployHeadend/scripts

./SteelConnect-EX_Stage-DefaultHeadhendStandalone.ps1
```

4. Deploy Terraform (init, plan and apply)

```PowerShell
../../Azure-DeployHeadend/scripts/SteelConnect-EX_Deploy-Terraform.ps1
```

After 3 to 5 minutes the resource group in Azure should look like this:

![resource group](./images/steelconnect-ex-headend-resources.png)

and you can start accessing the appliance via Azure Serial Console or SSH.
For example, the Director via Azure Serial Console:

![resource group](./images/steelconnect-ex-director-serial-console.png)

## Copyright (c) 2020 Riverbed Technology