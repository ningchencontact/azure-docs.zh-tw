---
title: 針對將更新管理、變更追蹤和清查上線時的錯誤進行疑難排解
description: 了解如何針對將更新管理、變更追蹤和清查解決方案上線時的錯誤進行疑難排解
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 8b4ee999bb23abdcea3411720bde244b2da4e89f
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516407"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>針對將解決方案上線時的錯誤進行疑難排解

當您將更新管理或變更追蹤和清查等解決方案上線時，可能會發生錯誤。 本文描述可能發生的各種錯誤及解決方法。

## <a name="known-issues"></a>已知問題

### <a name="node-rename"></a>案例：重新命名已註冊的節點需要再次取消註冊/登錄

#### <a name="issue"></a>問題

節點會註冊為 Azure 自動化, 然後作業系統 computername 會變更。  節點中的報表會繼續以原始名稱出現。

#### <a name="cause"></a>原因

重新命名已註冊的節點並不會更新 Azure 自動化中的節點名稱。

#### <a name="resolution"></a>解決方法

從 Azure 自動化狀態設定取消註冊節點, 然後再次登錄。  在該時間之前發行至服務的報表將無法再使用。


### <a name="resigning-cert"></a>案例：不支援透過 HTTPs proxy 重新簽署憑證

#### <a name="issue"></a>問題

客戶回報當透過會終止 HTTPs 流量的 proxy 解決方案進行連線, 然後使用新的憑證重新加密流量時, 服務不允許連線。

#### <a name="cause"></a>原因

Azure 自動化不支援重新簽署用來加密流量的憑證。

#### <a name="resolution"></a>解決方法

此問題沒有因應措施。

## <a name="general-errors"></a>一般錯誤

### <a name="missing-write-permissions"></a>案例：上線失敗並顯示以下訊息：無法啟用解決方案

#### <a name="issue"></a>問題

當您嘗試將虛擬機器上架到解決方案時, 您會收到下列其中一則訊息:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>原因

此錯誤是由於虛擬機器、工作區或使用者的許可權不正確或遺失所造成。

#### <a name="resolution"></a>解決方法

