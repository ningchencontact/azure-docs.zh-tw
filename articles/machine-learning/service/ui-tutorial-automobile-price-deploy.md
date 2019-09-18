---
title: 教學課程：透過視覺化介面部署機器學習模型
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure Machine Learning 服務的視覺化介面中建置預測性分析解決方案。 使用拖放模組進行機器學習模型的定型、評分和部署。 此教學課程是使用線性迴歸預測汽車價格相關系列的第二部分 (共兩個部分)。
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: 64062aeab9a807d2aee7f4bca05d4019a3d9b736
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858643"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>教學課程：透過視覺化介面部署機器學習模型

為了讓其他人有機會使用[教學課程第一部分](ui-tutorial-automobile-price-train-score.md)中開發的預測模型，您可以將其部署為 Azure Web 服務。 截至目前為止，您一直在試驗如何定型模型。 現在，是時候根據使用者輸入來產生新預測了。 在教學課程的這個部分中，您將：

> [!div class="checklist"]
> * 準備模型進行部署
> * 部署 Web 服務
> * 測試 Web 服務
> * 管理 Web 服務
> * 取用 Web 服務

## <a name="prerequisites"></a>必要條件

完成[教學課程的第一個部分](ui-tutorial-automobile-price-train-score.md)，了解如何在視覺化介面中定型和評分機器學習模型。

## <a name="prepare-for-deployment"></a>準備部署

將實驗部署為 Web 服務之前，您必須先將「訓練實驗」  轉換成「預測實驗」  。

1. 請選取實驗畫布底端的 [建立預測實驗]  *。

    ![示範訓練實驗如何自動轉換為預測實驗的 gif 動畫](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    當您選取 [建立預測實驗]  時，有幾件事會發生：
    
    * 定型的模型會儲存為模組選擇區中**定型模型**模組。 您可以在**定型模型**下找到此項目。
    * 用於定型的模組已遭到移除，尤其是：
      * 定型模型
      * 分割資料
      * 評估模型
    * 儲存的定型模型會加回實驗中。
    * 將會新增 **Web 服務輸入**和 **Web 服務輸出**模組。 這些模組會識別使用者資料將在何處輸入模型，以及資料會傳回何處。

    **訓練實驗**仍儲存在實驗畫布頂端的新索引標籤下。

1. **執行** 實驗。

1. 請選取 [評分模型]  模組的輸出，並選取 [檢視結果]  來確認模型仍然有效。 您可以看到原始資料顯示，以及預測的價格 (「評分標籤」)。

實驗現在看起來如下：  

![實驗在準備好進行部署之後的預期組態螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>部署 Web 服務

1. 在畫布下方選取 [部署 Web 服務]  。

1. 選取要執行您 Web 服務的**計算目標**。

    目前，視覺化介面僅支援對 Azure Kubernetes Service (AKS) 計算目標進行部署。 您可以在機器學習服務工作區中選擇可用的 AKS 計算目標，或是使用所顯示對話方塊中的步驟來設定新的 AKS 環境。

    ![顯示新計算目標可能組態的螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. 選取 [部署 Web 服務]  。 部署完成時，您會看到下列通知。 部署可能需要幾分鐘的時間。

    ![顯示部署成功確認訊息的螢幕擷取畫面。](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>測試 Web 服務

您可以藉由流覽至 [Web 服務]  索引標籤，來測試和管理您視覺化介面的 Web 服務。

1. 移至 Web 服務區段。 您會看到您部署的 Web 服務，其名稱為**教學課程 - 預測汽車價格 [預測實驗]** 。

     ![醒目提示最近所建立 Web 服務的 Web 服務所引標籤螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. 選取 Web 服務名稱以檢視其他詳細資料。

1. 選取 [測試]  。

    [![顯示 Web 服務測試頁面的螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)](./media/ui-tutorial-automobile-price-deploy/web-service-test.png#lightbox)

1. 輸入測試資料或使用自動填入的範例資料，然後選取 [測試]  。

    測試要求會傳送至 Web 服務，而結果會顯示在頁面上。 雖然對輸入資料會產生價格值，但該值並不會用來產生預測值。

## <a name="consume-the-web-service"></a>取用 Web 服務

使用者現在可以將 API 要求傳送至您的 Azure Web 服務並接收結果，藉此預測新汽車的價格。

**要求/回應** - 使用者可以使用 HTTP 通訊協定，將一列或多列汽車資料傳送至服務。 服務會回應一組或多組結果。

您可以在 Web 服務詳細資料頁面的 [取用]  索引標籤中，找到 REST 呼叫範例。

   ![使用者可以在 [取用] 索引標籤中找到的 REST 呼叫範例螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

瀏覽至 [API 文件]  索引標籤，以尋找更多 API 詳細資料。

## <a name="manage-models-and-deployments"></a>管理模型和部署

您也可以透過 Azure Machine Learning 服務工作區來管理在視覺化介面中建立的模型和 Web 服務部署。

1. 在 [Azure 入口網站](https://portal.azure.com/)中開啟工作區。  

1. 在您的工作區中，選取 [模型]  。 然後選取您建立的實驗。

    ![示範如何在 Azure 入口網站中瀏覽至實驗的螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    在此頁面上，您會看到模型的其他詳細資料。

1. 選取 [部署]  ，這會列出使用模型的任何 Web 服務。 選取 Web 服務名稱，即可移至 Web 服務的詳細資料頁面。 在此頁面上，您可以取得更詳細的 Web 服務資訊。

    [![詳細執行報告的螢幕擷取畫面](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)](./media/ui-tutorial-automobile-price-deploy/deployment-details.png#lightbox)

您也可以在您[工作區登陸頁面 (預覽)](https://ml.azure.com) 的 [Models]  \(模型\) 和 [Endpoints]  \(端點\) 區段中找到這些模型和部署。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已了解在視覺化介面中建立、部署和使用機器學習模型的關鍵步驟。 若要深入了解如何使用視覺化介面來解決其他類型的問題，請參閱我們的其他範例實驗。

> [!div class="nextstepaction"]
> [信用風險分類範例](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
