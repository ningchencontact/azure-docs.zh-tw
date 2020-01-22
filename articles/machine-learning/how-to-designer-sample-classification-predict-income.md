---
title: 設計工具：分類、預測收入範例
titleSuffix: Azure Machine Learning
description: 遵循此範例建立一個無程式碼分類器，使用 Azure Machine Learning 設計工具來預測收入。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: bfae0d8eed80a88475c447a141097022fed9adff
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311101"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>建立分類器 & 使用特徵選取來預測具有 Azure Machine Learning 設計工具的收入

**設計師範例3**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

瞭解如何使用設計工具來建立機器學習分類器，而不需撰寫任何一行程式碼。 這個範例會訓練二元促進**式決策樹**來預測成人人口普查收入（> = 50K 或 < = 50K）。

因為問題是回應「哪一個？」 這稱為分類問題。 不過，您可以套用相同的基本程式來處理任何類型的機器學習問題-回歸、分類、叢集等。

以下是此範例的最後一個管線圖形：

![管線的圖形](./media/how-to-designer-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 按一下範例3加以開啟。



## <a name="data"></a>資料

此資料集包含14個特徵和一個標籤資料行。 有多種類型的功能，包括數值和類別。 下圖顯示資料集的摘錄： ![資料](media/how-to-designer-sample-classification-predict-income/sample3-dataset-1225.png)



## <a name="pipeline-summary"></a>管線摘要

請遵循下列步驟來建立管線：

1. 將 [成人人口普查收入二進位資料集] 模組拖曳至管線畫布。
1. 加入**分割資料**模組，以建立定型集和測試集。 將第一個輸出資料集中的資料列分數設定為0.7。 此設定會指定將70% 的資料輸出到模組的左側埠，並將其餘埠輸出至正確的埠。 我們會使用左側的資料集進行定型，將它用來測試。
1. 新增以**篩選為基礎的特徵選取**模組，以選取5個功能（依 PearsonCorreclation）。 
1. 加入**兩個類別的推進式決策樹**模組，以初始化促進式決策樹分類器。
1. 新增**訓練模型**模組。 將上一個步驟中的分類器連接到**定型模型**的左側輸入埠。 從以篩選為基礎的特徵選取模組，將篩選的資料集連接為訓練資料集。  **定型模型**會將分類器定型。
1. 新增選取資料行轉換和套用轉換模組，以將相同的轉換（篩選後的特徵選取）套用至測試資料集。
![套用-轉換](./media/how-to-designer-sample-classification-predict-income/transformation.png)
1. 新增**評分模型**模組，並將**定型模型**模組連接到它。 然後將測試集（套用至測試集的套用轉換模組輸出）新增至**評分模型**。 **評分模型**會進行預測。 您可以選取其輸出埠，以查看預測和正面類別機率。


    此管線有兩個計分模組，右側的其中一個會在進行預測之前排除標籤資料行。 這已準備好部署即時端點，因為 web 服務輸入只會預期功能不會加上標籤。 

1. 新增 [**評估模型**] 模組，並將評分資料集連接到其左側輸入埠。 若要查看評估結果，請選取 [**評估模型**] 模組的輸出埠，然後選取 [**視覺化**]。

## <a name="results"></a>結果

![評估結果](media/how-to-designer-sample-classification-predict-income/sample3-evaluate-1225.png)

在評估結果中，您可以看到 [ROC]、[精確度召回] 和 [混淆計量] 這類曲線。 

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

探索適用于設計工具的其他範例：

- [範例 1-回歸：預測汽車的價格](how-to-designer-sample-regression-automobile-price-basic.md)
- [範例 2-回歸：比較汽車價格預測的演算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [範例 4-分類：預測信用風險（區分成本）](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [範例 5-分類：預測流失](how-to-designer-sample-classification-churn.md)
- [範例 6-分類：預測航班延誤](how-to-designer-sample-classification-flight-delay.md)
- [範例 7-文字分類：維琪百科 SP 500 資料集](how-to-designer-sample-text-classification.md)
