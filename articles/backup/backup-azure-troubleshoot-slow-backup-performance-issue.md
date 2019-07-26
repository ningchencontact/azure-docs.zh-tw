---
title: 疑難排解 Azure 備份的檔案和資料夾備份速度緩慢問題
description: 提供疑難排解指導方針，以協助您診斷 Azure 備份效能問題的原因
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 3a39d39412c8b64d1851ea0fc9511d116f3b232a
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465348"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>疑難排解 Azure 備份的檔案和資料夾備份速度緩慢問題
這篇文章提供疑難排解指引，可協助您診斷當您使用 Azure 備份時，檔案與資料夾備份效能緩慢的原因。 當您使用 Azure 備份代理程式來備份檔案時，備份處理程序進行的時間可能比預期的還要久。 此延遲可能是因為下列一或多個原因所造成：

* [正在備份的電腦有效能瓶頸。](#cause1)
* [有其他處理程序或防毒軟體在干擾 Azure 備份處理程序。](#cause2)
* [備份代理程式是在 Azure 虛擬機器 (VM) 上執行。](#cause3)  
* [您正在備份大量 (數百萬計) 的檔案。](#cause4)

在開始對問題進行疑難排解之前，建議您下載並安裝 [最新版的 Azure 備份代理程式](https://aka.ms/azurebackup_agent)。 我們會經常更新備份代理程式，以修正各種問題、新增功能和改善效能。

我們也強烈建議您檢閱 [Azure 備份服務常見問題集](backup-azure-backup-faq.md) ，以確定所遇到的問題並非任何常見的組態問題。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>原因：電腦上的效能瓶頸
所備份電腦上的瓶頸可能會造成延遲。 例如，電腦讀取或寫入磁碟的能力，或者透過網路傳送資料的可用頻寬，都可能造成瓶頸。

Windows 提供了稱為 [效能監視器](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) 的內建工具，以偵測這些瓶頸。

以下是能幫助您診斷最佳化備份瓶頸的一些效能計數器和範圍。

| 計數器 | 狀態 |
| --- | --- |
| 邏輯磁碟 (實體磁碟)--% 閒置 |• 100% 閒置至 50% 閒置 = 狀況良好</br>• 49% 閒置至 20% 閒置 = 警告或監視</br>• 19% 閒置至 0% 閒置 = 重大或超出規範 |
| 邏輯磁碟 (實體磁碟)--% 平均磁碟的讀取或寫入秒數 |• 0.001 毫秒到 0.015 毫秒 = 狀況良好</br>• 0.015 毫秒到 0.025 毫秒 = 警告或監視</br>• 0.026 毫秒或更長 = 嚴重或超出規範 |
| 邏輯磁碟 (實體磁碟)--(所有執行個體) 目前的磁碟佇列長度 |80 個要求超過 6 分鐘 |
| 記憶體--集區未分頁位元組 |• 耗用不到 60% 集區 = 狀況良好<br>• 耗用 61% 到 80% 集區 = 警告或監視</br>• 耗用超過 80% 集區 = 重大或超出規範 |
| 記憶體--集區分頁位元組 |• 耗用不到 60% 集區 = 狀況良好</br>• 耗用 61% 到 80% 集區 = 警告或監視</br>• 耗用超過 80% 集區 = 重大或超出規範 |
| 記憶體--可用 MB 數 |• 50% 以上的可用記憶體可供使用 = 狀況良好</br>• 25% 的可用記憶體可供使用 = 監視</br>• 10% 的可用記憶體可供使用 = 警告</br>• 不到 100 MB 或 5% 的可用記憶體可供使用 = 重大或超出規範 |
| Processor--\%Processor Time (all instances) |• Less than 60% consumed = Healthy</br>• 61% to 90% consumed = Monitor or Caution</br>• 91% to 100% consumed = Critical |

> [!NOTE]
> If you determine that the infrastructure is the culprit, we recommend that you defragment the disks regularly for better performance.
>
>

<a id="cause2"></a>

## Cause: Another process or antivirus software interfering with Azure Backup
We've seen several instances where other processes in the Windows system have negatively affected performance of the Azure Backup agent process. For example, if you use both the Azure Backup agent and another program to back up data, or if antivirus software is running and has a lock on files to be backed up, the multiple locks on files might cause contention. In this situation, the backup might fail, or the job might take longer than expected.

The best recommendation in this scenario is to turn off the other backup program to see whether the backup time for the Azure Backup agent changes. Usually, making sure that multiple backup jobs are not running at the same time is sufficient to prevent them from affecting each other.

For antivirus programs, we recommend that you exclude the following files and locations:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe as a process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ folders
* Scratch location (if you're not using the standard location)

<a id="cause3"></a>

## Cause: Backup agent running on an Azure virtual machine
If you're running the Backup agent on a VM, performance will be slower than when you run it on a physical machine. This is expected due to IOPS limitations.  However, you can optimize the performance by switching the data drives that are being backed up to Azure Premium Storage. We're working on fixing this issue, and the fix will be available in a future release.

<a id="cause4"></a>

## Cause: Backing up a large number (millions) of files
Moving a large volume of data will take longer than moving a smaller volume of data. In some cases, backup time is related to not only the size of the data, but also the number of files or folders. This is especially true when millions of small files (a few bytes to a few kilobytes) are being backed up.

This behavior occurs because while you're backing up the data and moving it to Azure, Azure is simultaneously cataloging your files. In some rare scenarios, the catalog operation might take longer than expected.

The following indicators can help you understand the bottleneck and accordingly work on the next steps:

* **UI is showing progress for the data transfer**. The data is still being transferred. The network bandwidth or the size of data might be causing delays.
* **UI is not showing progress for the data transfer**. Open the logs located at C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, and then check for the FileProvider::EndData entry in the logs. This entry signifies that the data transfer finished and the catalog operation is happening. Don't cancel the backup jobs. Instead, wait a little longer for the catalog operation to finish. If the problem persists, contact [Azure support](https://portal.azure.com/#create/Microsoft.Support).處理器--\` 處理器時間 (所有執行個體)es and folders in Azure Backup
description: Provides troubleshooting guidance to help you diagnose the cause of Azure Backup performance issues

author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
---
# Troubleshoot slow backup of files and folders in Azure Backup
This article provides troubleshooting guidance to help you diagnose the cause of slow backup performance for files and folders when you're using Azure Backup. When you use the Azure Backup agent to back up files, the backup process might take longer than expected. This delay might be caused by one or more of the following:

* [There are performance bottlenecks on the computer that’s being backed up.](#cause1)
* [Another process or antivirus software is interfering with the Azure Backup process.](#cause2)
* [The Backup agent is running on an Azure virtual machine (VM).](#cause3)  
* [You're backing up a large number (millions) of files.](#cause4)

Before you start troubleshooting issues, we recommend that you download and install the [latest Azure Backup agent](https://aka.ms/azurebackup_agent). We make frequent
updates to the Backup agent to fix various issues, add features, and improve performance.

We also strongly recommend that you review the [Azure Backup service FAQ](backup-azure-backup-faq.md) to make sure you're not experiencing any of the common configuration issues.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## Cause: Performance bottlenecks on the computer
Bottlenecks on the computer that's being backed up can cause delays. For example, the computer's ability to read or write to disk, or available bandwidth to send data over the network, can cause bottlenecks.

Windows provides a built-in tool that's called [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) to detect these bottlenecks.

Here are some performance counters and ranges that can be helpful in diagnosing bottlenecks for optimal backups.

| Counter | Status |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |• 100% idle to 50% idle = Healthy</br>• 49% idle to 20% idle = Warning or Monitor</br>• 19% idle to 0% idle = Critical or Out of Spec |
| Logical Disk(Physical Disk)--%Avg. Disk Sec Read or Write |• 0.001 ms to 0.015 ms  = Healthy</br>• 0.015 ms to 0.025 ms = Warning or Monitor</br>• 0.026 ms or longer = Critical or Out of Spec |
| Logical Disk(Physical Disk)--Current Disk Queue Length (for all instances) |80 requests for more than 6 minutes |
| Memory--Pool Non Paged Bytes |• Less than 60% of pool consumed = Healthy<br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Pool Paged Bytes |• Less than 60% of pool consumed = Healthy</br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Available Megabytes |• 50% of free memory available or more = Healthy</br>• 25% of free memory available = Monitor</br>• 10% of free memory available = Warning</br>• Less than 100 MB or 5% of free memory available = Critical or Out of Spec |
| Processor--\%Processor Time (all instances) |• 已消耗低於 60% = 狀況良好</br>• 已消耗 61% 到 90% = 監視或警告</br>• 已消耗 91% 到 100% = 嚴重 |

> [!NOTE]
> 如果您判斷是基礎結構問題，我們建議您定期執行磁碟重組以提升效能。
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>原因：干擾 Azure 備份的另一個處理常式或防毒軟體
我們曾看過許多例子，Windows 系統中的其他處理程序會對 Azure 備份代理程式處理程序的效能造成不良影響。 例如，如果您同時使用 Azure 備份代理程式和其他程式來備份資料，或是如果防毒軟體正在執行因而鎖定了要備份的檔案，檔案有多個鎖定可能會造成爭用情形。 在此情況下，備份可能會失敗，或作業執行時間可能會比預期的還要久。

此案例的最佳建議是關閉其他備份程式，以查看 Azure 備份代理程式的備份時間是否有變化。 通常只要確定多個備份作業不會在同一時間執行，就足以防止作業彼此干擾。

至於防毒程式，我們建議您排除下列檔案和位置︰

* 將 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe 做為處理程序
* C:\Program Files\Microsoft Azure Recovery Services Agent\ 資料夾
* 暫存位置 (如果您不是使用標準位置)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>原因：在 Azure 虛擬機器上執行的備份代理程式
如果您在 VM 上執行備份代理程式，其效能會比在實體機器上執行來得慢。 這是預期行為，因為有 IOPS 限制。  不過，您可以藉由將正在備份的資料磁碟機切換到 Azure 進階儲存體來獲得最佳效能。 我們正在努力修正此問題，未來的版本將會有這方面的修正。

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>原因：備份大量 (數百萬個) 的檔案
移動大量資料所花費的時間會比移動較少量資料更久。 在某些情況下，備份時間不僅與資料大小有關，也與檔案或資料夾的數目有關。 尤其是在備份數百萬計的小檔案 (幾位元組到幾千位元組) 時，更是如此。

發生這個行為的原因是因為當您備份資料並將資料移動至 Azure 的時候，Azure 會同時分類您的檔案。 在某些罕見的案例中，目錄作業花費的時間可能比預期時間更長。

下列指標可協助您了解瓶頸並據以處理下一個步驟︰

* **UI 正在顯示資料傳輸的進度**。 資料仍在傳輸中。 網路頻寬或資料大小可能會造成延遲。
* **UI 未顯示資料傳輸的進度**。 請開啟位於 C:\Program Files\Microsoft Azure 復原服務代理程式\Temp 的記錄，然後查看記錄中是否有 FileProvider::EndData 項目。 此項目表示資料傳輸完成，且目錄作業正在進行中。 請勿取消備份工作。 請稍微多等待一些時間讓目錄作業完成。 若問題持續發生，請連絡 [Azure 支援](https://portal.azure.com/#create/Microsoft.Support)。