確定您有正確的權限可讓虛擬機器上線。 檢閱[讓機器上線所需的權限](../automation-role-based-access-control.md#onboarding)，嘗試讓解決方案再次上線。 如果您收到錯誤`The solution cannot be enabled on this VM because the permission to read the workspace is missing`, 請確定您`Microsoft.OperationalInsights/workspaces/read`有權能夠找出 VM 是否已上架至工作區。

### <a name="diagnostic-logging"></a>案例：上架失敗並出現訊息-無法設定自動化帳戶進行診斷記錄

#### <a name="issue"></a>問題

當您嘗試讓虛擬機器在解決方案上線時，您會收到下列訊息：

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>原因

如果定價層不符合訂用帳戶的計費模型, 可能會導致此錯誤。 如需詳細資訊, 請參閱[Azure 監視器中的監視使用量和估計成本](https://aka.ms/PricingTierWarning)。

#### <a name="resolution"></a>解決方法

手動建立您的 Log Analytics 工作區, 並重複上執行緒序以選取所建立的工作區。

### <a name="computer-group-query-format-error"></a>案例：ComputerGroupQueryFormatError

#### <a name="issue"></a>問題

這個錯誤碼表示未正確地將用來將目標設定為解決方案的已儲存搜尋電腦群組查詢格式化。 

#### <a name="cause"></a>原因

您可能已變更查詢，或者系統可能已變更該查詢。

#### <a name="resolution"></a>解決方法

您可以刪除此解決方案的查詢，然後將解決方案重新上線，這樣會重新建立查詢。 您可以在工作區內的 [儲存的搜尋] 下方找到該查詢。 查詢的名稱是 **MicrosoftDefaultComputerGroup**，而查詢的類別是與這個查詢相關聯的解決方案名稱。 如果已啟用多個解決方案，**MicrosoftDefaultComputerGroup** 會在 [儲存的搜尋] 下方多次顯示。

### <a name="policy-violation"></a>案例：PolicyViolation

#### <a name="issue"></a>問題

這個錯誤碼表示部署因違反一個或多個原則而失敗。

#### <a name="cause"></a>原因 

有一個原則導致作業無法完成。

#### <a name="resolution"></a>解決方法

若要成功部署解決方案，您必須考量改變指定的原則。 由於可定義的原則有許多不同類型，因此所需的特定變更就要根據違反的原則來決定。 例如，如果在定義原則的資源群組中，拒絕對於該資源群組內特定類型資源之內容的變更權限，您可以執行下列任何一項作業：

* 完全移除原則。
* 嘗試上架到不同的資源群組。
* 透過下列方法修改原則，例如：
  * 將原則的目標重新設定為特定資源 (例如設定為特定的自動化帳戶)。
  * 對於原則已設定為拒絕的資源集合進行修改。

檢查 Azure 入口網站右上角的通知, 或流覽至包含您自動化帳戶的資源群組, 然後選取 [**設定**] 底下的 [**部署**] 以查看失敗的部署。 若要深入了解 Azure 原則，請造訪：[Azure 原則的概觀](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)。

### <a name="unlink"></a>案例：嘗試取消連結工作區時發生錯誤

#### <a name="issue"></a>問題

當您嘗試取消連結工作區時, 收到下列錯誤:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>原因

當您的 Log Analytics 工作區中仍有作用中的解決方案, 而這取決於您要連結的自動化帳戶和記錄 Analytics 工作區時, 就會發生此錯誤。

### <a name="resolution"></a>解決方法

若要解決此問題, 您必須從工作區中移除下列解決方案 (如果您使用它們):

* 更新管理
* 變更追蹤
* 於下班時間開始/停止 VM

移除解決方案後, 您就可以取消連結您的工作區。 請務必從您的工作區和自動化帳戶清除這些解決方案中的任何現有成品。  

* 更新管理
  * 從您的自動化帳戶移除更新部署 (排程)
* 於下班時間開始/停止 VM
  * 在 [**設定** > ] [**鎖定**] 底下的自動化帳戶中, 移除解決方案元件的任何鎖定。
  * 如需在下班時間解決方案中移除啟動/停止 Vm 的其他步驟, 請參閱[在離峰期間移除啟動/停止 vm 解決方案](../automation-solution-vm-management.md##remove-the-solution)。

## <a name="mma-extension-failures"></a>MMA 延伸模組失敗

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

部署解決方案時，會部署各種相關的資源。 其中一個資源是「Microsoft Monitoring Agent 延伸模組」或「適用於 Linux 的 Log Analytics 代理程式」。 這些是虛擬機器的來賓代理程式所安裝的虛擬機器擴充功能, 負責與已設定的 Log Analytics 工作區通訊, 以供日後協調下載二進位檔和其他檔案您正在上架的解決方案會在開始執行時依賴。
您通常最先從出現在「通知中樞」中的通知察覺 MMA 或「適用於 Linux 的 Log Analytics 代理程式」安裝失敗。 按一下通知可提供特定失敗的進一步資訊。 巡覽至資源群組資源，再到其中的部署項目，也會提供所發生部署失敗的詳細資料。
MMA 或「適用於 Linux 的 Log Analytics 代理程式」可能因各種原因而安裝失敗，解決這些失敗所需採取的步驟會因問題而有所不同。 以下是特定疑難排解步驟。

下一節說明當上架時, 您可能會遇到的各種問題, 這會導致 MMA 延伸模組的部署失敗。

### <a name="webclient-exception"></a>案例：在 WebClient 要求期間發生例外狀況

虛擬機器上的 MMA 延伸模組無法與外部資源通訊且部署會失敗。

#### <a name="issue"></a>問題

以下是傳回的錯誤訊息範例：

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>原因

此錯誤的一些可能原因包括：

* VM 中已設定 proxy, 只允許特定的埠。

* 防火牆設定已封鎖對必要連接埠和位址的存取。

#### <a name="resolution"></a>解決方法

確定您已開啟適當的連接埠和位址進行通訊。 如需連接埠和位址清單，請參閱[規劃您的網路](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="transient-environment-issue"></a>案例：因為暫時性環境問題, 所以安裝失敗

因為另一個安裝或動作封鎖了安裝, 所以在部署期間安裝 Microsoft Monitoring Agent 擴充功能失敗

#### <a name="issue"></a>問題

以下是可能傳回的錯誤訊息範例：

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>原因

此錯誤的一些可能原因包括：

* 其他安裝正在進行
* 系統會在範本部署期間觸發以重新開機

#### <a name="resolution"></a>解決方法

此錯誤本質上是暫時性錯誤。 請重試部署以安裝延伸模組。

### <a name="installation-timeout"></a>案例：安裝逾時

MMA 延伸模組的安裝未完成, 因為有超時時間。

#### <a name="issue"></a>問題

下列範例是可能傳回的錯誤訊息:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>原因

發生此錯誤的原因是虛擬機器在安裝期間負載過重。

### <a name="resolution"></a>解決方法

請在 VM 負載較低時，嘗試安裝 MMA 延伸模組。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答
* 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
* 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
