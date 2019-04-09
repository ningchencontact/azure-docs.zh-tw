---
title: 什麼是 Azure Stack？ | Microsoft Docs
description: 了解 Azure Stack 如何讓您在資料中心內執行 Azure 服務。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: e5e05ad4f2ae7e718e160916144f03bfb74a2a52
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631243"
---
# <a name="azure-stack-overview"></a>Azure Stack 概觀

Azure Stack 是 Azure 的一項擴充功能，可讓您在內部部署環境中執行應用程式，並在您的資料中心提供 Azure 服務。 透過一致的雲端平台，組織可以安心地讓根據業務需求制定技術決策，而不是讓商業決策遷就技術限制。

## <a name="why-use-azure-stack"></a>為何要使用 Azure Stack？

Azure 提供了豐富的平台，讓開發人員建置新型應用程式。 不過，某些雲端式應用程式仍面臨延遲、連線不穩定和法規等障礙。 Azure 和 Azure Stack 針對面向客戶與內部的企業營運應用程式，開拓全新的混合式雲端使用案例：

- **邊緣和離線解決方案**。 藉由在 Azure Stack 本機處理資料，然後在 Azure 中彙總以進一步分析，並在兩者之間使用通用應用程式邏輯，來解決延遲和連線需求。 您甚至可在網際網路連線中斷、而未連線至 Azure 的情況下部署 Azure Stack。 例如，工廠樓層、海上船隻和礦井，都處於這種情況。

- **符合各種法規的雲端應用程式**。 在 Azure 中開發及部署應用程式，並且有充分的彈性可在 Azure Stack 上的內部部署進行部署以符合法規或原則需求，完全不需要變更程式碼。 應用程式範例包括全域稽核、財務報告、外匯交易、線上遊戲和費用報告。

- **內部部署雲端應用程式模型**。 使用 Azure 服務、容器、無伺服器和微服務架構來更新及延伸現有應用程式，或建置新的應用程式。 跨雲端中的 Azure 和內部部署的 Azure Stack 使用一致的 DevOps 程序，讓任務關鍵性應用程式加速進行應用程式現代化。

Azure Stack 提供下列功能以支援這些使用案例：

- 整合式傳遞體驗，以使用受信任的硬體合作夥伴專門打造的 Azure Stack 整合系統，快速啟動並執行。 傳遞之後，Azure Stack 可透過 System Center Operations Manager 管理組件或 Nagios 擴充功能輕易整合至資料中心的監視功能。

- 將 Azure Active Directory (Azure AD) 用於 Azure 和 Azure Stack 混合式環境，並將 Active Directory 同盟服務 (AD FS) 運用於無連線部署的彈性身分識別管理。 

- Azure 一致的應用程式開發環境，可將開發人員的生產力最大化，並啟用跨混合式環境的一般 DevOps 方法。

- 使用混合式雲端運算能力從內部部署傳遞 Azure 服務。 採用跨 Azure 和 Azure Stack 的通用運作方式，使用與 Azure 相同的系統管理體驗和工具部署和操作 Azure IaaS 和 PaaS 服務。 Microsoft 持續將 Azure 創新傳遞至 Azure Stack，包括新的 Azure 服務、現有服務的更新，以及其他 Azure Marketplace 應用程式和映像。

## <a name="azure-stack-architecture"></a>Azure Stack 架構
Azure Stack 整合式系統包含由 4 到 16 個伺服器組成的機架，而這些伺服器由受信任的硬體合作夥伴所建置，並直接傳遞至您的資料中心。 傳遞之後，解決方案提供者將協助您部署整合式系統，並確保 Azure Stack 解決方案符合您的業務需求。 您必須確保已具備所有必要的電源和冷卻系統、邊界連線能力，以及其他必要的資料中心整合需求，以備妥資料中心。 

> 如需有關 Azure Stack 資料中心整合體驗的詳細資訊，請參閱 [Azure Stack 資料中心整合](azure-stack-customer-journey.md)。

Azure Stack 是以業界標準硬體建置的，並使用您用來管理 Azure 訂用帳戶的相同工具進行管理。 因此，無論您是否連線至 Azure，都可以套用一致的 DevOps 程序。 

