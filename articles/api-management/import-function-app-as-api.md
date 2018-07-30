---
title: 在 Azure 入口網站中匯入 Azure Functions 應用程式作為 API | Microsoft Docs
description: 本教學課程說明如何使用「Azure API 管理」匯入 Azure Functions 應用程式作為 API。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/15/2018
ms.author: apimpm
ms.openlocfilehash: 670fa58de7155028b0f72f1f819b9f269e07b9eb
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239047"
---
# <a name="import-an-azure-functions-app-as-an-api"></a>匯入 Azure Functions 應用程式作為 API

本文說明如何匯入 Azure Functions 應用程式作為 API。 本文也說明如何測試 Azure API 管理 API。

在本文中，您將了解：

> [!div class="checklist"]
> * 匯入函式應用程式作為 API
> * 在 Azure 入口網站中測試 API
> * 在開發人員入口網站中測試 API

## <a name="prerequisites"></a>必要條件

+ 完成[建立 Azure API 管理執行個體](get-started-create-service-instance.md)快速入門。
+ 確定您的訂用帳戶中有 Azure Functions 應用程式。 如需詳細資訊，請參閱[建立函式應用程式](../azure-functions/functions-create-first-azure-function.md#create-a-function-app)。
+ 為您的 Azure Functions 應用程式[建立 OpenAPI 定義](../azure-functions/functions-openapi-definition.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"></a>匯入和發佈後端 API

1. 在 [API 管理] 下方，選取 [API]。
2. 在 [新增 API] 清單中，選取 [函式應用程式]。

    ![函式應用程式](./media/import-function-app-as-api/function-app-api.png)
3. 選取 [瀏覽] 以查看您訂用帳戶中的函式應用程式清單。
4. 選取應用程式。 APIM 管理會尋找與選取的應用程式相關聯的 Swagger，並在擷取後加以匯入。 
5. 新增 API URL 尾碼。 此尾碼可用來在此 API 管理執行個體中識別這個 API 的名稱。 尾碼在此 API 管理執行個體中必須是唯一的。
6. 透過將 API 關聯至某個產品來發佈 API。 在此案例中，我們使用「無限制」的產品。 如果您想要發佈 API 以供開發人員使用，請將 API 新增至產品。 您可以在建立 API 時將 API 新增至產品，或稍後再新增。

    產品是一或多個 API 的關聯。 您可以包含多個 API，並透過開發人員入口網站將其提供給開發人員。 開發人員必須先訂閱產品，才能取得 API 的存取權。 當開發人員訂閱時，他們會取得適用於該產品中任何 API 的訂用帳戶金鑰。 如果您已建立 API 管理執行個體，您將是系統管理員。 根據預設，系統管理員會訂閱每個產品。

    依預設，每個 API 管理執行個體會隨附兩個範例產品：

    * **入門**
    * **無限制**   
7. 選取 [建立] 。

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>在 Azure API 管理中填入 Azure Functions 金鑰

如果匯入的 Azure Functions 應用程式受到金鑰的保護，則 API 管理會自動為金鑰建立*具名值*。 API 管理不會在項目中填入祕密。 請為每個項目完成下列步驟：  

1. 在 API 管理執行個體中，選取 [具名值] 索引標籤。
2. 選取項目，然後在資訊看板中選取 [顯示值]。

    ![具名值](./media/import-function-app-as-api/apim-named-values.png)

3. 如果顯示在 [值] 方塊中的文字類似於 **\<Azure Functions 名稱\> 的程式碼**，請移至 [函式應用程式]，然後移至 [函式]。
4. 選取 [管理]，然後根據應用程式的驗證方法複製相關金鑰。

    ![函式應用程式 - 複製金鑰](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. 將金鑰貼到 [值] 方塊中，然後選取 [儲存]。

    ![函式應用程式 - 貼上金鑰值](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-api-management-api-in-the-azure-portal"></a>在 Azure 入口網站中測試新的 API 管理 API

您可以直接從 Azure 入口網站呼叫作業。 使用 Azure 入口網站可方便您檢視和測試 API 的作業。  

1. 選取您在先前的小節中建立的 API。
2. 選取 [測試] 索引標籤。
3. 選取作業。

    頁面會顯示查詢參數的欄位和標頭的欄位。 其中一個標頭是 **Ocp-Apim-Subscription-Key**，它適用於此 API 相關產品的訂用帳戶金鑰。 如果您建立了 API 管理執行個體，您就已經是系統管理員，因此會自動填入此金鑰。 
4. 選取 [傳送]。

    後端會回應 **200 確定**與部分資料。

## <a name="call-operation"></a>從開發人員入口網站呼叫作業

您也可以從開發人員入口網站呼叫作業，以測試 API。 

1. 選取您在[匯入和發佈後端 API](#create-api) 中建立的 API。
2. 選取 [開發人員入口網站]。

    開發人員入口網站隨即開啟。
3. 選取您所建立的 **API**。
4. 選取您要測試的作業。
5. 選取 [試試看]。
6. 選取 [傳送]。
    
    叫用作業之後，開發人員入口網站會顯示 [回應狀態]、[回應標頭]，以及任何的 [回應內容]。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [轉換及保護已發佈的 API](transform-api.md)