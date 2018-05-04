---
title: Azure 安全性與合規性藍圖 - 符合 FFIEC 金融服務規範的工作負載
description: Azure 安全性與合規性藍圖 - 符合 FFIEC 金融服務規範的工作負載
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
ms.date: 02/09/2018
ms.author: frasim
ms.openlocfilehash: 497c5a987753cbbe577c1d042d6bf61be9d905ab
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="azure-security-and-compliance-blueprint---ffiec-financial-services-regulated-workloads"></a>Azure 安全性與合規性藍圖 - 符合 FFIEC 金融服務規範的工作負載

## <a name="overview"></a>概觀

「Azure 安全性與合規性藍圖 - 符合 FFIEC 金融服務規範的工作負載」可協助部署安全且符合規範的平台即服務 (PaaS) Web 應用程式，此應用程式是設計來在雲端中處理敏感性資料。 此藍圖包含可展示簡單參考架構的自動化指令碼和指導方針，以及可協助簡化採用 Microsoft Azure 解決方案的設計。 此藍圖說明端對端解決方案，以符合組織尋找方法在雲端中減少部署負擔和成本的需求。

此藍圖的設計目的是符合由美國會計師協會 (American Institute of Certified Public Accountants) 所設定且符合規範之嚴格標準的需求，例如，SOC 1、SOC 2、支付卡產業資料安全標準委員會的 DSS 3.2，以及適用於收集、儲存及擷取敏感性財務資料的 FFIEC。 它會透過部署解決方案以在安全、符合規範的多層式環境中管理財務資料，來示範如何正確處理此類資料。 此解決方案會部署為端對端 Azure 型 PaaS 解決方案。 

此藍圖旨在為客戶奠定基礎，以及了解在雲端中管理安全資料的需求。 此解決方案不應依現狀在生產部署中使用，但可用來了解、設計及部署 Azure 服務；它是設計來作為基準，協助客戶以安全且符合規範的方式使用 Microsoft Azure。

合格的稽核員必須認證以此藍圖為基礎的任何生產客戶解決方案。 解決方案可能會因為每個客戶的實作和地理位置詳細規格而有所不同。