Azure Stack 架構可讓您在處於遠端位置、連線不穩定，或網際網路連線中斷之處的邊緣上提供 Azure 服務。 您可以建立混合式解決方案，以在本機使用 Azure Stack 處理資料，以及再使用 Azure 加以彙總以進行其他處理和分析。 最後，由於 Azure Stack 安裝於內部，因此您可以靈活地在內部部署雲端應用程式而不需要變更任何程式碼，以符合特定的法規或原則需求。 

## <a name="deployment-options"></a>部署選項

### <a name="production-or-evaluation-environments"></a>生產或評估環境
Azure Stack 提供兩種部署選項以因應您的需求：用於生產環境的 Azure Stack 整合式系統，和用來評估 Azure Stack 的 Azure Stack 開發套件 (ASDK)：

- **Azure Stack 整合系統**。 Azure Stack 整合系統是透過 Microsoft 與硬體合作夥伴的合作而提供的，可建立既提供雲端步調的創新又兼顧計算管理簡易性的解決方案。 由於是以整合的軟硬體系統形式來提供 Azure Stack，因此您可以獲得所需的彈性和控制力，以及從雲端創新的能力。 Azure Stack 整合系統的大小範圍為 4 到 16 個節點，並且由硬體合作夥伴與 Microsoft 共同支援。 請使用 Azure Stack 整合系統，來為生產環境工作負載建立新案例和部署新解決方案。

- **Azure Stack 開發套件**。 [Azure Stack 開發套件 (ASDK)](./asdk/asdk-what-is.md) 是單一節點的免費 Azure Stack 部署，您可以用來評估和了解 Azure Stack。 您也可以使用 ASDK 作為開發人員環境，來使用與 Azure 一致的 API 和工具建置應用程式。 不過，ASDK 不適用於生產環境，且相較於完整的整合式系統生產部署具有下列限制：

    - ASDK 只能與單一 Azure Active Directory (Azure AD) 或 Active Directory 同盟服務 (AD FS) 識別提供者相關聯。
    - Azure Stack 元件會部署在一部主機電腦上，因此租用戶資源可用的實體資源有限。 此組態不適合進行縮放或效能評估。
    - 因為單一主機和 NIC 部署需求，所以網路功能案例會受到限制。

### <a name="connection-models"></a>連線模型
您可以選擇在**已連線**或**未連線**網際網路 (和 Azure) 的情況下部署 Azure Stack。 此選擇會定義您身分識別存放區可用的選項 (Azure AD 或 AD FS) 和計費模型 (使用時付費式的計費或以容量為基礎的計費)。

> 如需詳細資訊，請參閱[已連線](azure-stack-connected-deployment.md)和[未連線](azure-stack-disconnected-deployment.md)部署模型的考量。 

### <a name="identity-provider"></a>識別提供者 
Azure Stack 使用 Azure Active Directory (Azure AD) 或 Active Directory 同盟服務 (AD FS) 作為識別提供者，以建立 Azure Stack 身分識別。 

> [!IMPORTANT]
> 這是重要決策點！ 選擇以 Azure AD 或 AD FS 作為識別提供者，是您必須在部署期間進行的一次性決定。 若未重新部署整個系統，之後便無法變更此選擇。

Azure AD 是 Microsoft 的雲端式、多租用識別提供者。 就已連線網際網路的部署而言，大部分的混合式情節會使用 Azure AD 作為身分識別存放區。 不過，您可以選擇使用 Active Directory 同盟服務 (AD FS) 進行中斷連線的 Azure Stack 部署。 Azure Stack、資源提供者和其他應用程式使用 AD FS 的方式，與使用 Azure AD 的方式差不多。 Azure Stack 包含自己的 Active Directory 執行個體，以及 Active Directory 圖形 API。 

> 您可以在 [Azure Stack 的身分識別概觀](azure-stack-identity-overview.md)深入了解 Azure Stack 身分識別的考量。

## <a name="how-is-azure-stack-managed"></a>Azure Stack 會受到哪些管理？
在整合式系統部署或 ASDK 安裝中部署 Azure Stack 之後，系統管理入口網站、使用者入口網站和 PowerShell 將是與 Azure Stack 互動的主要方法。 每個 Azure Stack 入口網站皆由 Azure Resource Manager 的個別執行個體支援。 **Azure Stack 操作員**會使用系統管理入口網站來管理 Azure Stack 以及執行一些作業，例如建立租用戶供應項目、維護整合式系統的健康情況以及監視其狀態等等。 使用者入口網站 (也稱為租用戶入口網站) 提供使用雲端資源的自助式服務體驗，像是虛擬機器、儲存體帳戶，以及 Web Apps。 

