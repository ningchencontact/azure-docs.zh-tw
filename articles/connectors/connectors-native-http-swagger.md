---
title: 從 Azure Logic Apps 連線到 REST 端點
description: 使用 Azure Logic Apps 監視自動化的工作、 處理程序與工作流程中的 REST 端點
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: f0410ed7a98e4838e41407868cf26b5254811ae3
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541614"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 呼叫 REST 端點

具有[Azure Logic Apps](../logic-apps/logic-apps-overview.md)和內建 HTTP + Swagger 連接器，您可以將自動化工作流程，定期呼叫任何 REST 端點，透過[Swagger 檔案](https://swagger.io)藉由建置邏輯應用程式。 HTTP + Swagger 觸發程序和動作的運作方式相同[HTTP 觸發程序和動作](connectors-native-http.md)但提供較佳的體驗，在邏輯應用程式設計工具中公開的 API 結構和 Swagger 檔案所述的輸出。 若要實作輪詢觸發程序，請依照下列所述的輪詢模式[建立自訂 Api 來從邏輯應用程式呼叫其他 Api、 服務和系統](../logic-apps/logic-apps-create-api-app.md#polling-triggers)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 描述目標 REST 端點的 Swagger 檔案的 URL

  一般而言，REST 端點必須符合此準則，連接器才能運作：

  * Swagger 檔案必須裝載於可公開存取 HTTPS URL。

  * Swagger 檔案必須具有[跨原始資源共用 (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)啟用。

  若要參考未裝載或不符合安全性和跨原始來源需求的 Swagger 檔案，您可以[的 Swagger 檔案上傳至 Azure 儲存體帳戶中的 blob 容器](#host-swagger)，並因此在該儲存體帳戶上啟用 CORS您可以參考檔案。

  在本主題使用範例[認知服務臉部 API](https://docs.microsoft.com/azure/cognitive-services/face/overview)，而這需要[認知服務帳戶和存取金鑰](../cognitive-services/cognitive-services-apis-create-account.md)。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)

* 從您要呼叫的目標端點的邏輯應用程式。 開始使用 HTTP + Swagger 觸發程序，[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP + Swagger 動作與您想要的任何觸發程序啟動邏輯應用程式。 此範例會使用 HTTP + Swagger 觸發程序的第一個步驟。

## <a name="add-an-http--swagger-trigger"></a>新增 HTTP + Swagger 觸發程序

此內建的觸發程序會傳送 HTTP 要求，以描述 REST API，並傳回包含該檔案的內容回應的 Swagger 檔案的 URL。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟空白的邏輯應用程式。

1. 在設計工具中，在 [搜尋] 方塊中，輸入"swagger"作為篩選條件。 從**觸發程序**清單中，選取**HTTP + Swagger**觸發程序。

   ![選取 HTTP + Swagger 觸發程序](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. 在 [ **SWAGGER 端點 URL**方塊中，Swagger 檔案中，輸入的 URL，然後選取**下一步]** 。

   此範例會使用位在美國西部區域，以 Swagger URL[認知服務臉部 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![輸入 Swagger 端點 URL](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. 當設計工具顯示的 Swagger 檔案所述的作業時，請選取您想要使用的作業。

   ![Swagger 檔案中的作業](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. 觸發程序參數，而而有所不同，根據選取的作業，您想要包含在端點的呼叫中提供的值。 若要呼叫的端點設定的循環觸發程序的頻率。

   此範例會重新命名觸發程序 」 HTTP + Swagger 觸發程序：面臨-偵測 」，讓此步驟中有更具描述性的名稱。

   ![作業詳細資料](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. 若要新增其他可用的參數，請開啟**加入新參數**清單，然後選取您想要的參數。

   HTTP + Swagger 可用的驗證類型的相關資訊，請參閱[驗證的 HTTP 觸發程序和動作](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

1. 請使用當引發觸發程序時執行的動作，繼續建置邏輯應用程式的工作流程。

1. 當您完成時，請務必儲存您的邏輯應用程式。 在設計工具的工具列中，選取**儲存**。

## <a name="add-an-http--swagger-action"></a>新增 HTTP + Swagger 動作

此內建動作進行 HTTP 要求之 url 的 Swagger 檔案以描述 REST API，並傳回回應，其中包含該檔案的內容。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 底下的步驟，您要新增 HTTP + Swagger 動作中，選取**新增步驟**。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選取加號 ( **+** )，隨即出現，然後按**新增動作**。

1. 在設計工具中，在 [搜尋] 方塊中，輸入"swagger"作為篩選條件。 從**動作**清單中，選取**HTTP + Swagger**動作。

    ![選取 HTTP + Swagger 動作](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. 在 [ **SWAGGER 端點 URL**方塊中，Swagger 檔案中，輸入的 URL，然後選取**下一步]** 。

   此範例會使用位在美國西部區域，以 Swagger URL[認知服務臉部 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![輸入 Swagger 端點 URL](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. 當設計工具顯示的 Swagger 檔案所述的作業時，請選取您想要使用的作業。

   ![Swagger 檔案中的作業](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. 動作參數，而而有所不同，根據選取的作業，您想要包含在端點的呼叫中提供的值。

   此範例中沒有參數，但重新命名的動作 」 HTTP + Swagger 動作：面臨-識別 」，讓此步驟中有更具描述性的名稱。

   ![作業詳細資料](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. 若要新增其他可用的參數，請開啟**加入新參數**清單，然後選取您想要的參數。

   HTTP + Swagger 可用的驗證類型的相關資訊，請參閱[驗證的 HTTP 觸發程序和動作](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

1. 當您完成時，請務必儲存您的邏輯應用程式。 在設計工具的工具列中，選取**儲存**。

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>在 Azure 儲存體託管 Swagger

您可以參考 Swagger 檔案，不裝載或不符合安全性和跨原始來源要求該檔案上傳至 Azure 儲存體帳戶中的 blob 容器，並在該儲存體帳戶上啟用 CORS。 若要建立、 設定，並將 Swagger 檔案儲存在 Azure 儲存體，請遵循下列步驟：

1. [建立 Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md)。

1. 現在針對 blob 啟用 CORS。 在您的儲存體帳戶 功能表中，選取**CORS**。 在  **Blob 服務**索引標籤上，指定這些值，然後選取**儲存**。

   | 屬性 | 值 |
   |----------|-------|
   | **允許的來源** | `*` |
   | **允許的方法** | `GET`, `HEAD`, `PUT` |
   | **允許的標頭** | `*` |
   | **公開的標頭** | `*` |
   | **最大壽命**（以秒為單位） | `200` |
   |||

   雖然此範例會使用[Azure 入口網站](https://portal.azure.com)，您可以使用一種工具，例如[Azure 儲存體總管](https://storageexplorer.com/)，或使用此範例中，自動設定此設定[PowerShell 指令碼](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [建立 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md)。 在容器上**概觀**窗格中，選取**變更存取層級**。 從**公用存取層級**清單中，選取**Blob （匿名讀取權限僅限 blob）** ，然後選取**確定**。

1. [將 Swagger 檔案上傳至 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)，透過[Azure 入口網站](https://portal.azure.com)或是[Azure 儲存體總管](https://storageexplorer.com/)。

1. 若要參考的 blob 容器中的檔案，請使用未遵循此格式，這是區分大小寫的 HTTPS 連結：

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>連接器參考

以下是詳細資訊的輸出 HTTP + Swagger 觸發程序或動作。 HTTP + Swagger 呼叫會傳回這項資訊：

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