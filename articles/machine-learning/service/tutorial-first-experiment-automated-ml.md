---
title: 建立您的第一個自動化機器學習實驗
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure Machine Learning 的工作區登陸頁面 (預覽) 中，使用自動化機器學習來定型及部署分類模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/26/2019
ms.openlocfilehash: 38c319fb89e8c763f8231c18cbb59bef099193e2
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259320"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>教學課程：使用自動化機器學習建立第一個分類模型

在此教學課程中，您會了解如何透過工作區登陸頁面 (預覽)，建立第一個自動化機器學習實驗，而且不需要撰寫任何一行程式碼。 此範例會建立分類模型來預測客戶是否會向金融機構申請定期存款。

透過自動化機器學習，您可以將耗費大量時間的工作自動化。 自動化機器學習會快速地逐一嘗試多種演算法和超參數的組合，協助您根據所選擇的成功計量找到最佳模型。

在本教學課程中，您將了解如何執行下列工作：

> [!div class="checklist"]
> * 建立 Azure Machine Learning 工作區。
> * 執行自動化機器學習實驗。
> * 檢視實驗詳細資料。
> * 部署模型。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://aka.ms/AMLFree)。

* 下載 [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) 資料檔案。 **y** 資料行指出客戶是否申請定期存款，稍後本教學課程會將其識別為預測的目標資料行。 

## <a name="create-a-workspace"></a>建立工作區

Azure Machine Learning 工作區是雲端中您用來實驗、定型及部署機器學習模型的基礎資源。 工作區可將您的 Azure 訂用帳戶和資源群組與服務中容易使用的物件結合。 

您透過 Azure 入口網站建立工作區 (管理 Azure 資源的 Web 型主控台)。 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> 記下您的**工作區**和**訂用帳戶**。 您會需要這些項目，以確保您在正確位置建立實驗。 

## <a name="create-and-run-the-experiment"></a>建立及執行實驗

您在工作區登陸頁面中完成下列實驗設定和執行步驟，該頁面是統一的介面，為所有技能等級的資料科學從業人員，提供執行資料科學情節的機器學習工具。

