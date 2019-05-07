---
title: 教學課程：透過視覺化介面部署機器學習模型
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure Machine Learning 服務的視覺化介面中建置預測性分析解決方案。 使用拖放模組進行機器學習模型的定型、評分和部署。 本教學課程是使用線性迴歸預測汽車價格相關系列的第二部分 (共兩個部分)。
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 5f29e3820416686b42167fa278c4b7d0f9a58f1f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190909"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>教學課程：透過視覺化介面部署機器學習模型

在本教學課程中，您將進一步了解如何在 Azure Machine Learning 服務視覺化介面中開發預測解決方案。 本教學課程是**兩部分教學課程系列的第二部分**。 在[教學課程的第一部分](ui-tutorial-automobile-price-train-score.md)中，您已定型、評分及評估模型來預測汽車價格。 在教學課程的這個部分中，您將：

> [!div class="checklist"]
> * 準備模型進行部署
> * 部署 Web 服務
> * 測試 Web 服務
> * 管理 Web 服務
> * 取用 Web 服務

## <a name="prerequisites"></a>必要條件

完成[本教學課程的第一部分](ui-tutorial-automobile-price-train-score.md)。

## <a name="prepare-for-deployment"></a>準備部署

為了讓其他人有機會使用本教學課程中開發的預測模型，您可以將其部署為 Azure Web 服務。

截至目前為止，您一直在試驗如何定型模型。 現在，是時候根據使用者輸入來產生新預測了。

部署準備是包含兩個步驟的程序：  

1. 將您所建立的「訓練實驗」轉換成「預測實驗」
1. 將預測實驗部署為 Web 服務

您可能需要選取實驗畫布底部的 [另存新檔]，先建立一份實驗複本。

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>將訓練實驗轉換為預測實驗

若要備妥此模型以進行部署，請將此訓練實驗轉換為預測實驗。 這通常包含三個步驟：

1. 儲存已定型的模型，並取代您的定型模組
1. 精簡實驗，移除只有定型才需要的模組
1. 定義 Web 服務將接受輸入資料的位置和產生輸出的位置

您可以手動執行這些步驟，或是選取實驗畫布底部的 [設定 Web 服務]，讓其自動完成。

![示範訓練實驗如何自動轉換為預測實驗的 gif 動畫](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

當您選取 [設定 Web 服務] 時，會發生幾件事：

* 定型模型會轉換成單一的**定型模型**模組。 這會儲存在實驗畫布左側的模組選擇區。 您可以在**定型模型**下找到此項目。
* 用於定型的模組已遭到移除，尤其是：
  * 定型模型
  * 分割資料
  * 評估模型
* 儲存的定型模型會加回實驗中
* 將會新增 **Web 服務輸入**和 **Web 服務輸出**模組。 這些模組會識別使用者資料將在何處輸入模型，以及資料會傳回何處。

您可以看到，實驗已在實驗畫布頂端的新索引標籤下儲存為兩個部分。 原始的訓練實驗位於 [訓練實驗] 索引標籤底下，新建立的預測實驗位於 [預測實驗] 底下。 您將預測實驗部署為 Web 服務。

實驗現在看起來如下：  

![實驗在準備好進行部署之後的預期組態螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

最後一次執行實驗 (選取 [執行])。 在快顯對話方塊中，選擇您要在其中執行實驗的計算目標。 若要確認模型仍然有效，請選取評分模型模組的輸出，並選取 [檢視結果]。 您可以看到原始資料顯示，以及預測的價格 (「評分標籤」)。

## <a name="deploy-the-web-service"></a>部署 Web 服務

部署衍生自您實驗的新式 Web 服務：

1. 在畫布下方選取 [部署 Web 服務]。
1. 選取要執行您 Web 服務的**計算目標**。

    目前，視覺化介面僅支援對 Azure Kubernetes Service (AKS) 計算目標進行部署。 您可以在機器學習服務工作區中選擇可用的 AKS 計算目標，或是使用所顯示對話方塊中的步驟來設定新的 AKS 環境。

    ![顯示新計算目標可能組態的螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. 選取 [部署 Web 服務]。 部署完成時，您會看到下列通知。 部署可能需要幾分鐘的時間。

    ![顯示部署成功確認訊息的螢幕擷取畫面。](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>測試 Web 服務

使用者輸入資料會透過 **Web 服務輸入**模組進入您已部署的模型。 接著，輸入會在**評分模型**模組中進行評分。 基於您設定預測實驗的方式，模型預期會得到與原始汽車價格資料集相同格式的資料。 最後，透過 **Web 服務輸出**模組，將結果傳回給使用者。

您可以在視覺化介面中的 Web 服務索引標籤中測試 Web 服務。

1. 移至 Web 服務區段。 您會看到您部署的 Web 服務，其名稱為**教學課程 - 預測汽車價格 [預測實驗]**。

     ![醒目提示最近所建立 Web 服務的 Web 服務所引標籤螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. 選取 Web 服務名稱以檢視其他詳細資料。

     ![Web 服務檢視中其他可用的詳細資料螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. 選取 [測試]。

    ![顯示 Web 服務測試頁面的螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. 輸入測試資料或使用自動填入的範例資料，然後選取底部的 [測試]。 測試要求會傳送至 Web 服務，而結果會顯示在頁面上。

## <a name="manage-the-web-service"></a>管理 Web 服務

一旦部署 Web 服務之後，您就可以從視覺化介面的 [Web 服務] 索引標籤中進行管理。

若要刪除 Web 服務，您可以在 Web 服務詳細資料頁面中選取 [刪除]。

   ![顯示視窗底部刪除 Web 服務按鈕位置的螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/web-service-delete.png)

## <a name="consume-the-web-service"></a>取用 Web 服務

在本教學課程的前幾個步驟中，您已將汽車預測模型部署為 Azure Web 服務。 現在，使用者可以透過 REST API 將資料傳送至服務並接收結果。

**要求/回應** - 使用者可以使用 HTTP 通訊協定，將一列或多列汽車資料傳送至服務。 服務會回應一組或多組結果。

您可以在 Web 服務詳細資料頁面的 [取用] 索引標籤中，找到 REST 呼叫範例。

   ![使用者可以在 [取用] 索引標籤中找到的 REST 呼叫範例螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

瀏覽至 [API 文件] 索引標籤，以尋找更多 API 詳細資料。

  ![使用者可以在 [API 文件] 索引標籤中找到其他 API 詳細資料的螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments-in-azure-machine-learning-service-workspace"></a>在 Azure Machine Learning 服務工作區中管理模型和部署

您可以透過 Azure Machine Learning 服務工作區來管理在視覺化介面中建立的模型和 Web 服務部署。

1. 在 [Azure 入口網站](https://portal.azure.com/)中開啟工作區。  

1. 在您的工作區中，選取 [模型]。 然後選取您建立的實驗。

    ![示範如何在 Azure 入口網站中瀏覽至實驗的螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    在此頁面上，您會看到模型的其他詳細資料。

    ![在 Azure 入口網站中顯示實驗統計資料概觀的螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/model-details.png)

1. 選取 [部署]，這會列出使用模型的任何 Web 服務。 選取 Web 服務名稱，即可移至 Web 服務的詳細資料頁面。 在此頁面上，您可以取得更詳細的 Web 服務資訊。

    ![詳細執行報告的螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解在視覺化介面中建立、部署和使用機器學習模型的關鍵步驟。 若要深入了解如何使用視覺化介面來解決其他類型的問題，請參閱範例實驗。

> [!div class="nextstepaction"]
> [信用風險分類範例](ui-sample-classification-predict-credit-risk-basic.md)
