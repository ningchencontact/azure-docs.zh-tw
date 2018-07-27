---
title: Azure 整合服務企業整合參考架構
description: 說明示範如何使用 Logic Apps、API 管理、服務匯流排及事件方格來實作企業整合模式的參考架構。
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
ms.openlocfilehash: a86c4c4227795a712dd51ace1fbefe9d2b96518a
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116107"
---
# <a name="reference-architecture-enterprise-integration-with-queues-and-events"></a>參考架構：搭配佇列和事件的企業整合

下列參考架構示範一組經過證實的做法，您可以將此做法套用至使用 Azure 整合服務的整合應用程式。 針對許多需要 HTTP API、工作流程及協調流程的不同應用程式模式，此架構可作為這些應用程式模式的基礎。

![架構圖 - 搭配佇列和事件的企業整合](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

*有許多可能適用於整合技術的應用程式。它們的範圍從簡單的點對點應用程式到完整的企業 Azure 服務匯流排應用程式。架構系列說明了可重複使用的元件組件，而這類元件組件可應用於建置一般性整合應用程式。架構設計人員應該針對其應用程式和基礎結構，考慮需要實作的元件。*

## <a name="architecture"></a>架構

此架構的「建置基礎」為[簡單的企業整合](logic-apps-architectures-simple-enterprise-integration.md)架構。 此處適用於[對簡單企業架構的建議](logic-apps-architectures-simple-enterprise-integration.md#recommendations)。 為求簡單明瞭，本文中的[建議](#recommendations)將會省略它們。 

此架構具有下列元件：

- **資源群組**。 [資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)是 Azure 資源的邏輯容器。
- **Azure API 管理**。 [API 管理](https://docs.microsoft.com/azure/api-management/)是一個完全受控的平台，可用來發佈、保護和轉換 HTTP API。
- **Azure API 管理開發人員入口網站**。 Azure API 管理的每個執行個體都隨附[開發人員入口網站](https://docs.microsoft.com/azure/api-management/api-management-customize-styles)的存取權。 API 管理開發人員入口網站可讓您存取文件和程式碼範例。 您可以在開發人員入口網站中測試 API。
- **Azure Logic Apps**。 [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) 是一個用於建置企業工作流程和整合的無伺服器平台。
- **連接器**。 Logic Apps 會使用[連接器](https://docs.microsoft.com/azure/connectors/apis-list)來連線到常用的服務。 Logic Apps 已經有數百個各種不同的連接器，但您也可以建立自訂連接器。
- **Azure 服務匯流排**。 [服務匯流排](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview)提供安全可靠的傳訊。 傳訊可用來分離應用程式，然後與其他訊息型系統整合。
- **Azure 事件方格**。 [事件方格](https://docs.microsoft.com/azure/event-grid/overview)是一個用來發佈和傳遞應用程式事件的無伺服器平台。
- **IP 位址**。 Azure API 管理服務有固定的公用 [IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)和網域名稱。 網域名稱是 azure-api.net 的子網域，例如 contoso.azure-api.net。 Logic Apps 和服務匯流排也有公用 IP 位址。 不過，在此架構中，我們會將呼叫 Logic Apps 端點的存取權限制成僅供 API 管理的 IP 位址使用 (基於安全考量)。 對服務匯流排的呼叫會受到共用存取簽章 (SAS) 保護。
- **Azure DNS**。 [Azure DNS](https://docs.microsoft.com/azure/dns/) 是一個適用於 DNS 網域的主機服務。 Azure DNS 採用 Microsoft Azure 基礎結構來提供名稱解析。 只要將您的網域裝載於 Azure，就可以使用您用於其他 Azure 服務的相同認證、API、工具和計費方式來管理 DNS 記錄。 若要使用自訂網域名稱 (例如 contoso.com)，請建立將自訂網域名稱對應至 IP 位址的 DNS 記錄。 如需詳細資訊，請參閱[在 API 管理中設定自訂網域名稱](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain)。
- **Azure Active Directory (Azure AD)**。 使用 [Azure AD](https://docs.microsoft.com/azure/active-directory/) 或其他識別提供者來進行驗證。 Azure AD 藉由傳遞[適用於 API 管理的 JSON Web 權杖](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims)進行驗證，以提供用於存取 API 端點的驗證。 Azure AD 可以保護 API 管理開發人員入口網站 (僅限標準和進階層) 的存取權。

此架構具有一些可作為其作業基礎的模式：

* 現有的後端 HTTP API 是透過 API 管理開發人員入口網站來發佈的。 在入口網站中，開發人員 (可能是您的組織內部、外部或兩者) 可以將對這些 API 的呼叫整合到應用程式。
* 複合 API 的建置方式是使用 Logic Apps，以及協調對下列三者的呼叫：軟體即服務 (SaaS) 系統、Azure 服務及任何發佈至 API 管理的 API 呼叫。 [Logic Apps 的發佈方式](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api)也是透過 API 管理開發人員入口網站來進行。
- 應用程式會使用 Azure AD 來[取得 OAuth 2.0 安全性權杖](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)，此為取得 API 存取權所需的權杖。
- API 管理會[驗證安全性權杖](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)，然後將要求傳遞給後端 API 或邏輯應用程式。
- 服務匯流排佇列可用來將應用程式活動[分離](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview)並[緩和負載中的高峰](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling)。 訊息會由 Logic Apps、協力廠商應用程式新增至佇列，或 (未描述) 藉由透過 API 管理將佇列發佈成 HTTP API 來新增至佇列。
- 將訊息新增至服務匯流排佇列時，就會觸發事件。 此事件會觸發邏輯應用程式。 邏輯應用程式接著會處理訊息。
- 其他 Azure 服務 (例如 Azure Blob 儲存體和 Azure 事件中樞) 也會將事件發佈至事件方格。 這些服務會觸發 Logic Apps 來接收事件，然後執行後續動作。

## <a name="recommendations"></a>建議

您的特定需求可能與本文所述的一般架構不同。 以本節的建議作為起點。

### <a name="service-bus-tier"></a>服務匯流排層

使用服務匯流排進階層。 進階層支援事件方格通知。 如需詳細資訊，請參閱[服務匯流排價格](https://azure.microsoft.com/pricing/details/service-bus/)。

### <a name="event-grid-pricing"></a>事件方格價格

事件方格會使用無伺服器模型。 計費的計算方式是以作業 (事件執行) 的數目為基礎。 如需詳細資訊，請參閱[事件方格價格](https://azure.microsoft.com/pricing/details/event-grid/)。 目前沒有任何針對事件方格的階層考量。

### <a name="use-peeklock-to-consume-service-bus-messages"></a>使用 PeekLock 來取用服務匯流排訊息

當您建立邏輯應用程式來取用服務匯流排訊息時，請在邏輯應用程式中使用 [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) 來存取一組訊息。 當您使用 PeekLock 時，邏輯應用程式可以先執行步驟來驗證每個訊息，然後再完成或放棄訊息。 此方法可防止意外的訊息遺失。

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>引發事件格線觸發程序時，請檢查是否有多個物件

引發事件方格觸發程序時，只是表示「這些事項中至少發生了一個事項。」 例如，當事件方格針對出現在服務匯流排佇列中的訊息觸發邏輯應用程式時，該邏輯應用程式應該一律假設有一或多個訊息可供處理。

### <a name="region"></a>區域

將 API 管理、Logic Apps 及服務匯流排佈建在相同區域中，以將網路延遲降到最低。 通常會選擇最接近使用者的區域。

資源群組也有區域。 此區域會指定部署中繼資料的儲存位置，以及從何處執行部署範本。 將資源群組及其資源放在相同區域，以改善部署期間的可用性。

## <a name="scalability"></a>延展性

服務匯流排進階層可以將傳訊單位數目相應放大規模，以達到更高的延展性。 進階層設定可以有一個、兩個或四個傳訊單位。 如需調整服務匯流排規模的詳細資訊，請參閱[使用服務匯流排傳訊的效能改進最佳作法](../service-bus-messaging/service-bus-performance-improvements.md)。

## <a name="availability"></a>可用性

目前，適用於 Azure API 管理的基本、標準及進階層服務等級協定 (SLA) 為 99.9%。 「進階」層設定搭配在兩個以上的區域中至少部署 1 個單位時，SLA 為 99.95%。

目前，適用於 Azure Logic Apps 的 SLA 為 99.9%。

### <a name="disaster-recovery"></a>災害復原

請考慮實作服務匯流排進階層中的異地災害復原，以便能夠在發生嚴重服務中斷時進行容錯移轉。 如需詳細資訊，請參閱 [Azure 服務匯流排異地災害復原](../service-bus-messaging/service-bus-geo-dr.md)。

## <a name="manageability"></a>可管理性

針對生產、部署和測試環境，各別建立資源群組。 個別的資源群組可讓您更輕鬆地管理部署、刪除測試部署及指派存取權限。

當您將資源指派給資源群組時，請考慮下列因素：

- **生命週期**。 一般來說，會將具有相同生命週期的資源放在同一個資源群組中。
- **存取**。 您可以使用[角色型存取控制](../role-based-access-control/overview.md) (RBAC)，將存取原則套用至群組中的資源。
- **計費**。 您可以檢視資源群組的彙總成本。
- **適用於 API 管理的定價層**。 我們建議針對開發和測試環境使用開發人員層。 針對生產階段前環境，建議您部署生產環境的複本、執行測試，然後關閉，以將成本降到最低。

如需詳細資訊，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)。

### <a name="deployment"></a>部署

建議您使用 [Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)來部署 API 管理、Logic Apps、事件方格及服務匯流排。 範本可讓您透過 PowerShell 或 Azure CLI，更輕鬆地自動執行部署。

建議您將 API 管理、任何個別的 Logic Apps、事件方格主題及服務匯流排命名空間放在自己的個別 Resource Manager 範本中。 當您使用不同的範本時，可以將資源儲存於原始檔控制系統中。 您接著可以一起部署這些範本，或者在持續整合/持續部署 (CI/CD) 流程中個別部署這些範本。

### <a name="diagnostics-and-monitoring"></a>診斷和監控

就像 API 管理和 Logic Apps 一樣，您可以使用 Azure 監視器來監視服務匯流排。 Azure 監視器會根據針對每個服務所設定的計量來提供資訊。 Azure 監視器預設為啟用。

## <a name="security"></a>安全性

使用 SAS 來保護服務匯流排。 您可以使用 [SAS 驗證](../service-bus-messaging/service-bus-sas.md)來授與使用者具特定權限的服務匯流排資源存取權。 如需詳細資訊，請參閱[服務匯流排驗證和授權](../service-bus-messaging/service-bus-authentication-and-authorization.md)。

如果服務匯流排佇列必須以 HTTP 端點的形式公開 (以張貼新訊息)，您應該使用 API 管理讓該端點成為前端，以便保護端點。 接著，即可視情況使用憑證或 OAuth 來保護此端點。 保護端點的最簡單方法是，使用邏輯應用程式搭配要求/回應 HTTP 觸發程序作為媒介。

「事件格線」會透過驗證碼來保護事件傳遞。 如果您使用 Logic Apps 來取用事件，將會自動執行驗證。 如需詳細資訊，請參閱 [Event Grid 安全性和驗證](../event-grid/security-authentication.md)。

## <a name="next-steps"></a>後續步驟

- 了解[簡單的企業整合](logic-apps-architectures-simple-enterprise-integration.md)。