---
title: Schedule regular quick and full scans with Microsoft Defender Antivirus
description: Set up recurring (scheduled) scans, including when they should run and whether they run as full or quick scans
keywords: quick scan, full scan, quick vs full, schedule scan, daily, weekly, time, scheduled, recurring, regular
search.product: eADQiWindows 10XVcnh
ms.prod: m365-security
ms.mktglfcycl: manage
ms.sitesec: library
ms.pagetype: security
localization_priority: normal
author: denisebmsft
ms.author: deniseb
ms.custom: nextgen
ms.date: 11/02/2020
ms.reviewer: pauhijbr
manager: dansimp
ms.technology: mde
---

# Configure scheduled quick or full Microsoft Defender Antivirus scans

[!INCLUDE [Microsoft 365 Defender rebranding](../../includes/microsoft-defender.md)]


**Applies to:**

- [Microsoft Defender for Endpoint](/microsoft-365/security/defender-endpoint/)


> [!NOTE]
> By default, Microsoft Defender Antivirus checks for an update 15 minutes before the time of any scheduled scans. You can [Manage the schedule for when protection updates should be downloaded and applied](manage-protection-update-schedule-microsoft-defender-antivirus.md) to override this default. 

In addition to always-on real-time protection and [on-demand](run-scan-microsoft-defender-antivirus.md) scans, you can set up regular, scheduled scans. 

You can configure the type of scan, when the scan should occur, and if the scan should occur after a [protection update](manage-protection-updates-microsoft-defender-antivirus.md) or if the endpoint is being used. You can also specify when special scans to complete remediation should occur.

This article describes how to configure scheduled scans with Group Policy, PowerShell cmdlets, and WMI. You can also configure schedules scans with [Microsoft Endpoint Configuration Manager](/configmgr/protect/deploy-use/endpoint-antimalware-policies#scheduled-scans-settings) or [Microsoft Intune](/mem/intune/configuration/device-restrictions-windows-10).

## To configure the Group Policy settings described in this article

1.  On your Group Policy management machine, open the [Group Policy Management Console](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11)), right-click the Group Policy Object you want to configure and click **Edit**.

3.  In the **Group Policy Management Editor** go to **Computer configuration**.

4.  Click **Administrative templates**.

5.  Expand the tree to **Windows components > Microsoft Defender Antivirus** and then the **Location** specified in the table below.

6. Double-click the policy **Setting** as specified in the table below, and set the option to your desired configuration. 

7. Click **OK**, and repeat for any other settings.

Also see the [Manage when protection updates should be downloaded and applied](manage-protection-update-schedule-microsoft-defender-antivirus.md) and [Prevent or allow users to locally modify policy settings](configure-local-policy-overrides-microsoft-defender-antivirus.md) topics.

## Quick scan versus full scan and custom scan

When you set up scheduled scans, you can set up whether the scan should be a full or quick scan.

Quick scans look at all the locations where there could be malware registered to start with the system, such as registry keys and known Windows startup folders. 

Combined with [always-on real-time protection capability](configure-real-time-protection-microsoft-defender-antivirus.md) - which reviews files when they are opened and closed, and whenever a user navigates to a folder - a quick scan helps provide strong coverage both for malware that starts with the system and kernel-level malware.  

In most instances, this means a quick scan is adequate to find malware that wasn't picked up by real-time protection.

A full scan can be useful on endpoints that have encountered a malware threat to identify if there are any inactive components that require a more thorough clean-up. In this instance, you may want to use a full scan when running an [on-demand scan](run-scan-microsoft-defender-antivirus.md).

A custom scan allows you to specify the files and folders to scan, such as a USB drive. 

>[!NOTE]
>By default, quick scans run on mounted removable devices, such as USB drives.

## Set up scheduled scans

Scheduled scans will run at the day and time you specify. You can use Group Policy, PowerShell, and WMI to configure scheduled scans.

