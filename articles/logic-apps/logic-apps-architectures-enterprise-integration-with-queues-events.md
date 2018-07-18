---
title: Azure 整合服務參考架構
description: 示範如何使用 Logic Apps、「API 管理」、「服務匯流排」及「事件格線」來實作簡單企業整合模式的參考架構
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
ms.openlocfilehash: db3350b3c70f6e6f35949e8423334061849b7b37
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231929"
---
# <a name="enterprise-integration-with-queues-and-events-reference-architecture"></a>搭配佇列和事件的企業整合：參考架構

## <a name="overview"></a>概觀

此參考架構針對使用「Azure 整合服務」的整合應用程式，示範一組經過證實的做法。 針對許多需要 HTTP API、工作流程及協調流程的不同應用程式模式，此架構可作為這些應用程式模式的基礎。

*有許多可能的整合技術應用程式，從簡單的點對點應用程式，到完整的企業服務匯流排，一應俱全。此架構系列說明了適用於建置任何整合應用程式的可重複使用的元件組件 – 架構應該考量其應用程式和基礎結構所需的元件。*

![架構圖 - 搭配佇列和事件的企業整合](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

## <a name="architecture"></a>架構

此架構以[簡單的企業整合](logic-apps-architectures-simple-enterprise-integration.md)中所示的架構為基礎。 包括下列元件：

- 資源群組。 資源群組是 Azure 資源的邏輯容器。
- Azure API 管理。 「Azure API 管理」是一個用於發佈、保護及轉換 HTTP API 的完全受控平台。
- Azure API 管理開發人員入口網站。 每個「Azure API 管理」執行個體都隨附「開發人員入口網站」，可讓您存取文件、程式碼範例，並能夠測試 API。
- Azure Logic Apps。 Logic Apps 是一個用於建置企業工作流程和整合的無伺服器平台。
- 連接器。 Logic Apps 會使用連接器來連線到常用的服務。 Logic Apps 已經有數百個各種不同的連接器，但您也可以使用自訂連接器來建立它們。
- Azure 服務匯流排。 「服務匯流排」提供安全可靠的傳訊。 傳訊可用來將應用程式彼此分離，然後與其他訊息型系統整合。
- Azure 事件格線。 「事件格線」是一個發佈和傳遞應用程式事件的無伺服器平台。
- IP 位址。 「Azure API 管理」服務有固定的公用 IP 位址和網域名稱。 網域名稱是 azure-api.net 的子網域，例如 contoso.azure-api.net。 Logic Apps 和「服務匯流排」也有公用 IP 位址；不過，在此架構中，我們會將呼叫 Logic Apps 端點的存取權限制成僅供「API 管理」的 IP 位址使用 (基於安全考量)。 對「服務匯流排」的呼叫會受共用存取簽章保護。
- Azure DNS。 Azure DNS 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構提供名稱解析。 只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。 若要使用自訂網域名稱 (例如 contoso.com)，請建立將自訂網域名稱對應至 IP 位址的 DNS 記錄。 如需詳細資訊，請參閱＜在 Azure API 管理中設定自訂網域名稱＞。
- Azure Active Directory (Azure AD)。 使用 Azure AD 或其他識別提供者進行驗證。 Azure AD 會提供驗證來存取 API 端點 (藉由傳遞要讓「API 管理」驗證的「JSON Web 權杖」)，並可保護對「API 管理開發人員入口網站」的存取 (僅限「標準」層和「進階」層)。

此架構有其作業的一些基本模式：

1. 現有的後端 HTTP API 是透過「API 管理開發人員入口網站」發佈的，可讓開發人員 (不論是您組織內部、外部，還是兩者都包括) 將對這些 API 的呼叫整合至應用程式。
2. 複合 API 時是使用 Logic Apps 來建置的；其中會協調對 SAAS 系統、Azure 服務及任何發佈至「API 管理」之 API 的呼叫。 Logic Apps 也是透過「API 管理開發人員入口網站」發佈的。
3. 應用程式會使用 Azure Active Directory 來取得存取 API 所需的 OAuth 2.0 安全性權杖。
4. 「Azure API 管理」會驗證該安全性權杖，然後將要求傳遞給後端 API/邏輯應用程式。
5. 「服務匯流排」佇列可用來將應用程式活動分離並緩和負載中的高峰。 訊息會由 Logic Apps、協力廠商應用程式新增至佇列，或 (未描述) 藉由透過「API 管理」將佇列發佈成 HTTP API 來新增至佇列。
6. 將訊息新增至「服務匯流排」佇列時，就會觸發「事件」。 邏輯應用程式會受此事件觸發，然後處理訊息。
7. 同樣地，其他 Azure 服務 (例如 Blob 儲存體、事件中樞) 也會將事件發佈至「事件格線」。 這些會觸發 Logic Apps 來接收事件並執行後續的動作。

## <a name="recommendations"></a>建議

您的需求可能和此處所述的架構不同。 以本節的建議作為起點。

### <a name="service-bus-tier"></a>服務匯流排層

請使用「服務匯流排」進階層，因為這目前可支援事件格線通知 (預期會在所有層都支援)。 請參閱[服務匯流排價格](https://azure.microsoft.com/pricing/details/service-bus/)。

### <a name="event-grid-pricing"></a>事件格線價格

「事件格線」是以無伺服器模型運作 – 計費的計算方式是根據作業數目 (事件執行)。 如需詳細資訊，請參閱[事件格線價格](https://azure.microsoft.com/pricing/details/event-grid/)。 目前針對「事件格線」沒有任何階層考量。

### <a name="use-peeklock-when-consuming-service-bus-messages"></a>取用服務匯流排訊息時，請使用 PeekLock

建立 Logic Apps 來取用「服務匯流排」訊息時，請在邏輯應用程式內使用 [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) 來存取一組訊息。 接著，邏輯應用程式可以先執行步驟來驗證每個訊息，再完成或放棄訊息。 此方法可防止意外的訊息遺失。

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>引發事件格線觸發程序時，請檢查是否有多個物件

「事件格線」觸發程序的引發只是表示「至少發生了這些情況其中之一」。 例如，當「事件格線」針對出現在「服務匯流排」佇列中的訊息觸發邏輯應用程式時，該邏輯應用程式應該一律假設有一或多個訊息可供處理。

### <a name="region"></a>區域

將「API 管理」、Logic Apps 及「服務匯流排」佈建在相同區域中，以將網路延遲降到最低。 通常會選擇最接近使用者的區域。

資源群組也有區域，用來指定將部署中繼資料儲存在哪裡，以及從哪裡執行部署範本。 將資源群組及其資源放置於相同區域。 這麼做可在部署期間提升可用性。

## <a name="scalability-considerations"></a>延展性考量

「Azure 服務匯流排」進階層可以相應放大傳訊單位數目，以達到更高的延展性。 進階層可以有 1、2 或 4 個傳訊單位。 如需有關調整「Azure 服務匯流排」規模的進一步指引，請參閱[使用服務匯流排傳訊的效能改進最佳做法](../service-bus-messaging/service-bus-performance-improvements.md)。

## <a name="availability-considerations"></a>可用性考量

撰寫本文時，「Azure API 管理」的「基本」、「標準」及「進階」層服務等級協定 (SLA) 是 99.9%。 「進階」層設定搭配在兩個以上的區域中至少部署 1 個單位時，SLA 為 99.95%。

撰寫本文時，Azure Logic Apps 的服務等級協定 (SLA) 是 99.9%。

### <a name="disaster-recovery"></a>災害復原

請考慮實作「服務匯流排」進階層中的異地災害復原，以便能夠在發生嚴重服務中斷時進行容錯移轉。 如需詳細資訊，請參閱 [Azure 服務匯流排異地災害復原](../service-bus-messaging/service-bus-geo-dr.md)。

## <a name="manageability-considerations"></a>管理性考量

針對生產、部署和測試環境，各別建立資源群組。 這可讓您更輕鬆地管理部署、刪除測試部署及指派存取權限。
將資源指派給資源群組時，請考慮下列項目：

- 生命週期。 一般來說，會將生命週期相同的資源放在同個資源群組中。
- 存取。 您可以使用[角色型存取控制](../role-based-access-control/overview.md) (RBAC) 將存取原則套用至群組中的資源。
- 計費。 您可以檢視資源群組的彙總成本。
- 「API 管理」的定價層 – 針對開發和測試環境，建議您使用「開發人員層」。 針對生產階段前環境，建議您部署生產環境複本、執行測試，然後關閉，以將成本降到最低。

如需詳細資訊，請參閱[資源群組](../azure-resource-manager/resource-group-overview.md)概觀。

### <a name="deployment"></a>部署

建議您使用 [Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)來部署「Azure API 管理」、Logic Apps、「事件格線」及「服務匯流排」。 範本可讓您透過 PowerShell 或 Azure 命令列介面 (CLI)，更輕鬆地自動執行部署。

建議您將「Azure API 管理」、任何個別的 Logic Apps、「事件格線」主題及「服務匯流排命名空間」放在它們自己的個別 Resource Manager 範本中。 這可允許將它們儲存在原始檔控制系統中。 接著，便可在持續整合/持續部署 (CI/CD) 流程中，將這些範本一起或個別部署。

### <a name="diagnostics-and-monitoring"></a>診斷和監控

就像監視「API 管理」和 Logic Apps 一樣，您也可以使用「Azure 監視器」來監視「服務匯流排」。 「Azure 監視器」是預設啟用的功能，並且會根據為每項服務設定的不同計量來提供資訊。

## <a name="security-considerations"></a>安全性考量

請使用「共用存取簽章」來保護「服務匯流排」。 [SAS 驗證](../service-bus-messaging/service-bus-sas.md)可讓您授與使用者具特定權限的服務匯流排資源存取權。 如需詳細資訊，請參閱[服務匯流排驗證和授權](../service-bus-messaging/service-bus-authentication-and-authorization.md)。

此外，如果「服務匯流排」佇列必須以 HTTP 端點的形式公開 (以便允許張貼新訊息)，則應該使用「API 管理」作為該端點的前端來保護它。 接著，即可視情況使用憑證或 OAuth 來提供保護。 最簡單的做法是，使用邏輯應用程式搭配「要求/回應」HTTP 觸發程序作為媒介。

「事件格線」會透過驗證碼來保護事件傳遞。 如果您使用 Logic Apps 來取用事件，將會自動執行此操作。 如需更多詳細資訊，請參閱[事件格線安全性與驗證](../event-grid/security-authentication.md)。

## <a name="next-steps"></a>後續步驟

* [簡單的企業整合](logic-apps-architectures-simple-enterprise-integration.md)
