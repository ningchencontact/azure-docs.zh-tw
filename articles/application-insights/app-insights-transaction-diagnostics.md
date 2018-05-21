---
title: Azure Application Insights 交易診斷 | Microsoft Docs
description: Application Insights 端對端交易診斷
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2018
ms.author: mbullwin;sdash
ms.openlocfilehash: 7a4e4f74c02358fc117e0a66977ee3f0aef5b1dd
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2018
---
# <a name="unified-cross-component-transaction-diagnostics"></a>整合跨元件交易診斷

這項體驗目前為預覽狀態，會取代現有的伺服器端要求、相依性和例外狀況的診斷刀鋒視窗。

本預覽導入了全新的整合診斷體驗，自動將所有 Application Insights 監視元件的伺服器端遙測資料相互關聯為單一檢視。 多個資源是否各自擁有檢測金鑰並不重要。 Application Insights 能偵測基礎關聯性，讓您輕鬆診斷出導致交易緩慢或失敗的應用程式元件、相依性或例外狀況。

## <a name="what-is-a-component"></a>什麼是元件？

元件是分散式/微服務應用程式中可獨立部署的組件。 開發人員和作業小組能在程式碼層級檢視或存取這些應用程式元件所產生的遙測資料。

* 元件不同於 SQL、EventHub 等小組/組織可能無法存取的「可觀察」外部相依性 (程式碼或遙測資料)。
* 元件能在任何數量的伺服器/角色/容器執行個體上執行。
* 元件可以是不同的 Application Insights 檢測金鑰 (即使訂用帳戶不同)，也可以是回報給單一 Application Insights 檢測金鑰的不同角色。 新體驗能顯示所有元件的詳細資料，不論設定方式為何。