>[!NOTE]
>If a computer is unplugged and running on battery during a scheduled full scan, the scheduled scan will stop with event 1002, which states that the scan stopped before completion. Microsoft Defender Antivirus will run a full scan at the next scheduled time.

### Use Group Policy to schedule scans

|Location | Setting | Description | Default setting (if not configured) |
|:---|:---|:---|:---|
|Scan | Specify the scan type to use for a scheduled scan | Quick scan |
|Scan | Specify the day of the week to run a scheduled scan | Specify the day (or never) to run a scan. | Never |
|Scan | Specify the time of day to run a scheduled scan | Specify the number of minutes after midnight (for example, enter **60** for 1 a.m.). | 2 a.m. |
|Root | Randomize scheduled task times |In Microsoft Defender Antivirus: Randomize the start time of the scan to any interval from 0 to 4 hours. <br>In FEP/SCEP: randomize to any interval plus or minus 30 minutes. This can be useful in VM or VDI deployments. | Enabled |


### Use PowerShell cmdlets to schedule scans

Use the following cmdlets:

```PowerShell
Set-MpPreference -ScanParameters
Set-MpPreference -ScanScheduleDay
Set-MpPreference -ScanScheduleTime
Set-MpPreference -RandomizeScheduleTaskTimes

```

See [Use PowerShell cmdlets to configure and run Microsoft Defender Antivirus](use-powershell-cmdlets-microsoft-defender-antivirus.md) and [Defender cmdlets](/powershell/module/defender/) for more information on how to use PowerShell with Microsoft Defender Antivirus.

### Use Windows Management Instruction (WMI) to schedule scans

Use the [**Set** method of the **MSFT_MpPreference**](/previous-versions/windows/desktop/legacy/dn455323(v=vs.85)) class for the following properties:

```WMI
ScanParameters
ScanScheduleDay
ScanScheduleTime
RandomizeScheduleTaskTimes
```

See the following for more information and allowed parameters:
- [Windows Defender WMIv2 APIs](/previous-versions/windows/desktop/defender/windows-defender-wmiv2-apis-portal)




## Start scheduled scans only when the endpoint is not in use

You can set the scheduled scan to only occur when the endpoint is turned on but not in use with Group Policy, PowerShell, or WMI.

> [!NOTE]
> These scans will not honor the CPU throttling configuration and take full advantage of the resources available to complete the scan as fast as possible.

### Use Group Policy to schedule scans

|Location | Setting | Description | Default setting (if not configured) |
|:---|:---|:---|:---|
|Scan | Start the scheduled scan only when computer is on but not in use | Scheduled scans will not run, unless the computer is on but not in use | Enabled |

### Use PowerShell cmdlets

Use the following cmdlets:

```PowerShell
Set-MpPreference -ScanOnlyIfIdleEnabled
```

See [Use PowerShell cmdlets to configure and run Microsoft Defender Antivirus](use-powershell-cmdlets-microsoft-defender-antivirus.md) and [Defender cmdlets](/powershell/module/defender/) for more information on how to use PowerShell with Microsoft Defender Antivirus.

### Use Windows Management Instruction (WMI)

Use the [**Set** method of the **MSFT_MpPreference**](/previous-versions/windows/desktop/legacy/dn455323(v=vs.85)) class for the following properties:

```WMI
ScanOnlyIfIdleEnabled
```

See the following for more information and allowed parameters:
- [Windows Defender WMIv2 APIs](/previous-versions/windows/desktop/defender/windows-defender-wmiv2-apis-portal)

<a id="remed"></a>
## Configure when full scans should be run to complete remediation

Some threats may require a full scan to complete their removal and remediation. You can schedule when these scans should occur with Group Policy, PowerShell, or WMI.

### Use Group Policy to schedule remediation-required scans

