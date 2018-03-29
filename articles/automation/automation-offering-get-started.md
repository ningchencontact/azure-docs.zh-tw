---
title: 開始使用 Azure 自動化
description: 本文提供 Azure 自動化服務的概觀。 它會檢閱為了讓供應項目在 Azure Marketplace 上架所準備的設計和實作詳細資料。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 961b783b44b95a871c98f96d3783f3429636f295
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-automation"></a>開始使用 Azure 自動化

本文會介紹 Azure 自動化的部署相關核心概念。 如果您不熟悉 Azure 中的自動化，或具備使用自動化工作流程軟體 (例如 System Center Orchestrator) 的經驗，您可以了解如何準備及讓自動化上架。 閱讀本文之後，您會準備好開始開發 Runbook，以支援您的處理程序自動化需求。 


## <a name="automation-architecture-overview"></a>自動化架構概觀

![Azure 自動化概觀](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Azure 自動化是軟體即服務 (SaaS) 應用程式，提供可擴充和可靠的多租用戶環境，您可以在其中使用 Runbook 來自動化程序。 您可以在 Azure、其他雲端服務，或內部部署環境中使用 Desired State Configuration (DSC)，以管理對 Windows 與 Linux 系統的設定變更。 您自動化帳戶中的實體 (包括 Runbook、資產和執行身分帳戶) 會與您的訂用帳戶和其他訂閱中的其他自動化帳戶隔離。  

您在 Azure 中執行的 Runbook 會在自動化沙箱上執行。 沙箱會裝載於 Azure 平台即服務 (PaaS) 虛擬機器。 

自動化沙箱可為 Runbook 執行的所有層面提供租用戶隔離，包括模組、儲存體、記憶體、網路通訊和作業串流。 這個角色是由服務來管理。 您無法從您的 Azure 或自動化帳戶存取或管理角色。         

若要讓本機資料中心或其他雲端服務中的資源部署和管理自動化，在建立自動化帳戶之後，您可以指定一或多部 VM 來執行[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)。 每個混合式 Runbook 背景工作角色都需要安裝 Microsoft 管理代理程式，以及自動化帳戶。 代理程式必須連線到 Azure Log Analytics 工作區。 您可以使用 Log Analytics 來啟動安裝程序、維護 Microsoft 管理代理程式及監視混合式 Runbook 背景工作角色的功能。 Azure 自動化會執行 Runbook 的傳遞和執行的指示。

您可以部署多個混合式 Runbook 背景工作角色。 使用混合式 Runbook 背景工作角色為您的 Runbook 提供高可用性，以及對 Runbook 作業進行負載平衡。 在某些情況下，您可以讓 Runbook 作業專屬於特定工作負載或環境。 混合式 Runbook 背景工作角色上的 Microsoft Monitoring Agent 會透過 TCP 通訊埠 443，起始與自動化服務的通訊。 混合式 Runbook 背景工作角色沒有輸入防火牆需求。  

您可能需要一個在混合式 Runbook 背景工作角色上執行的 Runbook，以針對環境中的其他機器或服務執行管理工作。 在該案例中，Runbook 可能也需要存取其他連接埠。 如果您的 IT 安全性原則不允許您網路上的電腦連線到網際網路，請檢閱 [OMS 閘道](../log-analytics/log-analytics-oms-gateway.md)。 Operations Management Suite (OMS) 閘道會作為混合式 Runbook 背景工作角色的 Proxy。 它會收集作業狀態，並且從您的自動化帳戶接收設定資訊。

在混合式 Runbook 背景工作角色上執行的 Runbook，會在電腦本機系統帳戶的內容中執行。 當您在本機 Windows 電腦上執行系統管理動作時，我們會提供資訊安全內容的建議。 如果您想要讓 Runbook 針對本機電腦以外的資源執行工作，您可能需要在自動化帳戶中定義安全認證資產。 您可以從 Runbook 存取安全認證資產，並用它們對外部資源進行驗證。 您可以使用 Runbook 中的[認證](automation-credentials.md)、[憑證](automation-certificates.md)和[連線](automation-connections.md)資產。 搭配可以用來指定認證以進行驗證的 Cmdlet 來使用資產。

您可以將儲存在 Azure 自動化的 DSC 設定套用至虛擬機器。 其他實體和虛擬機器可以從 Automation DSC 提取伺服器中要求設定。 您不需要部署任何基礎結構以支援 Automation DSC 提取伺服器，來管理內部部署實體或虛擬 Windows 和 Linux 系統的設定。 只需要即將使用 Automation DSC 管理的每個系統能進行輸出網際網路存取。 通訊是透過 TCP 連接埠 443 連線到 OMS 服務。   

## <a name="prerequisites"></a>先決條件

### <a name="automation-dsc"></a>自動化 DSC
您可以使用 Automation DSC 來管理這些機器：

* 執行 Windows 或 Linux 的 Azure 虛擬機器 (傳統)。
* 執行 Windows 或 Linux 的 Azure 虛擬機器。
* 執行 Windows 或 Linux 的 Amazon Web Services (AWS) 虛擬機器。
* 位於內部部署或 Azure 或 AWS 以外之雲端中的實體和虛擬 Windows 電腦。
* 位於內部部署或 Azure 或 AWS 以外之雲端中的實體和虛擬 Linux 電腦。

針對 Windows 機器，必須安裝 Windows Management Framework (WMF) 5 的最新版本。 針對 Linux 機器，必須安裝[適用於 Linux 的 PowerShell DSC 代理程式](https://www.microsoft.com/en-us/download/details.aspx?id=49150)的最新版本。 PowerShell DSC 代理程式會使用 WMF 5 與自動化通訊。 

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker  
當您指定電腦執行混合式 Runbook 作業時，電腦必須符合下列必要條件：

* Windows Server 2012 或更新版本。
* Windows PowerShell 4.0 或更新版本。 為了增加可靠性，我們建議使用 Windows PowerShell 5.0。 您可以從 Microsoft 下載中心[下載新版本](https://www.microsoft.com/download/details.aspx?id=50395)。
* .NET Framework 4.6.2 或更新版本。
* 至少雙核心。
* 至少 4 GB 的 RAM。

### <a name="permissions-required-to-create-an-automation-account"></a>建立自動化帳戶所需的權限
若要建立或更新自動化帳戶，以及完成本文中所述的工作，您必須具有下列權限：   
 
* 若要建立自動化帳戶，必須將您的 Azure Active Directory (Azure AD) 使用者帳戶新增至權限相當於 **Microsoft.Automation** 資源「擁有者」角色的角色。 如需詳細資訊，請參閱 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)。  
* 在 Azure 入口網站的 [Azure Active Directory] > [管理] > [應用程式註冊] 下方，如果將 [應用程式註冊] 設為 [是]，則 Azure AD 租用戶中的非管理使用者就能[註冊 Active Directory 應用程式](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions)。 如果將 [應用程式註冊] 設為 [否]，則執行此動作的使用者必須是 Azure AD 中的全域管理員。 

若您在加入至訂用帳戶的全域管理員/共同管理員角色之前，並非訂用帳戶 Active Directory 執行個體的成員，系統會將您以來賓身分加入至 Active Directory。 在此案例中，您會在 [新增自動化帳戶] 頁面中看到此訊息：「您無權建立」。 

如果先將使用者加入至全域管理員/共同管理員角色，您可以從訂用帳戶的 Active Directory 執行個體中移除他們，然後將他們重新加入至 Active Directory 中的完整使用者角色。

驗證使用者角色：
1. 在 Azure 入口網站中，移至 [Azure Active Directory] 窗格。
2. 選取 [使用者和群組]。
3. 選取 [所有使用者]。 
4. 當您選取特定的使用者之後，選取 [設定檔]。 使用者設定檔下方的 [使用者類型] 屬性值不應為 [來賓]。

## <a name="authentication-planning"></a>規劃驗證
在 Azure 自動化中，您可以針對 Azure、內部部署以及其他雲端服務中的資源自動執行工作。 若要讓 Runbook 執行其必要動作，它必須有權限可以安全地存取資源。 其必須具有訂用帳戶內所需的最小權限。  

### <a name="what-is-an-automation-account"></a>什麼是自動化帳戶 
在 Azure 自動化中使用 Cmdlet 對資源執行的所有自動化工作，均使用 Azure AD 組織身分識別的認證型驗證向 Azure 進行驗證。  自動化帳戶與您用來登入入口網站以設定和使用 Azure 資源的帳戶不同。 

下列資源隨附於自動化帳戶：

* **憑證**。 包含用來從 Runbook 或 DSC 設定進行驗證的憑證。 您也可以新增憑證。
* **連接**。 包含從 Runbook 或 DSC 設定連線到外部服務或應用程式所需的驗證或設定資訊。
* **認證**。 包含 **PSCredential** 物件，其中具有安全性認證，例如使用者名稱和密碼。 從 Runbook 或 DSC 設定進行驗證需要認證。
* **整合模組**。 隨附於自動化帳戶的 PowerShell 模組。 使用 PowerShell 模組以在 Runbook 和 DSC 設定中執行 Cmdlet。
* **排程**。 包含可在指定的時間 (包括週期性頻率) 啟動或停止 Runbook 的排程。
* **變數**。 包含 Runbook 或 DSC 設定提供的值。
* **DSC 設定**。 PowerShell 指令碼，描述如何設定作業系統功能或設定，或如何在 Windows 或 Linux 電腦上安裝應用程式。  
* **Runbook**。 一組工作，可在以 Windows PowerShell 為基礎的自動化中執行自動程序。    

每個自動化帳戶的自動化資源與單一 Azure 區域相關聯。 不過，您可以使用自動化帳戶來管理您訂用帳戶中的所有資源。 如果您擁有需要將資料和資源區隔到特定區域的原則，請在不同的區域中建立自動化帳戶。

當您在 Azure 入口網站中建立自動化帳戶時，會自動建立兩個驗證實體：

* **執行身分帳戶**。 此帳戶會執行下列工作：
  - 在 Azure AD 中建立服務主體。
  - 建立憑證。
  - 指派參與者角色型存取控制 (RBAC)，此控制可使用 Runbook 來管理 Azure Resource Manager 資源。
* **傳統執行身分帳戶**。 此帳戶會上傳管理憑證。 憑證會使用 Runbook 來管理傳統資源。

RBAC 可讓資源管理員將允許的動作授與 Azure AD 使用者帳戶和執行身分帳戶。 您也可以使用 RBAC 來驗證該服務主體。 如需詳細資訊，以及開發模型以管理自動化權限的協助，請參閱 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)一文。  

#### <a name="authentication-methods"></a>驗證方法
下表摘要說明 Azure 自動化支援之每個環境所適用的驗證方法。

| 方法 | 環境 
| --- | --- | 
| Azure 執行身分和傳統執行身分帳戶 |Azure Resource Manager 和 Azure 傳統部署 |  
| Azure AD 使用者帳戶 |Azure Resource Manager 和 Azure 傳統部署 |  
| Windows 驗證 |使用混合式 Runbook 背景工作角色的本機資料中心或其他雲端服務提供者 |  
| Amazon Web Services 認證 |Amazon Web Services |  

下列文章提供設定這些環境的驗證概觀和實作步驟。 文章將說明使用您專用於該環境的現有或新帳戶。 
* [建立獨立的 Azure 自動化帳戶](automation-create-standalone-account.md)
* [使用 Azure 傳統部署和資源管理員驗證 Runbook](automation-create-aduser-account.md)
* [使用 Amazon Web Services 驗證 Runbook](automation-config-aws-account.md)
* [更新自動化執行身分帳戶](automation-create-runas-account.md)

對於 Azure 執行身分和傳統執行身分帳戶，[更新自動化執行身分帳戶](automation-create-runas-account.md)說明如何從入口網站透過執行身分帳戶更新現有的自動化帳戶。 它也說明如果原先並未以執行身分或傳統執行身分帳戶設定自動化帳戶，則該如何使用 PowerShell。 您可以使用企業憑證授權單位 (CA) 所核發的憑證，來建立執行身分帳戶和傳統執行身分帳戶。 檢閱[更新自動化執行身分帳戶](automation-create-runas-account.md)以了解如何使用此設定來建立帳戶。     
 
## <a name="network-planning"></a>規劃您的網路
若要讓混合式 Runbook 背景工作角色連線至 OMS 並向其註冊，它必須能夠存取本節所述的連接埠號碼和 URL。 此外，這是 [Microsoft Monitoring Agent 連線至 OMS 所需的連接埠和 URL](../log-analytics/log-analytics-windows-agent.md)。 

如果您使用 Proxy 伺服器在代理程式和 OMS 服務之間進行通訊，請確保可以存取適當的資源。 如果您使用防火牆來限制網際網路存取，您必須設定防火牆以允許存取。

若要讓混合式 Runbook 背景工作角色與自動化進行通訊，需要下列連接埠和 URL：

* 連接埠︰只需要 TCP 443 以便進行傳出網際網路存取。
* 全域 URL：*.azure-automation.net。

如果您有針對特定區域定義的自動化帳戶，您可以將通訊限制為該區域資料中心。 下表提供每個區域的 DNS 記錄。

| **區域** | **DNS 記錄** |
| --- | --- |
| 美國中南部 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 美國東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 美國中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net |
| 西歐 |we-jobruntimedata-prod-su1.azure-automation.net |
| 北歐 |ne-jobruntimedata-prod-su1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 東南亞 |sea-jobruntimedata-prod-su1.azure-automation.net |
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 日本東部 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| 澳大利亞東南部 |ase-jobruntimedata-prod-su1.azure-automation.net |
| 英國南部 | uks-jobruntimedata-prod-su1.azure-automation.net |
| 美國政府維吉尼亞州 | usge-jobruntimedata-prod-su1.azure-automation.us |

如需區域 IP 位址 (而非名稱) 的清單，請從「Microsoft 下載中心」下載 [Azure 資料中心 IP 位址](https://www.microsoft.com/download/details.aspx?id=41653) XML 檔案。 

> [!NOTE]
> Azure 資料中心 IP 位址 XML 檔會列出在 Microsoft Azure 資料中心使用的 IP 位址範圍。 計算、SQL 和儲存體範圍會包含在檔案中。 
>
>每週會公佈已更新的檔案。 檔案會反映目前已部署的範圍及任何即將進行的 IP 範圍變更。 出現在檔案中的新範圍至少有一週的時間不會在資料中心中使用。 
>
> 最好是每週下載新的 XML 檔案。 接著，更新您的網站以便正確地識別在 Azure 中執行的服務。 Azure ExpressRoute 使用者應該注意到，在每個月的第一週，此檔案會用來更新 Azure 空間的邊界閘道協定 (BGP) 公告。 
> 

## <a name="creating-an-automation-account"></a>建立自動化帳戶

下表簡介在 Azure 入口網站中建立自動化帳戶的方法。 資料表說明每種部署經驗和其間的差異。  

|方法 | 說明 |
|-------|-------------|
| 在 Azure Marketplace 中選取 [自動化與控制] | Azure Marketplace 供應項目會建立自動化帳戶和 OMS 工作區，這些項目會連結並且在相同的資源群組與區域中。 與 OMS 的整合也包括使用 Log Analytics 來監視和分析一段時間 Runbook 工作狀態和作業資料流的好處。 您也可以使用 Log Analytics 中的進階功能來提升效能或調查問題。 供應項目會部署「變更追蹤」和「更新管理」解決方案，這些項目預設會啟用。 |
| 在 Marketplace 中選取 [自動化] | 這個方法會在未連結到 OMS 工作區的新建或現有資源群組中，建立自動化帳戶。 它不包含來自 [自動化與控制] 供應項目的任何可用解決方案。 這個方法屬於基本設定，有助於您了解自動化。 它可以協助您了解如何撰寫 Runbook 和 DSC 設定，並了解如何使用服務的功能。 |
| 選取「管理」解決方案 | 如果您選取「管理」解決方案，包括[「更新管理」](../operations-management-suite/oms-solution-update-management.md)、[「停機期間啟動/停止 VM」](automation-solution-vm-management.md)或[「變更追蹤」](../log-analytics/log-analytics-change-tracking.md)，解決方案會提示您選取現有的自動化帳戶與 OMS 工作區。 此解決方案會提供建立自動化帳戶與 OMS 工作區所需的選項，以便您在訂用帳戶中部署該解決方案。 |

### <a name="create-an-automation-account-thats-integrated-with-oms"></a>建立與 OMS 整合的自動化帳戶
若要讓自動化上架，建議您在 Marketplace 中選取 [自動化與控制] 供應項目。 使用此方法建立自動化帳戶，並且建立與 OMS 工作區的整合。 當您使用這個方法時，您也會有安裝適用於供應項目的管理解決方案選項。  

[建立獨立的自動化帳戶](automation-create-standalone-account.md)會藉由將 [自動化與控制] 供應項目上架，逐步引導您建立自動化帳戶和 OMS 工作區的程序。 您可以了解如何針對測試或預覽服務，建立獨立的自動化帳戶。  

若要藉由使用 [自動化與控制] Marketplace 供應項目來建立自動化帳戶與 OMS 工作區：

1. 以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入 Azure 入口網站。
2. 選取 [ **新增**]。<br><br> ![在 Azure 入口網站中選取 [新增]](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. 搜尋 [自動化]。 在搜尋結果中，選取 [自動化與控制]。<br><br> ![在 Azure Marketplace 中搜尋並選取 [自動化與控制]](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png)。<br>   
4. 檢閱供應項目的描述，然後選取 [建立]。  
5. 在 [自動化與控制] 底下，選取 [OMS 工作區]。 在 [OMS 工作區] 底下，選取連結到自動化帳戶所在之 Azure 訂用帳戶的 OMS 工作區。 如果您沒有 OMS 工作區，選取 [建立新工作區]。 在 [OMS 工作區] 底下： 
  1. 對於 [OMS 工作區]，輸入新工作區的名稱。
  2. 對於 [訂用帳戶]，選取要連結的訂用帳戶。 如果預設選取項目不是您想要使用的訂用帳戶，請從下拉式清單中選取訂用帳戶。
  3. 對於 [資源群組]，您可以建立資源群組，或選取現有的資源群組。  
  4. 對於 [位置]，選取區域。 如需詳細資訊，請參閱 [Azure 自動化的可用區域](https://azure.microsoft.com/regions/services/)。 解決方案是以兩種定價層提供︰免費和每個節點 (OMS) 層。 免費層會限制每日可收集的資料量、保留期和 Runbook 作業執行階段分鐘數。 每個節點 (OMS) 層不會限制每日可收集的資料量。  
  5. 選取 [自動化帳戶]。  如果您建立新的 OMS 工作區，您也必須建立與新 OMS 工作區相關聯的自動化帳戶。 包含您的 Azure 訂用帳戶、資源群組與區域。 
    1. 選取 [建立自動化帳戶]。
    2. 在 [自動化帳戶] 底下的 [名稱] 欄位中，輸入自動化帳戶的名稱。
    系統會根據所選的 OMS 工作區自動填入所有其他選項。 您無法修改這些選項。 
    3. Azure 執行身分帳戶是供應項目的預設驗證方法。 在您選取 [確定] 之後，就會驗證設定選項並建立自動化帳戶。 若要追蹤其進度，請在功能表上選取 [通知]。 
    4. 不然，選取現有的自動化執行身分帳戶。 您選取的帳戶不能已連結至另一個 OMS 工作區。 如果是此情況，便會出現通知訊息。 如果該帳戶已經連結至 OMS 工作區，則選取不同的自動化執行身分帳戶或建立一個帳戶。
    5. 您輸入或選取所需的資訊之後，選取 [建立]。 系統會驗證此資訊，並建立自動化帳戶和執行身分帳戶。 您會自動回到 [OMS 工作區] 窗格。  
6. 您在 [OMS 工作區] 窗格輸入或選取所需的資訊之後，選取 [建立]。  系統會驗證資訊，並建立工作區。 若要追蹤其進度，請在功能表上選取 [通知]。 您會回到 [新增解決方案] 窗格。  
7. 在 [自動化與控制] 設定底下，確認您要安裝建議的預先選取解決方案。 如果您變更任何預設選項，您可以在稍後個別安裝解決方案。  
8. 若要繼續將自動化和 OMS 工作區上架，選取 [建立]。 系統會驗證所有設定，然後 Azure 會嘗試在您的訂用帳戶中部署此供應項目。 此流程可能需要數秒鐘的時間。 若要追蹤其進度，請在功能表上選取 [通知]。 

供應項目上架之後，您就可以執行下列工作：
* 開始建立 Runbook。
* 使用您已啟用的管理解決方案。
* 部署[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)。
* 開始使用 [Log Analytics](https://docs.microsoft.com/azure/log-analytics) 來收集由雲端或內部部署環境中資源所產生的資料。   

## <a name="next-steps"></a>後續步驟
* 確認新的自動化帳戶可以針對 Azure 資源進行驗證。 如需詳細資訊，請參閱[測試 Azure 自動化執行身分帳戶驗證](automation-verify-runas-authentication.md)。
* 在開始撰寫之前，了解如何開始建立 Runbook 和相關考量。 如需詳細資訊，請參閱[自動化 Runbook 類型](automation-runbook-types.md)。


