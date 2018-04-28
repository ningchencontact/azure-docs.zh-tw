---
title: Azure 中的更新管理解決方案
description: 本文旨在協助您了解，如何使用這個方案管理 Windows 和 Linux 電腦的更新。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/05/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 2c54435d893753306e903c0851e319fc3d1621b1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="update-management-solution-in-azure"></a>Azure 中的更新管理解決方案

Azure 自動化中的「更新管理」解決方案，可讓您管理 Azure 中所部署 Windows 和 Linux 電腦的作業系統安全性更新、內部部署環境或其他雲端提供者。 您可以快速評估所有代理程式電腦上可用更新的狀態，並管理為伺服器安裝必要更新的程序。

您可以直接從您的 [Azure 自動化](automation-offering-get-started.md)帳戶啟用虛擬機器的更新管理。
若要了解如何從您的自動化帳戶啟用虛擬機器的更新管理，請參閱[管理多部虛擬機器的更新](manage-update-multi.md)。

## <a name="solution-overview"></a>解決方案概觀

「更新管理」所管理的電腦會使用下列各項來執行評估和更新部署︰

* 適用於 Windows 或 Linux 的 Microsoft Monitoring Agent
* 適用於 Linux 的 PowerShell 預期狀態組態 (DSC)
* 自動化 Hybrid Runbook Worker
* 適用於 Windows 電腦的 Microsoft Update 或 Windows Server Update Services

下列圖表顯示行為和資料流程的概念性檢視，說明解決方案如何評估安全性更新並將及套用至工作區中所有連線的 Windows Server 和 Linux 電腦。

![更新管理程序流程](media/automation-update-management/update-mgmt-updateworkflow.png)

在電腦執行更新合規性掃描之後，代理程式會將大量資訊轉送至 Log Analytics。 在 Windows 電腦上，合規性掃描預設會每 12 小時執行一次。 除了掃描排程，如果在更新安裝之前與更新安裝之後重新啟動 Microsoft Monitoring Agent (MMA)，則會在 15 分鐘內起始更新合規性掃描。 在 Linux 電腦上，合規性掃描預設會每 3 小時執行一次，而如果重新啟動 MMA 代理程式，則會在 15 分鐘內起始相容性掃描。

解決方案會根據您設定要同步處理的來源，報告電腦的最新狀態。 如果 Windows 電腦設定為向 WSUS 報告，則視 WSUS 上次與 Microsoft Update 同步處理的時間，結果可能不同於 Microsoft Update 所顯示的結果。 對於設定為向本機存放庫和公用存放庫報告的 Linux 電腦，同樣也是如此。

您可以藉由建立排定的部署，在需要更新的電腦上部署和安裝軟體更新。 歸類為「選擇性」的更新不會包含在 Windows 電腦的部署範圍內，只需要更新。 排定的部署會藉由明確指定電腦，或選取以一組特定電腦之記錄搜尋為基礎的[電腦群組](../log-analytics/log-analytics-computer-groups.md)，定義哪些目標電腦將會收到適用的更新。 您也可以指定核准排程，並指定允許安裝更新的一段時間。 在 Azure 自動化中，會由 Runbook 安裝更新。 您無法檢視這些 Runbook，而它們也不需要任何設定。 更新部署在建立後便會建立排程，以在指定的時間為所包含的電腦啟動主要更新 Runbook。 這個主要 Runbook 會在每個代理程式上啟動子 Runbook，以安裝必要的更新。

在更新部署中指定的日期和時間，目標電腦會以平行方式執行部署。 系統會先執行掃描，以確認安裝仍然需要並加以安裝。 對於 WSUS 用戶端電腦而言，如果未在 WSUS 中核准更新，則更新部署會失敗。

## <a name="clients"></a>用戶端

### <a name="supported-client-types"></a>支援的用戶端類型

下表列出支援的作業系統： 

|作業系統  |注意  |
|---------|---------|
|Windows Server 2008、Windows Server 2008 R2 RTM    | 僅支援更新評估         |
|Windows Server 2008 R2 SP1 和更新版本     |需要 Windows PowerShell 4.0 或更新版本 ([下載 WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))。<br> 建議使用 Windows PowerShell 5.1 ([下載 WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)) 以增加可靠性。         |
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理程式必須能夠存取更新存放庫。        |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理程式必須能夠存取更新存放庫。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理程式必須能夠存取更新存放庫。        |
|Ubuntu 12.04 LTS 和更新的 x86/x64       |Linux 代理程式必須能夠存取更新存放庫。         |

