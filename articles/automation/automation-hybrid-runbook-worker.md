---
title: Azure 自動化混合式 Runbook 背景工作
description: 本文提供有關安裝和使用混合式 Runbook 背景工作角色的相關資訊，它是 Azure 自動化的一項功能，可讓您在本機資料中心內或雲端提供者的機器上執行 Runbook。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 270990d5dea53c2467bf2d7df4695a14b3dbec8c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194616"
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>使用混合式 Runbook 背景工作角色將資料中心內或雲端的資源自動化

Azure 自動化中的 Runbook 可能無法存取其他雲端或內部部署環境中的資源，因為這些資源是在 Azure 雲端中執行。 Azure 自動化的混合式 Runbook 背景工作角色功能可讓您直接在裝載角色的電腦上，以及針對環境中的資源執行 Runbook，從而管理這些本機資源。 Runbook 會儲存並在 Azure 自動化中管理，接著傳遞至一或多個指定的電腦。

下圖說明這項功能：

![混合式 Runbook 背景工作概觀](media/automation-hybrid-runbook-worker/automation.png)

## <a name="hybrid-runbook-worker-groups"></a>混合式 Runbook 背景工作群組

每一個混合式 Runbook 背景工作是您安裝代理程式時指定的混合式 Runbook 背景工作群組的成員。 群組可包含單一代理程式，但您可以在群組中安裝多個代理程式以獲得高可用性。

在 Hybrid Runbook Worker 上啟動 Runbook 時，您會指定要執行它的群組。 每個群組中的背景工作角色會對 Azure 自動化進行輪詢，以查看是否有任何可用的作業。 若有可用的作業，便由第一個取得該作業的背景工作角色負責。 您無法指定特定的背景工作。

## <a name="installing-a-hybrid-runbook-worker"></a>安裝混合式 Runbook 背景工作角色

依據作業系統的不同，混合式 Runbook 背景工作角色的安裝流程也會有所不同。 下表內有連結，可連至混合式 Runbook 工作角色的不同安裝方式。 安裝和設定 Windows 混合式 Runbook 背景工作角色有兩種可用的方法。 建議的方法是使用自動化 Runbook，將設定 Windows 電腦所需的程序完全自動化。 第二種方法採取逐步程序來手動安裝和設定角色。 若為 Linux 機器，您可以執行 Python 指令碼，在機器上安裝代理程式

|作業系統  |部署類型  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[手動](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-linux-hybrid-runbook-worker)        |

> [!NOTE]
> 若要透過「預期狀態設定」(DSC) 管理支援 Hybrid Runbook Worker 之伺服器的設定，您需要將它們新增為 DSC 節點。 如需有關讓它們上線以透過 DSC 進行管理的詳細資訊，請參閱[讓機器上線以透過 Azure 自動化 DSC 進行管理](automation-dsc-onboarding.md)。
>
>如果您啟用[更新管理解決方案](automation-update-management.md)，則任何連線到 Log Analytics 工作區的電腦都會自動設定為混合式 Runbook 背景工作角色，以支援此解決方案所包含的 Runbook。 不過，它不會向您已在自動化帳戶中定義的任何 Hybrid Worker 群組註冊。 您可以將電腦新增到您「自動化」帳戶中的 Hybrid Runbook Worker 群組來支援「自動化」Runbook，只要解決方案和 Hybrid Runbook Worker 群組成員資格兩者所用的帳戶相同即可。 此功能已新增至 Hybrid Runbook Worker 7.2.12024.0 版。

