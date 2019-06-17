---
title: 包含檔案
description: 包含檔案
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/01/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 0f3303e7bc87ca0bd29f367405372568ed6da7a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66131549"
---
1. 移至 [Azure 入口網站](https://portal.azure.com)。

1. 選取  **+ 建立資源**在左手邊，然後選擇**函式應用程式**。

1. 針對**主控方案**，選擇**App Service 方案**，然後選取**App Service 方案/位置**。

    ![建立函數應用程式](./media/functions-premium-create/create-function-app-resource.png)

1. 選取 **新建**，型別**App Service 方案**名稱，選擇 **位置**中[區域](https://azure.microsoft.com/regions/)您附近或接近其他服務您的函式存取，然後按**定價層**。

    ![建立 App Service 方案](./media/functions-premium-create/new-app-service-plan.png)

1. 選擇**EP1** (彈性 Premium) 計劃，然後選取**套用**。

    ![選取 進階方案](./media/functions-premium-create/hosting-plan.png) 

1. 選取 **確定**建立方案，則可使用其餘的函式應用程式設定為指定在影像下方資料表中。 

    ![完成的應用程式服務計劃](./media/functions-premium-create/create-function-app.png)  

    | 設定      | 建議值  | 描述                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **應用程式名稱** | 全域唯一的名稱 | 用以識別新函式應用程式的名稱。 有效字元是 `a-z`、`0-9` 和 `-`。  | 
    | **訂用帳戶** | 您的訂用帳戶 | 將在其下建立這個新函式應用程式的訂用帳戶。 |
    | **[資源群組](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | 要在其中建立函式應用程式的新資源群組名稱。 您也可以使用建議的值。 |
    | **OS** | Windows | 「 進階 」 方案目前不支援 Linux。 |
    | **執行階段堆疊** | 慣用語言 | 選擇支援您慣用函式程式設計語言的執行階段。 針對 C# 和 F # 函式選擇 **.NET**。 只支援所選的語言**OS**會顯示。 |
    | **[儲存體](../articles/storage/common/storage-quickstart-create-account.md)** |  全域唯一的名稱 |  建立您函式應用程式使用的儲存體帳戶。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。 您也可以使用現有帳戶，條件是必須符合[儲存體帳戶需求](../articles/azure-functions/functions-scale.md#storage-account-requirements)。 |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | 預設值 | 在最近的支援區域中，建立相同*應用程式名稱*的 Application Insights 資源。 您可以展開此設定，變更 [新資源名稱]  或在 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中依您希望儲存資料的地點，選擇不同的**位置**。 |

1. 會驗證您的設定之後，請選取**建立**。

1. 選取入口網站右上角的 [通知] 圖示，查看是否有**部署成功**訊息。

    ![定義新的函式應用程式設定](./media/functions-premium-create/function-app-create-notification.png)

1. 選取 [前往資源]  ，以檢視您新的函式應用程式。 您也可以選取 [釘選到儀表板]  。 釘選可讓您更輕鬆地從儀表板返回此函式應用程式資源。