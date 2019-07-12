---
title: 處理來自事件中樞 Azure 中使用 Stream Analytics 資料 |Microsoft Docs
description: 這篇文章會示範如何處理從 Azure 事件中樞使用 Azure Stream Analytics 作業的資料。
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: f179687b0983e145244e228a3d3b06b4eabead48
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723411"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>處理來自事件中樞使用 Azure Stream Analytics 的資料
Azure Stream Analytics 服務可讓內嵌、 處理及分析來自 Azure 事件中樞、 串流資料變得更加容易啟用磁碟機即時動作的強大深入解析。 這項整合可讓您快速建立 最忙碌路徑分析管線。 您可以使用 Azure 入口網站來將內送的資料視覺化，並撰寫 Stream Analytics 查詢。 準備您的查詢之後，您可以將它移到生產環境只需點選幾下。 

## <a name="key-benefits"></a>主要權益
以下是 Azure 事件中樞和 Azure Stream Analytics 整合的主要優點： 
- **預覽資料**– 您可以預覽從 Azure 入口網站中的事件中樞傳入的資料。
- **測試您的查詢**– 準備轉換查詢，並直接在 Azure 入口網站中進行測試。 如需查詢語言語法，請參閱[Stream Analytics 查詢語言](/stream-analytics-query/built-in-functions-azure-stream-analytics)文件。
- **部署到生產環境的查詢**– 您可以透過建立和啟動 Azure Stream Analytics 作業到生產環境部署查詢。

## <a name="end-to-end-flow"></a>端對端流程

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 瀏覽至您**事件中樞命名空間**，然後瀏覽**事件中樞**，其中包含內送資料。 
1. 選取 [**處理序資料**事件中樞] 頁面上。  

    ![處理資料的圖格](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. 選取 **瀏覽**上**啟用事件的即時見解**圖格。 

    ![選取串流分析](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. 您可以看到查詢 頁面上的下列欄位已設定的值：
    1. 您**事件中樞**做為輸入的查詢。
    1. 範例**SQL 查詢**搭配 SELECT 陳述式。 
    1. **輸出**別名以指向您的測試結果。 

        ![查詢編輯器](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  當您第一次使用這項功能時，此頁面會要求您建立取用者群組和事件中樞的原則，來預覽內送資料的權限。
1. 選取  **Create**中**輸入預覽**窗格，如上圖所示。 
1. 您會立刻看到最新的內送資料，此索引標籤中的快照集。
    - 在您的資料中的序列化型別會自動偵測到 (JSON/CSV)。 您可以手動變更它也以 JSON/CSV/AVRO。
    - 您可以預覽中的資料表格式或未經處理格式的內送資料。 
    - 如果您顯示的資料不是最新項目，選取**重新整理**以查看最新的事件。 

        以下是範例中的資料**表格格式**: ![以資料表格式的結果](./media/process-data-azure-stream-analytics/snapshot-results.png)

        以下是範例中的資料**未經處理格式**: 

        ![未經處理格式的結果](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. 選取 [**測試查詢**若要查看測試結果中之查詢的快照集**測試結果**] 索引標籤。您也可以下載結果。

    ![測試查詢結果](./media/process-data-azure-stream-analytics/test-results.png)
1. 撰寫您自己的查詢來轉換資料。 請參閱[Stream Analytics 查詢語言參考](/stream-analytics-query/stream-analytics-query-language-reference)。
1. 一旦測試過的查詢，而且您想要在中移至生產環境，選取**部署查詢**。 若要部署查詢，建立 Azure Stream Analytics 作業，您可以在此將輸出設定為您的作業，並啟動作業。 若要建立的 Stream Analytics 作業，指定作業名稱，然後選取**建立**。

      ![建立 Azure 串流分析作業](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  我們建議您建立取用者群組，以及為每個新的 Azure Stream Analytics 工作從 [事件中樞] 頁面上所建立的原則。 取用者群組允許只有五個的並行讀取器，因此每個工作提供專用取用者群組可避免超過該限制可能會發生任何錯誤。 專用的原則可讓您旋轉您的金鑰，或撤銷權限，而不會影響其他資源。 
1. 您的查詢相當於您的測試，而輸入是事件中樞，現在已建立您的 Stream Analytics 作業。 

9.  若要完成管線，將**輸出**查詢，然後選取**開始**來啟動作業。

    > [!NOTE]
    > 開始之前的作業，別忘了您在 Azure Stream Analytics 中建立的輸出名稱來取代輸出別名。

      ![設定輸出和開始工作](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>已知限制
測試您的查詢，需要大約 6 秒鐘的時間載入測試的結果。 我們正努力提升的效能測試。 不過，部署在生產環境中，Azure Stream Analytics 會有次秒的延遲。

## <a name="streaming-units"></a>串流單位
Azure Stream Analytics 作業預設值為三個串流單元 (Su)。 若要調整此設定，請選取**擴展**上的左側功能表中**Stream Analytics 作業**在 Azure 入口網站中的頁面。 若要深入了解串流處理單位，請參閱[了解及調整串流單位](../stream-analytics/stream-analytics-streaming-unit-consumption.md)。

![調整串流單位](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>後續步驟
若要深入了解 Stream Analytics 查詢，請參閱[Stream Analytics 查詢語言](/stream-analytics-query/built-in-functions-azure-stream-analytics)