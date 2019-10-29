---
title: 教學課程：透過視覺化介面部署機器學習模型
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure Machine Learning 視覺化介面中建置預測性分析解決方案。 使用拖放模組進行機器學習模型的定型、評分和部署。
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/22/2019
ms.openlocfilehash: 6f8717f70a2cb03a7fd683cfe61f1198461f4305
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792667"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>教學課程：透過視覺化介面部署機器學習模型

為了讓其他人有機會使用[教學課程第一部分](ui-tutorial-automobile-price-train-score.md)中開發的預測模型，您可以將其部署為即時端點。 在第一部分中，您已將模型定型。 現在，是時候根據使用者輸入來產生新預測了。 在教學課程的這個部分中，您將：

> [!div class="checklist"]
> * 部署即時端點
> * 建立推斷叢集
> * 測試即時端點

## <a name="prerequisites"></a>必要條件

完成[教學課程的第一個部分](ui-tutorial-automobile-price-train-score.md)，了解如何在視覺化介面中定型和評分機器學習模型。

## <a name="deploy-a-real-time-endpoint"></a>部署即時端點

若要部署管線，您必須：

1. 將訓練管線轉換成即時推斷管線，這會移除訓練模組，並新增推斷要求的輸入和輸出。
1. 部署推斷管線。

### <a name="create-a-real-time-inference-pipeline"></a>建立即時推斷管線

1. 在管線畫布頂端，選取 [建立推斷管線]   > [即時推斷管線] 

    當您選取 [建立推斷管線]  時，會發生若干情況：
    
    * 定型的模型會儲存為模組選擇區中的**資料集**模組。 您可以在 [我的資料集]  下方找到該項目。
    * 用於定型的模組 (例如**訓練模型**和**分割資料**) 會被移除。
    * 儲存的定型模型會加回管線中。
    * 會新增 **Web 服務輸入**和 **Web 服務輸出**模組。 這些模組會識別使用者資料將在何處輸入模型，以及資料會傳回何處。

    > [!Note]
    > **訓練管線**會儲存在管線畫布頂端的新索引標籤下。 它也會在視覺化介面中呈現為已發佈的管線。
    >

    您的管線此時應會顯示如下：  

   ![此螢幕擷取畫面顯示管線在做好部署準備後的預期組態](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

1. 選取 [執行]  ，並使用您在第一部分中使用的相同計算目標和實驗。

1. 選取**評分模型**模組。

1. 在 [屬性] 窗格中選取 [輸出]   > [視覺化]  ，以確認模型仍在運作中。 您可以看到原始資料顯示，以及預測的價格 (「評分標籤」)。

1. 選取 [部署]  。

### <a name="create-an-inferencing-cluster"></a>建立推斷叢集

在出現的對話方塊中，您可以從工作區中現有的 Azure Kubernetes Service (AKS) 叢集進行選取，以部署您的模型。 如果您沒有 AKS 叢集，請使用下列步驟建立一個。

1. 在對話方塊中選取 [計算]  ，以瀏覽至 [計算]  頁面。

1. 在導覽功能區中，選取 [推斷叢集]   > [+ 新增]  。

    ![顯示如何瀏覽至 [新增推斷叢集] 窗格的螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. 在推斷叢集窗格中，設定新的 Kubernetes 服務。

1. 輸入 "aks-compute" 作為**計算名稱**。
    
1. 選取附近可用的**區域**。

1. 選取 [建立]  。

    > [!Note]
    > 建立新的 AKS 服務需要約 15 分鐘。 您可以在 [推斷叢集]  頁面上查看佈建狀態
    >

### <a name="deploy-the-real-time-endpoint"></a>部署即時端點

在您的 AKS 服務完成佈建後，請回到即時推斷管線以完成部署。

1. 選取畫布上方的 [部署]  。

1. 選取 [部署新的即時端點]  。 

1. 選取您建立的 AKS 叢集。

1. 選取 [部署]  。

    ![顯示如何設定新即時端點的螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    當部署完成時，畫布上方會出現成功通知，這可能需要幾分鐘的時間。

## <a name="test-the-real-time-endpoint"></a>測試即時端點

您可以在左側的工作區導覽窗格中瀏覽至 [端點]  頁面，以測試您的即時端點。

1. 在 [端點]  頁面上，選取您部署的端點。

    ![顯示即時端點索引標籤的螢幕擷取畫面，其中醒目提示了最近建立的端點](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. 選取 [測試]  。

1. 輸入測試資料或使用自動填入的範例資料，然後選取 [測試]  。

    測試要求會提交至端點，而結果會顯示在頁面上。 雖然對輸入資料會產生價格值，但該值並不會用來產生預測值。

    ![顯示如何測試即時端點的螢幕擷取畫面，其中醒目提示了價格的評分標籤](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解在視覺化介面中建立、部署和使用機器學習模型的關鍵步驟。 若要深入了解如何使用視覺化介面來解決其他類型的問題，請參閱我們的其他範例管線。

> [!div class="nextstepaction"]
> [信用風險分類範例](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
