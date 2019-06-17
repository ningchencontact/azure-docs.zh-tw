---
title: 建立新的 Azure Application Insights 資源 | Microsoft Docs
description: 針對新的即時應用程式手動設定 Application Insights 監視。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbullwin
ms.openlocfilehash: 9da52e5a9dfa3b55431d66ed3162172226f71a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073264"
---
# <a name="create-an-application-insights-resource"></a>建立 Application Insights 資源

Azure Application Insights 會在 Microsoft Azure「資源」  中顯示您應用程式的相關資料。 因此，建立新的資源是屬於[設定 Application Insights 以監視新應用程式][start]的一環。 您已建立新的資源之後，您可以取得其檢測金鑰，並使用它來設定 Application Insights SDK。 檢測金鑰會連結至資源的遙測資料。

## <a name="sign-in-to-microsoft-azure"></a>登入 Microsoft Azure

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="create-an-application-insights-resource"></a>建立 Application Insights 資源

登入[Azure 入口網站](https://portal.azure.com)，然後建立 Application Insights 資源：

![按一下左上角中的 '+' 號。 選取後面跟著 Application Insights 的開發人員工具](./media/create-new-resource/new-app-insights.png)

   | 設定        |  值           | 描述  |
   | ------------- |:-------------|:-----|
   | **名稱**      | 通用唯一值 | 識別您要監視的應用程式的名稱。 |
   | **資源群組**     | myResourceGroup      | 新的或現有的資源群組來裝載 App Insights 資料的名稱。 |
   | **Location** | 美國東部 | 選擇您附近的位置，或接近託管您的應用程式。 |

必要的欄位中，輸入適當的值，然後選取**檢閱 + 建立**。

![必要的欄位中輸入值，然後選取 [檢閱 + 建立]。](./media/create-new-resource/review-create.png)

建立您的應用程式後，便會開啟一個新的窗格。 此窗格是在其中您看到效能和使用量資料的受監視應用程式。 

## <a name="copy-the-instrumentation-key"></a>複製檢測金鑰

檢測金鑰會識別您想要關聯您的遙測資料的資源。 您必須複製到將檢測金鑰新增至您的應用程式程式碼。

![按一下，然後複製檢測金鑰](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>在應用程式中安裝 SDK

在應用程式中安裝 Application Insights SDK 核心。 此步驟高度仰賴於應用程式的類型。

使用檢測金鑰來設定[您在應用程式中安裝的 SDK][start]。

SDK 包含傳送遙測資料，而不必撰寫任何額外的程式碼的標準模組。 若要更詳細追蹤使用者動作或診斷問題，請[使用 API][api] 來傳送您自己的遙測。

## <a name="creating-a-resource-automatically"></a>自動建立資源
您可以撰寫 [PowerShell 指令碼](../../azure-monitor/app/powershell.md) 來自動建立資源。

## <a name="next-steps"></a>後續步驟
* [診斷搜尋](../../azure-monitor/app/diagnostic-search.md)
* [探索度量](../../azure-monitor/app/metrics-explorer.md)
* [撰寫分析查詢](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md