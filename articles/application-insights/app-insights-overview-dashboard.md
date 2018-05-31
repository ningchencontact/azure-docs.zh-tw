---
title: Azure Application Insights 概觀儀表板 | Microsoft Docs
description: 使用 Azure Application Insights 和概觀儀表板的功能監控應用程式。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mbullwin
ms.openlocfilehash: 0be54c47965c6a27c3506fd37a7bf67e4b2b7924
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356081"
---
# <a name="application-insights-overview-dashboard-preview"></a>Azure Application Insights 概觀儀表板 (預覽)

Application Insights 一直以來都在提供摘要概覽窗格，以便一眼便能快速評量應用程式的健康情況和效能。 全新的預覽概觀儀表板可提供更快、更有彈性的體驗。

## <a name="how-do-i-test-out-the-new-experience"></a>如何嘗試新的體驗？

 在 Application Insights 的 [概觀] 底下，選取 [請在變成預設體驗之前嘗試新的概觀]。

![概觀預覽](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-01.png)

如此便會啟動新的預設概觀儀表板：

![概觀預覽窗格](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-02.png)

## <a name="better-performance"></a>效能更佳

已將時間範圍選取功能簡化為輕鬆的單鍵介面。

![時間範圍](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-03.png)

整體效能已大幅提升。 預設的每個動態更新 KPI 磚皆連結至對應的 Application Insights 功能。 例如，選取失敗的要求將會啟動 [失敗] 窗格：

![失敗](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>應用程式儀表板

應用程式儀表板會使用 Azure 中現有的儀表板技術，提供可完全自訂的單一窗格，讓您檢視應用程式的健康情況和效能。

若要使用預設儀表板，請選取左上角的 [應用程式儀表板]。

![儀表板檢視](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-05.png)

如果這是您第一次使用儀表板，便會啟動預設檢視：

![儀表板檢視](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-06.png)

如果您喜歡預設檢視，即可保持原狀繼續使用，但也可以在儀錶板上新增和刪除項目，盡可能滿足您小組的需求。

> [!NOTE]
> 具有 Application Insights 資源存取權的所有使用者，都會共用相同的應用程式儀表板體驗。 一位使用者所做的變更將會修改所有使用者的檢視。

若要回到概觀的畫面，只需選取：

![[概觀] 按鈕](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-07.png)

## <a name="next-steps"></a>後續步驟

- [漏斗圖](usage-funnels.md)
- [保留](app-insights-usage-retention.md)
- [使用者流程](app-insights-usage-flows.md)
- [儀表板](app-insights-dashboards.md)
