---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b6cafcfe6c892cd43f056458fe3586da834c2fd1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60403571"
---
函式可讓您輕鬆地從 [Azure 入口網站]中，將 Application Insights 整合新增至函式應用程式。

1. 在[入口網站][Azure 入口網站]中，選取 [所有服務] > [函式應用程式]，然後選取函式應用程式，接著在視窗頂端選擇 [Application Insights] 橫幅

    ![從入口網站啟用 Application Insights](media/functions-connect-new-app-insights/enable-application-insights.png)

1. 使用資料表 (圖片下方) 中指定的設定，建立 Application Insights 資源：

   ![建立 Application Insights 資源](media/functions-connect-new-app-insights/ai-general.png)

    | 設定      | 建議的值  | 說明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名稱** | 唯一的應用程式名稱 | 最簡單的方式是使用與您函式應用程式一樣的名稱，而這必須是您訂用帳戶中唯一的名稱。 | 
    | **位置** | 西歐 | 可能的話，請使用與函式應用程式相同的[地區](https://azure.microsoft.com/regions/)，或其附近的地區。 |

1. 選擇 [確定]。 Application Insights 資源會建立在函式應用程式所在的資源群組和訂用帳戶。 建立完成之後，請關閉 Application Insights 視窗。

1. 回到您函式應用程式中，選取 [應用程式設定]，並向下捲動至 [應用程式設定]。 當您看到名為 `APPINSIGHTS_INSTRUMENTATIONKEY` 的設定時，表示 Azure 中執行的函式應用程式已啟用 Application Insights 整合。

[Azure 入口網站]: https://portal.azure.com