> 如需使用系統管理入口網站來管理 Azure Stack 的詳細資訊，請參閱 [Azure Stack 系統管理入口網站快速入門](azure-stack-manage-portals.md)。

身為 Azure Stack 操作員，您可以提供多種不同的服務與應用程式，例如[虛擬機器](azure-stack-tutorial-tenant-vm.md)、[Web 應用程式](azure-stack-app-service-overview.md)、高可用性 [SQL Server](azure-stack-tutorial-sql.md) 和 [MySQL Server](azure-stack-tutorial-mysql.md)。 您也可以使用 [Azure Stack 快速入門 Azure Resource Manager 範本](https://github.com/Azure/AzureStack-QuickStart-Templates)來部署 SharePoint、Exchange 和更多功能。 

使用系統管理入口網站可以[設定 Azure Stack](azure-stack-plan-offer-quota-overview.md)，以使用方案、配額、供應項目和訂用帳戶將服務提供給租用戶。 租用戶可以訂閱多個供應項目。 供應項目可以有一個或多個方案，而方案則可有一或多項服務。 操作員也管理容量並會回應警示。 

設定 Azure Stack 之後，**Azure Stack 使用者** (也稱為租用戶) 即可使用操作員所提供的服務。 使用者可以佈建、監視及管理他們所訂閱的服務，例如 Web Apps、儲存體以及虛擬機器。

> 若要深入了解如何管理 Azure Stack，包括哪些帳戶可用於何處，一般操作員責任、應告知使用者的事項，以及如何取得協助，請檢閱 [Azure Stack 系統管理基本概念](azure-stack-manage-basics.md)。

## <a name="resource-providers"></a>資源提供者 
資源提供者是為所有 Azure Stack IaaS 與 PaaS 服務構成基礎的 Web 服務。 Azure Resource Manager 會仰賴不同的資源提供者來提供服務的存取權。 每個資源提供者皆可協助您設定及控制其各自的資源。 服務管理員也可以加入新的自訂資源提供者。 

### <a name="foundational-resource-providers"></a>基本資源提供者 
基本 IaaS 資源提供者有三個：計算、網路和儲存體：

- **計算**。 Azure Stack 的租用戶可透過計算資源提供者建立自己的虛擬機器。 計算資源提供者也有功能可建立虛擬機器，以及虛擬機器擴充功能。 虛擬機器擴充功能服務有助於為 Windows 與 Linux 虛擬機器提供 IaaS 功能。  例如，您可以使用計算資源提供者來佈建 Linux 虛擬機器，並在部署期間執行 Bash 指令碼以設定虛擬機器。
- **網路資源提供者**。 網路資源提供者可為私人雲端提供一系列的軟體定義網路功能 (SDN) 以及網路函式虛擬化 (NFV) 功能。 您可以使用網路資源提供者來建立資源，像是軟體負載平衡器、公用 IP、網路安全性群組，以及虛擬網路。
- **儲存體資源提供者**。 儲存體資源提供者提供四個 Azure 一致的儲存體服務：[Blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage)、[佇列](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage)、[資料表](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)，以及提供秘密 (例如密碼和憑證) 的管理和稽核功能的 KeyVault 帳戶管理。 儲存體資源提供者也提供儲存體雲端管理服務，以輔助 Azure 一致的儲存體服務的服務提供者管理。 Azure 儲存體能為儲存及擷取大量非結構化資料提供彈性，例如 Azure Blob 的文件與媒體檔案，以及具備 Azure 資料表的結構化 NoSQL 資料。 

### <a name="optional-resource-providers"></a>選擇性資源提供者
有三個選擇性 PaaS 資源提供者可供您部署並搭配 Azure Stack 使用：App Service、SQL Server 和 MySQL Server 資源提供者：

- **App Service**。 [Azure App Service on Azure Stack](azure-stack-app-service-overview.md) 是 Microsoft Azure 的平台即服務 (PaaS) 供應項目，提供給 Azure Stack 使用。 此服務可讓您的內部或外部客戶為任何平台或裝置建立 Web、API 和 Azure Functions 應用程式。 
- **SQL Server**。 使用 [SQL Server 資源提供者](azure-stack-sql-resource-provider.md)，提供 SQL 資料庫作為 Azure Stack 的服務。 在您安裝資源提供者，並將它連線到一或多個 SQL Server 執行個體之後，您和使用者就可以為雲端原生應用程式、使用 SQL 的網站，以及其他使用 SQL 的工作負載建立資料庫。
- **MySQL Server**。 使用 [MySQL Server 資源提供者](azure-stack-mysql-resource-provider-deploy.md)將 MySQL 資料庫公開成 Azure Stack 服務。 MySQL 資源提供者在 Windows Server 2016 Server Core 虛擬機器 (VM) 上會以服務的形式執行。

## <a name="providing-high-availability"></a>提供高可用性
為了達到 Azure 中多 VM 生產環境的高可用性，會將 VM 放在[可用性設定組](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)中，此設定組會將 VM 分散在多個容錯網域和更新網域中。 在較小規模的 Azure Stack 中，可用性設定組中的容錯網域會定義為縮放單位中的單一節點。  

雖然 Azure Stack 的基礎結構已經具備失敗復原能力，但在發生硬體故障時，基礎技術 (容錯移轉叢集) 仍然會造成受影響實體伺服器上的 VM 產生一些停機時間。 Azure Stack 支援的可用性設定組最多可以有三個容錯網域 (與 Azure 一致)。

- **容錯網域**。 系統會將放在可用性設定組中的 VM 儘可能平均分散到多個容錯網域 (Azure Stack 節點)，讓這些 VM 在實體上彼此隔離。 當發生硬體故障時，失敗容錯網域中的 VM 會在其他容錯網域中重新啟動，但可能的話，會放在與相同可用性設定組中其他 VM 不同的容錯網域中。 當硬體回到線上時，系統會將 VM 重新平衡以保持高可用性。 
 
- **更新網域**。 更新網域是另一個可在可用性設定組中提供高可用性的 Azure 概念。 更新網域是可以同時進行維護的基礎硬體邏輯群組。 位於相同更新網域中的 VM 會在預定進行的維護期間一起重新啟動。 當租用戶在可用性設定組內建立 VM 時，Azure 平台會自動將 VM 分散到這些更新網域中。 在 Azure Stack 中，會先將 VM 即時移轉至叢集內的各個其他線上主機，然後才更新 VM 的基礎主機。 由於在主機更新期間並不會導致租用戶停機，因此 Azure Stack 上更新網域功能的存在只是為了與 Azure 的範本相容。 

## <a name="role-based-access-control"></a>角色型存取控制
您可以使用角色型存取控制 (RBAC) 在訂用帳戶、資源群組或各個資源層級，為授權的使用者、群組與服務指派角色，即可為其授與系統存取權。 每個角色皆會定義使用者、群組或服務對於 Microsoft Azure Stack 資源所具有的存取層級。

Azure Stack RBAC 有適用於所有資源類型的三個基本角色：擁有者、 參與者和讀取器。 擁有者具有所有資源的完整存取權，包括將存取權委派給其他人的權限。 參與者可以建立和管理所有類型的 Azure 資源，但是不能將存取權授與其他人。 讀者只能檢視現有的資源。 其餘的 RBAC 角色可以管理特定 Azure 資源。 例如，虛擬機器參與者角色可以建立和管理虛擬機器，但是無法管理虛擬網路或虛擬機器所連接的子網路。

> 如需詳細資訊，請參閱[管理角色型存取控制](azure-stack-manage-permissions.md)。 

## <a name="reporting-usage-data"></a>報告使用量資料
Microsoft Azure Stack 會從所有資源提供者收集彙總的使用方式資料，並將其傳輸至 Azure 以由 Azure 商務進行處理。 透過 REST API，您可以檢視 Azure Stack 上收集的使用方式資料。 同時有與 Azure 一致的租用戶 API 和提供者，以及委派的提供者 API，以取得所有租用戶訂用帳戶的使用量資料。 此資料可用於與外部工具或收費或退款服務相整合。 一旦使用方式經由 Azure 商務處理，其就可以在 Azure 計費入口網站中檢視。

> 深入了解如何[將 Azure Stack 使用量資料報告給 Azure](azure-stack-usage-reporting.md)。

## <a name="next-steps"></a>後續步驟

[比較 Azure Stack 與全域 Azure](compare-azure-azure-stack.md)

[管理基本知識](azure-stack-manage-basics.md)

[快速入門：使用 Azure Stack 系統管理入口網站](azure-stack-manage-portals.md)