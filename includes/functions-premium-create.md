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
ms.openlocfilehash: 9bac92bc1cc94b88dc694b468b795049db4ac9df
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443970"
---
1. 移至 [Azure 入口網站](https://portal.azure.com)。

1. 選取左側的 [ **+ 建立資源**], 然後選擇 [**函數應用程式**]。

1. 針對 [**主控方案**], 選擇 [ **App Service 方案**], 然後選取 [ **App Service 方案/位置**]。

    ![建立函數應用程式](./media/functions-premium-create/create-function-app-resource.png)

1. 選取 [**新建**], 輸入**App Service 方案**名稱, 選擇您的函式在您的[區域](https://azure.microsoft.com/regions/)附近或接近您函數存取的其他服務**位置**, 然後選取 [**定價層**]。

    ![建立 App Service 方案](./media/functions-premium-create/new-app-service-plan.png)

1. 選擇 [ **EP1** (彈性 Premium)] 方案, 然後選取 [套用]。

    ![選取 premium 方案](./media/functions-premium-create/hosting-plan.png) 

1. 選取 **[確定]** 以建立方案, 然後使用影像下方資料表中所指定的其餘函數應用程式設定。 

    ![已完成 app service 方案](./media/functions-premium-create/create-function-app.png)  

    | 設定      | 建議值  | 描述                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **應用程式名稱** | 全域唯一的名稱 | 用以識別新函式應用程式的名稱。 有效字元是 `a-z`、`0-9` 和 `-`。  | 
    | **訂用帳戶** | 您的訂用帳戶 | 將在其下建立這個新函式應用程式的訂用帳戶。 |
    | **[資源群組](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | 要在其中建立函式應用程式的新資源群組名稱。 您也可以使用建議的值。 |
    | **OS** | 慣用的作業系統 | Premium 方案支援 Linux 和 Windows。 |
    | **執行階段堆疊** | 慣用語言 | 選擇支援您慣用函式程式設計語言的執行階段。 針對 C# 和 F # 函式選擇 **.NET**。 只會顯示您所選**OS**上支援的語言。 |
    | **[儲存體](../articles/storage/common/storage-quickstart-create-account.md)** |  全域唯一的名稱 |  建立您函式應用程式使用的儲存體帳戶。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。 您也可以使用現有帳戶，條件是必須符合[儲存體帳戶需求](../articles/azure-functions/functions-scale.md#storage-account-requirements)。 |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | 預設 | 在最近的支援區域中，建立相同*應用程式名稱*的 Application Insights 資源。 您可以展開此設定，變更 [新資源名稱] 或在 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中依您希望儲存資料的地點，選擇不同的**位置**。 |

1. 驗證設定之後, 請選取 [**建立**]。

1. 選取入口網站右上角的 [通知] 圖示，查看是否有**部署成功**訊息。

    ![定義新的函式應用程式設定](./media/functions-premium-create/function-app-create-notification.png)

1. 選取 [前往資源]，以檢視您新的函式應用程式。 您也可以選取 [釘選到儀表板]。 釘選可讓您更輕鬆地從儀表板返回此函式應用程式資源。