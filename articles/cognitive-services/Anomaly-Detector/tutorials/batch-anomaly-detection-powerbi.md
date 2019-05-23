---
title: 以視覺化方式檢視使用批次偵測及 Power BI 的異常
titlesuffix: Azure Cognitive Services
description: 使用異常偵測器 API 和 Power BI 來視覺化整個時間序列資料的異常狀況。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 04/30/2019
ms.author: aahi
ms.openlocfilehash: de353c946da46b2bbe4e755e21e355f6bd18725b
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827147"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>教學課程：以視覺化方式檢視使用批次偵測及 Power BI 的異常

您可以使用本教學課程中尋找異常狀況，以批次的時間序列資料集內。 使用 Power BI desktop，您將 Excel 檔案、 準備資料以供異常偵測器 API，以及透過視覺化整個統計異常。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 使用 Power BI Desktop 匯入和轉換時間序列資料集
> * Power BI Desktop 整合進行批次異常偵測異常偵測器 API
> * 以視覺化方式檢視您的資料，包括預期和所見的值和異常偵測界限內找到的異常狀況。

## <a name="prerequisites"></a>必要條件

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/)，可免費。
* Excel (.xlsx) 檔案包含時間序列資料點。 本快速入門中的範例資料都位於[GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>載入並格式化時間序列資料

若要開始使用，請開啟 Power BI Desktop 並載入時間序列資料，您已下載的必要條件。 這個 excel 檔案包含一系列的 Coordinated Universal Time (UTC) 時間戳記和值組。  

> [!NOTE]
> Power BI 可以使用來自各種來源，例如.csv 檔案、 SQL 資料庫、 Azure blob 儲存體，及其他資料。  

在主要 Power BI Desktop 視窗中，按一下**首頁**功能區。 在 **外部的資料**功能區中，開啟群組**取得資料**下拉式選單，然後按一下  **Excel**。

![在 Power BI 中的 [取得資料] 按鈕的映像](../media/tutorials/power-bi-get-data-button.png)

對話方塊出現之後，請瀏覽至您下載範例.xlsx 檔案的資料夾，並加以選取。 之後**Navigator**  對話方塊出現時，按一下**Sheet1**，然後**編輯**。

![Power BI 中的資料來源 「 導覽 」 畫面的映像](../media/tutorials/navigator-dialog-box.png)

Power BI 會將轉換的第一個資料行中的時間戳記`Date/Time`資料型別。 這些時間戳記必須轉換成文字，才能傳送到的異常偵測器 API。 如果 Power Query 編輯器不會自動開啟，請按一下**編輯查詢**常用索引標籤上。 

按一下 **轉換**功能區中 Power Query 編輯器中。 在 **任何資料行**群組中，開啟**資料類型：** 下拉式選單，然後選取**文字**。

![Power BI 中的資料來源 「 導覽 」 畫面的映像](../media/tutorials/data-type-drop-down.png)

當您取得有關變更資料行類型的通知時，請按一下**取代目前**。 之後，按一下**關閉並套用**或是**套用**中**首頁**功能區。 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>建立將資料傳送，並將回應格式化為函式

若要設定格式及資料檔案傳送到的異常偵測器 API，您可以叫用上面建立的資料表上的查詢。 在 Power Query 編輯器中，從**首頁**功能區中，開啟**新來源**下拉功能表，然後按一下**空白查詢**。

請確定已選取您新的查詢，然後按一下 **進階編輯器**。 

![在 Power BI 中的 [進階編輯器] 按鈕的映像](../media/tutorials/advanced-editor-screen.png)

在 [進階編輯器]，使用下列的 Power Query M 程式碼片段從資料表擷取資料行，並將它傳送至 API。 之後，查詢會從 JSON 回應中，建立資料表，並將它傳回。 取代`apiKey`變數具有有效的異常偵測器 API 索引鍵和`endpoint`與您的端點。 您在 [進階編輯器] 中輸入查詢之後，請按一下**完成**。

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({
                    
                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),
    
    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

叫用您的資料工作表上的查詢，方法是選取`Sheet1`如下**輸入參數**，然後按一下**Invoke**。 

![[進階編輯器] 按鈕的影像](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>資料來源的隱私權和驗證

> [!NOTE]
> 留意資料隱私性及存取您組織的原則。 請參閱[Power BI Desktop 隱私權等級](https://docs.microsoft.com/power-bi/desktop-privacy-levels)如需詳細資訊。

當您嘗試執行查詢，因為它會利用外部資料來源時，可能會收到一則警告訊息。 

![顯示警告，Power BI 所建立的影像](../media/tutorials/blocked-function.png)

若要修正此問題，按一下**檔案**，並**選項和設定**。 然後按一下**選項**。 下面**目前的檔案**，選取**隱私權**，並**忽略隱私權等級，並可能會改善效能**。 

此外，您可能會收到訊息，要求您指定您要連線到 API 的方式。

![顯示指定的存取認證的要求的影像](../media/tutorials/edit-credentials-message.png)

若要修正此問題，按一下**編輯認證**訊息中。  對話方塊中出現之後，請選取**Anonymous**以匿名方式連線到 API。 然後按一下 [ **連接**]。 

之後，按一下**關閉並套用**中**首頁**以套用變更的功能區。

## <a name="visualize-the-anomaly-detector-api-response"></a>以視覺化方式檢視異常偵測器 API 回應

在主要 Power BI 畫面中，開始使用上面建立視覺化資料的查詢。 先選取**折線圖**中**視覺效果**。 然後加入時間戳記從叫用的函式的折線圖**軸**。 以滑鼠右鍵按一下它，然後選取**時間戳記**。 

![以滑鼠右鍵按一下 時間戳記值](../media/tutorials/timestamp-right-click.png)

加入下列欄位從**叫用函式**圖表**值**欄位。 使用以下螢幕擷取畫面，可協助您建立您的圖表。

    * Value
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![新的 [快速量值] 畫面的影像](../media/tutorials/chart-settings.png)

新增欄位之後, 按一下圖表，並調整其大小以顯示所有資料點。 您的圖表看起來類似下列螢幕擷取畫面：

![新的 [快速量值] 畫面的影像](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>顯示異常資料點

Power BI 視窗中，右邊如下**欄位** 窗格中，以滑鼠右鍵按一下**值**下**叫用函數查詢**，然後按一下**新快速量值**。

![新的 [快速量值] 畫面的影像](../media/tutorials/new-quick-measure.png)

在出現的畫面，選取**篩選值**的計算。 設定**基底值**至`Sum of Value`。 然後將拖曳`IsAnomaly`從**叫用函式**欄位**篩選**。 選取 `True`從**篩選**下拉式選單。

![新的 [快速量值] 畫面的影像](../media/tutorials/new-quick-measure-2.png)

按一下後**Ok**，就會`Value for True`欄位中，您的欄位清單的底部。 以滑鼠右鍵按一下它，並重新命名為**異常**。 將它加入至圖表**值**。 然後選取**格式**工具，並將 x 軸類型設定為**Categorical**。

![新的 [快速量值] 畫面的影像](../media/tutorials/format-x-axis.png)

套用至圖表的色彩，按一下**格式**工具並**資料色彩**。 您的圖表看起來應該如下所示：

![新的 [快速量值] 畫面的影像](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[使用 Azure Databricks 的資料流異常偵測](anomaly-detection-streaming-databricks.md)