1. 登入[工作區登陸頁面](https://ml.azure.com/workspaceportal/) \(英文\)。

1. 選取訂用帳戶與您建立的工作區。

1. 選取 [馬上開始]  。

1. 在左側窗格中，選取 [撰寫]  下的 [自動化 ML]  。

   由於這是您的第一個自動化 ML 實驗，因此您會看到使用者入門畫面。

   ![Azure Machine Learning Studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. 選取 [建立實驗]  。 

1. 輸入此實驗名稱：`my-1st-automl-experiment`

1. 選取 [建立新的計算]  ，並設定您的計算目標。 計算目標是本機或雲端式資源環境，用來執行訓練指令碼，或裝載服務部署。 在此實驗中，我們會使用雲端式計算。 

   欄位 | 說明 | 教學課程的值
   ----|---|---
   計算名稱 |可識別您計算內容的唯一名稱。|automl-compute
   虛擬機器大小&nbsp;&nbsp;| 為您的計算選取虛擬機器大小。|Standard_DS12_V2
   最小/最大節點數 (在 [進階設定] 中)| 若要分析資料，您必須指定一個或多個節點。|最小節點數：1<br>最大節點數：6

   >[!NOTE]
   >在此教學課程中，您會使用隨新計算建立的預設儲存體帳戶和容器。 這些項目會自動填入表單。
    
1. 選取 [建立]  以取得計算目標。 

   **這需要幾分鐘來完成。** 

1. 建立完成後，請從下拉式清單中選取新的計算目標，然後選取 [下一步]  。

1. 選取 [從本機檔案上傳]  以開始建立新的資料集。 

    1. 選取 [瀏覽]  。
    
    1. 選擇您本機電腦上的 **bankmarketing_train.csv** 檔案。 這是您作為[必要條件](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)下載的檔案。

    1. 為您的資料集提供唯一名稱，並提供選擇性的描述。 

    1. 選取左下角 [下一步]  ，將它上傳至在您的工作區建立期間自動設定的預設容器。 公開預覽版僅支援本機檔案上傳。 
    
       上傳完成時，系統會根據檔案類型，預先填入 [設定與預覽] 表單。 
       
    1. 確認 [設定與預覽]  表單的填入方式如下，然後選取 [下一步]  。
        
        欄位|教學課程的值
        ---|---
        檔案格式| Delimited (分隔檔)
        分隔符號| Comma (逗號)
        編碼| UTF-8
        資料行標題| All files have same headers (所有檔案都有相同的標頭)
        Skip rows (略過資料列) | None
    
    1. [Schema]  \(結構描述\) 表單可讓您進一步設定此實驗的資料。 針對此範例，請選取 [day_of_week]  特徵的切換開關，如此一來，就不會將它包含在此實驗中。 選取 [Done]  \(完成\)，以完成檔案上傳及建立實驗資料集。

        ![預覽索引標籤的設定](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

1. 請選取 [分類]  作為預測工作。

1. 選取 [y]  作為您要預測的目標資料行。 此資料行會指出用戶端是否已申請定期存款。

1. 展開 [進階設定]  並填入欄位，如下所示。

   >[!NOTE]
   > 在此教學課程中，您不會為每個反覆項目的計量分數或最大核心數設定閾值。 您也不會防止演算法受到測試。
   
   進階設定&nbsp;|說明|教學課程的值&nbsp;&nbsp;
   ------|---------|---
   主要計量| 用於測量機器學習演算法的評估計量。|AUC_weighted
   允出準則| 如果符合條件，訓練作業就會停止。 |訓練作業時間：&nbsp;&nbsp;5 <br> <br> 反覆項目數量上限：10
   前置處理| 啟用由自動化機器學習完成的前置處理。 這包括用以產生綜合特性的自動化資料清理、準備和轉換。| 啟用
   驗證類型 | 選擇交叉驗證類型。|K 摺疊交叉驗證
   驗證次數 | 測試次數。 | 雙交叉驗證 
   並行| 最大並行反覆項目數量。|5
   
1. 選取 [開始]  來執行實驗。 當實驗準備開始時，畫面會出現並顯示狀態訊息。

>[!IMPORTANT]
> 準備實驗執行需要 **10-15 分鐘**的時間。 執行之後，**每個反覆項目需要 2-3 分鐘以上的時間**。  
>
> 在生產環境中，您可以先離開一下。 但是在本教學課程中，在其他項目仍在執行時，我們建議您開始探索已完成的反覆項目結果。 

##  <a name="explore-iteration-results"></a>探索反覆項目結果

當實驗進行時，該畫面會以完成時建立的不同反覆項目 (模型) 來更新 [反覆項目圖表]  和 [反覆項目清單]  ，並以計量分數進行排序。 依預設，根據所選 **AUC_weighted** 計量評分最高的模型會在清單頂端。

當您等候所有實驗反覆項目完成時，即可選取已完成反覆項目的**名稱**，來探索其效能詳細資料。 
   
以下顯示每個反覆項目所產生的圖表和執行計量，例如，精確度與召回率曲線、混淆矩陣、加權精確度分數等等。 

![執行反覆項目的詳細資料](media/tutorial-1st-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>部署模型

工作區登陸頁面中的自動化機器學習可讓您透過幾個步驟，將最佳模型部署為 Web 服務。 部署是模型的整合，因此可以根據新資料進行預測，並找出潛在的商機區域。 此實驗中對 Web 服務的部署表示金融機構現在有可反覆進行且可調整的 Web 解決方案，能識別潛在的定期存款客戶。 

執行完成後會返回 [反覆項目圖表]  和 [反覆項目清單]  詳細資料頁面。 

在此實驗內容中，根據 **AUC_weighted** 計量，**VotingEnsemble** 會被視為最佳模型。  我們會部署此模型，但提醒您部署大約需要 20 分鐘的時間才能完成。 部署程序需要幾個步驟，包括註冊模型、產生資源，以及為 Web 服務設定這些資源。

1. 選取右上角的 [部署最佳模型]  按鈕。

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

1. 移至 [Azure 入口網站](https://portal.azure.com//)。 瀏覽至您的工作區，並在左窗格的 [資產]  下，選取 [部署]  。 

1. 選取您想要刪除的部署，然後選取 [刪除]  。 

1. 選取 [繼續]  。

### <a name="delete-the-resource-group"></a>刪除資源群組

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在此自動化機器學習教學課程中，您已使用工作區登陸頁面來建立和部署分類模型。 請參閱下列文章，以了解更多資訊及接下來的步驟：

> [!div class="nextstepaction"]
> [取用 Web 服務](how-to-consume-web-service.md)

+ 深入了解[前置處理](how-to-create-portal-experiments.md#preprocess)。
+ 深入了解[資料分析](how-to-create-portal-experiments.md#profile)。
+ 深入了解[自動化機器學習](concept-automated-ml.md)。

>[!NOTE]
> 此銀行行銷資料集可在 [Creative Commons (CCO：公用網域) 授權](https://creativecommons.org/publicdomain/zero/1.0/)底下取得。 個別資料庫內容中的任何權限都是以[資料庫內容授權](https://creativecommons.org/publicdomain/zero/1.0/)為依據，並可在 [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset) 上取得。 此資料集原本位在 [UCI Machine Learning 資料庫](https://archive.ics.uci.edu/ml/datasets/bank+marketing)內。<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez and P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, June 2014.
