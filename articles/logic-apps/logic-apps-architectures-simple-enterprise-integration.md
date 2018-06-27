---
title: Azure 整合服務 - 簡單的企業整合
description: 示範如何使用 Azure Logic Apps 和「Azure API 管理」來實作簡單企業整合模式的參考架構
author: mattfarm
manager: jonfan
editor: ''
services: logic-apps api-management
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/15/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 17f591a470bf65d3c9365bca9c5ae2d5a6c9574f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231891"
---
# <a name="simple-enterprise-integration---reference-architecture"></a>簡單的企業整合 - 參考架構

## <a name="overview"></a>概觀

此參考架構針對使用「Azure 整合服務」的整合應用程式，示範一組經過證實的做法。 針對許多需要 HTTP API、工作流程及協調流程的不同應用程式模式，此架構可作為這些應用程式模式的基礎。

*有許多可能的整合技術應用程式，從簡單的點對點應用程式，到完整的企業服務匯流排，一應俱全。此架構系列說明了適用於建置任何整合應用程式的可重複使用的元件組件 – 架構應該考量其應用程式和基礎結構所需的元件。*

   ![架構圖 - 簡單的企業整合](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

## <a name="architecture"></a>架構

此架構具有下列元件：

- 資源群組。 資源群組是 Azure 資源的邏輯容器。
- Azure API 管理。 「Azure API 管理」是一個用於發佈、保護及轉換 HTTP API 的完全受控平台。
- Azure API 管理開發人員入口網站。 每個「Azure API 管理」執行個體都隨附「開發人員入口網站」，可讓您存取文件、程式碼範例，並能夠測試 API。
- Azure Logic Apps。 Logic Apps 是一個用於建置企業工作流程和整合的無伺服器平台。
- 連接器。 Logic Apps 會使用連接器來連線到常用的服務。 Logic Apps 已經有數百個各種不同的連接器，但您也可以使用自訂連接器來建立它們。
- IP 位址。 「Azure API 管理」服務有固定的公用 IP 位址和網域名稱。 網域名稱是 azure-api.net 的子網域，例如 contoso. azure-api.net。 Logic Apps 也有公用 IP 位址；不過，在此架構中，我們會將呼叫 Logic Apps 端點的存取權限制成僅供「API 管理」的 IP 位址使用 (基於安全考量)。
- Azure DNS。 Azure DNS 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構提供名稱解析。 只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。 若要使用自訂網域名稱 (例如 contoso.com)，請建立將自訂網域名稱對應至 IP 位址的 DNS 記錄。 如需詳細資訊，請參閱＜在 Azure API 管理中設定自訂網域名稱＞。
- Azure Active Directory (Azure AD)。 使用 Azure AD 或其他識別提供者進行驗證。 Azure AD 會提供驗證來存取 API 端點 (藉由傳遞要讓「API 管理」驗證的「JSON Web 權杖」)，並可保護對「API 管理開發人員入口網站」的存取 (僅限「標準」層和「進階」層)。

此架構有其作業的一些基本模式：

1. 現有的後端 HTTP API 是透過「API 管理開發人員入口網站」發佈的，可讓開發人員 (不論是您組織內部、外部，還是兩者都包括) 將對這些 API 的呼叫整合至應用程式。
2. 複合 API 時是使用 Logic Apps 來建置的；其中會協調對 SAAS 系統、Azure 服務及任何發佈至「API 管理」之 API 的呼叫。 Logic Apps 也是透過「API 管理開發人員入口網站」發佈的。
3. 應用程式會使用 Azure Active Directory 來取得存取 API 所需的 OAuth 2.0 安全性權杖。
4. 「Azure API 管理」會驗證該安全性權杖，然後將要求傳遞給後端 API/邏輯應用程式。

## <a name="recommendations"></a>建議

您的需求可能和此處所述的架構不同。 以本節的建議作為起點。

### <a name="azure-api-management-tier"></a>Azure API 管理層

請使用「基本」、「標準」或「進階」層，因為它們提供生產環境 SLA，並且支援在 Azure 區域內相應放大規模 (單位數目因階層而異)。 「進階」層也支援跨多個 Azure 區域相應放大規模。 請根據您所需的輸送量層級和功能集來選擇階層。 如需詳細資訊，請參閱 [API 管理價格](https://azure.microsoft.com/pricing/details/api-management/)。

我們會向您收取所有執行中「API 管理」執行個體的費用。 如果您已相應擴增規模，而不需要一直保有該層級的效能，請考慮利用「API 管理」的每小時計費方式並相應縮減規模。

### <a name="logic-apps-pricing"></a>Logic Apps 價格

Logic Apps 是以[無伺服器](logic-apps-serverless-overview.md)模型運作 – 計費的計算方式是根據動作和連接器的執行。 如需詳細資訊，請參閱 [Logic Apps 價格](https://azure.microsoft.com/pricing/details/logic-apps/)。 目前針對 Logic Apps 沒有任何階層考量。

### <a name="logic-apps-for-asynchronous-api-calls"></a>非同步 API 呼叫的 Logic Apps

Logic Apps 最適合在不需要低延遲的案例中運作 – 例如非同步或半長時間執行的 API 呼叫。 如果需要低延遲 (例如會封鎖使用者介面的呼叫)，建議您使用不同的技術 (例如 Azure Functions 或使用 App Service 來部署的 Web API) 來實作該 API 或作業。 我們仍然向 API 取用者建議使用「API 管理」作為此 API 的前端。

### <a name="region"></a>區域

將「API 管理」和 Logic Apps 佈建在相同區域中，以將網路延遲降到最低。 通常會選擇最接近使用者的區域。

資源群組也有區域，用來指定將部署中繼資料儲存在哪裡，以及從哪裡執行部署範本。 將資源群組及其資源放置於相同區域。 這麼做可在部署期間提升可用性。

## <a name="scalability-considerations"></a>延展性考量

「API 管理」系統管理員應該在適當的情況下新增[快取原則](../api-management/api-management-howto-cache.md)，以提升服務延展性及降低其後端服務的負載。

「Azure API 管理」的「基本」、「標準」及「進階」層都可以在 Azure 區域中相應放大規模來提供更大的容量。 系統管理員可以使用 [計量] 功能表內的 [容量計量] 來分析其服務的使用情況，然後視需要相應擴增或縮減規模。

調整「API 管理」服務規模的建議事項：

- 調整規模時需要考慮流量模式 – 客戶的流量模式如果較具變動性，就比較需要擴增容量。
- 容量如果一直超過 66%，可能代表需要相應擴增規模。
- 容量如果一直低於 20%，則可能代表需要相應縮減規模。
- 一律建議您先以代表性的負載進行「API 管理」服務負載測試，然後才在生產環境中啟用此服務。

「進階」層服務也可以跨多個 Azure 區域相應放大規模。 客戶如果採用此部署方式，將會獲得較高的 SLA (99.95%，而不是 99.9%)，並可將服務部署在多個區域中靠近使用者的地方。

Logic Apps 的無伺服器模型意謂著系統管理員不需有額外的服務延展性考量；服務會自動調整規模來滿足需求。

## <a name="availability-considerations"></a>可用性考量

撰寫本文時，「Azure API 管理」的「基本」、「標準」及「進階」層服務等級協定 (SLA) 是 99.9%。 「進階」層設定搭配在兩個以上的區域中至少部署 1 個單位時，SLA 為 99.95%。

撰寫本文時，Azure Logic Apps 的服務等級協定 (SLA) 是 99.9%。

### <a name="backups"></a>備份

「Azure API 管理」的設定應該[定期備份](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (適當地根據變更的規律性)，而備份檔案應該儲存在與服務所在位置不同的位置或 Azure 區域中。 接著，客戶可以選擇兩個選項其中之一來作為其 DR 策略：

1. 在 DR 事件中，會佈建新的「API 管理」執行個體、將備份還原至該執行個體，然後重新設置 DNS 記錄。
2. 客戶將其服務的被動複本保存在另一個 Azure 區域中 (會產生額外費用)。系統會將備份定期還原至該複本。 在 DR 事件中，只有 DNS 記錄需要重新配置來還原服務。

由於 Logic Apps 的重新建立速度很快且是無伺服器服務，因此會藉由儲存相關 Azure Resource Manager 範本的複本來備份它們。 這些可以儲存至原始檔控制/整合至客戶的持續整合/持續部署 (CI/CD) 流程。

已透過「API 管理」發佈的 Logic Apps 如果移至不同的資料中心，將需要更新位置。 透過簡單的 PowerShell 指令碼來更新 API 的 Backend 屬性，即可達到此目的。

## <a name="manageability-considerations"></a>管理性考量

針對生產、部署和測試環境，各別建立資源群組。 這可讓您更輕鬆地管理部署、刪除測試部署及指派存取權限。
將資源指派給資源群組時，請考慮下列項目：

- 生命週期。 一般來說，會將生命週期相同的資源放在同個資源群組中。
- 存取。 您可以使用[角色型存取控制](../role-based-access-control/overview.md) (RBAC) 將存取原則套用至群組中的資源。
- 計費。 您可以檢視資源群組的彙總成本。
- 「API 管理」的定價層 – 針對開發和測試環境，建議您使用「開發人員層」。 針對生產階段前環境，建議您部署生產環境複本、執行測試，然後關閉，以將成本降到最低。

如需詳細資訊，請參閱[資源群組](../azure-resource-manager/resource-group-overview.md)概觀。

### <a name="deployment"></a>部署

建議您使用 [Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)來部署「Azure API 管理」和 Azure Logic Apps。 範本可讓您透過 PowerShell 或 Azure 命令列介面 (CLI)，更輕鬆地自動執行部署。

建議您將「Azure API 管理」及任何個別的 Logic Apps 放在它們自己的個別 Resource Manager 範本中。 這可允許將它們儲存在原始檔控制系統中。 接著，便可在持續整合/持續部署 (CI/CD) 流程中，將這些範本一起或個別部署。

### <a name="versions"></a>版本

每次您對邏輯應用程式進行設定變更 (或透過 Resource Manager 範本部署更新) 時，系統都會為了您的方便起見，保留該版本的複本 (將會保留所有具有執行歷程記錄的版本)。 您可以使用這些版本來追蹤歷程記錄變更，也可以將某個版本升階成邏輯應用程式的目前設定；例如，這樣做意謂著您可以有效地復原邏輯應用程式。

「API 管理」有兩個截然不同 (但互補) 的[版本設定概念](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/)：

- 版本 - 可用來為您的 API 取用者提供可根據其需求取用的 API 選項 (例如 v1、v2、搶鮮版 (Beta) 或生產環境版)。
- 修訂 - 可讓 API 系統管理員安全地對 API 進行變更，然後將其部署至使用者並視需要提供評論。

在部署內容中 – 應將「API 管理」修訂視為一種安全地進行變更、保留變更歷程記錄，以及讓 API 取用者了解這些變更的方式。 您可以使用 Resource Manager 範本在開發環境中建立修訂，然後在其他環境之間部署此修訂。

雖然可以在將 API 設定為「目前」API 並提供給使用者存取之前，先使用修訂來測試 API，但不建議使用此機制來進行負載或整合測試 - 應該改用個別測試或生產階段前環境。

### <a name="configuration"></a>組態

請一律不要將密碼、存取金鑰或連接字串簽入原始檔控制。 如果需要它們，請使用適當的技術來部署和保護這些值。 

在 Logic Apps 中，邏輯應用程式內所需的任何敏感值 (可藉由連線形式建立的值) 都應該儲存在 Azure Key Vault 中，然後從 Resource Manager 範本參考該值。 此外，也建議您搭配每個環境的參數檔使用部署範本參數。 如需更多指引，請參閱[保護工作流程中的參數和輸入](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow)。

在「API 管理」中，會使用名為「具名值/屬性」的物件來管理祕密。 這些物件能安全地儲存可在「API 管理」原則中存取的值。 了解如何[管理 API 管理中的祕密](../api-management/api-management-howto-properties.md)。

### <a name="diagnostics-and-monitoring"></a>診斷和監控

[API 管理](../api-management/api-management-howto-use-azure-monitor.md)和 [Logic Apps](logic-apps-monitor-your-logic-apps.md) 都支援透過 [Azure 監視器](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md)進行作業監視。 「Azure 監視器」是預設啟用的功能，並且會根據為每項服務設定的不同計量來提供資訊。

此外，每項服務還有進一步的選項：

- Logic Apps 記錄檔可傳送給 [Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) 來進行更深入的分析並顯示在儀表板上。
- 「API 管理」支援設定 Application Insights 來進行 DevOps 監視。
- 「API 管理」支援[適用於自訂 API 分析的 Power BI 解決方案範本](http://aka.ms/apimpbi)。 此解決方案範本可讓客戶建立自己的自訂分析解決方案，Power BI 中還會為企業用戶提供報告。

## <a name="security-considerations"></a>安全性考量

針對本文所述、部署在所述架構中的 Azure 服務，本節列出其專屬的安全性考量。 但不是安全性最佳做法的完整清單。

- 使用角色型存取控制 (RBAC) 來確保使用者的存取層級適當。
- 使用 OAuth/Open IDConnect 來保護「API 管理」中的公用 API 端點。 做法是設定身分識別提供者並新增 JWT 驗證原則。
- 使用相互憑證從「API 管理」連線至後端服務
- 藉由建立指向「API 管理」IP 位址的 IP 位址白名單，保護 HTTP 觸發程序型 Logic Apps。 這可防止不先經過「API 管理」就從公用網際網路呼叫邏輯應用程式的情況。

此參考架構示範了如何使用「Azure 整合服務」來建置簡單的企業整合平台。

## <a name="next-steps"></a>後續步驟

* [搭配佇列和事件的企業整合](logic-apps-architectures-enterprise-integration-with-queues-events.md)
