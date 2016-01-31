---
layout: post_simple
title:  "Debugging Recycling Cloud Service Roles in Azure"
date:   2015-12-13 
permalink: "/blog/Debugging-Recycling-Cloud-Service-Roles-in-Azure"


tags: [azure]
---

It can be frustrating enough to diagnose errors that prevent your .NET applications from starting, but bring Azure Cloud Services into the picture and it becomes even more so. One of the more annoying errors in Azure is when your roles fail to start and keep recycling themselves. Depending on the underlying issue and your type of role (web vs. worker), the debugging process will look different. For web roles, assuming you have the right .pdb level and custom error configurations, you can often work with the stack trace shown when you try to access the site. 

However, worker roles or web roles that won't even load a page can be a bit more challenging. The following debugging process can help you get a full stack trace regardless of your type of role or how deep the error goes. All you need to do is remote into the cloud service instance and run a debugger. Follow these four steps: 

<div class="spacer"></div>

## 1. Configure Remote Desktop

Remote desktop can either be configured in source control as part of your cloud service definition and configuration, or at runtime after the cloud service has been uploaded. I'll cover the latter here in case you don't want to have remote desktop on by default. You can enable it even if your application is already in the process of recycling.

First, log into your [Azure management portal](https://manage.windowsazure.com). Navigate to your cloud service, go to the configuration tab, and click on "Remote" at the bottom.

<img src="/img/debugrecycling/remote-desktop.jpg" style="width: 100%"/>

In the dialog window that comes up choose which role(s) for which you want to enable access, a username / password combination, an existing certificate (or create a new one), and the expiration time for remote access. After confirming, it will take a minute or two for the remote desktop access to take effect.

Note that configuring remote desktop this way is only temporary, and if you delete the deployment and re-deploy it will reset back to being disabled. For the purposes of debugging a temporary issue, this might be exactly what we want.

<div class="spacer"></div>

## 2. Install Debugging Tools with Powershell

Now that remote desktop is enabled, you can access your cloud service by going to the "Instances" tab in the management portal (see the top tab bar in the screenshot above). On the instances tab, you can choose the one that is recycling, and click "Connect" on the bottom tab bar. This will download the .rdp file you need to remote in.

Once you're in the VM, open powershell and paste in this script:

<pre>
md c:\tools; Import-Module bitstransfer; Start-BitsTransfer http://dsazure.blob.core.windows.net/azuretools/AzureTools.exe c:\tools\AzureTools.exe; c:\tools\AzureTools.exe
</pre>

This script creates a new tools directory, downloads the "AzureTools.exe" utility, and opens it. In the window that pops up, right-click the "X64 Debuggers And Tools-x64_en-us.msi" entry and download it.

<img src="/img/debugrecycling/debug-install.png" style="width: 100%"/>

<div class="spacer"></div>

## 3. Run the Debugger

After the download finishes, go to the "Utils" tab, click "Attach Debugger". Select the process to debug in the popup (WaIISHost for web roles, WaWorkerHost for worker roles), and click "Attach Debugger". The process will appear in the "Currently Monitoring" list. Give it a few seconds, and it should bring up a WinDbg output window.

<img src="/img/debugrecycling/debug-attach.png" style="width: 100%"/>

<img src="/img/debugrecycling/debug-output.png" style="width: 100%"/>

In the text entry field at the bottom, type "g" and hit enter to let the debugger go. 

## 4. Capture the Stack Trace

It will continue churning for a little bit, then it should stop on the exception that's causing your problem. As you can see in the highlighted output below, there was an exception thrown in my AutomationUtils.cs file on line 192 - a deliberate throw new Exception() I put in for the purposes of this post. 

<img src="/img/debugrecycling/stacktrace.jpg" style="width: 100%"/>

Fix your error, re-deploy your application, and you should be good to go!