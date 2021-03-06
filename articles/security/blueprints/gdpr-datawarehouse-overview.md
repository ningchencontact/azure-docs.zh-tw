---
title: Azure 安全性與合規性藍圖 - 適用於 GDPR 的資料倉儲
description: Azure 安全性與合規性藍圖 - 適用於 GDPR 的資料倉儲
services: security
author: jomolesk
ms.assetid: 7a33fb3b-cfb6-49dd-ab4d-c53cf0d5da41
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: a091f4c941b1f4a338ad23956dbba3a7b89b87e8
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161861"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-gdpr"></a>Azure 安全性與合規性藍圖：適用於 GDPR 的資料倉儲

## <a name="overview"></a>概觀
一般資料保護規定 (GDPR) 包含許多有關收集、儲存和使用個人資訊的需求，包括組織如何識別及保護個人資料、容納透明度需求、偵測和報告個人資料缺口，以及訓練隱私權人員和其他員工。 GDPR 讓個人可進一步控制其個人資料，並對收集、處理或分析個人資料的組織加諸許多新義務。 GDPR 會針對提供商品和服務給歐盟 (EU) 居民，或收集和分析 EU 居民相關資料的組織，施加新的規則。 無論組織位於何處都適用 GDPR。

Microsoft 設計的 Azure 具有領先業界的安全性措施和隱私權原則，可保護雲端資料，包括 GDPR 所識別的個人資料類別。 Microsoft 的[合約條款](http://aka.ms/Online-Services-Terms)要求 Microsoft 符合處理器需求。

此 Azure 安全性與合規性藍圖提供在 Azure 中部署資料倉儲架構的指引，並協助符合 GDPR 需求。 此解決方案示範的方式讓客戶能符合特定的安全性和合規性需求，並且作為客戶在 Azure 上建置及設定他們自有資料倉儲解決方案的基礎。 客戶可以在符合 GDPR 規範的過程中利用此參考架構，並遵循 Microsoft 的[四個步驟程序](https://aka.ms/gdprebook)：
1. 探索：識別存在哪些個人資料，及其所在位置。
2. 管理：管理個人資料使用及存取的方式。
3. 保護：建立安全性控制，以防止、偵測及回應弱點和資料缺口。
4. 報告：保留必要文件以及管理資料要求和缺口通知。

與實作指南相關聯的此參考架構和威脅模型，主要是用來作為基礎，讓客戶進行調整以符合其特定需求，不應完全未經修改就用於生產環境中。 請注意：
- 架構提供了一個基準，可協助客戶以符合 GDPR 規範的方式來將工作負載部署至 Azure。
- 客戶須負責為任何使用架構建置的解決方進行置適當的安全性與合規性評量，因為需求可能會隨每個客戶的實作細節而有所不同。

## <a name="architecture-diagram-and-components"></a>架構圖表與元件
此解決方案提供參考架構，可實作高效能且安全的雲端資料倉儲。 此架構中有兩個不同的資料層：一個層級可用於將資料匯入、儲存，以及暫存至叢集的 SQL 環境中，而另一個層級用於 Azure SQL 資料倉儲，其中使用 ETL 工具載入資料 (例如 [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) T-SQL 查詢) 以進行處理。 一旦資料儲存在 Azure SQL 資料倉儲，您就可以大規模執行分析。

Azure 為客戶提供各種報告和分析服務。 此解決方案包括可從 Azure SQL 資料倉儲快速建立報告的 SQL Server Reporting Services (SSRS)。 所有 SQL 流量都會使用 SSL 透過內含的自我簽署憑證來加密。 Azure 建議的最佳做法是使用受信任的憑證授權單位，來增強安全性。

Azure SQL 資料倉儲中的資料會以單欄式儲存方式，儲存在關聯式資料表中，單欄式儲存是可大幅減少資料儲存體成本，又可提高查詢效能的格式。  根據使用需求，Azure SQL 資料倉儲的計算資源可以相應增加或減少，或在沒有使用中程序需要計算資源時完全關閉。

SQL Load Balancer 會管理 SQL 流量，確保維持高效能。 為具備高可用性和災害復原的能力，此參考架構中的所有虛擬機器都部署在可用性設定組中，並搭配使用設定在 AlwaysOn 可用性群組中的 SQL Server 執行個體。

此資料倉儲參考架構也包含用來管理架構中資源的 Active Directory (AD) 層。 Active Directory 子網路讓您更願意採用較大型 AD 樹系結構，因為即使無法存取較大樹系，也可讓該環境的作業繼續執行。 所有虛擬機器都已透過網域加入 Active Directory 層，而且已使用 Active Directory 群組原則來強化作業系統層級的安全性與合規性設定。

作為管理防禦主機使用的虛擬機器，會提供可讓系統管理員存取部署資源的安全連線。 資料會透過此管理防禦主機載入暫存區域。 **Azure 會建議設定 VPN 或 Azure ExpressRoute 連線，以便管理和將資料匯入參考架構子網路。**

![適用於 GDPR 的資料倉儲參考架構圖表](images/gdpr-datawarehouse-architecture.png?raw=true "適用於 GDPR 的資料倉儲參考架構圖表")

此解決方案會使用下列 Azure 服務。 部署架構的詳細資料位於[部署架構](#deployment-architecture)一節中。

-   Azure 虛擬機器
    - (1) 防禦主機
    -   (2) Active Directory 網域控制站
    -   (2) SQL Server 叢集節點
    -   (1) SQL Server 見證
-   可用性設定組 (Availability Sets)
    - Active Directory 網域控制站
    - SQL 叢集節點和見證
-   虛擬網路
    - (4) 子網路
    -   (4) 網路安全性群組
- SQL 資料倉儲
- SQL Server Reporting Services
- Azure SQL Load Balancer
- Azure Active Directory
- 復原服務保存庫
- Azure 金鑰保存庫
- Operations Management Suite (OMS)
- Azure 資料目錄
- Azure 資訊安全中心

## <a name="deployment-architecture"></a>部署架構
下一節會詳細說明部署和實作元素。

**SQL 資料倉儲**：[SQL 資料倉儲](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)是企業資料倉儲 (EDW)，利用大量平行處理 (MPP) 以快速地在數以 PB 計的資料中執行複雜的查詢，讓使用者可以有效率地識別個人資料。 使用者可以使用簡單的 PolyBase T-SQL 查詢將巨量資料匯入 SQL 資料倉儲，然後使用 MPP 功能來執行高效能分析。

**SQL Server Reporting Services (SSRS)**：[SQL Server Reporting Services](https://docs.microsoft.com/en-us/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) 可快速地以資料表、圖表、地圖、量測計、矩陣等形式，為 Azure SQL 資料倉儲建立報表。

**資料目錄**：[資料目錄](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog)能讓管理資料的使用者輕鬆地探索和了解資料來源。 您可以註冊、標記常用資料來源，並在其中搜尋個人資料。 資料會保留在現有的位置，但其中繼資料的複本會連同資料來源位置的參考，一起新增至資料目錄。 此中繼資料也會編製索引，透過搜尋輕鬆找到每個資料來源，並讓探索資料來源的使用者了解每個資料來源。

**防禦主機**：防禦主機是單一進入點，可讓使用者存取此環境中已部署的資源。 防禦主機為已部署的資源提供安全連線，其允許的遠端流量僅限來自安全清單上的公用 IP 位址。 若要允許遠端桌面 (RDP) 流量，必須在網路安全性群組 (NSG) 中定義該流量的來源。

此解決方案會建立虛擬機器，作為具有下列設定之加入網域的防禦主機：
-   [反惡意程式碼軟體擴充功能](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [OMS 擴充功能](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure 診斷擴充功能](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   使用 Azure Key Vault 的 [Azure 磁碟加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)
-   [自動關閉原則](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) \(英文\)，可減少不使用虛擬機器資源時的耗用量。
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) 已啟用，因此認證和其他祕密都會在受保護的環境中執行，且該環境會與正在執行的作業系統隔離

### <a name="virtual-network"></a>虛擬網路
此參考架構會定義位址空間為 10.0.0.0/16 的私人 VNet。

**網路安全性群組**：[NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 包含能允許或拒絕 VNet 內流量的存取控制清單 (ACL)。 NSG 可以用來保護子網路或個別 VM 層級的流量。 下列 NSG 已經存在:
  - 適用於資料層的 NSG (SQL Server 叢集、SQL Server 見證和 SQL Load Balancer)
  - 適用於管理防禦主機的 NSG
  - 適用於 Active Directory 的 NSG
  - 適用於 SQL Server Reporting Services 的 NSG

每個 NSG 都會開放特定連接埠及通訊協定，讓解決方案可安全且正確地運作。 此外，以下設定會針對每個 NSG 啟用：
  - 啟用[診斷記錄和事件](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)並儲存在儲存體帳戶
  - OMS Log Analytics 會連線至 [NSG 的診斷](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**子網路**：確認每個子網路都與對應的 NSG 建立關聯。

### <a name="data-at-rest"></a>待用資料
架構會透過多個量值來保護待用資料，包括加密和資料庫稽核。

**Azure 儲存體**：為符合加密的待用資料需求，所有 [Azure 儲存體](https://azure.microsoft.com/services/storage/)都會使用[儲存體服務加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)。 這有助於保護與防衛個人資料，以支援組織的安全性承諾及 GDPR 所定義的合規性需求。

**Azure 磁碟加密**：[Azure 磁碟加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)利用 Windows 的 BitLocker 功能來提供 OS 和資料磁碟的磁碟區加密。 此解決方案與 Azure Key Vault 整合，以協助控制和管理磁碟加密金鑰。

**Azure SQL Database**：Azure SQL Database 執行個體會使用下列資料庫安全性量值：
-   [AD 驗證與授權](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)可讓您在一個中央位置管理資料庫使用者及其他 Microsoft 服務的身分識別。
-   [SQL 資料庫稽核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)會追蹤資料庫事件並將事件寫入 Azure 儲存體帳戶中的稽核記錄。
-   SQL Database 設定成使用[透明資料加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)，它會執行資料庫、相關聯的備份及交易記錄檔的即時加密和解密，以保護待用資訊。 TDE 能保證已儲存的個人資料不會遭到未經授權的存取。
-   [防火牆規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)可在授與適當權限之前，防止所有對資料庫伺服器的存取。 此防火牆會根據每一個要求的來源 IP 位址來授與資料庫存取權。
-   [SQL 威脅偵測](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)可針對可疑的資料庫活動、潛在弱點、SQL 插入式攻擊，以及異常的資料庫存取模式，提供安全性警示，藉以在發生潛在威脅時啟用偵測及回應。
-   [Always Encrypted 資料行](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)可確保敏感性個人資料在資料庫系統內一律不會以純文字顯示。 啟用資料加密之後，只有具備金鑰存取權的用戶端應用程式或應用程式伺服器才可以存取純文字資料。
- [擴充屬性](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql)功能可用來中斷資料主體的處理，因為它可讓使用者將自訂屬性新增至資料庫物件，並將資料標記為「已中止」，以支援應用程式邏輯來防止處理相關聯的個人資料。
- [資料列層級安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)可讓使用者定義原則，限制資料存取以中斷處理。
- [SQL 資料庫動態資料遮罩 (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) 可藉由遮罩資料，使不具權限的使用者或應用程式無法看見敏感性個人資料。 DDM 可以自動探索潛在的敏感性資料，並建議套用適當的遮罩。 這有助於識別符合 GDPR 保護資格的個人資料，並降低存取以確保這類資料不會因未經授權的存取而離開資料庫。 **注意：客戶必須調整 DDM 設定，以配合其資料庫結構描述。**

### <a name="identity-management"></a>身分識別管理
下列技術可在 Azure 環境中提供管理個人資料存取的功能：
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 的多租用戶雲端式目錄和身分識別管理服務。 此解決方案的所有使用者都是在 AAD 中建立，包括存取 SQL Database 的使用者。
-   應用程式的驗證是使用 AAD 執行。 如需詳細資訊，請參閱[整合應用程式與 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)。 此外，資料庫資料行加密會使用 AAD 向 Azure SQL Database 驗證應用程式。 如需詳細資訊，請參閱如何[保護 SQL Database 中的敏感性資料](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)。
-   [Azure 角色型存取控制 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) 可讓系統管理員定義微調存取權限，只對使用者授與其執行工作所需的存取權數量。 系統管理員可以只允許存取個人資料的特定動作，而不是授與所有使用者不受限制的 Azure 資源使用權限。 只有訂用帳戶管理員可擁有訂用帳戶的存取權。
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) 可讓客戶將可存取特定資訊 (例如個人資料) 的使用者人數降至最低。  系統管理員可以使用 AAD Privileged Identity Management 來探索、限制和監視特殊權限的身分識別和其對資源的存取。 如有需要，這項功能也可用來強制執行隨選 Just-In-Time 系統管理存取權。
- [AAD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 會偵測影響組織身分識別的潛在弱點，並為偵測到的組織身分識別相關可疑動作設定自動回應，以及調查可疑事件並採取適當動作來解決這些可疑事件。

### <a name="security"></a>安全性
**祕密管理**：解決方案會使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 來管理金鑰和祕密。 Azure 金鑰保存庫可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 下列 Azure Key Vault 功能可協助客戶保護個人資料及存取這類資料：
- 進階存取原則是視需要設定的。
- 金鑰保存庫存取原則是使用金鑰和祕密的最低必要權限所定義的。
- 金鑰保存庫中的所有金鑰和祕密都有到期日。
- 金鑰保存庫中的所有金鑰都會受到特製化硬體安全模組 (HSM) 的保護。 金鑰類型為 HSM 保護的 2048 位元 RSA 金鑰。
- 所有使用者和身分識別皆使用 RBAC 授與最低必要權限。
- 金鑰保存庫的診斷記錄的保留期至少 365 天。
- 允許的金鑰密碼編譯作業僅限於需要的密碼編譯項目。

**修補程式管理**：根據預設，部署為此參考架構一部分的 Windows 虛擬機器會設定為從 Windows Update 服務接收自動更新。 此解決方案也包括 OMS [Azure 自動化](https://docs.microsoft.com/azure/automation/automation-intro)服務，您可以透過此服務建立更新的部署，以在有所需要時修補虛擬機器。

**惡意程式碼防護**：適用於虛擬機器的 [Microsoft 反惡意程式碼軟體](https://docs.microsoft.com/azure/security/azure-security-antimalware)提供即時防護功能，能幫助識別及移除病毒、間諜軟體及其他惡意軟體，具有可設定的警示，可在已知惡意或垃圾軟體嘗試在受保護的虛擬機器上安裝或執行時發出警示。

**安全性警訊**：[Azure 資訊安全中心](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro)可讓客戶監視流量、收集記錄並分析資料來源中的威脅。 此外，Azure 資訊安全中心會存取 Azure 服務的現有設定，以提供設定和服務建議，協助改善安全性狀態並保護個人資料。 Azure 資訊安全中心針對每個偵測到的威脅會包含[威脅情報報告](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report)，以協助事件回應小組調查和修復威脅。

### <a name="business-continuity"></a>業務持續性
**高可用性**：伺服器工作負載會組成[可用性設定組](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，以協助確保 Azure 中虛擬機器的高可用性。 在進行計劃性或非計劃性的維護事件期間，至少有一部虛擬機器可使用，進而符合 99.95% 的 Azure SLA。

**復原服務保存庫**：[復原服務保存庫](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)可裝載備份資料，並保護此架構中所有 Azure 虛擬機器的設定。 客戶可以使用復原服務保存庫，從 IaaS VM 還原檔案和資料夾，而非還原整個 VM，這樣可加速還原時間。

### <a name="logging-and-auditing"></a>記錄與稽核
[Operations Management Suite (OMS)](https://docs.microsoft.com/en-us/azure/operations-management-suite/operations-management-suite-overview) 提供系統、使用者活動及系統健康狀態的廣泛記錄。 OMS [Log Analytics](https://azure.microsoft.com/services/log-analytics/) 解決方案可收集及分析 Azure 和內部部署環境中資源所產生的資料。
- **活動記錄**：[活動記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)能讓您深入了解在訂用帳戶資源上執行的作業。 活動記錄可協助判斷作業的啟動者、發生時間和狀態。
- **診斷記錄**：[診斷記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包含每個資源發出的所有記錄。 這些記錄包含 Windows 事件系統記錄檔，以及 Azure Blob 儲存體、資料表和佇列記錄。
- **記錄封存**：所有診斷記錄都會寫入到集中且加密的 Azure 儲存體帳戶進行封存。 保留期是由使用者自訂，視組織特定的保留期需求，最長可達 730 天。 這些記錄會連線至 Azure Log Analytics 以進行處理、儲存及從儀表板顯示報表。

此外，此架構包含下列 OMS 解決方案：
-   [AD 評定](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)：Active Directory 健康狀態檢查解決方案會定期評估伺服器環境的風險和健康狀態，並專門針對部署的伺服器基礎結構，提供優先的建議清單。
-   [惡意程式碼評定](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)：反惡意程式碼軟體解決方案會報告惡意程式碼、威脅及保護狀態。
-   [Azure 自動化](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)：Azure 自動化解決方案會儲存、執行和管理 Runbook。
-   [安全性和稽核](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)：安全性和稽核儀表板會藉由提供有關安全性網域、值得注意的問題、偵測、威脅情報和常見的安全性查詢等計量，來提供資源安全性狀態的整體見解。
-   [SQL 評定](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)：SQL 健康狀態檢查解決方案會定期評估伺服器環境的風險和健康狀態，並專門針對部署的伺服器基礎結構，提供優先的建議清單給客戶。
-   [更新管理](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)：更新管理解決方案可讓客戶管理作業系統的安全性更新，包括可用更新的狀態和安裝必要更新的程序。
-   [代理程式健全狀況](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)：代理程式健全狀況解決方案會報告部署的代理程式數目和其地理分佈，以及沒有回應的代理程式數目和正在提交作業資料的代理程式數目。
-   [Azure 活動記錄](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：活動記錄分析解決方案可協助您分析客戶所有 Azure 訂用帳戶的 Azure 活動記錄。
-   [變更追蹤](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：變更追蹤解決方案可讓客戶輕鬆地識別環境中的變更。

## <a name="threat-model"></a>威脅模型

此參考架構的資料流程圖可供[下載](https://aka.ms/gdprDWdfd)，或可以在以下位置找到。 此模型可協助客戶在進行修改時，了解系統基礎結構中的潛在風險要點。

![適用於 GDPR 的資料倉儲威脅模型](images/gdpr-datawarehouse-threat-model.png?raw=true "適用於 GDPR 的資料倉儲威脅模型")

## <a name="compliance-documentation"></a>合規性文件
[Azure 安全性與合規性藍圖 - GDPR 客戶責任矩陣](https://aka.ms/gdprCRM)列出所有 GDPR 條款中的控制者和處理者責任。 請注意，在 Azure 服務中，客戶通常是控制者，而 Microsoft 則扮演處理者的角色。

[Azure 安全性與合規性藍圖 - GDPR 資料倉儲實作矩陣](https://aka.ms/gdprDW)提供資料倉儲架構處理哪些 GDPR 條款的相關資訊，包括詳細說明此實作如何符合每條涵蓋條款的需求。

## <a name="guidance-and-recommendations"></a>指引與建議
### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
您需要設定安全的 VPN 通道或 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)，以安全地連線到部署為此資料倉儲參考架構一部分的資源。 透過適當地設定 VPN 或 ExpressRoute，客戶可以在傳輸過程中新增資料保護層。

藉由實作與 Azure 的安全 VPN 通道，即可建立內部部署網路與 Azure 虛擬網路之間的虛擬私人連線。 此連線透過網際網路進行，可讓客戶在客戶的網路與 Azure 之間的加密連結內安全地「輸送」資訊。 站對站 VPN 是安全成熟的技術，各種規模的企業已部署數十年。 此選項使用 [IPsec 通道模式](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))作為加密機制。

由於 VPN 通道內的流量會透過站對站 VPN 周遊網際網路，因此 Microsoft 提供另一個更安全的連線選項。 Azure ExpressRoute 是 Azure 與內部部署位置或 Exchange 主機服務提供者之間專用的 WAN 連結。 ExpressRoute 連線不會經過網際網路，相較於一般網際網路連線，這些連線提供了更可靠、更快速、更低延遲、更安全的連線。 此外，因為這是客戶電信提供者的直接連線，所以資料不會透過網際網路傳輸，因此不會公開給網際網路。

如需實作將內部部署網路擴充至 Azure 之安全混合式網路的最佳做法，請參閱[這裡](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

### <a name="extract-transform-load-etl-process"></a>擷取-轉換-載入 (ETL) 程序
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) 可以將資料載入 Azure SQL 資料倉儲中，而不需要個別的 ETL 或匯入工具。 PolyBase 允許透過 T-SQL 查詢來存取資料。 Microsoft 的商業智慧和分析堆疊，以及與 SQL Server 相容的協力廠商工具，都可以搭配 PolyBase 使用。

### <a name="azure-active-directory-setup"></a>Azure Active Directory 設定
您必須使用 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 來管理部署，以及為與環境互動的人員佈建存取權。 您只要[點按四下](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)，就可整合現有 Windows Server Active Directory 與 AAD。 客戶也可以透過讓部署的 Active Directory 基礎結構成為 AAD 樹系的子網域，來將部署的 Active Directory 基礎結構 (網域控制站) 繫結至現有 AAD。

### <a name="optional-services"></a>選擇性服務
Azure 提供各種不同的服務，以協助儲存及暫存格式化與未格式化的資料。 根據客戶需求，可將下列服務新增至此參考架構：
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) 是一個針對複雜的混合式擷取-轉換-載入 (ETL)、擷取-載入-轉換 (ELT) 及資料整合專案建置的受控雲端服務。 Azure Data Factory 中的功能可協助追蹤及尋找個人資料，包括用來識別資料何時抵達及其來源的視覺化和監視工具。 客戶可以使用 Azure Data Factory 建立並排程資料驅動的工作流程 (稱為管線)，以從不同的資料存放區內嵌資料。 這些管線可讓客戶從內部和外部來源內嵌資料。 接著，客戶可以處理並轉換資料，以輸出至資料存放區，例如 Azure SQL 資料倉儲。
- 客戶可以在 [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) 中暫存非結構化的資料，這會在單一位置以任何擷取速度來擷取任何大小和類型的資料，以進行運作和探勘分析。  Azure Data Lake 具有擷取及轉換資料的功能。 Azure Data Lake Store 與 Hadoop 生態系統中大部分的開放原始碼元件相容，並與其他 Azure 服務 (例如 Azure SQL 資料倉儲) 順利整合。

## <a name="disclaimer"></a>免責聲明

 - 此文件僅供參考之用。 Microsoft 對本文件中的資訊不做任何明示、暗示或成文之擔保。 這份文件係依「現狀」提供。 本文件中說明的資訊與畫面 (包括 URL 及其他網際網路網站參考資料) 如有變更，恕不另行通知。 閱讀這份文件的客戶須自行承擔使用風險。
 - 本文件未提供給客戶任何 Microsoft 產品或解決方案中任何智慧財產的任何法定權利。
 - 客戶可以複製並使用這份文件，供內部參考之用。
 - 本文件的某些建議可能會導致資料、網路或 Azure 計算資源使用量增加，並可能增加客戶的 Azure 授權或訂用帳戶成本。
 - 此架構的目的是作為基礎，讓客戶進行調整以符合其特定需求，不應完全未經修改就用於生產環境中。
 - 此文件編製做為參考資料，且不應以此文件定義為客戶能符合特定合規性需求和法規的所有手段。 對於核准的客戶實作，客戶應向其組織尋求法務支援。
