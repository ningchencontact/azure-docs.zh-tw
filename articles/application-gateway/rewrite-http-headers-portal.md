---
title: 重新撰寫 HTTP 要求和回應標頭與 Azure 應用程式閘道-Azure 入口網站 |Microsoft Docs
description: 了解如何使用 Azure 入口網站設定 Azure 應用程式閘道就能重寫中的要求和回應通過閘道的 HTTP 標頭
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/10/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6afc07f98905469b06622e7829ec4a215b94845e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716147"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>重新撰寫 HTTP 要求和回應標頭與 Azure 應用程式閘道-Azure 入口網站

這篇文章會示範如何使用 Azure 入口網站來設定[應用程式閘道 v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>)重寫中的要求和回應的 HTTP 標頭。

> [!IMPORTANT]
> 自動調整規模和區域備援應用程式閘道 SKU 目前為公開預覽版。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="before-you-begin"></a>開始之前

您需要有 v1 SKU 不支援 SKU，因為標頭重寫功能的應用程式閘道 v2。 如果您還沒有 v2 SKU，建立[應用程式閘道 v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps)開始之前。

## <a name="what-is-required-to-rewrite-a-header"></a>什麼是需要重寫的標頭

若要設定 HTTP 標頭重寫，您將需要：

1. 建立重寫 HTTP 標頭所需的新物件：

   - **請重寫動作**： 用來指定要求和您想要重寫的要求標頭欄位和原始的標頭需要重寫，以新值。 您可以選擇將關聯一或多個重寫條件重寫動作。

   - **請重寫條件**:它是選擇性的設定。 如果新增重寫條件，則會評估的 HTTP (S) 要求和回應的內容。 是否與重寫條件相符的 HTTP (S) 要求或回應以決定来執行的重寫條件相關聯的重寫動作。 

     如果多個條件相關聯動作，則動作將會執行只有當符合所有條件，也就是，將執行邏輯 AND 運算。

   - **重寫規則**： 重寫規則包含多個重寫動作-重寫條件組合。

   - **規則順序**： 能協助您判斷執行所在的不同重寫規則的順序。 重寫集合中有多個重寫規則時，這是很有幫助。 取得第一次執行的重寫規則，以較低的規則順序值。 如果您提供兩個重寫規則的相同規則順序執行的順序將會不具決定性的。

   - **請重寫組**： 包含多個重寫規則的要求路由規則建立關聯。

2. 您必須將設定的路由規則重寫。 這是因為的重寫組態附加至來源接聽程式，透過路由規則。 使用基本路由規則時，標頭重寫設定會與來源接聽程式相關聯，並且會是全域標頭重寫。 使用路徑型錄由規則時，會在 URL 路徑對應上定義標頭重寫設定。 因此，它只適用於網站的特定路徑區域。

您可以建立多個 http 標頭重寫集，而且每個重寫集可以套用至多個接聽程式。 不過，您可以套用只有一個重寫設為特定的接聽程式。

## <a name="sign-in-to-azure"></a>登入 Azure

使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。

## <a name="configure-header-rewrite"></a>設定標頭重寫

在此範例中，我們將修改重新導向 URL 重寫後端應用程式傳送的 http 回應的 location 標頭。 

1. 選取 **的所有資源**，然後選取 應用程式閘道。

2. 選取 **重寫**從左側功能表中。

3. 按一下  **+ 重寫組**。 

   ![新增重寫集](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. 提供用來重寫集名稱和其關聯的路由規則：

   - 輸入重寫中設定的名稱**名稱**文字方塊中。
   - 選取一或多個規則中所列**相關聯的路由規則**清單。 您只能選取尚未與其他重寫集相關聯的規則。 已經被其他重寫集相關聯的規則將會變成灰色。
   - 按 [下一步]。
   
     ![新增名稱和關聯](media/rewrite-http-headers-portal/name-and-association.png)

5. 建立重寫規則：

   - 按一下  **+ 新增重寫規則**。![新增重寫規則](media/rewrite-http-headers-portal/add-rewrite-rule.png)
   - 提供在重寫規則名稱 文字方塊的重寫規則的名稱，並提供規則的順序。![新增規則名稱](media/rewrite-http-headers-portal/rule-name.png)

6. 在此範例中，我們將會重寫的 location 標頭只包含"azurewebsites.net"的參考時，才。 若要這樣做，請加入條件來評估是否在回應中的 location 標頭包含 azurewebsites.net:

   - 按一下  **+ 新增條件**，然後按一下 [使用] 區段**如果**指示來展開它。![新增規則名稱](media/rewrite-http-headers-portal/add-condition.png)

   - 選取  **HTTP 標頭**從**檢查變數的型別**下拉式清單。 

   - 選取 **標頭型別**作為**回應**。

   - 因為在此範例中，我們正在評估剛好是常見的標頭，選取的位置標頭**常見的標頭**做為選項按鈕**標頭名稱**。

   - 選取 **位置**從**常見的標頭**下拉式清單。

   - 選取  **No**作為**區分大小寫**設定。

   - 選取 **等號 （=）** 從**運算子**下拉式清單。

   - 輸入規則運算式模式。 在此範例中，我們將使用模式`(https?):\/\/.*azurewebsites\.net(.*)$`。

   - 按一下 [確定]。

     ![修改位置標頭](media/rewrite-http-headers-portal/condition.png)

7. 新增動作，來重寫的 location 標頭：

   - 選取 **設定**作為**動作類型**。

   - 選取 **回應**作為**標頭類型**。

   - 選取 **常見的標頭**作為**標頭名稱**。

   - 選取 **位置**從**常見的標頭**下拉式清單。

   - 輸入的標頭值。 在此範例中，我們將使用`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`做為標頭值。 這會取代*azurewebsites.net*具有*contoso.com* location 標頭中。

   - 按一下 [確定]。

     ![修改位置標頭](media/rewrite-http-headers-portal/action.png)

8. 按一下 **建立**建立重寫設定。

   ![修改位置標頭](media/rewrite-http-headers-portal/create.png)

9. 您將重寫集檢視來瀏覽。 請確認您先前建立的重寫集合位於重寫集合的清單。

   ![修改位置標頭](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>後續步驟

若要了解深入了解完成的一些常見的所需的組態使用的情況下，請參閱[常見的標頭重寫案例](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。

   
