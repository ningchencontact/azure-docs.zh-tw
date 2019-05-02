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
ms.openlocfilehash: e144214a58f9fe383cf4edd878554792d9d6a6f9
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947156"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>重新撰寫 HTTP 要求和回應標頭與 Azure 應用程式閘道-Azure 入口網站

本文說明如何使用 Azure 入口網站來設定[應用程式閘道 v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>)重寫要求和回應中的 HTTP 標頭的執行個體。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="before-you-begin"></a>開始之前

您需要有應用程式閘道 v2 SKU 執行個體來完成這篇文章中的步驟。 V1 SKU 中不支援重寫標頭。 如果您還沒有 v2 SKU，建立[應用程式閘道 v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps)在您開始前執行個體。

## <a name="create-required-objects"></a>建立所需的物件

若要設定 HTTP 標頭重寫，您需要完成下列步驟。

1. 建立物件所需的 HTTP 標頭重寫：

   - **請重寫動作**:用來指定要求和您想要重寫的要求標頭欄位和標頭的新值。 您可以將其中一個或多個重寫重寫動作的情況。

   - **請重寫條件**:選擇性的組態。 重寫條件評估結果 HTTP (S) 要求和回應的內容。 如果在 HTTP (S) 要求或回應符合重寫條件，就會發生重新寫入動作。

     如果您將多個條件與動作時，只在符合所有條件時，就會發生的動作。 換句話說，此作業是邏輯 AND 運算。

   - **重寫規則**:包含多個重寫動作 / 重寫條件組合。

   - **規則順序**:可協助判斷在其中執行的重寫規則的順序。 當您在重寫組有多個重寫規則，此設定是很有幫助。 重寫規則具有較低的規則順序值會先執行。 如果您將相同的規則順序值指派給兩個重寫規則時，執行順序是不具決定性。

   - **請重寫組**:包含與要求的路由規則相關聯的多個重寫規則。

2. 將連結設為路由規則重寫。 重寫組態會附加至來源接聽程式，透過路由規則。 當您使用基本路由規則時，標頭重寫組態與來源接聽程式相關聯，並是全域的標頭重寫。 當您使用路徑型路由規則時，標頭重寫組態被定義 URL 路徑對應。 在此情況下，它只適用於站台的特定路徑區域。

您可以建立多個 HTTP 標頭重寫集，並套用設定多個接聽程式為每個重寫。 但您可以只有一個重寫設為特定的接聽程式。

## <a name="sign-in-to-azure"></a>登入 Azure

使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。

## <a name="configure-header-rewrite"></a>設定標頭重寫

在此範例中，我們將修改重新導向 URL 重寫後端應用程式所傳送的 HTTP 回應的 location 標頭。

1. 選取 **的所有資源**，然後選取 應用程式閘道。

2. 選取 **重寫**的左窗格中。

3. 選取 **重寫組**:

   ![新增重寫集](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. 提供重寫集的名稱，並將它與路由規則產生關聯：

   - 輸入重寫中設定的名稱**名稱** 方塊中。
   - 選取一或多個所列的規則**相關聯的路由規則**清單。 您可以選取從未到過其他重寫集相關聯的規則。 已經被其他重寫集相關聯的規則會以灰色顯示。
   - 選取 [下一步] 。
   
     ![新增名稱和關聯](media/rewrite-http-headers-portal/name-and-association.png)

5. 建立重寫規則：

   - 選取 **新增重寫規則**。

     ![新增重寫規則](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - 輸入中的重寫規則的名稱**重寫規則名稱** 方塊中。 輸入中的數字**規則順序** 方塊中。

     ![新增重寫規則名稱](media/rewrite-http-headers-portal/rule-name.png)

6. 在此範例中，我們將會重寫的 location 標頭只包含 azurewebsites.net 的參考時，才。 若要這樣做，請加入條件來評估是否在回應中的 location 標頭包含 azurewebsites.net:

   - 選取 **新增條件**，然後選取 包含的方塊**如果**指示將它展開。

     ![新增條件](media/rewrite-http-headers-portal/add-condition.png)

   - 在 **檢查變數的型別**清單中，選取**HTTP 標頭**。

   - 在 **標頭型別**清單中，選取**回應**。

   - 選取 在此範例中，我們會評估位置標頭，也就是常見的標頭，因為**常見的標頭**下方**標頭名稱**。

   - 在 **常見的標頭**清單中，選取**位置**。

   - 底下**區分大小寫**，選取**No**。

   - 在 **運算子**清單中，選取**等號 （=）**。

   - 輸入規則運算式模式。 在此範例中，我們將使用此模式`(https?):\/\/.*azurewebsites\.net(.*)$`。

   - 選取 [確定] 。

     ![設定 If 條件](media/rewrite-http-headers-portal/condition.png)

7. 新增動作，來重寫的 location 標頭：

   - 在 **動作類型**清單中，選取**設定**。

   - 在 **標頭型別**清單中，選取**回應**。

   - 底下**標頭名稱**，選取**常見的標頭**。

   - 在 **常見的標頭**清單中，選取**位置**。

   - 輸入的標頭值。 在此範例中，我們將使用`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`做為標頭值。 此值將會取代*azurewebsites.net*具有*contoso.com* location 標頭中。

   - 選取 [確定] 。

     ![新增動作](media/rewrite-http-headers-portal/action.png)

8. 選取 **建立**建立重寫設定：

   ![選取 [建立]](media/rewrite-http-headers-portal/create.png)

9. 重寫集檢視就會開啟。 請確認您所建立的重寫集處於重寫組清單：

   ![重寫集檢視](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>後續步驟

若要深入了解如何設定一些常見的使用案例，請參閱[常見的標頭重寫案例](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。