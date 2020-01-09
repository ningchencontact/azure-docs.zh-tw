---
title: 設計工具：預測航班延誤範例
titleSuffix: Azure Machine Learning
description: 建立分類器，並使用自訂 R 程式碼來預測 Azure Machine Learning 設計工具的航班延誤。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: a8564ae8c3d7499dd3d194ec61a9b24b1d0d2393
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660168"
---
# <a name="build-a-classifier--use-r-to-predict-flight-delays-with-azure-machine-learning-designer"></a>建立分類器 & 使用 R 來預測 Azure Machine Learning 設計工具的飛行延遲

**設計工具（預覽）範例6**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

此管線會使用歷程記錄和天氣資料來預測排程的乘客航班是否會延遲超過15分鐘。 這個問題可以做為分類問題來進行，預測兩個類別：延遲或準時。

以下是此範例的最後一個管線圖形：

[管線 ![圖形](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png)](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png#lightbox)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 按一下 [範例 6] 將其開啟。

## <a name="get-the-data"></a>取得資料

這個範例會使用**航班延遲資料**資料集。 這是美國運輸部門的 TranStats 資料收集的一部分。 此資料集包含從4月到2013年10月的航班延誤資訊。 此資料集已預先處理，如下所示：

* 已篩選為包含美國大陸境內70最忙碌的機場。
* 將已取消的航班重新標示為延遲超過15分鐘。
* 已篩選掉轉向航班。
* 選取了14個數據行。

若要補充航班資料，請使用**氣象資料集**。 天氣資料包含 NOAA 的每小時、以土地為基礎的氣象觀察，並代表機場氣象站的觀察，涵蓋與航班資料集相同的時間週期。 它已預先處理，如下所示：

* 氣象站識別碼會對應至相對應的機場識別碼。
* 未與70最忙碌的機場關聯的天氣電臺已移除。
* 日期資料行已分割為不同的資料行： Year、Month 和 Day。
* 選取的26個數據行。

## <a name="pre-process-the-data"></a>預先處理資料

資料集通常需要進行一些前置處理，然後才能進行分析。

![資料-進程](./media/how-to-designer-sample-classification-flight-delay/data-process.png)

### <a name="flight-data"></a>航班資料

[**貨運公司]** 、[ **OriginAirportID**] 和 [ **DestAirportID** ] 資料行會儲存為整數。 不過，它們是類別屬性，使用 [**編輯中繼資料**] 模組將它們轉換成類別。

![編輯-中繼資料](./media/how-to-designer-sample-classification-flight-delay/edit-metadata.png)

然後使用 [**選取**資料集中的資料行] 模組，從可能為目標 leakers 的資料集資料行中排除： **DepDelay**、 **DepDel15**、 **ArrDelay**、**已取消**、**年**。 

若要將航班記錄與每小時氣象記錄聯結，請使用已排程的出發時間做為其中一個聯結索引鍵。 若要執行聯結，CSRDepTime 資料行必須向下舍入到最接近的小時，這是在**執行 R 腳本**模組中完成。 

### <a name="weather-data"></a>天氣資料

使用 [**專案資料行**] 模組會排除具有大量遺漏值的資料行。 這些資料行包含所有字串值的資料行： **ValueForWindCharacter**、 **WetBulbFarenheit**、 **WetBulbCelsius**、 **PressureTendency**、 **PressureChange**、 **SeaLevelPressure**和**StationPressure**。

**清除遺漏的資料**模組接著會套用到其餘的資料行，以移除遺漏資料的資料列。

天氣觀察時間會無條件進位到最接近的完整時數。 排定航班時間和氣象觀察時間會以相反的方向舍入，以確保此模型只會在航班時間之前使用天氣。 

由於天氣資料會以當地時間回報，因此會藉由從排程出發時間和氣象觀察時間減去時區資料行的方式來考慮時區差異。 這些作業會使用 [**執行 R 腳本**] 模組來完成。

### <a name="joining-datasets"></a>聯結資料集

航班記錄會使用**聯結資料**模組，與航班（**OriginAirportID**）來源的氣象資料聯結。

 ![依來源加入航班和天氣](./media/how-to-designer-sample-classification-flight-delay/join-origin.png)


航班記錄會使用航班的目的地（**DestAirportID**）與氣象資料聯結。

 ![依目的地加入航班和天氣](./media/how-to-designer-sample-classification-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>準備訓練和測試範例

[**分割資料**] 模組會將資料分割成四月份的記錄，以供定型，以及10月份記錄進行測試。

 ![分割定型和測試資料](./media/how-to-designer-sample-classification-flight-delay/split.png)

[年]、[月] 和 [時區] 資料行會使用 [選取資料行] 模組從訓練資料集移除。

## <a name="define-features"></a>定義功能

在機器學習中，功能是您感興趣之某個專案的個別可測量屬性。 尋找一組強大的功能需要實驗和領域知識。 有些功能會比其他功能更適合用來預測目標。 此外，某些功能可能與其他功能具有強式相互關聯，而且不會將新資訊加入至模型。 這些功能可以移除。

若要建立模型，您可以使用所有可用的功能，或選取功能的子集。

## <a name="choose-and-apply-a-learning-algorithm"></a>選擇及套用學習演算法

使用**兩個類別的羅吉斯回歸**模組建立模型，並在訓練資料集上定型。 

**訓練模型**模組的結果是定型的分類模型，可用來對新的樣本進行評分以進行預測。 使用測試集從定型的模型產生分數。 然後使用 [**評估模型**] 模組來分析和比較模型的品質。
管線：執行管線之後，您可以按一下輸出埠並選取 [**視覺化**]，以查看 [**評分模型**] 模組的輸出。 輸出會包含評分標籤和標籤的機率。

最後，若要測試結果的品質，請將 [**評估模型**] 模組新增至管線畫布，然後將左側的輸入埠連接到 [評分模型] 模組的輸出。 執行管線並查看 [**評估模型**] 模組的輸出，方法是按一下輸出埠，然後選取 [**視覺化**]。

## <a name="evaluate"></a>評估
羅吉斯回歸模型在測試集上具有0.631 的 AUC。

 ![評估](media/how-to-designer-sample-classification-flight-delay/sample6-evaluate-1225.png)

## <a name="next-steps"></a>後續步驟

探索適用于設計工具的其他範例：

- [範例 1-回歸：預測汽車的價格](how-to-designer-sample-regression-automobile-price-basic.md)
- [範例 2-回歸：比較汽車價格預測的演算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [範例 3-使用特徵選取進行分類：收入預測](how-to-designer-sample-classification-predict-income.md)
- [範例 4-分類：預測信用風險（區分成本）](service/how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [範例 5-分類：預測流失](service/how-to-designer-sample-classification-churn.md)
- [範例 7-文字分類：維琪百科 SP 500 資料集](how-to-designer-sample-text-classification.md)