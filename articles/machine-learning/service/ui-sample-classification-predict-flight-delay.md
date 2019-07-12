---
title: 分類：預測航班誤點
titleSuffix: Azure Machine Learning service
description: 這篇文章會示範如何建置機器學習模型來預測航班誤點使用拖放視覺介面和自訂的 R 程式碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 773e55fe4b5ca5acf27ba1765e5a16075f625187
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607633"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>範例 6-分類：預測航班誤點使用 R

這項實驗中使用過去的航班和天氣資料來預測預定的客機航班是否將延遲超過 15 分鐘。

可以處理這個問題，分類問題中，預測兩個類別-延遲，或在時間上。 若要建置分類器，此模型使用大量的歷史的航班資料的範例。

以下是最終實驗圖形：

[![實驗的圖形](media/ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 選取 **開啟**範例 6 實驗的按鈕：

    ![開啟實驗](media/ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>取得資料

此實驗使用**航班延遲資料**資料集。 它的 TranStats 資料集合，與美國的一部分運輸部門。 資料集包含從四月起到 2013 年 10 月的航班延遲資訊。 上傳之前將資料視覺化介面，它已預先處理，如下所示：

* 篩選要納入為美國大陸 70 個最繁忙的機場。
* 針對取消航班，改為誤點達 15 分鐘。
* 篩選掉更改路徑的航班。
* 選取的 14 個資料行。

若要至航班資料，來補充**天氣資料集**用。 天氣資料包含 NOAA、 每小時起降天候觀測值，表示從機場天候觀測站，涵蓋相同時間週期的年 4 月 10 月的 2013年的觀察值。 再上傳至 Azure ML 的視覺化介面，它已預先處理，如下所示：

* 天候觀測站識別碼已對應到相對應的機場識別碼。
* 已移除與 70 個最繁忙的機場沒有關聯的天候觀測站。
* 日期資料行已分割成個別的資料行：年、 月和日。
* 選取的 26 個資料行。

## <a name="pre-process-the-data"></a>前置處理資料

資料集通常會需要某些前置處理之前進行分析。

![data-process](media/ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>航班資料

資料行**載波**， **OriginAirportID**，並**DestAirportID**會儲存為整數。 不過，它們是類別的屬性，請使用**編輯中繼資料**模組，將它們轉換成類別目錄。

![edit-metadata](media/ui-sample-classification-predict-flight-delay/edit-metadata.png)

然後使用**選取資料行**排除可能的目標 leakers 的資料集資料行的資料集模組中：**DepDelay**， **DepDel15**， **ArrDelay**，**取消**，**年**。 

若要加入的每小時天氣資料錄的飛行記錄，使用 排定的起飛時間做為其中一個聯結索引鍵。 若要執行聯結，CSRDepTime 資料行必須無條件捨去到最接近的整點，是藉由在**執行 R 指令碼**模組。 

### <a name="weather-data"></a>天氣資料

使用排除有遺漏值的比例很高的資料行**投射資料行**模組。 這些資料行包含字串值的所有資料行：**ValueForWindCharacter**， **WetBulbFarenheit**， **WetBulbCelsius**， **PressureTendency**， **PressureChange**，**SeaLevelPressure**，並**StationPressure**。

**清除遺漏資料**模組接著會套用到其餘的資料行，以移除具有遺失資料的資料列。

天氣觀測時間會無條件進位到最接近的小時。 相反的方向，以確保該模型會使用唯一的天氣飛行時間之前將會四捨五入排定的航班時間與天氣觀測時間。 

天氣資料會報告當地時間，因為時區差異會佔用減去時區中的資料行排定的起飛時間與天氣觀測時間。 完成這些作業使用**執行 R 指令碼**模組。

### <a name="joining-datasets"></a>加入資料集

航班記錄已加入的天氣資料的飛行的原點 (**OriginAirportID**) 使用**聯結資料**模組。

 ![原始聯結航班和天氣](media/ui-sample-classification-predict-flight-delay/join-origin.png)


航班資料錄加入與天氣資料使用的飛行目的地 (**DestAirportID**)。

 ![聯結航班和目的地的天氣](media/ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>準備訓練和測試範例

**分割資料**模組會將資料載入年 4 月分割年 9 月記錄對於訓練，並將年 10 月記錄測試。

 ![分割訓練和測試資料](media/ui-sample-classification-predict-flight-delay/split.png)

年、 月和時區的資料行，會移除從定型資料集使用選取的資料行的模組。

## <a name="define-features"></a>定義功能

在機器學習，功能是您感興趣的某項目的個別可測量的屬性。 尋找組功能強大的功能需要測試和網域知識。 有些功能會比其他功能更適合用來預測目標。 此外，某些功能可能會有很強的關聯，與其他功能，而不會將新的資訊加入至模型。 您可以移除這些功能。

若要建立模型時，您可以使用所有可用的功能，或選取功能的子集。

## <a name="choose-and-apply-a-learning-algorithm"></a>選擇及套用學習演算法

建立模型，使用**二級羅吉斯迴歸**模組和訓練的訓練資料集上。 

結果**定型模型**模組是可以用來進行預測的新範例評分定型的分類模型。 使用測試設定為從定型的模型產生分數。 然後使用**評估模型**模組來分析和比較模型的品質。

執行實驗之後，您可以檢視的輸出**評分模型**模組，藉由按一下輸出連接埠，然後選取**視覺化**。 輸出會包含 「 評分的標籤和標籤的機率。

最後，若要測試結果的品質，新增**評估模型**模組在實驗畫布，然後 「 評分模型 」 模組的輸出連線的左側輸入連接埠。 執行實驗及檢視的輸出**評估模型**模組中的，按一下輸出連接埠，然後選取**視覺化**。

## <a name="evaluate"></a>評估
羅吉斯迴歸模型具有 AUC 0.631 對測試的設定。

 ![評估](media/ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>後續步驟

瀏覽可用的視覺介面的其他範例：

- [範例 1-迴歸：預測汽車的價格](ui-sample-regression-predict-automobile-price-basic.md)
- [範例 2-迴歸：比較針對汽車價格預測演算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [範例 3-分類：預測信用風險](ui-sample-classification-predict-credit-risk-basic.md)
- [範例 4-分類：預測信用風險 （成本機密）](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [範例 5-分類：預測客戶流失](ui-sample-classification-predict-churn.md)
