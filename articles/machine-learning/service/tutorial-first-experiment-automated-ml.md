---
title: 建立您的第一個自動化機器學習實驗
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure 入口網站中使用自動化機器學習來定型及部署分類模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 08/14/2019
ms.openlocfilehash: 01228dc01b8006a0a2476ddbbd6fa8ff430e280a
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982749"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>教學課程：使用自動化機器學習建立第一個分類模型

在本教學課程中，您會了解如何在 Azure 入口網站 (預覽) 中建立第一個自動化機器學習實驗，而且不需要撰寫任何一行程式碼。 此範例會建立分類模型來預測客戶是否會向金融機構申請定期存款。

透過自動化機器學習，您可以將耗費大量時間的工作自動化。 自動化機器學習會快速地逐一嘗試多種演算法和超參數的組合，協助您根據所選擇的成功計量找到最佳模型。

在本教學課程中，您將了解如何執行下列工作：

> [!div class="checklist"]
> * 建立 Azure Machine Learning 服務工作區。
> * 執行自動化機器學習實驗。
> * 檢視實驗詳細資料。
> * 部署模型。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://aka.ms/AMLFree)。

* 下載 [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) 資料檔案。 **y** 資料行指出客戶是否申請定期存款，稍後本教學課程會將其識別為預測的目標資料行。 

## <a name="create-a-workspace"></a>建立工作區

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-and-run-the-experiment"></a>建立及執行實驗

這些步驟會逐步引導您完成實驗設定，也就是選取資料到選擇您的主要計量和模型類型。 

1. 移至工作區的左側窗格。 在 [製作 (預覽)]  區段下選取 [自動化機器學習]  。
您會看到 [歡迎使用自動化機器學習]  的畫面，因為這是您第一個使用自動化機器的實驗。

    ![Azure 入口網站導覽窗格](media/tutorial-1st-experiment-automated-ml/nav-pane.png)

1. 選取 [建立實驗]  。 然後輸入 **my-1st-automl-experiment** 作為實驗名稱。

1. 選取 [建立新的計算]  ，並設定此實驗的計算內容。

    欄位| 值
    ---|---
    計算名稱| 輸入可識別您計算內容的唯一名稱。 在此範例中，我們使用 **automl-compute**。
    虛擬機器大小| 為您的計算選取虛擬機器大小。 我們使用 **Standard_DS12_V2**。
    其他設定| 最小節點：  1. 若要啟用資料分析，您必須有一個或多個節點。 <br> 最大節點  ：6. 

    若要建立新的計算，請選取 [建立]  。 這需要一些時間。 

    建立完成時，請從下拉式清單中選取新的計算，然後選取 [下一步]  。

    >[!NOTE]
    >在本教學課程中，我們會使用以新計算建立的預設儲存體帳戶和容器。 這些項目會自動填入表單。

1. 請選取 [上傳]  ，然後從本機電腦選擇 **bankmarketing_train.csv**，以將其上傳到預設容器。 公開預覽版僅支援本機檔案上傳和 Azure Blob 儲存體帳戶。 上傳完成後，請從清單中選取檔案。 

1. [預覽]  索引標籤可讓我們進一步設定此實驗的資料。

    在 [預覽]  索引標籤上，使資料包含標題。 該服務預設會包含所有用於定型的特性 (資料行)。 在此範例中，請向右捲動並**忽略** **day_of_week** 特性。

    ![預覽索引標籤的設定](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)

    >[!NOTE]
    > 沒有最小節點的計算無法使用資料分析。

1. 請選取 [分類]  作為預測工作。

1. 選取 [y]  作為我們要在其中執行預測的目標資料行。 此資料行會指出用戶端是否已申請定期存款。

1. 展開 [進階設定]  並填入欄位，如下所示。

    進階設定|值
    ------|------
    主要計量| AUC_weighted 
    允出準則| 符合上述任一條件時，訓練作業就會在完整完成前結束： <br> 訓練作業時間 (分鐘)  ：5  <br> 反覆運算的次數上限  ：10 
    前置處理| 啟用由自動化機器學習完成的前置處理。 這包括用以產生綜合特性的自動化資料清理、準備和轉換。
    驗證| 選取 [K 折交叉驗證]  ，並選取 [2] 作為交叉驗證的數目。 
    並行| 選取 [5]  作為最大並行反覆運算次數。

   >[!NOTE]
   > 在此實驗中，我們不會為每次反覆運算的計量或最大核心數設定閾值。 我們也不會防止演算法受到測試。

