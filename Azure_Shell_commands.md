# Connecting to Azure from PowerShell

1. Install Azure module for PowerShell:
Install-Module -Name Az -AllowClobber

2. Allow script execution:  
https://docs.microsoft.com/en-gb/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope LocalMachine

3. Connect PowerShell to Azure account:  
Connect-AzAccount

#..do the job

4. Disconnect PowerShell from Azure:  
Disonnect-AzAccount


#Problems with installation of Azure Modules, please follow:  
install-Module PowerShellGet -Repository PSGallery -Force
install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview -AllowPrerelease -AllowClobber -Force

----------------------------------------------------------------------------

# Getting help with the commands

Get-Help <commandlet>

Example for getting help for the Cmdlet New-AzStorage:  
Get-Help New-AzStorage

----------------------------------------------------------------------------

# Resource Group Management

#Loop example for removing all resource groups from subscription:  
$rg = Get-AzResourceGroup
foreach ($g in $rg)
>> {
>> Remove-AzResourceGroup -Name $g.ResourceGroupName -Force         
>> }


#Create new resourcegroup:  
New-AzResourceGroup -Name mihail -Location 'East US'

#remove resourcegroup:  
Remove-AzResourceGroup -Name tom

#Lock the resource group to protect against deleting (LockLevel can be also read-only):  
New-AzResourceLock -LockLevel CanNotDelete -LockName tomtom -ResourceGroupName mihail

#Remove lock:  
#Create variable for lock ID and removes it:
$dr =(Get-AzResourceLock -ResourceGroupName mihail).lockid
Remove-AzResourceLock -LockId $dr

----------------------------------------------------------------------------

# Enablling monitoring of resource 

#Create variable $sa, that contains the ID of the storage account, where logs will be kept:  
$sa = (Get-AzStorageAccount -StorageAccountName bigdatastoragezb -ResourceGroupName Az104).id

#Create variable $resource, that contais the ID of the resource we want to apply monitoring to:  
$resource =(Get-AzResource -Name frenchpress -ResourceGroupName Az104 -ResourceType Microsoft.Network/loadBalancers).id

#Enabling monitoring of the resource:  
Set-AzDiagnosticSetting -ResourceId $resource -StorageAccountId $sa -Enabled $true

----------------------------------------------------------------------------

# Resource tagging

#Tag resource group:  
Set-AzResourceGroup -name lesson29 -Tag @{ Zip='zap'; Tag1='Value1' }

Tagging all resources in resource group:  
$group =Get-AzResourceGroup -Name lesson29
Get-AzResource -ResourceGroupName $group.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.Resourceid -Tag $group.Tags -Force}

#Tag all resources from all resource groups (Resource group has to have a tag prior to fdo it):  

$group =Get-AzResourceGroup
foreach ($g in $group)
>> {
>> Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.Resourceid -Tag $g.Tags -Force }
>> }

----------------------------------------------------------------------------

# Moving resource from one Resource Group to another

#Create variable for resource:
$tomove = Get-AzResource -ResourceGroupName tomtom -ResourceName servertomove

#Move resource to another group using resourceId:
Move-AzResource -DestinationResourceGroupName mihail -ResourceId $tomove.ResourceId

----------------------------------------------------------------------------

# Policy assignment via PowerShell

#Create a variable for the resourcegroup:
$group = Get-AzResourceGroup -Name lesson29

#Create variable for PolicyDefinition, with specified policy display name:
$definition =Get-AzPolicyDefinition | Where-Object {$_.Properties.displayname -eq 'Allowed locations our policy' }

#Assign policy:
New-AzPolicyAssignment -Name locationpermited -DisplayName locationallow  -Scope $group.Resourceid -PolicyDefinition $definition 

#For specific location use comand below:
New-AzPolicyAssignment -Name locationpermited -DisplayName locationallow  -Scope $group.Resourceid -PolicyDefinition $definition  -Listofallowedlocation 'central us'

----------------------------------------------------------------------------

# Creating Storage account

New-AzStorageAccount -ResourceGroupName StorageGroup -Name tbstr01 -SkuName Standard_LRS -Location 'UK South' -Kind StorageV2

----------------------------------------------------------------------------

# Uploading a blob/file to storage container using Access Key

az storage blob upload --container-name cont1 --account-name tbstr01 --account-key <here_your_key> --name test_text.txt --file test.txt

----------------------------------------------------------------------------

# Uploading a blob/file to storage container using Shared Access Signature token

az storage blob upload --container-name cont1 --account-name tbstr01 --sas-token <"your_sas_token_in_quotas"> --name test.txt --file test.txt

----------------------------------------------------------------------------

===========================================================================

# Useful links

#Keybard typing lessons:
https://www.typing.com/student/lessons

#azure cost management:
https://docs.microsoft.com/en-gb/azure/cost-management-billing/costs/get-started-partners

#Logs ans nalytics queries:
https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/get-started-portal

#Storage sequre file transfer:
https://docs.microsoft.com/en-us/azure/storage/common/storage-require-secure-transfer

#Azure Files identity-based authentication support for SMB access:
https://docs.microsoft.com/en-us/azure/storage/files/storage-files-active-directory-overview



#General info about Azure for admins:
https://docs.microsoft.com/en-us/azure/storage/files/storage-files-active-directory-overview

