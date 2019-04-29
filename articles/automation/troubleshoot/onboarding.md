---
title: 針對將更新管理、變更追蹤和清查上線時的錯誤進行疑難排解
description: 了解如何針對將更新管理、變更追蹤和清查解決方案上線時的錯誤進行疑難排解
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: eaafee304f606ae4d511a6cea1824c26db838635
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119124"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>針對將解決方案上線時的錯誤進行疑難排解

當您將更新管理或變更追蹤和清查等解決方案上線時，可能會發生錯誤。 本文描述可能發生的各種錯誤及解決方法。

## <a name="general-errors"></a>一般錯誤

### <a name="missing-write-permissions"></a>案例：上線失敗並顯示以下訊息：無法啟用解決方案

#### <a name="issue"></a>問題

您會收到下列訊息之一當您嘗試上架至方案的虛擬機器：

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>原因

此錯誤被因不正確或遺漏權限在虛擬機器，工作區，或使用者。

#### <a name="resolution"></a>解決方案

確定您有正確的權限可讓虛擬機器上線。 檢閱[讓機器上線所需的權限](../automation-role-based-access-control.md#onboarding)，嘗試讓解決方案再次上線。 如果您收到錯誤`The solution cannot be enabled on this VM because the permission to read the workspace is missing`，請確定您有`Microsoft.OperationalInsights/workspaces/read`能夠找出 VM 是否為上的架到工作區的權限。

### <a name="computer-group-query-format-error"></a>案例：ComputerGroupQueryFormatError

#### <a name="issue"></a>問題

這個錯誤碼表示未正確地將用來將目標設定為解決方案的已儲存搜尋電腦群組查詢格式化。 

#### <a name="cause"></a>原因

您可能已變更查詢，或者系統可能已變更該查詢。

#### <a name="resolution"></a>解決方案

您可以刪除此解決方案的查詢，然後將解決方案重新上線，這樣會重新建立查詢。 您可以在工作區內的 [儲存的搜尋] 下方找到該查詢。 查詢的名稱是 **MicrosoftDefaultComputerGroup**，而查詢的類別是與這個查詢相關聯的解決方案名稱。 如果已啟用多個解決方案，**MicrosoftDefaultComputerGroup** 會在 [儲存的搜尋] 下方多次顯示。

### <a name="policy-violation"></a>案例：PolicyViolation

#### <a name="issue"></a>問題

這個錯誤碼表示部署因違反一個或多個原則而失敗。

#### <a name="cause"></a>原因 

有一個原則導致作業無法完成。

#### <a name="resolution"></a>解決方案

若要成功部署解決方案，您必須考量改變指定的原則。 由於可定義的原則有許多不同類型，因此所需的特定變更就要根據違反的原則來決定。 例如，如果在定義原則的資源群組中，拒絕對於該資源群組內特定類型資源之內容的變更權限，您可以執行下列任何一項作業：

* 完全移除原則。
* 嘗試上架到不同的資源群組。
* 透過下列方法修改原則，例如：
  * 將原則的目標重新設定為特定資源 (例如設定為特定的自動化帳戶)。
  * 對於原則已設定為拒絕的資源集合進行修改。

檢查 Azure 入口網站中右上角的通知，或瀏覽至包含您的自動化帳戶並選取資源群組**部署**下方**設定**檢視失敗部署。 若要深入了解 Azure 原則，請造訪：[Azure 原則的概觀](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)。

## <a name="mma-extension-failures"></a>MMA 延伸模組失敗

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

部署解決方案時，會部署各種相關的資源。 其中一個資源是「Microsoft Monitoring Agent 延伸模組」或「適用於 Linux 的 Log Analytics 代理程式」。 這些是由負責設定的 Log Analytics 工作區，以更新版本的協調的二進位檔的下載與通訊的虛擬機器的客體代理程式安裝的虛擬機器擴充功能和其他檔案，您是解決方案上架取決於，一旦開始執行。
您通常最先從出現在「通知中樞」中的通知察覺 MMA 或「適用於 Linux 的 Log Analytics 代理程式」安裝失敗。 按一下通知可提供特定失敗的進一步資訊。 巡覽至資源群組資源，再到其中的部署項目，也會提供所發生部署失敗的詳細資料。
MMA 或「適用於 Linux 的 Log Analytics 代理程式」可能因各種原因而安裝失敗，解決這些失敗所需採取的步驟會因問題而有所不同。 以下是特定疑難排解步驟。

下節說明您可能會遇到登入，會造成失敗的 MMA 延伸模組部署時的各種問題。

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

* 沒有設定在 VM 中，只允許特定連接埠的 proxy。

* 防火牆設定已封鎖對必要連接埠和位址的存取。

#### <a name="resolution"></a>解決方案

確定您已開啟適當的連接埠和位址進行通訊。 如需連接埠和位址清單，請參閱[規劃您的網路](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="transient-environment-issue"></a>案例：安裝失敗，因為暫時性的環境問題

安裝 Microsoft Monitoring Agent 擴充功能無法在部署期間，因為另一個安裝或封鎖安裝的動作

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
* 系統會觸發在範本部署期間重新啟動

#### <a name="resolution"></a>解決方案

此錯誤本質上是暫時性錯誤。 請重試部署以安裝延伸模組。

### <a name="installation-timeout"></a>案例：安裝逾時

安裝 MMA 擴充功能未完成因為逾時。

#### <a name="issue"></a>問題

下列範例是可能傳回的錯誤訊息：

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>原因

發生這個錯誤是因為在安裝期間在負載過重的虛擬機器。

### <a name="resolution"></a>解決方案

請在 VM 負載較低時，嘗試安裝 MMA 延伸模組。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答
* 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
* 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