如需此解決方案運作方式的簡短概觀，請觀賞這段說明及示範其部署的[影片](https://aka.ms/fsiblueprintvideo) \(英文\)。

## <a name="solution-components"></a>方案元件

架構是由下列元件組成，並使用來自 Azure PCI DSS 合規性解決方案的部署功能。

- **架構圖**。 該圖顯示 Contoso Webstore 解決方案中所用的參考架構。
- **部署範本**。 在此部署中，透過在安裝期間指定設定參數，系統會使用 [Azure Resource Manager 範本](/azure/azure-resource-manager/resource-group-overview#template-deployment)來將架構的元件自動部署至 Microsoft Azure。
- **自動化部署指令碼**。 這些指令碼可協助您部署端對端解決方案。 指令碼包括：
    - 模組安裝和[全域管理員](/azure/active-directory/active-directory-assign-admin-roles-azure-portal)安裝指令碼會用來進行安裝，以及確認必要的 PowerShell 模組和全域管理員角色已正確設定。 
    - 安裝 PowerShell 指令碼會用來部署端對端解決方案，並透過 .zip 檔案和 .bacpac 檔案提供，其中包含預先建置的示範 Web 應用程式與 [SQL 資料庫的範例](https://github.com/Microsoft/azure-sql-security-sample)。 內容。 您可以在[付款處理藍圖代碼存放庫][code-repo]檢閱此解決方案的原始程式碼。 

## <a name="architectural-diagram"></a>架構圖

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>使用者案例

此藍圖可解決下列使用案例。

> 此案例說明虛構網路商店如何將敏感性資料移至 Azure 型 PaaS 雲端解決方案。 範例解決方案會說明如何處理和收集基本使用者資訊及所選取的敏感性資料。 此工作會以「Azure 安全性與合規性藍圖 - 符合 PCI DSS 規範的付款處理」環境為借鏡。 如需擴充此工作的詳細資訊，[「實作的檢閱和指導方針」](https://aka.ms/pciblueprintprocessingoverview)\(英文\) 白皮書提供了符合 PCI DSS 規範之環境的檢閱。

### <a name="use-case"></a>使用案例
有個名為 *Contoso Webstore* 的小型網路商店已經準備好將包括客戶付款資訊在內的財務資料移到雲端。 

Contoso Webstore 的系統管理員正在尋找可快速部署以達成其目標的解決方案。 他將使用此概念證明 (POC) 與他的專案關係人一起討論如何使用 Azure 來收集、儲存和擷取財務資料，同時又能符合嚴格的合規性需求。

> 您將負責為任何使用此 POC 所用結構建置的解決方案安置適當的安全性與合規性檢閱，因為您的實作和所在地區可能有不同特性，因此可能會有不同需求。 

### <a name="elements-of-the-foundational-architecture"></a>基本架構的元素

基本架構的設計包含下列虛構元素：

網域站台 `contosowebstore.com`

採用使用者角色來說明使用案例，並提供使用者介面的深入解析。

#### <a name="role-site-and-subscription-admin"></a>角色：站台和訂用帳戶管理員

|Item      |範例|
|----------|------|
|使用者名稱： |`adminXX@contosowebstore.com`|
| 名稱： |`Global Admin Azure PCI Samples`|
|使用者類型：| `Subscription Administrator and Azure Active Directory Global Administrator`|

- 系統管理帳戶無法讀取未遮罩的財務資訊。 所有動作皆會記錄。
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

- sqladmin 帳戶無法檢視未篩選的財務資訊。 所有動作皆會記錄。
- sqladmin 帳戶可以管理 SQL Database。

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
- Edna 可以覆寫財務資訊。
- Edna 帳戶無法檢視未篩選的財務資訊。



### <a name="contoso-webstore---estimated-pricing"></a>Contoso Webstore - 估算價格

此基本架構和範例 Web 應用程式使用每月計費結構，而使用量的費用為每小時計費，這些都是決定解決方案大小時須考慮的項目。 您可以使用 [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)來估算這些成本。 從 2017年 9 月開始，此解決方案的每月費用預估為 ~ $2500，這包含 ASE v2 每月 $1000 的使用費。 這些費用會因為使用量不同而有所差異，而且可能會有所變更。 客戶應在部署時估算每月費用，可取得更準確的估計值。 

此解決方案會使用下列 Azure 服務。 部署架構的詳細資料位於＜[部署架構](#deployment-architecture)＞一節中。

>- 應用程式閘道
>- Azure Active Directory
>- App Service Environment v2
>- Log Analytics
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

透過使用已啟用 Web 應用程式防火牆 (WAF) 和 OWASP 規則集的應用程式閘道，基礎架構可減少安全性弱點帶來的風險。 其他功能包括：

- [端對端 SSL](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- 已啟用 [SSL 卸載](/azure/application-gateway/application-gateway-ssl-portal)
- 已停用 [TLS v1.0 和 v1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web 應用程式防火牆](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) \(WAF 模式\)
- 使用 OWASP 3.0 規則集的[防止模式](/azure/application-gateway/application-gateway-web-application-firewall-portal)
- 已啟用[診斷記錄](/azure/application-gateway/application-gateway-diagnostics)
- [自訂健康情況探查](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center)和 [Azure Advisor](/azure/advisor/advisor-security-recommendations)，其會提供額外的保護和通知。 Azure 資訊安全中心也會提供評價系統。

#### <a name="virtual-network"></a>虛擬網路

基本架構會定義位址空間為 10.0.0.0/16 的私人虛擬網路。

#### <a name="network-security-groups"></a>網路安全性群組

每個網路層都有專用的網路安全性群組 (NSG)：

- 適用於防火牆和應用程式閘道 WAF 的 DMZ 網路安全性群組
- 適用於管理 jumpbox 的 NSG (防禦主機)
- 適用於應用程式環境的 NSG

每個 NSG 都具有特定連接埠及通訊協定，開放給安全且正確的解決方案作業使用。 

此外，以下設定會針對每個 NSG 啟用：

- 啟用的[診斷記錄和事件](/azure/virtual-network/virtual-network-nsg-manage-log)會儲存在儲存體帳戶 
- 將 Log Analytics 連線至 [NSG 的診斷](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

 
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

[Log Analytics](https://azure.microsoft.com/services/log-analytics) 可以提供所有系統和使用者活動的大量記錄給 Contoso Webstore ，包括財務資料記錄。 可對變更進行檢閱及驗證以確保正確性。 

- **活動記錄。**  [活動記錄](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)能讓您深入了解在訂用帳戶資源上執行的作業。
- **診斷記錄。**  [診斷記錄](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)是每個資源發出的所有記錄。 這些記錄包含 Windows 事件系統記錄、Azure Blob 儲存體記錄、資料表和佇列記錄。
- **防火牆規則。**  應用程式閘道會提供完整的診斷和存取記錄。 防火牆記錄可供已啟用 WAF 的應用程式閘道資源使用。
- **記錄封存。**  所有診斷記錄都會設定為寫入到集中且加密的 Azure 儲存體帳戶進行封存，並具有定義的保留期間 (2 天)。 接著，記錄會連線至 Azure Log Analytics 以進行處理、儲存及進行儀表板管理。 [Log Analytics](https://azure.microsoft.com/services/log-analytics) 是一項服務，可協助您收集和分析雲端及內部部署環境中的資源所產生的資料。

### <a name="encryption-and-secrets-management"></a>加密和密碼管理

Contoso Webstore 會加密所有敏感性資料，並使用 Azure Key Vault 管理金鑰並防止 CHD 遭到擷取。

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 
- [SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) 會用來加密所有客戶財務資料。
- 資料會使用 [Azure 磁碟加密](/azure/security/azure-security-disk-encryption)和 BitLocker 來儲存在磁碟上。

### <a name="identity-management"></a>身分識別管理

下列技術可在 Azure 環境中提供身分識別管理功能。

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 多租用戶雲端式目錄和身分識別管理服務。 解決方案的所有使用者都是在 Azure Active Directory 中建立，包括存取 SQL Database 的使用者。
- 應用程式的驗證是使用 Azure AD 執行。 如需詳細資訊，請參閱[整合應用程式與 Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications)。 此外，資料庫資料行加密也會使用 Azure AD，向 Azure SQL Database 驗證應用程式。 如需詳細資訊，請參閱 [Always Encrypted：保護 SQL Database 中的敏感性資料](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)。 
- [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) 會偵測可能影響您組織身分識別的潛在弱點、為偵測到的組織身分識別相關可疑活動設定自動回應，以及調查可疑事件並採取適當動作來解決這些可疑事件。
- [Azure 角色型存取控制 (RBAC)](/azure/role-based-access-control/role-assignments-portal) 可以對 Azure 進行精確且專注的存取權管理。 可存取訂用帳戶的身分會限制為訂用帳戶系統管理員，而可存取 Azure Key Vault 的身分則是限制為所有使用者。

若要了解使用 Azure SQL Database 安全性功能的詳細資訊，請參閱 [Contoso 診所示範應用程式](https://github.com/Microsoft/azure-sql-security-sample)範例。
   
### <a name="web-and-compute-resources"></a>Web 與計算資源

#### <a name="app-service-environment"></a>App Service 環境

[Azure App Service](/azure/app-service/) 是受控服務，適用於部署 Web 應用程式。 Contoso Webstore 應用程式會部署為 [App Service Web 應用程式](/azure/app-service-web/app-service-web-overview)。

[Azure App Service Environment (ASE v2)](/azure/app-service/app-service-environment/intro) 是 App Service 的功能，可提供完全隔離和專用的環境，以便安全地大規模執行 App Service 應用程式。 此基礎結構會使用此進階服務方案來符合 PCI DSS 的規範。

ASE 已經過隔離，可執行只有單一客戶的應用程式，且一律會部署到虛擬網路。 客戶對於輸入和輸出的應用程式網路流量都有更細微的控制，且應用程式可以透過虛擬網路建立與內部部署公司資源的高速安全連線。

下列控制項/設定可允許此架構使用 ASE：

- 在安全的虛擬網路和網路安全性規則內主控
- 使用自我簽署的 ILB 憑證設定 ASE，以進行 HTTPS 通訊
- [內部負載平衡模式](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (模式 3)
- 已停用 [TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings)
- 已變更 [TLS 加密](/azure/app-service-web/app-service-app-service-environment-custom-settings)
- 控制[輸入流量的 N/W 連接埠](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 
- [WAF：限制資料](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- 已允許 [SQL Database 流量](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)


#### <a name="jumpbox-bastion-host"></a>Jumpbox (防禦主機)

由於 App Service 環境會受到保護且鎖定，因此需要一個機制來允許任何可能必要的 DevOps 發行或變更，例如使用 Kudu 監視 Web 應用程式的功能。 虛擬機器會在 NAT 負載平衡器後方受到保護，NAT 負載平衡器提供在 TCP 3389 以外的連接埠上連線到虛擬機器的能力。 

虛擬機器已透過下列設定建立為 jumpbox (防禦主機)：

-   [反惡意程式碼軟體擴充功能](/azure/security/azure-security-antimalware)
-   [Log Analytics 擴充功能](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure 診斷擴充功能](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   使用 Azure Key Vault 的 [Azure 磁碟加密](/azure/security/azure-security-disk-encryption) 
-   [自動關閉原則](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) \(英文\)，可減少不使用虛擬機器資源時的耗用量。

### <a name="security-and-malware-protection"></a>安全性和惡意程式碼防護

[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)可提供所有 Azure 資源安全性狀態的集中檢閱。 只需看一眼，您就可以確認安全性控制項是否已就緒並正確設定，且可以快速找出任何需要注意的資源。  

[Azure Advisor](/azure/advisor/advisor-overview) 是個人化的雲端顧問，可協助您依最佳做法來最佳化您的 Azure 部署。 它會分析您的資源設定和使用量遙測，然後建議可協助改善 Azure 資源的成本效益、效能、高可用性和安全性的解決方案。

適用於 Azure 雲端服務和虛擬機器的 [Microsoft Antimalware](/azure/security/azure-security-antimalware) 是一個即時保護功能，能夠協助識別並移除病毒、間諜軟體與其他惡意軟體，且具有可設定的警示，可在已知的惡意或垃圾軟體嘗試在 Azure 系統上安裝或執行時發出警示。

### <a name="operations-management"></a>Operations Management

#### <a name="application-insights"></a>Application Insights

使用 [Application Insights](https://azure.microsoft.com/services/application-insights/) 來透過應用程式效能管理和立即分析，取得可操作的深入資訊。

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) 是一項服務，可協助您收集和分析雲端及內部部署環境中的資源所產生的資料。

#### <a name="managment-solutions"></a>管理解決方案

應考量以下額外的管理解決方案並進行設定： 
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

預設部署的目的是提供資訊安全中心建議的基準，以指出狀況良好且安全的設定狀態。 您可以在 Azure 資訊安全中心啟用資料收集。 如需詳細資訊，請參閱 [Azure 資訊安全中心 - 使用者入門](/azure/security-center/security-center-get-started)。

## <a name="deploy-the-solution"></a>部署解決方案

用於部署此解決方案的元件可在[藍圖程式碼存放庫][code-repo]中找到。 基本架構的部署需要透過 Microsoft PowerShell v5 執行多個步驟。 若要連線至網站，您必須提供自訂網域名稱 (例如 contoso.com)。 這會使用步驟 2 中的 `-customHostName`參數來指定。 如需詳細資訊，請參閱[針對 Azure Web Apps 購買自訂網域名稱](/azure/app-service-web/custom-dns-web-site-buydomains-web-app)。 自訂網域名稱並不是成功部署和執行解決方案的必要項目，但是您會無法連線至用於示範的網站。

指令碼會將網域使用者新增至您指定的 Azure AD 租用戶。 Microsoft 建議您建立新的 Azure AD 租用戶，以作為測試使用。

如果您在部署期間遇到任何問題，請參閱[常見問題和疑難排解](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md)。

Microsoft 強烈建議使用 PowerShell 的全新安裝來部署解決方案。 或者，請確認您已使用可正確執行安裝指令碼的最新模組。 此範例會登入 jumpbox (防禦主機)，並執行下列命令。 請注意，這會啟用自訂網域命令。


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
    
2. 安裝解決方案更新管理。 
 
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
    
3. Log Analytics 記錄和監視。 當您部署方案後，即可開啟 Log Analytics 工作區，並可使用解決方案存放庫中的範例範本來說明如何設定監視儀表板。 如需範例範本，請參考 [omsDashboards 資料夾](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md)。 請注意必須在 Log Analytics 中收集資料，範本才能正確部署。 根據網站活動，這可能需要花費一小時以上。
 
    設定您的 Log Analytics 記錄時，請考慮加入這些資源：
 
    - Microsoft.Network/applicationGateways
    - Microsoft.Network/NetworkSecurityGroups
    - Microsoft.Web/serverFarms
    - Microsoft.Sql/servers/databases
    - Microsoft.Compute/virtualMachines
    - Microsoft.Web/sites
    - Microsoft.KeyVault/vaults
    - Microsoft.Automation/automationAccounts
 

    
## <a name="threat-model"></a>威脅模型

適用於 Contoso Webstore [藍圖威脅模型](https://aka.ms/pciblueprintthreatmodel) \(英文\) 的資料流程圖 (DFD) 和範例威脅模型。

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>客戶責任對照表

客戶必須保留一份[責任摘要對照表](https://aka.ms/fsiblueprintcrm)，其中會概述屬於客戶責任的 FFIEC 需求以及屬於 Microsoft Azure 責任的 FFIEC 需求。



## <a name="disclaimer-and-acknowledgments"></a>免責聲明與認知

*2017 年 9 月*

- 此文件僅供參考之用。 Microsoft 和 AVYAN 對本文件中的資訊不做任何明示、暗示或成文之擔保 這份文件係依「現狀」提供。 本文件中說明的資訊與畫面 (包括 URL 及其他網際網路網站參考資料) 如有變更，恕不另行通知。 閱讀這份文件的客戶須自行承擔使用風險。  
- 本文件未提供給客戶任何 Microsoft 或 Avyan 產品或解決方案中任何智慧財產的任何法定權利。  
- 客戶可以複製並使用這份文件，供內部參考之用。  

  > [!NOTE]
  > 本文件的某些建議可能會導致資料、網路或 Azure 計算資源使用量增加，並可能增加客戶的 Azure 授權或訂用帳戶成本。  

- 這份文件中的解決方案旨在作為基本架構，請勿毫無修改地直接用於生產環境。 達到合規性需要客戶諮詢其稽核者，以驗證最終客戶解決方案。  
- 此頁面上的所有客戶名稱、交易記錄和任何相關資料皆屬虛構，僅為此基本架構而建立，並僅供示範用。 並未影射或關聯任何真實的人、事、物。  
- 此解決方案由 Microsoft 與 Avyan Consulting 共同開發，而且經過 [MIT 授權](https://opensource.org/licenses/MIT)許可。

### <a name="document-authors"></a>文件作者

* *Frank Simorjay (Microsoft)*  

[code-repo]: https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms "程式碼存放庫"