| Location | Setting | Description | Default setting (if not configured) |
|---|---|---|---|
|Remediation | Specify the day of the week to run a scheduled full scan to complete remediation | Specify the day (or never) to run a scan. | Never |
|Remediation | Specify the time of day to run a scheduled full scan to complete remediation | Specify the number of minutes after midnight (for example, enter **60** for 1 a.m.) | 2 a.m. |

### Use PowerShell cmdlets

Use the following cmdlets:

```PowerShell
Set-MpPreference -RemediationScheduleDay
Set-MpPreference -RemediationScheduleTime
```

See [Use PowerShell cmdlets to configure and run Microsoft Defender Antivirus](use-powershell-cmdlets-microsoft-defender-antivirus.md) and [Defender cmdlets](/powershell/module/defender/) for more information on how to use PowerShell with Microsoft Defender Antivirus.

### Use Windows Management Instruction (WMI)

Use the [**Set** method of the **MSFT_MpPreference**](/previous-versions/windows/desktop/legacy/dn455323(v=vs.85)) class for the following properties:

```WMI
RemediationScheduleDay
RemediationScheduleTime
```

See the following for more information and allowed parameters:
- [Windows Defender WMIv2 APIs](/previous-versions/windows/desktop/defender/windows-defender-wmiv2-apis-portal)




## Set up daily quick scans

You can enable a daily quick scan that can be run in addition to your other scheduled scans with Group Policy, PowerShell, or WMI.


### Use Group Policy to schedule daily scans


|Location | Setting | Description | Default setting (if not configured) |
|:---|:---|:---|:---|
|Scan | Specify the interval to run quick scans per day | Specify how many hours should elapse before the next quick scan. For example, to run every two hours, enter **2**, for once a day, enter **24**. Enter **0** to never run a daily quick scan. | Never |
|Scan | Specify the time for a daily quick scan | Specify the number of minutes after midnight (for example, enter **60** for 1 a.m.) | 2 a.m. |

### Use PowerShell cmdlets to schedule daily scans

Use the following cmdlets:

```PowerShell
Set-MpPreference -ScanScheduleQuickScanTime
```

See [Use PowerShell cmdlets to configure and run Microsoft Defender Antivirus](use-powershell-cmdlets-microsoft-defender-antivirus.md) and [Defender cmdlets](/powershell/module/defender/) for more information on how to use PowerShell with Microsoft Defender Antivirus.

### Use Windows Management Instruction (WMI) to schedule daily scans

Use the [**Set** method of the **MSFT_MpPreference**](/previous-versions/windows/desktop/legacy/dn455323(v=vs.85)) class for the following properties:

```WMI
ScanScheduleQuickScanTime
```

See the following for more information and allowed parameters:
- [Windows Defender WMIv2 APIs](/previous-versions/windows/desktop/defender/windows-defender-wmiv2-apis-portal)


## Enable scans after protection updates

You can force a scan to occur after every [protection update](manage-protection-updates-microsoft-defender-antivirus.md) with Group Policy.

### Use Group Policy to schedule scans after protection updates

|Location | Setting | Description | Default setting (if not configured)|
|:---|:---|:---|:---|
|Signature updates | Turn on scan after Security intelligence update | A scan will occur immediately after a new protection update is downloaded | Enabled |

## See also
- [Prevent or allow users to locally modify policy settings](configure-local-policy-overrides-microsoft-defender-antivirus.md)
- [Configure and run on-demand Microsoft Defender Antivirus scans](run-scan-microsoft-defender-antivirus.md)
- [Configure Microsoft Defender Antivirus scanning options](configure-advanced-scan-types-microsoft-defender-antivirus.md)
- [Manage Microsoft Defender Antivirus updates and apply baselines](manage-updates-baselines-microsoft-defender-antivirus.md)
- [Manage when protection updates should be downloaded and applied](manage-protection-update-schedule-microsoft-defender-antivirus.md) 
- [Microsoft Defender Antivirus in Windows 10](microsoft-defender-antivirus-in-windows-10.md)