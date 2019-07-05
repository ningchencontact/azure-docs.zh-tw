---
title: 從 Azure Logic Apps 連線到 HTTP 或 HTTPS 端點
description: 使用 Azure Logic Apps 監視自動化的工作、 處理程序與工作流程中的 HTTP 或 HTTPS 端點
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: fa5fd3ef8b144826468f56ea2a14be592cef5dc1
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541271"
---
# <a name="call-http-or-https-endpoints-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 呼叫 HTTP 或 HTTPS 端點

具有[Azure Logic Apps](../logic-apps/logic-apps-overview.md)以及內建的 HTTP 連接器，您可以自動化定期呼叫任何 HTTP 或 HTTPS 端點，藉由建置邏輯應用程式的工作流程。 例如，您也可以檢查在端點上指定的排程，以監視服務端點為您的網站。 當特定事件發生在該端點，例如您的網站停止運作，事件就會觸發邏輯應用程式的工作流程，並執行指定的動作。

若要檢查或*輪詢*端點定期的排程，您可以使用 HTTP 觸發程序的第一個步驟中您的工作流程。 在每次檢查時，觸發程序會將呼叫或「要求」  傳送至端點。 端點的回應會決定是否執行邏輯應用程式的工作流程。 觸發程序會將回應的任何內容，傳遞至邏輯應用程式中的動作。

您可以使用 HTTP 動作作為工作流程中的任何其他步驟，在您想要的時候檢查端點。 端點的回應會決定工作流程的剩餘動作如何執行。

根據目標端點的功能，HTTP 連接器支援傳輸層安全性 (TLS) 1.0、 1.1 和 1.2 版。 與端點的 logic Apps 是透過使用可能的最新支援的版本交涉。 因此，比方說，如果端點支援 1.2，連接器會使用 1.2 第一次。 否則，連接器會使用下一個最高支援的版本。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您想要呼叫的目標端點的 URL

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)

* 從您要呼叫的目標端點的邏輯應用程式。 HTTP 觸發程序中，啟動[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP 動作，請以您想要的任何觸發程序啟動邏輯應用程式。 此範例會使用 HTTP 觸發程序的第一個步驟。

## <a name="add-an-http-trigger"></a>新增 HTTP 觸發程序

此內建的觸發程序進行 HTTP 呼叫至為端點指定的 URL，並傳回回應。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟空白的邏輯應用程式。

1. 在設計工具中，在 [搜尋] 方塊中，輸入"http"作為篩選條件。 從**觸發程序**清單中，選取**HTTP**觸發程序。

   ![選取 HTTP 觸發程序](./media/connectors-native-http/select-http-trigger.png)

   此範例中重新命名 「 HTTP 觸發程序 「 觸發程序，以便在此步驟中有更具描述性的名稱。 此外，範例稍後會新增 HTTP 動作，，而且這兩個名稱必須是唯一。

1. 提供值給[HTTP 觸發程序參數](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)您想要包含在目標端點的呼叫中。 若要檢查目標端點設定的循環觸發程序的頻率。

   ![輸入 HTTP 觸發程序參數](./media/connectors-native-http/http-trigger-parameters.png)

   如需有關可用的驗證類型為 HTTP，請參閱[驗證的 HTTP 觸發程序和動作](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

1. 若要新增其他可用的參數，請開啟**加入新參數**清單，然後選取您想要的參數。

1. 請使用當引發觸發程序時執行的動作，繼續建置邏輯應用程式的工作流程。

1. 當您完成時，完成之後，請務必儲存您的邏輯應用程式。 在設計工具的工具列中，選取**儲存**。

## <a name="add-an-http-action"></a>新增 HTTP 動作

此內建動作進行 HTTP 呼叫至為端點指定的 URL，並傳回回應。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟邏輯應用程式。

   此範例會使用 HTTP 觸發程序的第一個步驟。

1. 在您要新增 HTTP 動作的步驟中，選取**新增步驟**。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選取加號 ( **+** )，隨即出現，然後按**新增動作**。

1. 在設計工具中，在 [搜尋] 方塊中，輸入"http"作為篩選條件。 從**動作**清單中，選取**HTTP**動作。

   ![選取 HTTP 動作](./media/connectors-native-http/select-http-action.png)

   此範例中重新命名 「 HTTP 動作 」 的動作，以便在此步驟中有更具描述性的名稱。

1. 提供值給[HTTP 動作參數](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)您想要包含在目標端點的呼叫中。

   ![輸入 HTTP 動作參數](./media/connectors-native-http/http-action-parameters.png)

   如需有關可用的驗證類型為 HTTP，請參閱[驗證的 HTTP 觸發程序和動作](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

1. 若要新增其他可用的參數，請開啟**加入新參數**清單，然後選取您想要的參數。

1. 當您完成時，請務必儲存您的邏輯應用程式。 在設計工具的工具列中，選取**儲存**。

## <a name="connector-reference"></a>連接器參考

如需有關觸發程序和動作參數的詳細資訊，請參閱下列各節：

* [HTTP 觸發程序參數](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [HTTP 動作參數](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>输出详细信息

以下是輸出的 HTTP 觸發程序或動作，它會傳回這項資訊的詳細資訊：

| 屬性名稱 | 類型 | 描述 |
|---------------|------|-------------|
| headers | object | 來自要求的標頭 |
| body | object | JSON 物件 | 要求的主體內容物件 |
| status code | int | 要求中的狀態碼 |
|||

| status code | 描述 |
|-------------|-------------|
| 200 | [確定] |
| 202 | 已接受 |
| 400 | 不正確的要求 |
| 401 | 未經授權 |
| 403 | 禁止 |
| 404 | 找不到 |
| 500 | 內部伺服器錯誤。 發生未知錯誤。 |
|||

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
