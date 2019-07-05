---
title: Azure Stream Analytics 功能比較
description: 本文比較 Azure Stream Analytics 雲端和 Azure 入口網站、 Visual Studio 和 Visual Studio Code 中的 IoT Edge 作業支援的功能。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 22d7ef90ee0cf4d09467516b7bb0664327b7dabe
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509777"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Azure Stream Analytics 功能比較

使用 Azure Stream Analytics，您可以建立串流解決方案在雲端中和使用 IoT edge [Azure 入口網站](stream-analytics-quick-create-portal.md)， [Visual Studio](stream-analytics-quick-create-vs.md)，並[Visual Studio Code](quick-create-vs-code.md)。 這篇文章中的表格會顯示這兩種作業類型的每個平台所支援的功能。

## <a name="cloud-job-features"></a>工作的雲端功能


|功能  |入口網站  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|跨平台     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|指令碼撰寫     |是         |是         |是         |
|指令碼的 Intellisense     |語法醒目提示         |語法醒目提示</br>程式碼完成功能</br>錯誤標記         |語法醒目提示</br>程式碼完成功能</br>錯誤標記         |
|定義輸入、 輸出和作業組態     |是         |是         |是         |
|Blob 輸出資料分割     |是         |是         |是         |
|Power BI 做為輸出     |是         |是         |否         |
|SQL database 的參考資料     |是         |是         |是         |
|自訂訊息屬性     |是         |否         |否         |
|共用跨多個查詢的輸入和輸出     |否         |yes         |是         |
|JavaScript UDF 和 UDA     |是         |是         |只有 Windows         |
|Machine Learning 的圖說文字     |可以，但查詢不能測試        |是，但無法在本機進行測試         |否         |
|相容性層級     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|內建的 ML 為基礎的異常偵測函式     |是         |是         |是         |
|內建的開放式地理空間函式     |是         |是         |是         |
|使用範例檔案來測試查詢     |是         |是         |是         |
|即時資料本機測試     |否         |是         |否         |
|列出工作和檢視 job 實體     |是         |是         |是         |
|匯出至本機專案的工作     |否         |yes         |是         |
|提交、 啟動及停止作業     |是         |是         |是         |
|原始檔控制     |否         |yes         |是         |
|CI/CD 支援     |部分         |是         |是         |
|檢視作業計量和圖表     |是         |是         |在入口網站中開啟         |
|檢視作業執行階段錯誤     |是         |是         |否         |
|診斷記錄     |是         |否         |否         |


## <a name="iot-edge-job-features"></a>IoT Edge 作業功能

|功能  |入口網站  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|工作撰寫     |是         |是         |否         |
|原始檔控制     |否         |是         |否         |
|匯出至本機專案的工作     |否         |是         |否         |
|使用範例檔案來測試查詢     |是         |是         |否         |
|共用跨多個查詢的輸入和輸出     |否         |是         |否         |
|C# UDF     |否         |是         |否         |
|提交、 啟動及停止作業     |是         |是         |否         |
|列出工作和檢視 job 實體     |是         |是         |否         |
|檢視作業計量和圖表     |是         |部分         |否         |
|檢視作業執行階段錯誤     |是         |部分         |否         |
|CI/CD 支援     |否         |否         |否         |


## <a name="next-steps"></a>後續步驟

* [Azure IoT Edge 串流分析](stream-analytics-edge.md)
* [教學課程：寫入C#Azure Stream Analytics IoT Edge 作業 （預覽） 的使用者定義函式](stream-analytics-edge-csharp-udf.md)
* [開發使用 Visual Studio 工具的 Stream Analytics IoT Edge 作業](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [使用 Visual Studio 檢視 Azure 串流分析工作](stream-analytics-vs-tools.md)
* [探索 Azure Stream Analytics 與 Visual Studio Code （預覽）](vscode-explore-jobs.md)


