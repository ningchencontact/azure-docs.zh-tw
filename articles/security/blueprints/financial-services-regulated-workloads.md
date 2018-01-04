---
title: "Azure 的藍圖自動化-涵蓋了金融服務規定的工作負載"
description: "涵蓋了金融服務規定的工作負載的藍圖"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 17794288-9074-44b5-acc8-1dacceb3f56c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2017
ms.author: frasim
ms.openlocfilehash: 19e26c16866dada8dcff04a520ce4c208d67c365
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="azure-blueprint-automation-financial-services-blueprint-for-regulated-workloads"></a>規定的工作負載的藍圖 azure 自動化： 涵蓋了金融服務藍圖

## <a name="overview"></a>概觀

財務服務藍圖，受規範的工作負載可協助您部署為服務 (PaaS) web 應用程式，設計用來處理在雲端中的機密資料安全且相容的平台。 藍圖包含自動化的指令碼和展示簡單參考架構和設計，可協助簡化採用 Microsoft Azure 解決方案的指引。 此藍圖說明的端對端解決方案，以符合搜尋的負擔和雲端中部署的成本降低組織的需求。

此藍圖為了符合嚴格的相容的標準設定由美國 Institute 的通過公用會計例如-SOC 1、 SOC 2、 付款卡產業資料安全標準委員會的 DSS 3.2 和 FFIEC 的需求收集、 儲存和擷取機密的財務資料。 它會示範這類資料的正確處理部署解決方案，可管理的安全、 標準、 多層環境中的財務資料。 方案會部署為端對端以 Azure 為基礎的 PaaS 解決方案。 

藍圖被要做為客戶上建置，並了解管理雲端中的安全資料的需求為基礎。 方案不應該用於實際執行部署為的是，但若要了解、 設計和部署 Azure 服務。它是做為基準設計來協助安全且相容的方式使用 Microsoft Azure 的客戶。

合格的稽核員必須認證根據此藍圖任何實際執行客戶解決方案。 解決方案會因每個客戶的實作和地理位置的詳細規格。

