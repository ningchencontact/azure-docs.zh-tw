---
title: 使用樣本資料測試 Azure 串流分析作業
description: 本文說明如何使用 Azure 入口網站測試 Azure 串流分析作業、輸入取樣，以及上傳樣本資料。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2019
ms.custom: seodec18
ms.openlocfilehash: 1a9caf83c6f4cd4ed15290afc872043c11234552
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508712"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>使用樣本資料測試串流分析查詢

藉由使用 Azure Stream Analytics，您可以從輸入資料取樣，或上傳至 Azure 入口網站中測試查詢，而不需要啟動或停止工作的範例資料。

## <a name="upload-or-sample-data-from-a-live-source-to-test-the-query"></a>上傳或從即時來源來測試查詢的範例資料

1. 登入 Azure 入口網站。 

2. 找到現有的串流分析作業並加以選取。

3. 在 [串流分析作業] 頁面上，[工作拓撲]  標題下方，選取 [查詢]  開啟 [查詢] 編輯器視窗。 

4. 若要測試您的查詢您可以接著是取樣的即時輸入或從檔案上的傳的資料。 必須以 JSON、CSV 或 AVRO 將資料序列化。 範例輸入必須以 UTF-8 編碼而且不壓縮。 在入口網站上測試 CSV 輸入時僅支援逗號 (,) 分隔符號。

    1. 使用即時輸入： 以滑鼠右鍵按一下任何輸入。 然後選取**範例資料從輸入**。 在下一個畫面中，您可以設定此範例的持續時間。 取樣事件從即時來源將會擷取最多 1000 個事件或 1 MB （視何者先），讓取樣的資料不一定代表指定完整的時間間隔。

    1. 使用檔案： 以滑鼠右鍵按一下任何輸入。 然後選取 [從檔案上傳樣本資料]  。 

    ![串流分析查詢編輯器的測試查詢](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. 取樣或上傳完成後，請選取**測試**來根據您所提供的範例資料測試此查詢。

    ![串流分析查詢編輯器的樣本資料](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. 如果您需要該測試輸出供稍後使用，您的查詢輸出會顯示在瀏覽器中，並提供連結，以便下載結果。 

7. 反覆修改查詢，然後再次測試，以查看輸出的變更狀況。

   ![串流分析查詢編輯器樣本輸出](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   當您在查詢中使用多個輸出時，結果會分別顯示在不同的索引標籤中，您可以輕鬆地切換索引標籤。

8. 驗證顯示在瀏覽器中的結果後，請按一下 [儲存]  以儲存查詢。 然後按一下 [啟動]  以啟動作業，並讓其處理連入事件。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
