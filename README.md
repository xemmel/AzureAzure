# Microsoft AZURE by Morten la Cour

## Table of Content
+ [PowerShell](#powershell)
1. [Install PowerShell](#install-powershell)
2. [Starting PowerShell](#starting-powershell)
3. [Changing Subscription](#changing-subscription)
4. [Exploring PowerShell](#exploring-powershell)
+ [Service Bus](#service-bus)
1. [List all Topics](#list-all-topics)
2. [Creating a subscription](#creating-a-subscription)
+ [Storage](#storage)
1. [List all Storage Accounts](#list-all-storage-accounts)
2. [Storage Account Details](#storage-account-details)
+ [Logic Apps](#logic-apps)
1. [List all Logic Apps](#list-all-logic-apps)
2. [Enable a Logic App](#enable-a-logic-app)

# PowerShell

## Install Powershell

>Run *Powershell* as Adminstrator


> Make sure you are running at least *Powershell* version 5.0. Check this by running the following command

> Also *Powershell* version 6.0 uses __.NET Core__ which is not supported by *PowerShell AzureRM*

```powershell

$PSVersionTable.PSVersion

```

Now install the *AzureRM Powershell Module* say *Yes* or *Yes to All* when prompted

```powershell
Install-Module -Name AzureRM
```


[Back to top](#table-of-content)

## Starting Powershell

This step needs to be done every time *PowerShell* is started. The import part, however, can be automated by setting up a *PowerShell Profile*

```powershell
Import-Module AzureRM
Connect-AzureRmAccount
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
Get-AzureRmContext
```
### List all subscriptions
```powershell
Get-AzureRmSubscription
```
If you need to change the subscription, get the **ID** from the list of subscriptions and use the following command

```powershell
Select-AzureRmSubscription -SubscriptionId [Id]
```
[Back to top](#table-of-content)


## Exploring PowerShell


### Get a list of all available modules

```powershell
Get-Module -Name AzureRM*
```

### Get a list of commands
In this example all commands in the module *AzureRM.ServiceBus* is retrieved

```powershell
Get-Command -Module AzureRM.ServiceBus
```


[Back to top](#table-of-content)

# Service Bus

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

#Storage

## List all Storage Accounts

```powershell
Get-AzureRmStorageAccount
```

[Back to top](#table-of-content)

## Storage Account Details

```powershell
Get-AzureRmStorageAccount -ResourceGroupName "[RESOURCE_GROUP]" -Name "[STORAGE_ACCOUNT]" | format-list -Property *
```

[Back to top](#table-of-content)


# Logic Apps

## List of all Logic Apps

```csharp
Get-AzureRmResource -ResourceType "Microsoft.Logic/workflows"
```
[Back to top](#table-of-content)

## Enable a Logic App

```powershell
Set-AzureRmLogicApp -ResourceGroupName "[RESOURCE_GROUP]" -Name "[STORAGE_ACCOUNT]" -State Enabled
```
[Back to top](#table-of-content)