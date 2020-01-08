---
title: Azure 串流分析功能比較
description: 本文會比較 Azure 串流分析雲端所支援的功能，以及 Azure 入口網站、Visual Studio 和 Visual Studio Code 中的 IoT Edge 作業。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 8cdba27ac949584e1fa96e3f7b0874f4fc0d4212
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443650"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Azure 串流分析功能比較

透過 Azure 串流分析，您可以使用[Azure 入口網站](stream-analytics-quick-create-portal.md)、 [Visual Studio](stream-analytics-quick-create-vs.md)和[Visual Studio Code](quick-create-vs-code.md)，在雲端和 IoT Edge 建立串流解決方案。 本文中的表格顯示兩種工作類型的每個平臺都支援哪些功能。

## <a name="cloud-job-features"></a>雲端作業功能


|功能  |入口網站  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|跨平台     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|撰寫腳本     |是         |是         |是         |
|腳本 Intellisense     |語法醒目提示         |語法醒目提示</br>程式碼完成</br>錯誤標記         |語法醒目提示</br>程式碼完成</br>錯誤標記         |
|定義所有類型的輸入、輸出和作業設定     |是         |是         |是         |
|原始檔控制     |否         |是         |是         |
|CI/CD 支援     |部分         |是         |是         |
|跨多個查詢共用輸入和輸出     |否         |是         |是         |
|使用範例檔案進行查詢測試     |是         |是        |是         |
|即時資料本機測試     |否         |是       |是      |
|列出作業和查看作業實體     |是         |是        |是         |
|將作業匯出至本機專案     |否         |是         |是         |
|提交、啟動和停止作業     |是         |是         |是         |
|查看作業計量和圖表     |是         |是         |在入口網站中開啟         |
|查看作業執行時間錯誤     |是         |是         |否         |
|診斷記錄     |是         |否         |否         |
|自訂訊息屬性     |是         |是         |否       |
|C#自訂程式碼函數和還原序列化程式|唯讀模式|是|否|
|JavaScript UDF 和 UDA     |是         |是         |僅限 Windows         |
|機器學習服務     |是，但無法測試查詢        |是         |否         |
|機器學習 Studio     |是，但無法測試查詢        |是 |否         |
|相容性層級     |1.0</br>1.1</br>1.2 （預設值）         |1.0</br>1.1</br>1.2 （預設值）           |1.0</br>1.1</br>1.2 （預設值）           |
|內建以 ML 為基礎的異常偵測函式     |是         |是         |是         |
|內建地理空間函數     |是         |是         |是         |



## <a name="iot-edge-job-features"></a>IoT Edge 作業功能

|功能  |入口網站  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|作業撰寫     |是         |是         |否         |
|原始檔控制     |否         |是         |否         |
|將作業匯出至本機專案     |否         |是         |否         |
|使用範例檔案進行查詢測試     |是         |是         |否         |
|跨多個查詢共用輸入和輸出     |否         |是         |否         |
|C#UDF     |否         |是         |否         |
|提交工作     |是         |是         |否         |
|列出作業和查看作業實體     |是         |是         |否         |
|查看作業計量和圖表     |是         |部分         |否         |
|查看作業執行時間錯誤     |是         |部分         |否         |
|CI/CD 支援     |否         |否         |否         |


## <a name="next-steps"></a>後續步驟

* [Azure IoT Edge 串流分析](stream-analytics-edge.md)
* [教學課程：為C# Azure 串流分析 IoT Edge 作業（預覽）撰寫使用者定義的函數](stream-analytics-edge-csharp-udf.md)
* [使用 Visual Studio 工具開發串流分析 IoT Edge 作業](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [使用 Visual Studio 檢視 Azure 串流分析工作](stream-analytics-vs-tools.md)
* [使用 Visual Studio Code 探索 Azure 串流分析（預覽）](visual-studio-code-explore-jobs.md)


