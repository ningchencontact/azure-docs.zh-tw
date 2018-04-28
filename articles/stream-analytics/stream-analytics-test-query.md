---
title: 使用範例資料測試 Azure 串流分析作業 | Microsoft Docs
description: 如何在串流分析作業中測試查詢。
keywords: 測試作業、輸入取樣、上傳範例日期
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: stream-analytics
ms.workload: data-services
ms.date: 03/18/2018
ms.author: sngun
ms.openlocfilehash: c026a91fff5b8ef5774993b335f8d61877aa5d39
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="test-your-stream-analytics-query-with-sample-data"></a>使用範例資料測試串流分析查詢

您可以使用 Azure 串流分析，在入口網站中上傳範例資料並測試查詢，而不需要啟動或停止作業。

## <a name="upload-sample-data-and-test-the-query"></a>上傳範例資料和測試查詢

1. 瀏覽至其中一個現有的串流分析作業 > 按一下 [查詢] 以開啟查詢編輯器視窗。 

2. 若要使用範例輸入資料測試查詢，請以滑鼠右鍵按一下任何輸入，然後選取 [從檔案上傳範例資料]。 目前您只能上傳 JSON 格式的資料。 如果您的資料採用不同格式 (例如 CSV)，請於上傳前先將其轉換為 JSON。 您可以使用任何開放原始碼轉換工具 (例如 [CSV 至 JSON 轉換器](http://www.convertcsv.com/csv-to-json.htm))，將資料轉換為 JSON。

    ![串流分析查詢編輯器的測試查詢](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

3. 上傳完成後，按一下 [測試] 以根據您提供的樣本資料測試此查詢。

    ![串流分析查詢編輯器的樣本資料](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

4. 如果您想要儲存測試輸出以供稍後使用，顯示您查詢輸出的瀏覽器中會包含下載結果的連結。 您現在可以輕鬆地反覆修改查詢並反覆測試查詢來查看輸出的變更狀況。

   ![串流分析查詢編輯器樣本輸出](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

當您在查詢中使用多個輸出時，您可以分別查看每個輸出的結果，並輕鬆地在兩者之間切換。 當您確認顯示在瀏覽器中的結果後，可以儲存查詢、啟動作業，以及讓其在沒有錯誤的情況下處理事件。
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
