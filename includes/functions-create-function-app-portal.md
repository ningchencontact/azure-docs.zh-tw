---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 2bfca370e8e89d98f700c8c5adfd29129e810d9b
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416261"
---
1. 選取 Azure 入口網站左上角的 [建立資源] 按鈕，然後選取 [計算] > [函式應用程式]。

    ![在 Azure 入口網站中建立函式應用程式](./media/functions-create-function-app-portal/function-app-create-flow.png)

2. 請使用影像下面的資料表中指定的函式應用程式設定。

    ![定義新的函式應用程式設定](./media/functions-create-function-app-portal/function-app-create-flow2.png)

    | 設定      | 建議的值  | 說明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **應用程式名稱** | 全域唯一的名稱 | 用以識別新函式應用程式的名稱。 有效字元是 `a-z`、`0-9` 和 `-`。  | 
    | **訂用帳戶** | 您的訂用帳戶 | 將在其下建立這個新函式應用程式的訂用帳戶。 | 
    | **[資源群組](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | 要在其中建立函式應用程式的新資源群組名稱。 |
    | **作業系統** | Windows | Linux 上的無伺服器裝載目前處於預覽階段。 如需詳細資訊，請參閱[此文章](https://aka.ms/funclinux)，了解各項應考量的內容。|
    | **[主控方案](../articles/azure-functions/functions-scale.md)** | 取用方案 | 會定義如何將資源配置給函式應用程式的主控方案。 在預設**取用方案**中，您的函式會根據需要來動態新增資源。 在此[無伺服器](https://azure.microsoft.com/overview/serverless-computing/)裝載中，您只需要針對函式有執行的時間來付費。 在 App Service 方案中執行時，您必須管理[函式應用程式的調整](../articles/azure-functions/functions-scale.md)。  |
    | **位置** | 西歐 | 選擇與您接近的[區域](https://azure.microsoft.com/regions/)，或選擇與函式將會存取之其他服務接近的區域。 |
    | **執行階段堆疊** | 慣用語言 | 選擇支援您慣用函式程式設計語言的執行階段。 針對 C# 和 F # 函式選擇 **.NET**。 |
    | **[儲存體](../articles/storage/common/storage-quickstart-create-account.md)** |  全域唯一的名稱 |  建立您函式應用程式使用的儲存體帳戶。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。 您也可以使用現有帳戶，條件是必須符合[儲存體帳戶需求](../articles/azure-functions/functions-scale.md#storage-account-requirements)。 |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | 預設值 | 在最近的支援區域中，建立相同*應用程式名稱*的 Application Insights 資源。 您可以展開此設定，變更 [新資源名稱] 或在 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中依您希望儲存資料的地點，選擇不同的**位置**。 |

3. 選取 [建立] 以佈建並部署函式應用程式。

4. 選取入口網站右上角的 [通知] 圖示，查看是否有**部署成功**訊息。

    ![定義新的函式應用程式設定](./media/functions-create-function-app-portal/function-app-create-notification.png)

5. 選取 [前往資源]，以檢視您新的函式應用程式。 您也可以選取 [釘選到儀表板]。 釘選可讓您更輕鬆地從儀表板返回此函式應用程式資源。