> [!NOTE]
> * **遺失相關項目連結？** 與伺服器端要求、相依性和例外狀況相關的所有遙測都位於左側的[頂端](#cross-component-transaction-chart)和[底部](#all-telemetry-related-to-the-selected-component-operation)區段中。 
> * [頂端](#cross-component-transaction-chart)區段會使所有元件的交易相互關聯。 為獲得最佳結果，請確認所有元件均經過最新 Application Insights 穩定版 SDK 檢測。 如果您有不同的 Application Insights 資源，請確認您有檢視其遙測資料的適當權限。
> * 左側的[底部](#all-telemetry-related-to-the-selected-component-operation)區段會顯示**所有**遙測，包括與所選元件要求相關的追蹤和事件。

## <a name="enable-transaction-diagnostics-experience"></a>啟用交易診斷體驗
從[預覽清單](app-insights-previews.md)啟用「整合詳細資料：E2E 交易診斷」

![啟用預覽](media/app-insights-e2eTxn-diagnostics/previews.png)

本預覽版本目前僅適用於伺服器端要求、相依性及例外狀況。 您可以從**搜尋結果**、**效能**或**失敗**的分級體驗存取該項體驗。 本預覽版本會取代對應的傳統詳細資料刀鋒視窗。

![效能範例](media/app-insights-e2eTxn-diagnostics/performanceSamplesClickThrough.png)

## <a name="transaction-diagnostics-experience"></a>交易診斷體驗
該檢視有三大重要組件：跨元件交易圖表、特定元件作業所有遙測資料的時間序列清單、左側任何選定的遙測項目的詳細資料窗格。

![重要組件](media/app-insights-e2eTxn-diagnostics/3partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>跨元件交易圖表

該圖表提供時間軸，以水平橫條呈現要求的持續時間和元件之間的相依性。 所有收集到的例外狀況也會標示在時間軸上。

* 這個圖表的上方資料列代表進入點，也就是此交易中呼叫之第一個元件的連入要求。 持續時間是交易完成所需的總時間。
* 所有外部相依性的呼叫都是不可折疊的簡單資料列，並以圖示代表相依性類別。
* 其他元件的呼叫是可折疊的資料列。 每個資料列都會對應到在元件叫用的特定作業。
* 按照預設，您一開始選取的要求、相依性或例外狀況會顯示在圖表上。
* 選取任何資料列即可[在右側查看詳細資料](#details-of-the-selected-telemetry)。 

> [!NOTE]
其他元件的呼叫有兩個資料列：一個資料列代表來自呼叫端元件的輸出呼叫 (相依性)，另一個資料列則對應至被呼叫元件的輸入要求。 主要圖示和樣式分明的持續時間橫條有助於區分兩者。

## <a name="all-telemetry-related-to-the-selected-component-operation"></a>與所選元件作業相關的所有遙測

在跨元件交易圖表中選取的任何資料列，都與特定元件叫用的作業有所關聯。 此處選取的元件作業會反映在底部區段的標題中。 這個區段開啟後會顯示一般時間序列，顯示與該特定作業相關的所有遙測資料。 您可以選取清單中的任何遙測項目，並在[右側查看對應的詳細資料](#details-of-the-selected-telemetry)。

![所有遙測資料的時間序列](media/app-insights-e2eTxn-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>所選遙測的詳細資料

這個窗格會根據您在左側任一區段中選取的項目，顯示其詳細資料。 [全部顯示] 能列出所有收集到的標準屬性。 任何自訂屬性都會分別列在標準集合下方。 按一下 [開啟分析工具追蹤] 或 [開啟偵錯快照集]，即可在對應的詳細資料窗格中查看程式碼層級的診斷資料。

![例外狀況詳細資料](media/app-insights-e2eTxn-diagnostics/exceptiondetail.png)

## <a name="profiler-and-snapshot-debugger"></a>分析工具和快照集偵錯工具

[Application Insights 分析工具](app-insights-profiler.md)或[快照集偵錯工具](app-insights-snapshot-debugger.md)能協助您從程式碼層級診斷效能和失敗問題。 透過這項體驗，只要按一下按鈕，您就可以查看任何元件的分析工具追蹤或快照集。

如果無法使用分析工具，請連絡 **serviceprofilerhelp@microsoft.com**

如果無法使用快照偵錯工具，請連絡 **snapshothelp@microsoft.com**

![偵錯工具整合](media/app-insights-e2eTxn-diagnostics/debugSnapshot.png)

## <a name="faq"></a>常見問題集

*我在圖表中看到一個元件，其他元件只顯示為外部相依性，無法確切得知這些元件的內部狀況。*

可能的原因：

* 您是否曾使用 Application Insights 檢測其他元件？
* 使用的是否為最新的穩定版 Application Insights SDK？
* 如果這些元件是個別的 Application Insights 資源，您是否擁有存取其遙測資料的必要權限？

如果您擁有存取權限，而且元件是由最新的 Application Insights SDK 進行檢測，請透過右上角的意見反應管道告訴我們。

*我只有外部相依性。這個預覽版本跟我有關係嗎？*

是。 新體驗能將所有相關的伺服器端遙測資料整合為單一檢視。 未來它會取代舊有的詳細資料刀鋒視窗，所以請逕行試用並給予我們意見反應。 以下是單一元件交易的外觀：

![單一元件體驗](media/app-insights-e2eTxn-diagnostics/singleComponent.png)

*我看到重複的相依性資料列。這是預期行為嗎？*

現階段我們會分別顯示輸出相依性呼叫和輸入要求。 這兩項呼叫看起來通常會一樣，唯一的差異在於持續時間值會因網路來回時間而有所不同。 主要圖示和樣式分明的持續時間橫條有助於區分兩者。 用這樣的方法呈現資料是否會混淆？ 請提供意見給我們！

*不同的元件執行個體之間有時鐘誤差嗎？*

交易圖表中的時間軸已調整過時鐘誤差。 您可以在詳細資料中或使用 Analytics 查看確切的時間戳記。

*為什麼新體驗遺漏大部分的相關項目查詢？*

原先的設計就是如此。 左側 (上方和下方區段) 已有全部元件的所有相關項目。 新體驗的左側未涵蓋兩項相關項目：此事件前後五分鐘的所有遙測資料和使用者時間軸。

*新體驗為什麼沒有舊有刀鋒視窗中我喜歡的功能？*

請不吝提供意見！ 新體驗上市後將會取代舊有的檢視，因此我們希望能在上市前解決您的疑慮。 目前，您可以停用預覽來返回舊有的刀鋒視窗。

## <a name="known-issues"></a>已知問題

* 應用程式對應的失敗範例會連至舊版的詳細資料刀鋒視窗。
* 搜尋結果中以 autocluster 為基礎的深入解析，會連至舊版的詳細資料刀鋒視窗。
* 新體驗不提供工作項目整合。