1. 選取 [開始]  來執行實驗。

   當實驗啟動時，您會在頂端看到具有下列狀態的空白 [執行詳細資料]  畫面。
      
實驗準備程序會需要幾分鐘的時間。 當程序完成時，狀態訊息會變更為 [執行中]  。

##  <a name="view-experiment-details"></a>檢視實驗詳細資料

當實驗進行時，[執行詳細資料]  畫面會以執行的不同反復項目 (模型) 來更新反覆運算圖表和清單。 反覆項目清單會依計量分數排序。 根據預設，**AUC_weighted** 計量最高分的模型會在清單頂端。

>[!TIP]
> 訓練作業需要幾分鐘的時間，來讓每個管線完成執行。

[![執行詳細資料儀表板](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>部署模型

藉由使用 Azure 入口網站中的自動化機器學習，我們可以將最佳模型部署為 Web 服務，以根據新資料進行預測，並找出潛在的商機區域。 此實驗中的部署表示金融機構現在有可反覆進行且可調整的解決方案，能識別潛在的定期存款客戶。

在此實驗內容中，根據 **AUC_weighted** 計量，**VotingEnsemble** 會被視為最佳模型。  我們會部署此模型，但提醒您部署大約需要 20 分鐘的時間才能完成。

1. 在 [執行詳細資料]  頁面上，選取 [部署最佳模型]  按鈕。

1. 填入 [部署最佳模型]  窗格，如下所示：

    欄位| 值
    ----|----
    部署名稱| my-automl-deploy
    部署描述| 我的第一個自動化機器學習實驗部署
    評分指令碼| 自動產生
    環境指令碼| 自動產生
    
1. 選取 [部署]  。

    當部署成功完成後，會出現部署完成的訊息。
    
現在您已有可運作的 Web 服務，可用來產生預測。

## <a name="clean-up-resources"></a>清除資源

部署檔案比資料和實驗檔案大，因此儲存的成本會較高。 如果您想要保留工作區和實驗檔案，那麼僅刪除部署檔案可將成本降到最低。 或者，如果您不打算使用任何檔案，您可以刪除整個資源群組。  

### <a name="delete-the-deployment-instance"></a>刪除部署執行個體

如果您想要保留資源群組和工作區以進行其他教學課程和探索，您可以只從 Azure 入口網站刪除部署執行個體。 

1. 移至左側的 [資產]  窗格，然後選取 [部署]  。 

1. 選取您想要刪除的部署，然後選取 [刪除]  。 

1. 選取 [繼續]  。

### <a name="delete-the-resource-group"></a>刪除資源群組

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在此自動化機器學習教學課程中，您已使用 Azure 入口網站來建立和部署分類模型。 請參閱下列文章，以了解更多資訊及接下來的步驟：

> [!div class="nextstepaction"]
> [取用 Web 服務](how-to-consume-web-service.md)

+ 深入了解[前置處理](how-to-create-portal-experiments.md#preprocess)。
+ 深入了解[資料分析](how-to-create-portal-experiments.md#profile)。
+ 深入了解[自動化機器學習](concept-automated-ml.md)。

>[!NOTE]
> 此銀行行銷資料集可在 [Creative Commons (CCO：公用網域) 授權](https://creativecommons.org/publicdomain/zero/1.0/)底下取得。 個別資料庫內容中的任何權限都是以[資料庫內容授權](https://creativecommons.org/publicdomain/zero/1.0/)為依據，並可在 [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset) 上取得。 此資料集原本位在 [UCI Machine Learning 資料庫](https://archive.ics.uci.edu/ml/datasets/bank+marketing)內。<br><br>
> 請引用下列內容： <br> [Moro et al., 2014] S. Moro, P. Cortez and P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, June 2014.
