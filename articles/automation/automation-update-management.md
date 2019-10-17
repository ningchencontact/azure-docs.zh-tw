---
title: Azure 中的更新管理解決方案
description: 本文旨在協助您了解如何利用 Azure 更新管理解決方案來管理 Windows 和 Linux 電腦的更新。
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1751e8d67f59285d011df33a2d4d1d6d8abcec6a
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376039"
---
# <a name="update-management-solution-in-azure"></a>Azure 中的更新管理解決方案

您可以使用 Azure 自動化中的更新管理解決方案，在 Azure、內部部署環境或其他雲端提供者中管理 Windows 和 Linux 電腦的作業系統更新。 您可以快速評估所有代理程式電腦上可用更新的狀態，並管理為伺服器安裝必要更新的程序。

您可以直接從您的「Azure 自動化」帳戶啟用虛擬機器的「更新管理」。 若要了解如何從您的自動化帳戶啟用虛擬機器的「更新管理」，請參閱[管理多部虛擬機器的更新](manage-update-multi.md)。 您也可以至 Azure 入口網站的虛擬機器頁面，啟用虛擬機器的「更新管理」。 此案例適用於 [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) 與 [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) 虛擬機器。

> [!NOTE]
> 更新管理解決方案需要將 Log Analytics 工作區連結至您的自動化帳戶。 如需所支援區域的確切清單，請參閱[Azure 工作區](./how-to/region-mappings.md)對應。 區域對應並不會影響在不同于您的自動化帳戶的區域中管理虛擬機器的能力。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>解決方案概觀

「更新管理」所管理的電腦會使用下列設定來執行評估和更新部署：

* 適用於 Windows 或 Linux 的 Microsoft Monitoring Agent (MMA)
* 適用於 Linux 的 PowerShell 預期狀態組態 (DSC)
* 自動化 Hybrid Runbook Worker
* 適用於 Windows 電腦的 Microsoft Update 或 Windows Server Update Services (WSUS)

下列圖表顯示行為和資料流程的概念性檢視，說明解決方案如何評估安全性更新並將其套用至工作區中所有連線的 Windows Server 和 Linux 電腦：

![更新管理程序流程](./media/automation-update-management/update-mgmt-updateworkflow.png)

您可以使用更新管理在相同租用戶中的多個訂用帳戶中以原生方式上架機器。

發行套件之後，需要2-3 個小時，才會顯示修補程式以供 Linux 機器進行評估。 如果是 Windows 機器，則在發行後需要 12-15 小時的時間，才會顯示修補程式以供評量。

在電腦完成更新相容性掃描之後，代理程式會將大量資訊轉送至 Azure 監視器記錄。 在 Windows 電腦上，合規性掃描預設會每 12 小時執行一次。

除了掃描排程，如果在更新安裝之前與更新安裝之後重新啟動 MMA，則會在 15 分鐘內起始更新合規性掃描。

針對 Linux 電腦，預設會每小時執行合規性掃描。 若 MMA 代理程式重新啟動，則會在 15 分鐘內起始合規性掃描。

解決方案會根據您設定要同步處理的來源，回報電腦的最新狀態。 如果 Windows 電腦設定為向 WSUS 回報，則視 WSUS 上次與 Microsoft Update 同步處理的時間而定，結果可能與 Microsoft Update 所顯示的結果不同。 針對設定為向本機存放庫 (而非公用存放庫) 回報的 Linux 電腦，此行為也相同。

