---
title: 如何啟動 Azure Stream Analytics 作業
description: 本文說明如何啟動 Stream Analytics 作業。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 9bc3e4132919e5fc5baadc78841e66efd3c34bcd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61362260"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>如何啟動 Azure Stream Analytics 作業

您可以開始使用 Azure 入口網站、 Visual Studio 和 PowerShell Azure Stream Analytics 工作。 當您啟動作業時，您會選取工作，以開始建立輸出的時間。 Azure 入口網站、 Visual Studio 中，與 PowerShell 各有不同的方法，來設定開始時間。 以下說明這些方法。

## <a name="start-options"></a>起始選項
若要啟動的工作有三個下列選項。 請注意下面所述的所有時間都是在指定的項目[TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)。 如果未指定 TIMESTAMP BY，則會使用抵達時間。
* **現在**:可讓作業開始時，串流處理相同的起始點的輸出事件。 如果使用時態性運算子 （例如時間範圍內，延遲或聯結），Azure Stream Analytics 會自動查看上一步中的輸入來源的資料。 比方說，如果 「 現在 」 開始工作，而且您的查詢會使用 5 分鐘輪轉視窗，則 Azure Stream Analytics 將會搜尋輸入中前 5 分鐘的資料。
第一個可能的輸出事件必須等於或大於目前時間的時間戳記，ASA 可保證已負責所有的輸入的事件，以邏輯方式可能會導致輸出。 比方說，就會不產生任何部分的視窗型彙總。 它一律是完整的彙的總值。

* **自訂**：您可以選擇輸出的起點。 類似於**現在**選項時，Azure Stream Analytics 會自動讀取此時間之前的資料，如果使用時態性運算子 

* **前次停止時間**。 當作業先前已啟動，但已手動停止或失敗時使用此選項。 選擇此選項時 Azure Stream Analytics 會使用上次輸出時間重新啟動工作，因此不會遺失資料。 同樣地以先前的選項，Azure Stream Analytics 會自動讀取此時間之前的資料如果時態性運算子用。 由於數個輸入分割區可能有不同的時間，會使用最早停止時間的所有資料分割，因此某些重複項目可能會出現在輸出中。 詳細資訊完全-處理一次是在網頁上可用[事件的傳遞保證](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)。


## <a name="azure-portal"></a>Azure 入口網站

瀏覽至您的作業，在 Azure 入口網站，然後選取**啟動**概觀 頁面上。 選取 **作業輸出開始時間**，然後選取**開始**。

選擇其中一個選項**作業輸出開始時間**。 選項包括*現在*，*自訂*，以及先前執行的作業，如果*前次停止時間*。 如需這些選項的詳細資訊，請參閱上述內容。

## <a name="visual-studio"></a>Visual Studio

在 [工作] 檢視中，選取綠色箭頭按鈕，以啟動作業。 設定**作業輸出啟動模式**，然後選取**開始**。 作業狀態會變成**執行**。

有三個選項可**作業輸出啟動模式**:*JobStartTime*， *CustomTime*，以及*LastOutputEventTime*。 如果這個屬性不存在，預設值是*JobStartTime*。 如需這些選項的詳細資訊，請參閱上述內容。


## <a name="powershell"></a>PowerShell

若要開始使用 PowerShell 工作中使用下列 cmdlet:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

有三個選項可**OutputStartMode**:*JobStartTime*， *CustomTime*，以及*LastOutputEventTime*。 如果這個屬性不存在，預設值是*JobStartTime*。 如需這些選項的詳細資訊，請參閱上述內容。

如需詳細資訊`Start-AzStreamAnalyitcsJob`cmdlet，檢視[開始 AzStreamAnalyticsJob 參考](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [快速入門：建立使用 Azure PowerShell 的 Stream Analytics 作業](stream-analytics-quick-create-powershell.md)
* [快速入門：使用適用於 Visual Studio 的 Azure 串流分析工具建立串流分析作業](stream-analytics-quick-create-vs.md)
