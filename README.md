# Microsoft AZURE by Morten la Cour

## Table of Content
1. [PowerShell](#powershell)
- [Install PowerShell](#install-powershell)
-   [Starting PowerShell](#starting-powershell)
-   [Exploring PowerShell](#exploring-powershell)
* [Service Bus](#service-bus)
*   [List all Topics](#list-all-topics)
*   [Creating a subscription](#creating-a-subscription)

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

