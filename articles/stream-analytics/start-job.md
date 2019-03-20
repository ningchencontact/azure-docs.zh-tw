---
title: 如何啟動 Azure Stream Analytics 作業
description: 本文說明如何啟動 Stream Analytics 作業。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: fb1d724907c09e2eb77930f5a235336ca8cd3a25
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886842"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>如何啟動 Azure Stream Analytics 作業

您可以開始使用 Azure 入口網站、 Visual Studio 和 PowerShell Azure Stream Analytics 工作。 當您啟動作業時，您會選取工作，以開始建立輸出的時間。 Azure 入口網站、 Visual Studio 中，與 PowerShell 各有不同的方法，來設定開始時間。 以下說明這些方法。

## <a name="azure-portal"></a>Azure 入口網站

瀏覽至您的作業，在 Azure 入口網站，然後選取**啟動**概觀 頁面上。 選取 **作業輸出開始時間**，然後選取**開始**。

有三個選項可**作業輸出開始時間**:*現在*，*自訂*，以及*前次停止時間*。 選取*現在*在目前的時間啟動工作。 選取*自訂*可讓您設定自訂的時間在過去或未來作業開始。 若要繼續已停止的工作，而不會遺失資料，請選擇*前次停止時間*。

## <a name="visual-studio"></a>Visual Studio

在 [工作] 檢視中，選取綠色箭頭按鈕，以啟動作業。 設定**作業輸出啟動模式**，然後選取**開始**。 作業狀態會變成**執行**。

有三個選項可**作業輸出啟動模式**:*JobStartTime*， *CustomTime*，以及*LastOutputEventTime*。 如果這個屬性不存在，預設值是*JobStartTime*。

*JobStartTime*讓輸出事件的開始點，串流作業開始時相同。

*CustomTime*在自訂時間中指定啟動輸出*OutputStartTime*參數。

*LastOutputEventTime*讓起點的輸出事件資料流的最後一個事件相同輸出的時間。

## <a name="powershell"></a>PowerShell

若要開始使用 PowerShell 工作中使用下列 cmdlet:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

有三個選項可**OutputStartMode**:*JobStartTime*， *CustomTime*，以及*LastOutputEventTime*。 如果這個屬性不存在，預設值是*JobStartTime*。

*JobStartTime*讓輸出事件的開始點，串流作業開始時相同。

*CustomTime*在自訂時間中指定啟動輸出*OutputStartTime*參數。

*LastOutputEventTime*讓起點的輸出事件資料流的最後一個事件相同輸出的時間。

如需詳細資訊`Start-AzStreamAnalyitcsJob`cmdlet，檢視[開始 AzStreamAnalyticsJob 參考](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [快速入門：建立使用 Azure PowerShell 的 Stream Analytics 作業](stream-analytics-quick-create-powershell.md)
* [快速入門：使用適用於 Visual Studio 的 Azure 串流分析工具建立串流分析作業](stream-analytics-quick-create-vs.md)