> [!NOTE]
> 為了能正確地向服務回報，「更新管理」需要啟用特定 URL 和連接埠。 若要深入了解這些需求，請參閱[混合式背景工作角色的網路規劃](automation-hybrid-runbook-worker.md#network-planning)。

您可以藉由建立排定的部署，在需要更新的電腦上部署和安裝軟體更新。 歸類為「選擇性」的更新不會包含在 Windows 電腦的部署範圍內。 部署範圍中僅包含必要更新。

排定的部署會藉由明確指定電腦，或選取以一組特定電腦的記錄搜尋或[Azure 查詢](automation-update-management-query-logs.md)為基礎的[電腦群組](../azure-monitor/platform/computer-groups.md)，來定義哪些目的電腦可以接收適用的更新。這會根據指定的準則動態選取 Azure Vm。 這些群組與[範圍](../azure-monitor/insights/solution-targeting.md)設定不同，只是用來判斷哪些機器取得可啟用解決方案的管理元件。

您也可以指定核准排程，並設定一段可安裝更新的期間。 這段時間稱為「維護期間」。 如果需要重新開機，而且您已選取適當的重新開機選項，維護時段的20分鐘會保留為重新啟動。 如果修補所花費的時間比預期還長，且維護期間內的時間少於20分鐘，將不會重新開機。

在 Azure 自動化中，會由 Runbook 安裝更新。 您無法檢視這些 Runbook，而且這些 Runbook 也不需要任何設定。 建立更新部署之後，更新部署會建立排程，以便在指定的時間內，針對包含的電腦啟動主要更新 Runbook。 主要 Runbook 會在每個代理程式上啟動子 Runbook，以安裝必要的更新。

到了在更新部署中指定的日期和時間時，目標電腦會以平行方式執行部署。 在安裝之前，系統會先執行掃描，以確認仍然需要更新。 針對 WSUS 用戶端電腦，若更新並未在 WSUS 中核准，則更新部署會失敗。

不支援在多個 Log Analytics 工作區（多路連接）中註冊更新管理的機器。

## <a name="clients"></a>用戶端

### <a name="supported-client-types"></a>支援的用戶端類型

下表顯示更新評估支援的作業系統清單。 修補需要混合式 Runbook 背景工作角色。 如需混合式 Runbook 背景工作角色需求的詳細資訊，請參閱適用于[WINDOWS HRW](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker)和 Linux 的安裝指南[HRW](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)。

|作業系統  |注意  |
|---------|---------|
|Windows Server 2019 （Datacenter/Datacenter Core/Standard）<br><br>Windows Server 2016 （Datacenter/Datacenter Core/Standard）<br><br>Windows Server 2012 R2 （Datacenter/Standard）<br><br>Windows Server 2012<br><br>Windows Server 2008 R2 （RTM 和 SP1 標準）||
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理程式必須能夠存取更新存放庫。 分類型修補需要 'yum' 才能傳回 CentOS 未內建的安全性資料。 如需 CentOS 上以分類為基礎之修補的詳細資訊，請參閱[Linux 上的更新分類](#linux-2)          |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理程式必須能夠存取更新存放庫。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理程式必須能夠存取更新存放庫。        |
|Ubuntu 14.04 LTS、16.04 LTS 和 18.04 (x86/x64)      |Linux 代理程式必須能夠存取更新存放庫。         |

> [!NOTE]
> 您可以使用更新管理來管理 Azure 虛擬機器擴展集。 更新管理適用于實例本身，而不是基底映射。 您將需要以累加方式排程更新，因為不會一次更新所有 VM 實例。
> 您可以遵循在[非 Azure 機器上架](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)底下的步驟來新增 VMSS 節點。

### <a name="unsupported-client-types"></a>不支援的用戶端類型

下表列出不支援的作業系統：

|作業系統  |注意  |
|---------|---------|
|Windows 用戶端     | 不支援用戶端作業系統 (例如 Windows 7 和 Windows 10)。        |
|Windows Server 2016 Nano Server     | 不支援。       |
|Azure Kubernetes Service 節點 | 不支援。 使用在[Azure Kubernetes Service 中將安全性和核心更新套用至 Linux 節點中](../aks/node-updates-kured.md)詳述的修補程式（AKS）|

### <a name="client-requirements"></a>用戶端需求

下列資訊描述 OS 特定的用戶端需求。  您也應該參閱[網路規劃](#ports)，以取得進一步的指引。

#### <a name="windows"></a>Windows

Windows 代理程式必須設定為可與 WSUS 伺服器通訊，或必須能夠存取 Microsoft Update。

您可以搭配 System Center Configuration Manager 使用「更新管理」。 若要深入了解整合案例，請參閱[整合 System Center Configuration Manager 與更新管理](oms-solution-updatemgmt-sccmintegration.md#configuration)。 需要 [Windows 代理程式](../azure-monitor/platform/agent-windows.md)。 此代理程式會在您讓 Azure 虛擬機器上線時自動安裝。

依預設，從 Azure Marketplace 部署的 Windows 虛擬機器會設定為從 Windows Update 服務接收自動更新。 當您新增這個解決方案或將 Windows 虛擬機器新增至您的工作區時，此行為並不會變更。 如果您未使用這個解決方案來主動管理更新，則會套用預設行為 (自動套用更新)。

> [!NOTE]
> 使用者可以修改群組原則，讓電腦重新開機只能由使用者執行，而不是由系統執行。 如果更新管理沒有重新開機電腦的許可權，而沒有使用者的手動互動，則受管理的機器可能會停滯。
>
> 如需詳細資訊，請參閱[設定自動更新的群組原則設定](https://docs.microsoft.com/en-us/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)。

#### <a name="linux"></a>Linux

針對 Linux，機器必須能夠存取更新存放庫。 更新存放庫可以是私人或公用。 必須使用 TLS 1.1 或 TLS 1.2，才能與更新管理互動。 此解決方案不支援已設定為向多個 Log Analytics 工作區回報的「適用於 Linux 的 Log Analytics 代理程式」。  電腦也必須安裝 Python 2.x。

如需有關如何安裝適用于 Linux 的 Log Analytics 代理程式及下載最新版本的詳細資訊，請參閱[適用于 linux 的 Log Analytics 代理程式](https://github.com/microsoft/oms-agent-for-linux)。 如需有關如何安裝適用于 Windows 的 Log Analytics 代理程式的詳細資訊，請參閱[適用于 windows 的 Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agent.md)。

需註冊從隨選 Red Hat Enterprise Linux (RHEL) 映像 (可在 Azure Marketplace 中找到) 建立的虛擬機器，以存取部署在 Azure 中的 [Red Hat Update Infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md)。 針對任何其他 Linux 發行版本，則必須從發行版本線上檔案存放庫，依照其支援的方法來更新。

## <a name="permissions"></a>使用權限

若要建立及管理更新部署，您必須具有特定權限。 若要了解這些權限，請參閱[角色型存取 - 更新管理](automation-role-based-access-control.md#update-management)。

## <a name="solution-components"></a>方案元件

解決方案包含下列資源。 資源會新增到您的自動化帳戶。 它們是直接連線的代理程式或是位於連線到 Operations Manager 的管理群組。

### <a name="hybrid-worker-groups"></a>混合式背景工作群組

啟用此解決方案之後，任何直接連線到 Log Analytics 工作區的 Windows 電腦都會自動設定為「混合式 Runbook 背景工作角色」，以支援此解決方案中所包含的 Runbook。

此解決方案管理的每部 Windows 電腦都會列在自動化帳戶的 [混合式背景工作角色群組] 窗格中，作為自動化帳戶的**系統混合式背景工作角色群組**。 解決方案會使用命名慣例 *Hostname FQDN_GUID*。 您不能讓這些群組以您帳戶中的 Runbook 為目標。 若您嘗試這樣做，它們會失敗。 這些群組只支援管理解決方案。

您可以將 Windows 電腦新增到自動化帳戶中的「混合式 Runbook 背景工作角色」群組來支援自動化 Runbook，只要解決方案和「混合式 Runbook 背景工作角色」群組成員資格兩者所用的帳戶相同即可。 此功能已新增至混合式 Runbook 背景工作角色 7.2.12024.0 版。

### <a name="management-packs"></a>管理組件

如果 System Center Operations Manager 管理群組已連線到 Log Analytics 工作區，則下列管理組件會安裝在 Operations Manager 中。 新增此解決方案之後，這些管理組件也會安裝在直接連線的 Windows 電腦上。 您不需要設定或管理這些管理組件。

* Microsoft System Center Advisor 更新評估智慧套件 (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新部署 MP

> [!NOTE]
> 如果您的 Operations Manager 1807 或2019管理群組中的代理程式是在管理群組層級設定為要與工作區相關聯，則要讓它們顯示的目前因應措施是在中覆寫**IsAutoRegistrationEnabled**為**True** **microsoft.intelligencepacks.applicationdependencymonitor. AzureAutomation. microsoft.enterprisemanagement.healthservice.azureautomation.hybridagent 事件. Init**規則。

如需有關方案管理元件如何更新的詳細資訊，請參閱[將 Operations Manager 連接到 Azure 監視器記錄](../azure-monitor/platform/om-agents.md)。

> [!NOTE]
> 就使用 Operations Manager 代理程式的系統而言，若要能夠完全受控於「更新管理」，則必須將代理程式更新為 Microsoft Monitoring Agent。 若要深入了解如何更新代理程式，請參閱[如何升級 Operations Manager 代理程式](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)。 針對使用 Operations Manager 的環境，您必須執行 System Center Operations Manager 2012 R2 UR 14 或更新版本。

## <a name="data-collection"></a>資料收集

### <a name="supported-agents"></a>支援的代理程式

下表描述此方案支援的連線來源：

| 連線的來源 | 支援的 | 描述 |
| --- | --- | --- |
| Windows 代理程式 |是 |解決方案會從 Windows 代理程式收集系統更新的相關資訊，然後起始必要更新的安裝。 |
| Linux 代理程式 |是 |解決方案會從 Linux 代理程式收集系統更新的相關資訊，然後在支援的發行版本上起始必要更新的安裝。 |
| Operations Manager 管理群組 |是 |方案會從所連線之管理群組中的代理程式收集系統更新的相關資訊。<br/>不需要從 Operations Manager 代理程式直接連接到 Azure 監視器記錄。 資料會從管理群組轉送至 Log Analytics 工作區。 |

### <a name="collection-frequency"></a>收集頻率

針對每部受控 Windows 電腦，每天會掃描兩次。 系統會每隔 15 分鐘呼叫一次 Windows API 來查詢上次更新時間，以判斷狀態是否已變更。 如果狀態已變更，則會起始合規性掃描。

每隔一小時就會針對每部受管理的 Linux 電腦執行掃描。

儀表板可能需要 30 分鐘到 6 小時，才能顯示來自受控電腦的已更新資料。

平均 Azure 監視器會使用更新管理記錄機器的資料使用量，每月大約 25 MB。 這只是近似值，它會根據您的環境而變更。 建議您監視您的環境，查看您的實際使用量。

## <a name="ports"></a>

以下為「更新管理」特別需求的位址。 與這些位址的通訊皆經由連接埠 443 進行。

|Azure 公用  |Azure 政府機構  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

對於 Windows 機器，您也必須允許流量傳送至 Windows Update 所需的任何端點。  您可以在[與 HTTP/Proxy 相關的問題](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中，找到所需端點的更新清單。 如果您有本機[Windows Update 伺服器](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)，您也必須允許流向[WSUS 金鑰](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)中所指定伺服器的流量。

對於 Red Hat Linux 電腦，請參閱[RHUI 內容傳遞伺服器的 ip](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers)以取得必要的端點。 如需其他 Linux 發行版本，請參閱提供者檔。

如需混合式 Runbook 背景工作角色所需連接埠的詳細資訊，請參閱[混合式背景工作角色連接埠](automation-hybrid-runbook-worker.md#hybrid-worker-role)。

建議使用定義例外狀況時所列出的位址。 針對 IP 位址，您可以下載 [Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 此檔案會每週更新，並反映目前已部署的範圍及任何即將進行的 IP 範圍變更。

依照[連接沒有網際網路存取的電腦](../azure-monitor/platform/gateway.md)中的指示，設定無法存取網際網路的電腦。

## <a name="view-update-assessments"></a>查看更新評量

在您的自動化帳戶中，選取 [更新管理] 來檢視機器的狀態。

此檢視會提供您的機器、缺少的更新、更新部署以及已排定之更新部署的相關資訊。 在 [合規性欄] 中，您可以看到機器上次的評估時間。 在 [更新代理程式整備程度] 欄中，您可以查看更新代理的健康情況。 如果發生問題，請選取連結來移至疑難排解文件，協助您了解應該執行哪些步驟來更正問題。

若要執行記錄搜尋來傳回與機器、更新或部署相關的資訊，請選取清單中的項目。 [記錄搜尋] 窗格隨即開啟，並顯示所選項目的查詢：

![更新管理的預設檢視](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>檢視缺少的更新

選取 [缺少的更新] 以檢視機器缺少的更新清單。 會列出每個更新，而且您可以選取更新。 畫面上會顯示需要更新的機器數目、作業系統以及一個能提供詳細資訊的連結。 [記錄搜尋] 窗格會顯示與更新有關的詳細資訊。

![缺少更新](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>更新分類

下表列出「更新管理」中的更新分類清單，以及每個分類的定義。

### <a name="windows"></a>Windows

|分類  |描述  |
|---------|---------|
|重大更新     | 特定問題的更新，負責處理與安全性無關的重大錯誤。        |
|安全性更新     | 特定產品的安全性相關更新。        |
|更新彙總套件     | 一組累計的 Hotfix，封裝在一起以便於部署。        |
|Feature Pack     | 在產品版本之外散發的新產品功能。        |
|Service Pack     | 一組套用到應用程式的累計 Hotfix。        |
|定義更新     | 病毒或其他定義檔案的更新。        |
|工具     | 有助於完成一或多個工作的公用程式或功能。        |
|更新     | 目前安裝之應用程式或檔案的更新。        |

### <a name="linux-2"></a>Linux

|分類  |描述  |
|---------|---------|
|重大更新和安全性更新     | 特定問題或特定產品的安全性相關問題的更新，         |
|其他更新     | 在性質上不重要或非安全性更新的所有其他更新。        |

針對 Linux，「更新管理」可以區分雲端中的重大更新和安全性更新，同時由於雲端中的資料擴充而顯示評估資料。 針對修補，「更新管理」仰賴機器上可用的分類資料。 與其他發行版本不同，CentOS 預設沒有此資訊。 如果您以某種方式將 CentOS 機器設定成傳回以下命令的安全性資料，則「更新管理」將能夠根據分類來進行修補。

```bash
sudo yum -q --security check-update
```

目前沒有支援的方法可以在 CentOS 上啟用原生分類資料可用性。 目前，只為那些已自行啟用此功能的客戶，提供盡力而為的支援。

## <a name="integrate-with-system-center-configuration-manager"></a>與 System Center Configuration Manager 進行整合

投資了 System Center Configuration Manager 來管理電腦、伺服器和行動裝置的客戶也需仰賴 Configuration Manager 的強度和成熟度，以協助他們管理軟體更新。 Configuration Manager 是其軟體更新管理 (SUM) 週期的一部分。

若要了解如何將管理解決方案與 Sytem Center Configuration Manager 整合，請參閱[將 System Center Configuration Manager 與更新管理整合](oms-solution-updatemgmt-sccmintegration.md)。

### <a name="third-party-patches-on-windows"></a>Windows 上的協力廠商修補程式

更新管理依賴本機設定的更新存放庫來修補支援的 Windows 系統。 這可能是 WSUS 或 Windows Update。 像 [System Center Updates Publisher](/sccm/sum/tools/updates-publisher) (Updates Publisher) 這樣的工具，允許您將自訂更新發佈至 WSUS。 此案例可讓更新管理利用協力廠商軟體，修補使用 System Center Configuration Manager 作為其更新存放庫的機器。 若要了解如何設定 Updates Publisher，請參閱[安裝Updates Publisher](/sccm/sum/tools/install-updates-publisher)。

## <a name="patch-linux-machines"></a>修補 Linux 機器

以下各節說明使用 Linux 修補的潛在問題。

### <a name="unexpected-os-level-upgrades"></a>未預期的 OS 層級升級

在某些 Linux 版本上 (例如 Red Hat Enterprise Linux)，安裝特定套件可能會造成 OS 層級升級。 這可能會導致「更新管理」在 OS 版本號碼變更時執行。 因為「更新管理」會使用與系統管理員在本機 Linux 電腦上相同的方式來更新套件，因此這個行為是刻意的。

若要避免透過「更新管理」執行來更新 OS 版本，請使用**排除**功能。

在 Red Hat Enterprise Linux 中，要排除的套件名稱將是：redhat-release-server.x86_64。

![要為 Linux 排除的套件](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>未套用重大/安全性修補程式

當您將更新部署至 Linux 機器時，可以選取更新分類。 這會篩選出要對符合指定準則的機器套用的更新。 此篩選會在更新部署時套用至本機電腦上。

由於更新管理會在雲端中執行更新擴充，因此有些更新可能會標示為具有安全性影響更新管理，即使本機電腦沒有該資訊也一樣。 因此，如果您將重大更新套用至 Linux 機器，某些未標示為在該機器上有安全性影響的更新，可能不會被套用。

不過，「更新管理」仍可能將該機器回報為不符合規範的機器，因為它對於該相關更新還有其他資訊。

CentOS 預設並不支援依更新分類來部署更新。 為了適當部署 CentOS 更新，請選取所有分類以確保套用所有更新。 針對 SUSE，只選取 [其他更新] 作為分類時，如果首先要的是與 zypper (套件管理員) 有關的安全性更新或其相依性時，有可能也會安裝某些安全性更新。 此行為是 zypper 的限制。 在某些情況下，您可能需要重新執行更新部署。 若要驗證，請檢查更新記錄。

### <a name="multi-tenant"></a>跨租用戶更新部署

如果您有另一個 Azure 租用戶的電腦向「更新管理」報告您需要修補，則您必須使用下列因應措施進行排程。 您可以將 [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) Cmdlet 搭配參數 `-ForUpdate` 使用以建立排程，然後使用 [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) Cmdlet 並將其他租用戶中的機器傳遞到 `-NonAzureComputer` 參數。 下列範例顯示做法：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="onboard"></a>啟用更新管理

若要開始修補系統，您需要啟用更新管理解決方案。 有許多方法可讓機器上線至更新管理。 下列是使解決方案上線的建議和支援方式：

* [從虛擬機器](automation-onboard-solutions-from-vm.md)
* [從瀏覽多部電腦](automation-onboard-solutions-from-browse.md)
* [從自動化帳戶](automation-onboard-solutions-from-automation-account.md)
* [使用 Azure 自動化 Runbook](automation-onboard-solutions.md)

## <a name="next-steps"></a>後續步驟

繼續進行本教學課程，以了解如何管理 Windows 虛擬機器的更新。

> [!div class="nextstepaction"]
> [管理 Azure Windows VM 的更新和修補程式](automation-tutorial-update-management.md)

* 使用[Azure 監視器記錄](../log-analytics/log-analytics-log-searches.md)檔中的記錄檔搜尋，以查看詳細的更新資料。
* 針對更新部署狀態[建立警示](automation-tutorial-update-management.md#configure-alerts)。

* 若要了解如何透過 REST API 與「更新管理」進行互動，請參閱[軟體更新設定](/rest/api/automation/softwareupdateconfigurations)
* 若要了解如何針對「更新管理」問題進行疑難排解，請參閱[更新管理疑難排解](troubleshoot/update-management.md)