開始部署混合式 Runbook 背景工作角色之前，請先檢閱[規劃網路的相關資訊](#network-planning)。 在您成功部署 Runbook 背景工作角色後，請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化。

## <a name="removing-hybrid-runbook-worker"></a>移除 Hybrid Runbook Worker

您可以移除群組中的一或多個 Hybrid Runbook 背景工作角色，或移除該群組，視您的需求而定。 若要從內部部署電腦中移除混合式 Runbook 背景工作角色，請執行下列步驟：

1. 在 Azure 入口網站中，瀏覽至您的自動化帳戶。
2. 從 [設定] 刀鋒視窗中，選取 [金鑰]，記下欄位 [URL] 和 [主要存取金鑰] 的值。 下一個步驟需要此資訊。

### <a name="windows"></a>Windows

在系統管理員模式中開啟 PowerShell 工作階段，並執行下列命令。 使用 **-Verbose** 參數可取得移除程序的詳細記錄。

```powershell
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

若要從混合式背景工作角色群組中移除過時的機器，請使用選擇性的 `machineName` 參數。

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> 這不會移除電腦上的 Microsoft Monitoring Agent，只會移除 Hybrid Runbook 背景工作角色的功能和組態。

## <a name="remove-hybrid-worker-groups"></a>移除混合式背景工作角色群組

若要移除群組，您必須先使用先前所示的程序，從群組的每一部成員電腦中移除 Hybrid Runbook Worker，然後執行下列步驟移除群組。

1. 在 Azure 入口網站中，開啟自動化帳戶。
1. 在 [程序自動化] 底下選取 [混合式背景工作角色群組]。 選取您想要刪除的群組。 選取特定的群組之後，[混合式背景工作角色群組] 屬性頁面隨即出現。

   ![[混合式 Runbook 背景工作角色群組] 頁面](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. 在所選群組的屬性頁面中，按一下 [刪除]。 將會出現一則訊息要求您確認此動作，如果您確定要繼續，請選取 [是]。

   ![刪除群組確認對話方塊](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   此程序需要幾秒鐘才能完成，您可以在功能表的 [通知] 底下追蹤其進度。

## <a name="network-planning"></a>設定您的網路

### <a name="hybrid-worker-role"></a>混合式背景工作角色

若要讓混合式 Runbook 背景工作角色連線至 Log Analytics 並向其註冊，它必須能夠存取本節所述的連接埠號碼和 URL。 此外，這是 [Microsoft Monitoring Agent 連線至 Log Analytics 所需的連接埠和 URL](../log-analytics/log-analytics-agent-windows.md)。

如果您使用 Proxy 伺服器在代理程式和 Log Analytics 服務之間進行通訊，請確保可以存取適當的資源。 如果您使用防火牆來限制網際網路存取，您必須設定防火牆以允許存取。

若要讓混合式 Runbook 背景工作角色與自動化進行通訊，需要下列連接埠和 URL：

* 連接埠︰只需要 TCP 443 以便進行傳出網際網路存取。
* 全域 URL：*.azure-automation.net
* US Gov 維吉尼亞州的全域 URL： *.azure automation.us
* 代理程式服務：https://\<workspaceId\>.agentsvc.azure-automation.net

如果您有針對特定區域定義的自動化帳戶，您可以將通訊限制為該區域資料中心。 下表提供每個區域的 DNS 記錄。

| **區域** | **DNS 記錄** |
| --- | --- |
| 美國中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 美國中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 美國東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 西歐 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 北歐 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 東南亞 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 日本東部 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 澳大利亞東南部 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 英國南部 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| 美國政府維吉尼亞州 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

如需區域 IP 位址 (而非名稱) 的清單，請從「Microsoft 下載中心」下載 [Azure 資料中心 IP 位址](https://www.microsoft.com/download/details.aspx?id=41653) XML 檔案。

> [!NOTE]
> Azure 資料中心 IP 位址 XML 檔會列出在 Microsoft Azure 資料中心使用的 IP 位址範圍。 計算、SQL 和儲存體範圍會包含在檔案中。
>
>每週會公佈已更新的檔案。 檔案會反映目前已部署的範圍及任何即將進行的 IP 範圍變更。 出現在檔案中的新範圍至少有一週的時間不會在資料中心中使用。
>
> 最好是每週下載新的 XML 檔案。 接著，更新您的網站以便正確地識別在 Azure 中執行的服務。 Azure ExpressRoute 使用者應該注意到，在每個月的第一週，此檔案會用來更新 Azure 空間的邊界閘道協定 (BGP) 公告。

### <a name="update-management"></a>更新管理

除了混合式 Runbook 背景工作角色所需的標準位址與連接埠之外，也需要下列位址 (尤其在更新管理時)。 這些位址的通訊皆經由連接埠 443 進行。

|Azure 公用  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="troubleshooting"></a>疑難排解

混合式 Runbook 背景工作角色取決於與自動化帳戶通訊的代理程式，來註冊背景工作角色、接收 Runbook 作業及報告狀態。 Windows 的代理程式為 Microsoft Monitoring Agent。 Linux 的代理程式為適用於 Linux 的 OMS 代理程式。 如果註冊背景工作角色失敗，請參考以下一些可能的錯誤原因：

### <a name="the-hybrid-worker-is-behind-a-proxy-or-firewall"></a>Hybrid Worker 位於 Proxy 或防火牆後方

確認電腦可透過連接埠 443 輸出存取 *.azure-automation.net。

### <a name="the-computer-the-hybrid-worker-is-running-on-has-less-than-the-minimum-hardware-requirements"></a>混合式背景工作角色執行所在的電腦未滿足最低硬體需求

執行 Hybrid Runbook Worker 的電腦應滿足最低硬體需求，您才能指派它來裝載這項功能。 否則，根據其他背景處理序的資源使用率和 Runbook 在執行期間造成的競爭，電腦會變得過度使用，並導致 Runbook 作業延遲或逾時。

確認指派來執行混合式 Runbook 背景工作角色功能的電腦滿足最低硬體需求。 如果滿足最低硬體需求，請監視 CPU 和記憶體使用率，判斷 Hybrid Runbook Worker 處理序之效能和 Windows 之間是否有任何相互關聯。 如果有記憶體或 CPU 壓力，這表示電腦可能需要升級或增加額外處理器，或增加記憶體來解決資源瓶頸及解決錯誤。 您也可以選擇其他可支援最低需求，也能在工作負載需求指出需要增加資源時擴充的計算資源。

如需特定作業系統的疑難排解相關資訊，請參閱 [Linux 的混合式 Runbook 背景工作角色](automation-linux-hrw-install.md#troubleshooting) 或 [Windows 的混合式 Runbook 背景工作角色](automation-windows-hrw-install.md#troubleshooting)

## <a name="next-steps"></a>後續步驟

請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化。