針對此解決方案運作方式的快速概觀，請觀賞這[視訊](https://aka.ms/fsiblueprintvideo)說明和示範其部署。

## <a name="solution-components"></a>方案元件

架構由下列元件組成，並使用從 Azure PCI DSS 相容性解決方案的部署功能。

- **架構圖**。 該圖顯示 Contoso Webstore 解決方案中所用的參考架構。
- **部署範本**。 在此部署中，透過在安裝期間指定設定參數，系統會使用 [Azure Resource Manager 範本](/azure/azure-resource-manager/resource-group-overview#template-deployment)來將架構的元件自動部署至 Microsoft Azure。
- **自動化部署指令碼**。 這些指令碼可協助您部署端對端解決方案。 指令碼包括：
    - 模組安裝和[全域管理員](/azure/active-directory/active-directory-assign-admin-roles-azure-portal)安裝指令碼會用來進行安裝，以及確認必要的 PowerShell 模組和全域管理員角色已正確設定。 
    - 安裝 PowerShell 指令碼會用來部署端對端解決方案，並透過 .zip 檔案和 .bacpac 檔案提供，其中包含預先建置的示範 Web 應用程式與 [SQL 資料庫的範例](https://github.com/Microsoft/azure-sql-security-sample)。 內容。 您可以在[付款處理藍圖代碼存放庫][code-repo]檢閱此解決方案的原始程式碼。 

## <a name="architectural-diagram"></a>架構圖

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>使用者案例

藍圖解決下列使用案例。 下方。

> 這個案例說明如何在虛構 webstore 移動 PaaS 機密資料的雲端 Azure 為基礎的解決方案。 範例解決方案說明處理和基本的使用者資訊與選取的機密資料的集合。 這項工作借用 Azure 藍圖自動化： 付款處理付款卡處理 PCI DSS 相容的環境。 如需展開這項工作的詳細資訊["檢閱和指引的實作 「](https://aka.ms/pciblueprintprocessingoverview)白皮書提供檢閱 PCI DSS 相容的環境。

### <a name="use-case"></a>使用案例
小型 webstore 呼叫*Contoso Webstore*已準備好移財務資料，包括客戶付款資訊至雲端。 

Contoso Webstore 的系統管理員正在尋找解決方案，可快速部署來達成此目標。 他將使用此的概念證明 (POC) 與如何使用 Azure，以收集、 儲存和擷取嚴苛的法規遵循需求依據的財務資料他專案關係人討論。

> 您將負責為任何使用此 POC 所用結構建置的解決方案安置適當的安全性與合規性檢閱，因為您的實作和所在地區可能有不同特性，因此可能會有不同需求。 

### <a name="elements-of-the-foundational-architecture"></a>基本架構的元素

基本架構的設計包含下列虛構元素：

網域站台 `contosowebstore.com`

使用者角色被用來說明使用案例，並提供深入的使用者介面。

#### <a name="role-site-and-subscription-admin"></a>角色：站台和訂用帳戶管理員

|Item      |範例|
|----------|------|
|使用者名稱： |`adminXX@contosowebstore.com`|
| 名稱： |`Global Admin Azure PCI Samples`|
|使用者類型：| `Subscription Administrator and Azure Active Directory Global Administrator`|

- 系統管理員帳戶無法讀取未遮罩的財務資訊。 所有動作皆會記錄。
- 管理員帳戶無法管理或登入 SQL Database。
- 系統管理員帳戶可以管理 Active Directory 和訂用帳戶。

#### <a name="role-sql-administrator"></a>角色：SQL 系統管理員

|Item      |範例|
|----------|------|
|使用者名稱： |`sqlAdmin@contosowebstore.com`|
| 名稱： |`SQLADAdministrator PCI Samples`|
| 名字： |`SQL AD Administrator`|
|姓氏： |`PCI Samples`|
|使用者類型：| `Administrator`|

- Sqladmin 帳戶無法檢視未篩選的財務資訊。 所有動作皆會記錄。
- Sqladmin 帳戶可以管理 SQL Database。

#### <a name="role-clerk"></a>角色：店員

|Item      |範例|
|----------|------|
|使用者名稱：| `receptionist_EdnaB@contosowebstore.com`|
| 名稱： |`Edna Benson`|
| 名字：| `Edna`|
|姓氏：| `Benson`|
| 使用者類型： |`Member`|

Edna Benson 是接待員和業務經理。 她負責確保客戶資訊正確且收費已完成。 Edna 是登入的使用者，負責 Contoso Webstore 測試網站的所有互動。 Edna 有以下權利︰ 

- Edna 可以建立和讀取客戶資訊。
- Edna 可以修改客戶資訊。
- Edna 可以覆寫的財務資訊。
- Edna 帳戶無法檢視未篩選的財務資訊。

> 在 Contoso Webstore，使用者會自動**Edna**使用者測試已部署環境的功能。

### <a name="contoso-webstore---estimated-pricing"></a>Contoso Webstore - 估算價格

此基本架構和範例 Web 應用程式使用每月計費結構，而使用量的費用為每小時計費，這些都是決定解決方案大小時須考慮的項目。 您可以使用 [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)來估算這些成本。 從 2017年 9 月開始，此解決方案的每月費用預估為 ~ $2500，這包含 ASE v2 每月 $1000 的使用費。 這些費用會因為使用量不同而有所差異，而且可能會有所變更。 客戶應在部署時估算每月費用，可取得更準確的估計值。 

此解決方案會使用下列 Azure 服務。 部署架構的詳細資料位於＜[部署架構](#deployment-architecture)＞一節中。

>- 應用程式閘道
>- Azure Active Directory
>- App Service Environment v2
>- OMS Log Analytics
>- Azure 金鑰保存庫
>- 網路安全性群組
>- Azure SQL DB
>- Azure Load Balancer
>- Application Insights
>- Azure 資訊安全中心
>- Azure Web 應用程式
>- Azure 自動化
>- Azure 自動化 Runbook
>- Azure DNS
>- Azure 虛擬網路
>- Azure 虛擬機器
>- Azure 資源群組和原則
>- Azure Blob 儲存體
>- Azure Active Directory 角色型存取控制 (RBAC)

## <a name="deployment-architecture"></a>部署架構

下一節會詳細說明開發和實作元素。

### <a name="network-segmentation-and-security"></a>網路分割和安全性

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>應用程式閘道

基本架構可減少使用的 web 應用程式的防火牆 (WAF)，以及啟用 OWASP ruleset 的應用程式閘道的安全性弱點的風險。 其他功能包括：

- [端對端 SSL](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL 卸載](/azure/application-gateway/application-gateway-ssl-portal)啟用
- [TLS v1.0 和 v1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)停用
- [Web 應用程式防火牆](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) \(WAF 模式\)
- 使用 OWASP 3.0 規則集的[防止模式](/azure/application-gateway/application-gateway-web-application-firewall-portal)
- [診斷記錄](/azure/application-gateway/application-gateway-diagnostics)啟用
- [自訂健康情況探查](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center)和[Azure Advisor](/azure/advisor/advisor-security-recommendations)，這兩個提供額外的保護和通知。 Azure 資訊安全中心也會提供評價系統。

#### <a name="virtual-network"></a>虛擬網路

基本架構會定義位址空間為 10.0.0.0/16 的私人虛擬網路。

#### <a name="network-security-groups"></a>網路安全性群組

每個網路層都有專用的網路安全性群組 (NSG)：

- 適用於防火牆和應用程式閘道 WAF 的 DMZ 網路安全性群組
- 適用於管理 jumpbox 的 NSG (防禦主機)
- 適用於應用程式環境的 NSG

每個 NSG 都具有特定連接埠及通訊協定，開放給安全且正確的解決方案作業使用。 如需詳細資訊，請參閱 [PCI 指導方針 - 網路安全性群組](#network-security-groups)。

此外，以下設定會針對每個 NSG 啟用：

- 啟用的[診斷記錄和事件](/azure/virtual-network/virtual-network-nsg-manage-log)會儲存在儲存體帳戶 
- 將 OMS Log Analytics 連線至 [NSG 的診斷](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

 
#### <a name="subnets"></a>子網路
 確認每個子網路都與對應的 NSG 相關聯。

#### <a name="custom-domain-ssl-certificates"></a>自訂網域 SSL 憑證
 使用自訂網域 SSL 憑證來啟用 HTTPS 流量。

### <a name="data-at-rest"></a>待用資料

架構會使用加密、資料庫稽核及其他量值來保護待用資料。

#### <a name="azure-storage"></a>Azure 儲存體

為符合加密的待用資料需求，所有 [Azure 儲存體](https://azure.microsoft.com/services/storage/)都會使用[儲存體服務加密](/azure/storage/storage-service-encryption)。

#### <a name="azure-sql-database"></a>連接字串

Azure SQL Database 執行個體會使用下列資料庫安全性量值：

- [AD 驗證和授權](/azure/sql-database/sql-database-aad-authentication)
- [SQL 資料庫稽核](/azure/sql-database/sql-database-auditing-get-started)
- [透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)
- [防火牆規則](/azure/sql-database/sql-database-firewall-configure)，可用於 ASE 背景工作集區與用戶端 IP 管理
- [SQL 威脅偵測](/azure/sql-database/sql-database-threat-detection-get-started)
- [Always Encrypted 資料行](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)
- [SQL Database 動態資料遮罩](/azure/sql-database/sql-database-dynamic-data-masking-get-started)，使用部署後 PowerShell 指令碼

### <a name="logging-and-auditing"></a>記錄與稽核

[Operations Management Suite (OMS)](/azure/operations-management-suite/)可以 Contoso Webstore 提供廣泛的記錄之所有系統和使用者活動，包括財務資料記錄。 可對變更進行檢閱及驗證以確保正確性。 

- **活動記錄檔。**  [活動記錄](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供深入了解您的訂用帳戶中的資源執行的作業。
- **診斷記錄檔。**  [診斷記錄檔](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)所發出的每個資源的所有記錄檔。 這些記錄檔包含 Windows 事件系統記錄檔、 Azure Blob 儲存體記錄、 資料表和佇列記錄檔。
- **防火牆記錄檔。**  應用程式閘道提供完整診斷，並存取記錄檔。 防火牆記錄可供已啟用 WAF 的應用程式閘道資源使用。
- **記錄封存。**  所有的診斷記錄檔會設定為寫入到集中式且加密的 Azure 儲存體帳戶進行保存為已定義的保留期間 （2 天）。 接著，記錄會連線至 Azure Log Analytics 以進行處理、儲存及進行儀表板管理。 [Log Analytics](https://azure.microsoft.com/services/log-analytics) 是種 OMS 服務，可協助您收集和分析雲端和內部部署環境中的資源所產生的資料。

### <a name="encryption-and-secrets-management"></a>加密和密碼管理

Contoso Webstore 加密所有機密資料，並管理金鑰，並防止擷取 CHD 使用 Azure 金鑰保存庫。

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 
- [SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption)用來加密所有客戶的財務資料。
- 資料會使用 [Azure 磁碟加密](/azure/security/azure-security-disk-encryption)和 BitLocker 來儲存在磁碟上。

### <a name="identity-management"></a>身分識別管理

下列技術可在 Azure 環境中提供身分識別管理功能。

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/)是 Microsoft 多租用戶雲端式目錄和身分識別管理服務。 解決方案的所有使用者都是在 Azure Active Directory 中建立，包括存取 SQL Database 的使用者。
- 應用程式的驗證是使用 Azure AD 執行。 如需詳細資訊，請參閱[整合應用程式與 Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications)。 此外，資料庫資料行加密也會使用 Azure AD 驗證至 Azure SQL Database 應用程式。 如需詳細資訊，請參閱 [Always Encrypted：保護 SQL Database 中的敏感性資料](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)。 
- [Azure Active Directory 識別身分保護](/azure/active-directory/active-directory-identityprotection)偵測到潛在的弱點可能會影響您的組織識別、 設定與貴組織的身分識別，相關的偵測到可疑活動的自動回應及調查可疑的事件，並採取適當的動作，加以解決。
- [Azure 角色型存取控制 (RBAC)](/azure/active-directory/role-based-access-control-configure) 可以對 Azure 進行精確且專注的存取權管理。 可存取訂用帳戶的身分會限制為訂用帳戶系統管理員，而可存取 Azure Key Vault 的身分則是限制為所有使用者。

若要了解使用 Azure SQL Database 安全性功能的詳細資訊，請參閱 [Contoso 診所示範應用程式](https://github.com/Microsoft/azure-sql-security-sample)範例。
   
### <a name="web-and-compute-resources"></a>Web 與計算資源

#### <a name="app-service-environment"></a>App Service 環境


            [Azure App Service](/azure/app-service/) 是受控服務，適用於部署 Web 應用程式。 Contoso Webstore 應用程式會部署為 [App Service Web 應用程式](/azure/app-service-web/app-service-web-overview)。

[Azure App Service Environment (ASE v2)](/azure/app-service/app-service-environment/intro) 是 App Service 的功能，可提供完全隔離和專用的環境，以便安全地大規模執行 App Service 應用程式。 此基礎結構會使用此進階服務方案來符合 PCI DSS 的規範。

ASE 已經過隔離，可執行只有單一客戶的應用程式，且一律會部署到虛擬網路。 客戶對於輸入和輸出的應用程式網路流量都有更細微的控制，且應用程式可以透過虛擬網路建立與內部部署公司資源的高速安全連線。

下列控制項/設定可允許此架構使用 ASE：

- 受保護的虛擬網路內裝載和網路安全性規則
- 設定為使用 HTTPS 通訊的自我簽署 ILB 憑證 ASE
- [內部負載平衡模式](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (模式 3)
- [TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings)停用
- [TLS 加密](/azure/app-service-web/app-service-app-service-environment-custom-settings)變更
- 控制[輸入流量的 N/W 連接埠](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 
- [WAF-限制資料](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [SQL 資料庫流量](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)允許


#### <a name="jumpbox-bastion-host"></a>Jumpbox (防禦主機)

App Service 環境安全，並鎖住，因為需要一個機制，允許任何 DevOps 版本或可能是必要的例如能夠監視使用 Kudu 的 web 應用程式的變更。 受保護虛擬機器可讓您連線 TCP 3389 以外的連接埠上的 vm NAT 負載平衡器後方。 

虛擬機器已透過下列設定建立為 jumpbox (防禦主機)：

-   [反惡意程式碼軟體擴充功能](/azure/security/azure-security-antimalware)
-   [OMS 擴充功能](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure 診斷擴充功能](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure 磁碟加密](/azure/security/azure-security-disk-encryption)使用 Azure 金鑰保存庫 
-   [自動關閉原則](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)以減少在不使用時的虛擬機器資源的耗用量

### <a name="security-and-malware-protection"></a>安全性和惡意程式碼防護

[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)提供集中式的所有 Azure 資源的安全性狀態檢視。 只需看一眼，您就可以確認安全性控制項是否已就緒並正確設定，且可以快速找出任何需要注意的資源。  

[Azure Advisor](/azure/advisor/advisor-overview) 是個人化的雲端顧問，可協助您依最佳做法來最佳化您的 Azure 部署。 它會分析您的資源設定和使用遙測，並再建議解決方案，協助提升成本效益、 效能、 高可用性和您的 Azure 資源的安全性。

[Microsoft 反惡意程式碼](/azure/security/azure-security-antimalware)Azure 雲端服務和虛擬機器的即時保護功能，可協助識別並移除病毒、 間諜軟體及其他惡意軟體，可設定的警示時的已知惡意或垃圾軟體軟體嘗試自行安裝或 Azure 系統上執行。

### <a name="operations-management"></a>Operations Management

#### <a name="application-insights"></a>Application Insights

使用 [Application Insights](https://azure.microsoft.com/services/application-insights/) 來透過應用程式效能管理和立即分析，取得可操作的深入資訊。

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) 是 Operations Management Suite (OMS) 中的一項服務，可協助您收集和分析雲端和內部部署環境中的資源所產生的資料。

#### <a name="oms-solutions"></a>OMS 解決方案

您應該考慮和設定這些額外的 OMS 解決方案： 
- [活動 Log Analytics](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
- [使用 Azure 網路分析](/azure/log-analytics/log-analytics-azure-networking-analytics?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Azure SQL 分析](/azure/log-analytics/log-analytics-azure-sql)
- [變更追蹤](/azure/log-analytics/log-analytics-change-tracking?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [金鑰保存庫分析](/azure/log-analytics/log-analytics-azure-key-vault?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [服務對應](/azure/operations-management-suite/operations-management-suite-service-map)
- [安全性和稽核](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [反惡意程式碼](/azure/log-analytics/log-analytics-malware?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [更新管理](/azure/operations-management-suite/oms-solution-update-management)

### <a name="security-center-integration"></a>資訊安全中心整合

預設的部署被要提供的基準線，表示為狀況良好且安全的設定狀態的資訊安全中心建議事項。 您可以在 Azure 資訊安全中心啟用資料收集。 如需詳細資訊，請參閱 [Azure 資訊安全中心 - 使用者入門](/azure/security-center/security-center-get-started)。

## <a name="deploy-the-solution"></a>部署解決方案

部署此解決方案的元件中可用[付款處理藍圖原始程式碼儲存機制][code-repo]。 基本架構的部署需要透過 Microsoft PowerShell v5 執行多個步驟。 若要連線至網站，您必須提供自訂網域名稱 (例如 contoso.com)。 這會使用步驟 2 中的 `-customHostName`參數來指定。 如需詳細資訊，請參閱[針對 Azure Web Apps 購買自訂網域名稱](/azure/app-service-web/custom-dns-web-site-buydomains-web-app)。 自訂網域名稱並不是成功部署和執行解決方案的必要項目，但是您會無法連線至用於示範的網站。

指令碼會將網域使用者新增至您指定的 Azure AD 租用戶。 Microsoft 建議您建立新的 Azure AD 租用戶使用做為測試。

如果您在部署期間遇到任何問題，請參閱[常見問題和疑難排解](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md)。

Microsoft 強眴呤 PowerShell 的全新安裝，用來部署方案。 或者，請確認您已使用可正確執行安裝指令碼的最新模組。 這個範例登入 jumpbox （防禦主機），並執行下列命令。 請注意，這會啟用自訂網域命令。


1. 安裝所需的模組，並正確地設定系統管理員角色。
 
    ```powershell
     .\0-Setup-AdministrativeAccountAndPermission.ps1 
        -azureADDomainName contosowebstore.com
        -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -configureGlobalAdmin 
        -installModules
    ```
    如需詳細的使用方式指示，請參閱[指令碼指示 - 安裝系統管理帳戶和權限](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/0-Setup-AdministrativeAccountAndPermission.md)。
    
2. 安裝方案更新管理。 
 
    ```powershell
    .\1-DeployAndConfigureAzureResources.ps1 
        -resourceGroupName contosowebstore
        -globalAdminUserName adminXX@contosowebstore.com 
        -globalAdminPassword **************
        -azureADDomainName contosowebstore.com 
        -subscriptionID XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
        -suffix PCIcontosowebstore
        -customHostName contosowebstore.com
        -sqlTDAlertEmailAddress edna@contosowebstore.com 
        -enableSSL
        -enableADDomainPasswordPolicy 
    ```
    
    如需詳細的使用方式指示，請參閱[指令碼指示 - 部署和設定 Azure 資源](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md)。
    
3. OMS 記錄和監視。 部署方案時[Microsoft Operations Management Suite (OMS)](/azure/operations-management-suite/operations-management-suite-overview)可以開啟工作區，並提供解決方案的儲存機制中的範例範本可以用來說明如何設定監視儀表板. 如範例 OMS 範本，請參閱[omsDashboards 資料夾](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md)。 請注意必須在 OMS 中收集資料，範本才能正確部署。 根據網站活動，這可能需要花費一小時以上。
 
    設定您的 OMS 記錄時，請考慮加入這些資源：
 
    - Microsoft.Network/applicationGateways
    - Microsoft.Network/NetworkSecurityGroups
    - Microsoft.Web/serverFarms
    - Microsoft.Sql/servers/databases
    - Microsoft.Compute/virtualMachines
    - Microsoft.Web/sites
    - Microsoft.KeyVault/vaults
    - Microsoft.Automation/automationAccounts
 

    
## <a name="threat-model"></a>威脅模型

適用於 Contoso Webstore 的資料流程圖 (DFD) 和範例威脅模型[付款處理藍圖威脅模型](https://aka.ms/pciblueprintthreatmodel)。

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>客戶責任對照表

客戶必須負責保留一份[責任摘要矩陣](https://aka.ms/fsiblueprintcrm)，其中會概述 FFIEC 需求的客戶與 Microsoft Azure 的責任的責任。



## <a name="disclaimer-and-acknowledgments"></a>免責聲明和通知

*2017 年 9 月*

- 此文件僅供參考之用。 Microsoft 和 AVYAN 對本文件中的資訊不做任何明示、暗示或成文之擔保 這份文件係依「現狀」提供。 資訊及檢視在此文件，包括 URL 及其他網際網路網站參考資料，可能會變更恕不另行通知。 閱讀這份文件的客戶須自行承擔使用風險。  
- 本文件未提供給客戶任何 Microsoft 或 Avyan 產品或解決方案中任何智慧財產的任何法定權利。  
- 客戶可以複製並使用這份文件，供內部參考之用。  

  > [!NOTE]
  > 本白皮書中的特定建議可能會導致增加的資料、 網路或在 Azure 中，計算資源使用狀況，且可能增加客戶的 Azure 授權或訂用帳戶成本。  

- 這份文件中的解決方案旨在作為基本架構，請勿毫無修改地直接用於生產環境。 達到相容性需要客戶諮詢其稽核者驗證最終客戶解決方案。  
- 此頁面上的所有客戶名稱、交易記錄和任何相關資料皆屬虛構，僅為此基本架構而建立，並僅供示範用。 並未影射或關聯任何真實的人、事、物。  
- 此解決方案由 Microsoft 與 Avyan Consulting 共同開發，而且經過 [MIT 授權](https://opensource.org/licenses/MIT)許可。

### <a name="document-authors"></a>文件作者

* *Frank Simorjay (Microsoft)*  

[code-repo]: https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms "程式碼存放庫"
