# Microsoft AZURE by Morten la Cour

> Starting migration to the **Azure PowerShell Az module**

## Table of Content
+ [PowerShell](#powershell)
1. [Install PowerShell](#install-powershell)
2. [Starting PowerShell](#starting-powershell)
3. [Changing Subscription](#changing-subscription)
4. [Exploring PowerShell](#exploring-powershell)
+ [Resource Groups](#resource-groups)
1. [Create Resource Group](#create-resource-group)
2. [Delete Resource Group](#delete-resource-group)
+ [Service Bus](#service-bus)
1. [List all Queues](#list-all-queues)
2. [List all Topics](#list-all-topics)
3. [Creating a subscription](#creating-a-subscription)
4. [All from scratch](#all-from-scratch)
+ [Storage](#storage)
1. [List all Storage Accounts](#list-all-storage-accounts)
2. [Storage Account Details](#storage-account-details)
3. [Create Storage Account](#create-storage-account)
4. [Create Blob Container](#create-blob-container)
5. [Upload File to Blob Container](#upload-file-to-blob-container)
6. [List Blobs in Container](#list-blobs-in-container)
7. [Download Blob](#download-blob)
+ [Logic Apps](#logic-apps)
1. [List all Logic Apps](#list-all-logic-apps)
2. [Enable a Logic App](#enable-a-logic-app)
+ [API Management](#api-management)
1. [List API'S](#list-apis)
+ [Event Grid](#event-grid)
1. [Create Standard Subscription](#create-standard-subscription)
# PowerShell

## Install Powershell

>Run *Powershell* as Adminstrator


> Make sure you are running at least *Powershell* version 5.0. Check this by running the following command

> When using *AZ* instead of *AzureRM* *Powershell* version 6.* is recommended. Also .NET Framework 4.7.2 is required.

Check .NET Framework version

```powershell
Get-ChildItem 'HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full\'

```
The **Release** number should be at least *461808*

Check Powershell version

```powershell

$PSVersionTable.PSVersion

```

Now install the *AZ Powershell Module* say *Yes* or *Yes to All* when prompted

```powershell
Install-Module -Name Az -AllowClobber
```


[Back to top](#table-of-content)

## Starting Powershell

> MIGHT BE OBSOLETE USING AZ??

This step needs to be done every time *PowerShell* is started. The import part, however, can be automated by setting up a *PowerShell Profile*

```powershell
Import-Module AZ
Connect-AzAccount
```
You might need to set the following in order to be able to run **Import-Module**
```powershell
Set-ExecutionPolicy RemoteSigned
```

[Back to top](#table-of-content)

## Changing Subscription
If more than one subscription is attached to your *Azure Account* you might need to change the subscription you are pointing at.

### Check current subscription

```powershell
Get-AzContext
```
### List all subscriptions
```powershell
Get-AzSubscription
```
If you need to change the subscription, get the **Id** from the list of subscriptions and use the following command

```powershell
Select-AzSubscription -SubscriptionId [Id]
```
[Back to top](#table-of-content)


## Exploring PowerShell


### Get a list of all available modules (Sort by version)

```powershell
Get-Module Az.* -ListAvailable | Sort-Object Version
```

### Get a list of commands
In this example all commands in the module *AzureRM.ServiceBus* is retrieved

```powershell
Get-Command -Module Az.ServiceBus
```


[Back to top](#table-of-content)

# Resource Groups

### List all Resource Groups

```powershell
Get-AzResourceGroup | Select-Object ResourceGroupName, Location
```
### List all Resources in a Resource Group

```powershell
get-azresource -ResourceGroupName $resourcegroup
```

## Create Resource Group

```powershell
New-AzResourceGroup -Name $resourcegroup -Location $location
```

## Delete Resource Group

```powershell
Remove-AzResourceGroup -Name $resourcegroup -Force
```

[Back to top](#table-of-content)

# Service Bus

## List all Queues

```powershell
Get-AzureRmServiceBusNamespace -ResourceGroupName [resourceGroup] -Name [namespace] | select ResourceGroup, @{Name="Namespace";Expression={$_."Name"}} | Get-AzureRmServiceBusQueue | select Name
```
Note that piping in PowerShell can pass parameters through pipes. However in this case the namespace's *Name* needs to be mapped to *Namespace* in order to be passed to the *Get-Queue* Cmdlet. 

The example above is only to show how to map parameters, the same can be accomplished by this:

```powershell
Get-AzureRmServiceBusQueue -ResourceGroupName [resourceGroup] -Namespace [namespace]
```
[Back to top](#table-of-content)

## List all Topics

```powershell

$rg = "MY_RESOURCE_GROUP"
$ns = "SERVICE_BUS_NAMESPACE"
$topics = Get-AzureRmServiceBusTopic -ResourceGroupName $rg -Namespace $ns
foreach($topic in $topics)
{
    Write-Host $topic.Name
    $subs = Get-AzureRmServiceBusSubscription -ResourceGroupName $rg -Namespace $ns -Topic $topic.Name
    foreach($sub in $subs)
    {
        Write-Host "`tSub:"  $sub.Name
        $filters = Get-AzureRmServiceBusRule -ResourceGroupName $rg -Namespace $ns -Topic $topic.Name -Subscription $sub.Name
        foreach($filter in $filters) 
        {
            Write-Host "`t`tFilter:" $filter.SqlFilter.SqlExpression
        }
    }
}

```

[Back to top](#table-of-content)

## Creating a Subscription

```powershell
$rg = "MY_RESOURCE_GROUP"
$ns = "SERVICE_BUS_NAMESPACE"
$topic = "TOPIC_NAME"
$sub = "NAME_OF_NEW_SUBSCRIPTION"
$ruleName = "NAME_OF_NEW_RULE"
$filter = "(receiver = 'thereceiver')"

New-AzureRmServiceBusSubscription -ResourceGroupName $rg -Namespace $ns -Topic $topic -SubscriptionName $sub
New-AzureRmServiceBusRule -ResourceGroupName $rg -Namespace $ns -Topic $topic -Subscription $sub -SqlExpression $filter  -Name $ruleName


```

[Back to top](#table-of-content)

## All from scratch

Check to see if the SB-Namespace is available
```powershell
Test-AzureRmServiceBusName -Namespace stupidnamespace
```
If the result **NameAvailable** is True, you can grab the globally unique namespace

Create the namespace

```powershell
New-AzureRmServiceBusNamespace -ResourceGroupName [ResourceGroupName] -Name stupidnamespace -Location "West Europe"
```

Retrieve the connection-string
> When created the namespace will only hold one Auth-Rule (RootManageSharedAccessKey) if others are created, all Auth-Rules can be listed here

```powershell
Get-AzureRmServiceBusAuthorizationRule -ResourceGroupName [ResourceGroupName] -Namespace stupidnamespace
```

Now get the connection-string from the rule (Replace *RootManageSharedAccessKey* if you wish to use another rule)

```powershell
Get-AzureRmServiceBusKey -ResourceGroupName MLC_Temp -Namespace stupidnamespace -Name RootManageSharedAccessKey | select PrimaryConnectionString
```
The last *pipe* is optional, it just gives us the ability to better see the *PrimaryConnectionString*

Create a new queue with *Duplicate Detection* enabled
```powershell
New-AzureRmServiceBusQueue -ResourceGroupName [ResourceGroupName] -Namespace stupidnamespace -Name messagein -RequiresDuplicateDetection $True
```


[Back to top](#table-of-content)


# Storage

## List all Storage Accounts

```powershell
Get-AzStorageAccount
```

[Back to top](#table-of-content)

## Storage Account Details

```powershell
Get-AzStorageAccount -ResourceGroupName "[RESOURCE_GROUP]" -Name "[STORAGE_ACCOUNT]" | format-list -Property *
```
## Create Storage Account

```powershell
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageName -Location $location -SkuName Standard_LRS -Kind StorageV2 -AccessTier Hot

$storageContext = $storageAccount.Context
```

## Create Blob Container

```powershell
New-AzStorageContainer -Name $blobContainerName -Context $storageContext
```

## Upload File to Blob Container

> If you need a test file
```powershell
New-Item -Path $filePath -Name $fileName -Type File -Value "This is the content"
```

```powershell
Set-AzStorageBlobContent -File "$filePath\$fileName" -Container $blobContainerName -Context $storageContext -Blob $fileName
```

## List Blobs in Container

```powershell
Get-AzStorageBlob -Container $blobContainerName -Context $storageContext
```

## Download Blob

```powershell
Get-AzStorageBlobContent -Blob $fileName -Container $blobContainerName -Context $storageContext -Destination C:\temp\fromazure.txt
```

[Back to top](#table-of-content)


# Logic Apps

## List of all Logic Apps

```csharp

```
[Back to top](#table-of-content)

## Enable a Logic App

```powershell
Set-AzureRmLogicApp -ResourceGroupName "[RESOURCE_GROUP]" -Name "[STORAGE_ACCOUNT]" -State Enabled
```
[Back to top](#table-of-content)

# API Management

## List APIS

```powershell
$apicontext = New-AzureRmApiManagementContext -ResourceGroupName [Resource Group] -ServiceName [name of Service]
Get-AzureRmApiManagementApi -Context $apicontext
```

[Back to top](#table-of-content)

## Azure On-premises Data Gateway

### Install the gateway on an on-prem Server
- Download the *GatewayInstall.exe* from the following address [https://aka.ms/azureasgateway](https://aka.ms/azureasgateway)
- Install and configure, follow the instructions
- You should now have a *Windows Service* named **Microsoft.PowerBI.EnterpriseGateway.exe** running
- Configure an **On-premises Data Gateway** in Azure, signing in with the same name as used when configuring the on-prem service

> (some *Location* restriction may be required. I had to change from *West Europe* to *North Europe* before anything showed up in
>  the **Installation Name** dropdown?)



[Back to top](#table-of-content)

# Event Grid

## Create Standard Subscription


```powershell
New-AzEventGridSubscription -EventSubscriptionName storageToRequestBin -ResourceId $storageId -Endpoint $endpoint
```
> $endpoint is a *WebHook* endpoint.
> https://en3jyjvadsa46.x.pipedream.net/

> $storageId (ResourceId) is the address for (for instance) the Storage Id
> /subscriptions/4954f2df-57b6-4e04-bcc5-f92b0b63837c/resourceGroups/sandbox/providers/Microsoft.Storage/storageAccounts/sandstorage

> NOTE: If you are using just an HTTP endpoint and not a true *WebHook* you will receive a **validationUrl** that you manually needs 
> to call with a *GET Method*

> Validation data sent to Endpoint

```json
[{
  "id": "755980c4-8571-4ac5-bf53-2108a33e4445",
  "topic": "/subscriptions/4954f2df-57b6-4e04-bcc5-f92b0b63837c/resourceGroups/sandbox/providers/microsoft.storage/storageaccounts/sandstorage",
  "subject": "",
  "data": {
    "validationCode": "9F0C5719-2B8B-4DAF-9C36-F5FD3B5C8B7C",
    "validationUrl": "https://rp-westeurope.eventgrid.azure.net/eventsubscriptions/storagetorequestbin/validate?id=9F0C5719-2B8B-4DAF-9C36-F5FD3B5C8B7C&t=2019-02-16T15:52:05.6430791Z&apiVersion=2019-01-01&token=3LYXtMkeUxlpMNwBcD2NcSkgZdgvHnXKB8HwBqElU5U%3d"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2019-02-16T15:52:05.6430791Z",
  "metadataVersion": "1",
  "dataVersion": "2"
}]

```

> When calling the *validationUrl* you should see the following message (note the spelling error, NICE MS!!)
*"Webhook succesfully validated as a subscription endpoint"*


[Back to top](#table-of-content)