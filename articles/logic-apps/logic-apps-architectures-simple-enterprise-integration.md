---
title: Azure 整合服務簡單的企業整合參考架構
description: 說明示範如何使用 Azure Logic Apps 和 Azure API 管理來實作簡單企業整合模式的參考架構。
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
ms.openlocfilehash: f73a9e59c0add664128b506172182afe566ca670
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444505"
---
# <a name="reference-architecture-simple-enterprise-integration"></a>參考架構：簡單的企業整合

下列參考架構示範一組經過證實的做法，您可以將此做法套用至使用 Azure 整合服務的整合應用程式。 針對許多需要 HTTP API、工作流程及協調流程的不同應用程式模式，此架構可作為這些應用程式模式的基礎。

![架構圖 - 簡單的企業整合](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

*有許多可能適用於整合技術的應用程式。它們的範圍從簡單的點對點應用程式到完整的企業 Azure 服務匯流排應用程式。架構系列說明了可重複使用的元件組件，而這類元件組件可應用於建置一般性整合應用程式。架構設計人員應該針對其應用程式和基礎結構，考慮需要實作的元件。*

## <a name="architecture"></a>架構

此架構具有下列元件：

- **資源群組**。 [資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)是 Azure 資源的邏輯容器。
- **Azure API 管理**。 [API 管理](https://docs.microsoft.com/azure/api-management/)是一個完全受控的平台，可用來發佈、保護和轉換 HTTP API。
- **Azure API 管理開發人員入口網站**。 Azure API 管理的每個執行個體都隨附[開發人員入口網站](https://docs.microsoft.com/azure/api-management/api-management-customize-styles)的存取權。 API 管理開發人員入口網站可讓您存取文件和程式碼範例。 您可以在開發人員入口網站中測試 API。
- **Azure Logic Apps**。 [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) 是一個用於建置企業工作流程和整合的無伺服器平台。
- **連接器**。 Logic Apps 會使用[連接器](https://docs.microsoft.com/azure/connectors/apis-list)來連線到常用的服務。 Logic Apps 已經有數百個各種不同的連接器，但您也可以建立自訂連接器。
- **IP 位址**。 Azure API 管理服務有固定的公用 [IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)和網域名稱。 預設網域名稱是 azure-api.net 的子網域，例如 contoso.azure-api.net，但也可以設定[自訂網域](https://docs.microsoft.com/azure/api-management/configure-custom-domain)。 Logic Apps 和服務匯流排也有公用 IP 位址。 不過，在此架構中，我們會將呼叫 Logic Apps 端點的存取權限制成僅供 API 管理的 IP 位址使用 (基於安全考量)。 對服務匯流排的呼叫會受到共用存取簽章 (SAS) 保護。
- **Azure DNS**。 [Azure DNS](https://docs.microsoft.com/azure/dns/) 是一個適用於 DNS 網域的主機服務。 Azure DNS 採用 Microsoft Azure 基礎結構來提供名稱解析。 只要將您的網域裝載於 Azure，就可以使用您用於其他 Azure 服務的相同認證、API、工具和計費方式來管理 DNS 記錄。 若要使用自訂網域名稱 (例如 contoso.com)，請建立將自訂網域名稱對應至 IP 位址的 DNS 記錄。 如需詳細資訊，請參閱[在 API 管理中設定自訂網域名稱](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain)。
- **Azure Active Directory (Azure AD)**。 使用 [Azure AD](https://docs.microsoft.com/azure/active-directory/) 或其他識別提供者來進行驗證。 Azure AD 藉由傳遞[適用於 API 管理的 JSON Web 權杖](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims)進行驗證，以提供用於存取 API 端點的驗證。 Azure AD 可以保護 API 管理開發人員入口網站 (僅限標準和進階層) 的存取權。

此架構具有一些可作為其作業基礎的模式：

- 複合 API 是以 Logic Apps 來建置的。 它們會協調對下列三者的呼叫：軟體即服務 (SaaS) 系統、Azure 服務，以及發佈至 API 管理的任何 API。 [Logic Apps 的發佈方式](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api)也是透過 API 管理開發人員入口網站來進行。
- 應用程式會使用 Azure AD 來[取得 OAuth 2.0 安全性權杖](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)，此為取得 API 存取權所需的權杖。
- Azure API 管理會[驗證安全性權杖](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)，然後將要求傳遞給後端 API 或邏輯應用程式。

## <a name="recommendations"></a>建議

您的特定需求可能與本文所述的一般架構不同。 以本節的建議作為起點。

### <a name="azure-api-management-tier"></a>Azure API 管理層

使用 API 管理的基本、標準或進階層。 階層會提供生產環境服務等級協定 (SLA)，並支援在 Azure 區域內相應放大規模 (單位數目因階層而異)。 進階層也支援跨多個 Azure 區域相應放大規模。 請根據所需的輸送量層級和您的功能集來選擇階層。 如需詳細資訊，請參閱 [API 管理價格](https://azure.microsoft.com/pricing/details/api-management/)。

我們會向您收取所有執行中「API 管理」執行個體的費用。 如果您已相應增加規模，而且不需要一直保有該層級的效能，請考慮利用 API 管理的每小時計費方式並相應減少規模。

### <a name="logic-apps-pricing"></a>Logic Apps 價格

Logic Apps 會使用[無伺服器](logic-apps-serverless-overview.md)模型。 計費的計算方式是以動作和連接器執行為基礎。 如需詳細資訊，請參閱 [Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps/)。 目前沒有任何針對 Logic Apps 的階層考量。

### <a name="logic-apps-for-asynchronous-api-calls"></a>非同步 API 呼叫的 Logic Apps

Logic Apps 在不需要低延遲的情況下運作得最好。 例如，它最適合非同步或執行時間稍長的 API 呼叫。 如果需要低延遲 (例如，會封鎖使用者介面的呼叫)，建議您使用不同的技術來實作該 API 或作業。 例如，使用 Azure Functions，或者以 App Service 來部署的 Web API。 我們仍然建議使用 API 管理，讓此 API 成為 API 取用者的前端。

### <a name="region"></a>區域

將「API 管理」和 Logic Apps 佈建在相同區域中，以將網路延遲降到最低。 通常會選擇最接近使用者的區域。

資源群組也有區域。 此區域會指定部署中繼資料的儲存位置，以及從何處執行部署範本。 將資源群組及其資源放在相同區域，以改善部署期間的可用性。

## <a name="scalability"></a>延展性

API 管理系統管理員應該在適當的情況下新增[快取原則](../api-management/api-management-howto-cache.md)，以提升服務延展性。 快取也有助於降低後端服務的負載。

Azure API 管理的基本、標準及進階層都可以在 Azure 區域中相應放大規模來提供更大的容量。 系統管理員可以使用 [計量] 功能表內的 [容量計量] 選項來分析其服務的使用情況，然後視需要相應增加或相應減少規模。

調整「API 管理」服務規模的建議事項：

- 調整規模需要將流量模式納入考量。 若客戶的流量模式變動較大，對於提高容量就會有更大的需求。
- 容量如果一直超過 66%，可能代表需要相應增加規模。
- 容量如果一直低於 20%，則可能代表需要相應減少規模。
- 一律建議您先以代表性的負載進行 API 管理服務負載測試，然後才在生產環境中啟用負載。

「進階」層服務也可以跨多個 Azure 區域相應放大規模。 藉由跨多個 Azure 區域調整服務規模來進行部署的客戶會獲得更高的 SLA (99.95%或 99.9%)，而且可以在多個區域中於使用者附近佈建服務。

Logic Apps 無伺服器模型代表系統管理員不需要針對服務延展性進行規劃。 服務會自動調整規模以滿足需求。

## <a name="availability"></a>可用性

目前，適用於 Azure API 管理的基本、標準及進階層 SLA 為 99.9%。 「進階」層設定搭配在兩個以上的區域中至少部署 1 個單位時，SLA 為 99.95%。

目前，適用於 Azure Logic Apps 的 SLA 為 99.9%。

### <a name="backups"></a>備份

您的 Azure API 管理設定應該[定期備份](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (基於變更的規律性)。 備份檔案應該儲存在不同於服務所在的位置或 Azure 區域中。 客戶接著可以針對其災害復原策略，選擇下列兩個選項之一：

- 在災害復原事件中，會佈建新的 API 管理執行個體、將備份還原至新的執行個體，然後重新指向 DNS 記錄。
- 客戶會將其服務的被動複本保存在另一個 Azure 區域中 (會產生額外費用)。 系統會將備份定期還原至該複本。 在災害復原事件中，只需重新指向 DNS 記錄以還原服務。

由於 Logic Apps 的重新建立速度很快且是無伺服器的，因此會藉由儲存相關 Azure Resource Manager 範本的複本來進行備份。 範本可以儲存至原始檔控制，而且可以整合至客戶的持續整合/持續部署 (CI/CD) 流程。

如果將已透過 API 管理發佈的邏輯應用程式移到不同的資料中心，請更新應用程式的位置。 若要更新應用程式的位置，請使用基本的 PowerShell 指令碼來更新 API 的 **Backend** 屬性。

## <a name="manageability"></a>可管理性

針對生產、部署和測試環境，各別建立資源群組。 使用個別的資源群組，可讓您更輕鬆地管理部署、刪除測試部署及指派存取權限。

當您將資源指派給資源群組時，請考慮下列因素：

- **生命週期**。 一般來說，會將具有相同生命週期的資源放在同一個資源群組中。
- **存取**。 您可以使用[角色型存取控制](../role-based-access-control/overview.md) (RBAC)，將存取原則套用至群組中的資源。
- **計費**。 您可以檢視資源群組的彙總成本。
- **適用於 API 管理的定價層**。 我們建議針對開發和測試環境使用開發人員層。 針對生產階段前環境，建議您部署生產環境的複本、執行測試，然後關閉，以將成本降到最低。

如需詳細資訊，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)。

### <a name="deployment"></a>部署

建議您使用 [Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)來部署 Azure API 管理和 Logic Apps。 範本可讓您透過 PowerShell 或 Azure CLI，更輕鬆地自動執行部署。

建議您將 Azure API 管理及任何個別的 Logic Apps 放在自己的個別 Resource Manager 範本中。 當您使用不同的範本時，可以將資源儲存於原始檔控制系統中。 您也可以一起部署資源，或者在 CI/CD 部署流程中個別部署資源。

### <a name="versions"></a>版本

每次您對邏輯應用程式進行設定變更時 (或透過 Resource Manager 範本部署更新時)，系統都會保留該版本的複本，以方便您使用 (會保留所有具有執行歷程記錄的版本)。 您可以使用這些版本來追蹤歷程記錄變更，以及將某個版本升階成邏輯應用程式的目前設定。 例如，您可以有效地復原邏輯應用程式。

「API 管理」有兩個截然不同 (但互補) 的[版本設定概念](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/)：

- 版本，可用來為您的 API 取用者提供可根據其需求取用的 API 選項 (例如 v1、v2、搶鮮版 (Beta) 或生產環境版)。
- 修訂，可讓 API 系統管理員安全地對 API 進行變更，然後將變更部署至使用者並視需要提供評論。

在部署環境中，建議將 API 管理修訂視為一種方式，可以安全地進行變更、保留變更歷程記錄，以及讓 API 取用者知道有這些變更。 您可以使用 Resource Manager 範本在開發環境中建立修訂，然後在其他環境之間部署此修訂。

雖然您可以先使用修訂來測試 API，然後再使其成為「目前的修訂」且可供使用者存取，但我們不建議使用此機制來進行載入或整合測試。 請改用個別的測試或生產階段前環境。

### <a name="configuration"></a>組態

一律不要將密碼、存取金鑰或連接字串簽入原始檔控制。 如果需要這些值，請使用適當的技術來部署和保護這些值。 

在 Logic Apps 中，邏輯應用程式內所需的任何機密值 (無法以連線形式建立) 都應該儲存在 Azure Key Vault 中，然後從 Resource Manager 範本參考該值。 此外，也建議您針對每個環境使用部署範本參數和參數檔。 如需詳細資訊，請參閱[保護工作流程中的參數和輸入](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow)。

在 API 管理中，會使用名為「具名值」或「屬性」的物件來管理祕密。 物件能安全地儲存可在 API 管理原則中存取的值。 如需詳細資訊，請參閱[管理 API 管理中的祕密](../api-management/api-management-howto-properties.md)。

### <a name="diagnostics-and-monitoring"></a>診斷和監控

[API 管理](../api-management/api-management-howto-use-azure-monitor.md)和 [Logic Apps](logic-apps-monitor-your-logic-apps.md) 都支援透過 [Azure 監視器](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md)進行作業監視。 Azure 監視器會根據針對每個服務所設定的計量來提供資訊。 Azure 監視器預設為啟用。

這些選項也適用於每個服務：

- Logic Apps 記錄可傳送給 [Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) 來進行更深入的分析並顯示在儀表板上。
- API 管理支援設定 Azure Application Insights 來進行 DevOps 監視。
- API 管理支援[適用於自訂 API 分析的 Power BI 解決方案範本](http://aka.ms/apimpbi) \(英文\)。 客戶可以使用解決方案範本來建立自己自訂的分析解決方案。 針對商務使用者，Power BI 中有可用的報表。

## <a name="security"></a>安全性

針對本文所述及部署在所述架構中的 Azure 服務，本節列出其專屬的安全性考量。 但不是安全性最佳做法的完整清單。

- 使用角色型存取控制 (RBAC) 來確保使用者的存取層級適當。
- 使用 OAuth/Open ID Connect 來保護 API 管理中的公用 API 端點。 若要保護公用 API 端點，請設定識別提供者，並新增 JSON Web 權杖 (JWT) 驗證原則。
- 使用相互憑證，從 API 管理連線至後端服務。
- 藉由建立指向 API 管理 IP 位址的 IP 位址白名單，以保護 HTTP 觸發程序型的 Logic Apps。 列入白名單的 IP 位址可防止不先經過 API 管理就從公用網際網路呼叫邏輯應用程式的情況。

## <a name="next-steps"></a>後續步驟

- 了解[搭配佇列和事件的企業整合](logic-apps-architectures-enterprise-integration-with-queues-events.md)。
