---
layout: post_simple
title:  "Automating Azure - Getting Started with Azure PowerShell"
date:   2016-01-30 
permalink: "/blog/Azure-PowerShell-Getting-Started"


tags: [azure]
---

Recently, I've been trying to further automate the deployment process used by my team. Since we almost exclusively use Microsoft Azure as our application platform and infrastructure, a core part of our deployment involves cloud service configuration changes and swaps. I have found Azure PowerShell to be a very useful tool in the automation process. It's simply a PowerShell module that provides several cmdlets for interacting with the Azure Service Management API to perform a wide varitey of tasks that you typcially do manually through the Azure management portal. 

My next few posts will cover different techniques I've used to optimize deployment scripts, and I wanted to start with a simple reference post for getting started. Since Windows already comes with PowerShell installed, there are really just two small steps before you're ready to go - install the Azure PowerShell module and link it with one of your Azure subscriptions.

Note that at the time of writing this post, there is also an Azure Resource Manager API which is different from the Service Management API on which I focus. This post [here](https://azure.microsoft.com/en-us/documentation/articles/resource-manager-deployment-model/) provides an excellent comparison. For me and my team the Service Management API works well enough with our current architecture, and the certificate based authentication method (described below and not supported by the Resrouce Manager API) fits well with our deployment processes.

<div class="spacer"></div>

## 1. Install Azure PowerShell

The Azure PowerShell Module has a direct download link from Github. Go [here](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi) to get the installer. Once installed on your PC, this will add a shortcut called "Microsoft Azure PowerShell" that simply points to the stock Windows Powershell with some command line arguments:

<pre>
C:\Windows\SysWOW64\WindowsPowerShell\v1.0\powershell.exe -NoExit -ExecutionPolicy Bypass -File "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\ShortcutStartup.ps1"
</pre>

<div class="spacer"></div> 

## 2. Connect to an Azure Subscription

There are two primary ways I've found for connecting to your subscription. The first uses Azure Active Directory (AD) is to run the "Add-AzureAccount" cmdlet from a PowerShell console. This will bring up an authentication dialog where you can enter the email and password associated with your account. By filling out the dialog, it will automatically import your subscription(s). 

The second method, certificate based authentication, is what I prefer for creating automation scripts. Run the "Get-AzurePublishSettingsFile" cmdlet to open a browser and download a Publish Settings File. The act of downloading this file creates a new management certificate in your Azure account that is valid for one year. The Publish Settings file links to that certificate, and it can be used by PowerShell (and other applications) to access the Azure Service Management API. Once you have the file downloaded, run the cmdlet "Import-AzurePublishSettingsFile -PublishSettingsFile [publish_settings_file_path]" to import your subscriptions.

The best part of the second method is that once you have the file, you can script out the import process. This means so long as you bundle your deployment scripts with the file, they can be run on any PC or server without having to manually fill out an authentication dialog.

Regardless of which path you took, you now have your subscriptions imported. When you execute certain cmdlets, they execute against the actively selected subscription. To see a list of which ones are available and set one, use the "Get-AzureSubscription" and the "Set-AzureSubscription -SubscriptionName [subscription_name]" cmdlets.

<div class="spacer"></div>

## 3. What's Next?

Once you have your account linked up to Azure PowerShell, the sky's the limit as far as what services you can control in your Azure account. Stay tuned for my subsequent posts for more specific Azure PowerShell techniques. Below is a quick reference section for the important links / cmdlets in this post.


[Download Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi)

<pre>
Add-AzureAccount   # Open authentication dialog to import an Azure Account
Get-AzurePublishSettingsFile
Import-AzurePublishSettingsFile -PublishSettingsFile [publish_settings_file_path]
Get-AzureSubscription
Set-AzureSubscription -SubscriptionName [subscription_name]
</pre>