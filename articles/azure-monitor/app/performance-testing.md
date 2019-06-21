---
title: 效能和負載測試使用 Azure Application Insights |Microsoft Docs
description: 設定效能和負載測試使用 Azure Application Insights
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 55d743e32f6db0828317d3764a97bcb35b104dad
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304921"
---
# <a name="performance-testing"></a>效能測試

> [!NOTE]
> 雲端式負載測試服務已被取代。 可以找到有關淘汰、 服務可用性，以及替代服務的詳細資訊[此處](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops)。

Application Insights 可讓您產生負載測試，為您的網站。 像是[可用性測試](monitor-web-app-availability.md)，您可以將其中一個基本的要求傳送或[多步驟要求](availability-multistep.md)從 Azure 測試在世界各地的代理程式。 效能測試可讓您模擬最多 60 分鐘的時間最多 20,000 個同時連接的使用者。

## <a name="create-an-application-insights-resource"></a>建立 Application Insights 資源

若要建立效能測試，您首先要建立 Application Insights 資源。 如果您已經建立資源就會繼續下一節。

從 Azure 入口網站中，選取**建立資源** > **開發人員工具** > **Application Insights**並建立 Application Insights資源。

## <a name="configure-performance-testing"></a>設定效能測試

如果這是您建立效能測試選取的第一次**設定的組織**，然後選擇 Azure DevOps 的組織您的效能測試的來源。

底下**設定**，請移至**效能測試**然後按一下**新增**建立測試。

![Fill at least the URL of your website](./media/performance-testing/new-performance-test.png)

若要建立基本的效能測試時，選取 測試類型**手動測試**並填妥所需的設定，為您的測試。

|設定| 最大值
|----------|------------|
| 使用者負載 | 20,000 |
| 持續時間 （分鐘）  | 60 |  

建立您的測試之後，按一下**執行測試**。

測試完成之後，您會看到類似下面的結果的結果：

![測試結果](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>設定 Visual Studio web 測試

Application Insights 的進階的效能測試的功能建置在 Visual Studio 效能和負載測試專案。

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>後續步驟

* [多重步驟 web 測試](availability-multistep.md)
* [Url ping 測試](monitor-web-app-availability.md)