### <a name="unsupported-client-types"></a>不支援的用戶端類型

下表列出不支援的作業系統：

|作業系統  |注意  |
|---------|---------|
|Windows 用戶端     | 不支援用戶端作業系統 (Windows 7、Windows 10 等)。        |
|Windows Server 2016 Nano Server     | 不支援       |

### <a name="client-requirements"></a>用戶端需求

#### <a name="windows"></a>Windows

Windows 代理程式必須設定為可與 Windows Server Update Services (WSUS) 伺服器通訊，或必須能夠存取 Microsoft Update。 更新管理可與 Sytem Center Configuration Manager 搭配使用，如需深入了解整合案例，請造訪[將 System Center Configuration Manager 與更新管理整合](oms-solution-updatemgmt-sccmintegration.md#configuration)。 需要 [Windows 代理程式](../log-analytics/log-analytics-agent-windows.md)。 此代理程式會在您登入 Azure VM 時自動安裝。

#### <a name="linux"></a>Linux

Linux 的機器必須能夠存取更新存放庫 (可以是私人或公用的)。 此解決方案不支援適用於 Linux 且設定為向多個 Log Analytics 工作區報告的 OMS 代理程式。

如需關於如何安裝適用於 Linux 的 OMS 代理程式及下載最新版本的詳細資訊，請參閱[適用於 Linux 的 Operations Management Suite 代理程式](https://github.com/microsoft/oms-agent-for-linux)。 如需有關如何安裝適用於 Windows 的 OMS 代理程式，請檢閱[適用於 Windows 的 Operations Management Suite 代理程式](../log-analytics/log-analytics-windows-agent.md)。

## <a name="permissions"></a>權限

若要建立及管理更新部署，您必須具有特定權限。 若要深入了解這些權限，請瀏覽[角色型存取 - 更新管理](automation-role-based-access-control.md#update-management)

## <a name="solution-components"></a>方案元件

此解決方案包含下列已新增到自動化帳戶的資源，以及直接連線的代理程式或 Operations Manager 連線的管理群組。

### <a name="hybrid-worker-groups"></a>混合式背景工作群組

啟用此解決方案之後，任何直接連線到 Log Analytics 工作區的 Windows 電腦都會自動設定為 Hybrid Runbook Worker，以支援此解決方案中所包含的 Runbook。 對於此解決方案管理的每部 Windows 電腦，它會遵循 Hostname FQDN_GUID 命名慣例，並且列在自動化帳戶的 [混合式背景工作群組] 頁面下，作為系統混合式背景工作群組。 您不能讓這些群組以您帳戶中的 Runbook 為目標，否則它們會失敗。 這些群組只為了支援管理解決方案。

然而，您可以將 Windows 電腦新增到自動化帳戶中的 Hybrid Runbook Worker 群組來支援自動化 Runbook，只要解決方案和 Hybrid Runbook Worker 群組成員資格兩者所用的帳戶相同即可。 此功能已新增至 Hybrid Runbook Worker 7.2.12024.0 版。

### <a name="management-packs"></a>管理組件

如果 System Center Operations Manager 管理群組已連線到 Log Analytics 工作區，則下列管理組件會安裝在 Operations Manager 中。 新增此解決方案之後，這些管理組件也會安裝在直接連線的 Windows 電腦上。 這些管理組件不需要進行任何設定或管理。

* Microsoft System Center Advisor 更新評估智慧套件 (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新部署 MP

如需有關方案管理組件如何更新的詳細資訊，請參閱 [將 Operations Manager 連接到 Log Analytics](../log-analytics/log-analytics-om-agents.md)。

### <a name="confirm-non-azure-machines-are-onboarded"></a>確認非 Azure 機器已登入

若要確認直接連線的機器正與 Log Analytics 通訊，您可以在幾分鐘之後執行下列記錄搜尋︰

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table`
```

在 Windows 電腦上，您可以檢閱下列各項來確認與 Log Analytics 的代理程式連線能力︰

1. 在控制台中開啟 Microsoft Monitoring Agent，而代理程式會在 [Azure Log Analytics] 索引標籤上顯示以下訊息︰**Microsoft Monitoring Agent 已成功連線到 Log Analytics**。   
2. 開啟 [Windows 事件記錄]，瀏覽至 [應用程式及服務記錄\Operations Manager] 並從來源服務連接器搜尋事件識別碼 3000 和 5002。 這些事件表示電腦已向 Log Analytics 工作區註冊，並且正在接收組態。

如果代理程式無法與 Log Analytics 通訊，並已設定為透過防火牆或 Proxy 伺服器來與網際網路通訊，請藉由檢閱 [Windows 代理程式的網路設定](../log-analytics/log-analytics-agent-windows.md)或 [Linux 代理程式的網路設定](../log-analytics/log-analytics-agent-linux.md)，確認防火牆或 Proxy 伺服器設定正確。

> [!NOTE]
> 如果您的 Linux 系統是設定為與 proxy 或 OMS 閘道通訊，而且您要將此解決方案上架，請更新 proxy.conf 權限，並執行下列命令，將檔案讀取權限授予 omiuser 群組：`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

在執行評估之後，新增的 Linux 代理程式的狀態會顯示為 [已更新]。 此程序可能需要多達 6 小時的時間。

若要確認 Operations Manager 管理群組正在與 Log Analytics 通訊，請參閱[驗證 Operations Manager 與 Log Analytics 的整合](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-oms)。

## <a name="data-collection"></a>資料收集

### <a name="supported-agents"></a>支援的代理程式

下表描述此方案支援的連接來源。

| 連接的來源 | 支援 | 說明 |
| --- | --- | --- |
| Windows 代理程式 |yes |方案會從 Windows 代理程式收集系統更新的相關資訊，並起始必要更新的安裝。 |
| Linux 代理程式 |yes |解決方案會從 Linux 代理程式收集系統更新的相關資訊，並且在支援的散發套件上起始必要更新的安裝。 |
| Operations Manager 管理群組 |yes |方案會從所連線之管理群組中的代理程式收集系統更新的相關資訊。<br>Operations Manager 代理程式不需要直接連線到 Log Analytics。 資料會從管理群組轉送至 Log Analytics 工作區。 |

### <a name="collection-frequency"></a>收集頻率

對於每部受控 Windows 電腦，每天會掃描兩次。 系統會每隔 15 分鐘呼叫一次 Windows API 來查詢上次更新時間，以判斷狀態是否變更，若是如此，則會起始合規性掃描。 對於每部受控 Linux 電腦，每 3 個小時會掃描一次。

儀表板可能需要 30 分鐘以至 6 小時的時間，顯示來自受控電腦的已更新資料。

## <a name="viewing-update-assessments"></a>檢視更新評估

按一下您自動化帳戶上的 [更新管理]，以檢視您的機器狀態。

此檢視會提供您的機器、遺漏的更新、更新部署以及排定更新部署的相關資訊。

您可以選取清單中的項目以執行記錄搜尋，傳回機器、更新或部署的資訊。 這會開啟 [記錄搜尋] 頁面，並顯示所選項目的查詢。

## <a name="installing-updates"></a>安裝更新

工作區中的所有 Linux 和 Windows 電腦皆進行過更新評估後，您可以建立「更新部署」來安裝必要的更新。 更新部署會排定為一或多部電腦安裝必要的更新。 除了應包含在部署範圍中的電腦或電腦群組外，您還要指定部署的日期和時間。 若要深入瞭解電腦群組，請參閱 [Log Analytics 中的電腦群組](../log-analytics/log-analytics-computer-groups.md)。 當您將電腦群組納入更新部署時，只會在建立排程時評估一次群組成員資格。 系統不會反映群組的後續變更。 若要解決這個問題，請刪除排定的更新部署並予以重建。

> [!NOTE]
> 依預設，從 Azure Marketplace 部署的 Windows VM 會設定為從 Windows Update 服務接收自動更新。 將此解決方案或 Windows VM 新增至您的工作區之後，此行為不會改變。 如果您未主動管理此解決方案的更新，則會套用預設行為 (自動套用更新)。

若要避免在 Ubuntu 維護期間以外套用更新，請將自動安裝升級套件重新設定為停用自動更新。 如需設定方式的資訊，請參閱 [Ubuntu Server 指南中的自動更新主題](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)。

針對從 Azure Marketplace 所提供之隨選 Red Hat Enterprise Linux (RHEL) 映像建立的虛擬機器，系統會將其註冊以存取部署在 Azure 中的 [Red Hat Update Infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md)。 針對任何其他 Linux 發行版本，則必須從發行版本線上檔案存放庫，依照其支援的方法來更新這些發行版本。

## <a name="viewing-missing-updates"></a>檢視遺漏的更新

按一下 [遺漏的更新]，以檢視您的機器遺漏的更新清單。 畫面中會列出每個更新，並顯示需要更新的機器數目、作業系統和連結的相關資訊。 每個更新皆可供選取，且 [記錄搜尋] 頁面會顯示關於更新的詳細資訊。

## <a name="viewing-update-deployments"></a>檢視更新部署

按一下 [更新部署] 索引標籤，以檢視現有更新部署的清單。 按一下表格中的任一個更新部署，即會開啟該更新部署的 [更新部署執行] 頁面。

![更新部署結果的概觀](./media/automation-update-management/update-deployment-run.png)

## <a name="creating-an-update-deployment"></a>建立更新部署

按一下畫面頂端的 [排程更新部署] 按鈕以開啟 [新增更新部署] 頁面，可建立新的更新部署。 您必須為下表中的屬性提供值：

| 屬性 | 說明 |
| --- | --- |
| Name |用以識別更新部署的唯一名稱。 |
|作業系統| Linux 或 Windows|
| 要更新的機器 |選取已儲存的搜尋，或從下拉式清單中選擇 [機器]，然後選取個別的機器 |
|更新分類|選取您需要的所有更新分類|
|要排除的更新|輸入要排除不含 'KB' 前置詞的所有 KB|
|排程設定|選取開始時間，並選取 [一次] 或 [週期性] 以定期執行|
| 維護時間範圍 |為更新設定的分鐘數。 此值不可小於 30 分鐘，且不可超過 6 小時 |

## <a name="search-logs"></a>搜尋記錄

除了入口網站中提供的詳細資訊以外，您也可以對記錄執行搜尋。 在 [變更追蹤] 頁面開啟的情況下，按一下 [記錄分析]，[記錄搜尋] 頁面會隨即開啟

### <a name="sample-queries"></a>範例查詢

下表提供此解決方案所收集之更新記錄的記錄搜尋範例：

| 查詢 | 說明 |
| --- | --- |
|更新<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; project Computer, Title, KBID, Classification, PublishedDate |遺漏更新的所有電腦<br>新增下列其中一項以限制 OS：<br>OSType = "Windows"<br>OSType == "Linux" |
| 更新<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; where Computer == "ContosoVM1.contoso.com"<br>&#124; project Computer, Title, KBID, Product, PublishedDate |特定電腦的遺漏更新 (以您自己的電腦名稱取代此值)|
| Event<br>&#124; where EventLevelName == "error" and Computer in ((Update &#124; where (Classification == "Security Updates" or Classification == "Critical Updates")<br>&#124; where UpdateState == "Needed" and Optional == false <br>&#124; distinct Computer)) |遺漏必要重大更新或安全性更新之機器的錯誤事件 |
| 更新<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; distinct Title |所有電腦的不同遺漏更新 |
| UpdateRunProgress<br>&#124; where InstallationStatus == "failed" <br>&#124; summarize AggregatedValue = count() by Computer, Title, UpdateRunName |在更新執行時更新失敗的電腦<br>新增下列其中一項以限制 OS：<br>OSType = "Windows"<br>OSType == "Linux" |
| 更新<br>&#124; where OSType == "Linux"<br>&#124; where UpdateState != "Not needed" and (Classification == "Critical Updates" or Classification == "Security Updates")<br>&#124; summarize AggregatedValue = count() by Computer |有可以解決重大漏洞或安全性漏洞之可用套件更新的所有 Linux 機器清單 | 
| UpdateRunProgress<br>&#124; where UpdateRunName == "DeploymentName"<br>&#124; summarize AggregatedValue = count() by Computer|在此更新回合中更新的電腦 (以您的更新部署名稱取代此值) | 

## <a name="integrate-with-system-center-configuration-manager"></a>與 System Center Configuration Manager 進行整合

投資了 System Center Configuration Manager 以管理電腦、伺服器和行動裝置的客戶也需仰賴其管理軟體更新的強度和成熟度，作為軟體更新管理 (SUM) 週期的一部分。

若要了解如何將管理解決方案與 Sytem Center Configuration Manager 整合，請參閱[將 System Center Configuration Manager 與更新管理整合](oms-solution-updatemgmt-sccmintegration.md)。

## <a name="patching-linux-machines"></a>修補 Linux 機器

以下各節說明使用 Linux 修補的潛在問題。

### <a name="package-exclusion"></a>套件排除

在某些 Linux 版本上 (例如 Red Hat Enterprise Linux)，OS 層級升級可透過套件執行。 這可能會導致「更新管理」在 OS 版本號碼變更時執行。 由於「更新管理」會使用與系統管理員在本機 Linux 電腦上相同的方式來更新套件，因此這項行為是刻意的。

若要避免透過「更新管理」執行來更新 OS 版本，您應使用**排除**功能。

在 Red Hat Enterprise Linux 中，要排除的套件名稱將是：redhat-release-server.x86_64

![要為 Linux 排除的套件](./media/automation-update-management/linuxpatches.png)

### <a name="security-patches-not-being-applied"></a>未套用安全性修補程式

將更新部署至 Linux 機器時，您可以選取更新分類。 這樣會篩選出要對符合指定準則的項目套用的更新。 此篩選會在更新部署時套用至本機電腦上。 由於「更新管理」會在雲端中執行更新擴充，因此有些更新可能會在「更新管理」中標示為有安全性影響，但在本機電腦上則無此資訊。 因此，如果您將重大更新套用至 Linux 機器，可能會有某些更新 (未在該機器上標示為有安全性影響) 不會套用。 不過，「更新管理」仍可能將該機器報告為不相容的機器，因為它對於該更新還有其他相關資訊。

在 openSUSE Linux 上可能會因為使用的修補模型不相同而無法以更新分類部署更新。

## <a name="troubleshooting"></a>疑難排解

本節提供的資訊有助於排解更新管理解決方案的疑難問題。

如果您在嘗試將解決方案或虛擬機器上架時遇到問題，請檢查**應用程式和服務記錄\Operations Manager** 事件記錄中具有事件識別碼 4502 和事件訊息內含 **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** 的事件。 下表特別說明特定錯誤訊息及各自的可能解決方式。

| 訊息 | 原因 | 解決方法 |
|----------|----------|----------|
| 無法註冊電腦進行修補程式管理，<br>註冊失敗並發生例外狀況<br>System.InvalidOperationException：{"Message":"電腦已經<br>註冊至不同的帳戶。 "} | 電腦已經上架到另一個工作區進行更新管理 | [刪除混合式 Runbook 群組](automation-hybrid-runbook-worker.md#remove-hybrid-worker-groups)以執行舊構件的清除|
| 無法註冊電腦進行修補程式管理，<br>註冊失敗並發生例外狀況<br>System.Net.Http.HttpRequestException：傳送要求時發生錯誤。 ---><br>System.Net.WebException：基礎連線<br>已關閉：接收時發生<br>意外的錯誤。 ---> System.ComponentModel.Win32Exception：<br>用戶端和伺服器無法通訊，<br>因為它們沒有共同的演算法 | Proxy/閘道/防火牆封鎖通訊 | [檢閱網路需求](automation-offering-get-started.md#network-planning)|
| 無法註冊電腦進行修補程式管理，<br>註冊失敗並發生例外狀況<br>Newtonsoft.Json.JsonReaderException：剖析正無限值時發生錯誤。 | Proxy/閘道/防火牆封鎖通訊 | [檢閱網路需求](automation-offering-get-started.md#network-planning)|
| 服務 <wsid>.oms.opinsights.azure.com<br>所提供的憑證不是由 Microsoft 服務所用的<br>憑證授權單位發出。 連絡人<br>您的網路管理員，以查看它們是否正在執行可攔截 TLS/SSL 通訊的<br>Proxy。 |Proxy/閘道/防火牆封鎖通訊 | [檢閱網路需求](automation-offering-get-started.md#network-planning)|
| 無法註冊電腦進行修補程式管理，<br>註冊失敗並發生例外狀況<br>AgentService.HybridRegistration。<br>PowerShell.Certificates.CertificateCreationException：<br>無法建立自我簽署憑證。 ---><br>System.UnauthorizedAccessException：存取遭到拒絕。 | 自我簽署的憑證產生失敗 | 確認系統帳戶具有<br>以下資料夾的讀取權限：<br>**C:\ProgramData\Microsoft\**<br>** Crypto\RSA**|

## <a name="next-steps"></a>後續步驟

繼續進行本教學課程，以了解如何管理 Windows VM 的更新。

> [!div class="nextstepaction"]
> [管理 Azure Windows VM 的更新和修補程式](automation-tutorial-troubleshoot-changes.md)

* 使用 [Log Analytics](../log-analytics/log-analytics-log-searches.md) 中的記錄搜尋，檢視詳細的更新資料。
* 在偵測到電腦遺漏重大更新或電腦已停用自動更新時[建立警示](../log-analytics/log-analytics-alerts.md)。
