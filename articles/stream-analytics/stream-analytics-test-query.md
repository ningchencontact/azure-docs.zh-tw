---
title: 測試 Azure 串流分析中的查詢
description: 本文說明如何使用串流分析作業中的範例資料檔案來測試查詢。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: fb7d936dfdc543c208ce98c588f5ad83704ff5dc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="test-azure-stream-analytics-queries-in-the-azure-portal"></a>在 Azure 入口網站測試串流分析查詢

透過 Azure 串流分析，您可以在 Azure 入口網站中測試查詢，且無須啟動或停止作業。

## <a name="test-the-input"></a>測試輸入

1. 若要測試樣本輸入資料，以滑鼠右鍵按一下任何輸入，然後選取 [從檔案上傳樣本資料]。 目前您只能上傳 JSON 格式的資料。 如果您的資料採用不同格式 (例如 CSV)，請於上傳前先將其轉換為 JSON。 您可以使用任何開放原始碼轉換工具 (例如 [CSV 至 JSON 轉換器](http://www.convertcsv.com/csv-to-json.htm))，將資料轉換為 JSON。

    ![串流分析查詢編輯器的測試查詢](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

2. 上傳完成後，按一下 [測試] 以根據您提供的樣本資料測試此查詢。

    ![串流分析查詢編輯器的樣本資料](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

查詢的輸出會顯示在瀏覽器中，還有 [下載結果] 連結可讓您儲存測試輸出以供稍後使用。 您現在可以輕鬆地反覆修改查詢並反覆測試查詢來查看輸出的變更狀況。

![串流分析查詢編輯器樣本輸出](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

透過在查詢中使用多個輸出，您可以分別查看兩個輸出的結果，並輕鬆地在兩者之間切換。

當您滿意顯示在瀏覽器中的結果後，您可以儲存查詢、啟動作業，以及讓其在沒有錯誤的情況下處理事件。

## <a name="get-help"></a>取得說明

如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
