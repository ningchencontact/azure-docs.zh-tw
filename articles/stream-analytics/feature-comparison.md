---
title: Azure 串流分析功能比較
description: 本文會比較 Azure 串流分析雲端所支援的功能，以及 Azure 入口網站、Visual Studio 和 Visual Studio Code 中的 IoT Edge 作業。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 4eb19a5b344cc5bda5ecad724daaddf9b0000d7e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580912"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Azure 串流分析功能比較

透過 Azure 串流分析，您可以使用[Azure 入口網站](stream-analytics-quick-create-portal.md)、 [Visual Studio](stream-analytics-quick-create-vs.md)和[Visual Studio Code](quick-create-vs-code.md)，在雲端和 IoT Edge 建立串流解決方案。 本文中的表格顯示兩種工作類型的每個平臺都支援哪些功能。

## <a name="cloud-job-features"></a>雲端作業功能


|功能  |入口網站  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|跨平臺     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|撰寫腳本     |是         |是         |是         |
|腳本 Intellisense     |語法反白顯示         |語法反白顯示</br>程式碼完成</br>錯誤標記         |語法反白顯示</br>程式碼完成</br>錯誤標記         |
|定義輸入、輸出和作業設定     |是         |是         |是         |
|Blob 輸出分割     |是         |是         |是         |
|Power BI 作為輸出     |是         |是         |否         |
|SQL database 參考資料     |是         |是         |是         |
|自訂訊息屬性     |是         |否         |否         |
|跨多個查詢共用輸入和輸出     |否         |是         |是         |
|JavaScript UDF 和 UDA     |是         |是         |僅限 Windows         |
|Machine Learning 標注     |是，但無法測試查詢        |是，但無法在本機測試         |否         |
|相容性層級     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|內建以 ML 為基礎的異常偵測函式     |是         |是         |是         |
|內建地理空間函數     |是         |是         |是         |
|使用範例檔案進行查詢測試     |是         |是         |是         |
|即時資料本機測試     |否         |是         |否         |
|列出作業和查看作業實體     |是         |是         |是         |
|將作業匯出至本機專案     |否         |是         |是         |
|提交、啟動和停止作業     |是         |是         |是         |
|原始檔控制     |否         |是         |是         |
|CI/CD 支援     |部分         |是         |是         |
|查看作業計量和圖表     |是         |是         |在入口網站中開啟         |
|查看作業執行時間錯誤     |是         |是         |否         |
|診斷記錄     |是         |否         |否         |


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
* [使用 Visual Studio Code 探索 Azure 串流分析（預覽）](vscode-explore-jobs.md)


