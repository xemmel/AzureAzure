# Microsoft AZURE by Morten la Cour

## Table of Content
* [Powershell](#powershell)
*    [Install Powershell](#install-powershell)
*    [Starting Powershell](#starting-powershell)

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
