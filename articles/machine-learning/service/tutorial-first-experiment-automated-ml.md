---
title: 建立您的第一個自動化機器學習實驗
titleSuffix: Azure Machine Learning service
description: 瞭解如何在 Azure 入口網站中使用自動化機器學習來定型和部署分類模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 07/23/2019
ms.openlocfilehash: 7ef19db472b30d82f14a5dd650cb8f4cb1f3ed3a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990081"
---
# <a name="tutorial-use-automated-machine-learning-to-train-and-deploy-your-first-classification-model-preview"></a>教學課程：使用自動化機器學習來定型及部署您的第一個分類模型 (預覽)

在本教學課程中, 您將瞭解如何在 Azure 入口網站中建立第一個自動化機器學習實驗。 這個範例會建立分類模型, 以預測用戶端是否會訂閱銀行的詞彙存款。

藉由使用服務和 Azure 入口網站的自動化機器學習功能, 您就可以開始自動化機器學習程式。 系統會為您完成演算法選取和超參數微調。 自動化機器學習技術會逐一查看演算法和超參數的多種組合, 直到根據您的準則找到最佳模型為止, 而不需要撰寫任何一行程式碼。

在本教學課程中，您會了解下列工作：

> [!div class="checklist"]
> * 設定 Azure Machine Learning 服務工作區。
> * 建立實驗。
> * 自動定型分類模型。
> * 查看定型執行詳細資料。
> * 部署模型。

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://aka.ms/AMLFree)。

* **Bankmarketing_train .csv**資料檔案。 [下載](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)。

## <a name="create-a-workspace"></a>建立工作區

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-an-experiment"></a>建立實驗

1. 移至工作區的左窗格。 選取 [**撰寫 (預覽)** ] 區段下的 [**自動化機器學習**服務]。

    ![Azure 入口網站流覽窗格](media/tutorial-1st-experiment-automated-ml/nav-pane.png)

    因為這是您第一次使用自動化 Machine Learning 的實驗, 所以您會看到 [**歡迎使用自動 Machine Learning** ] 畫面。 

1. 選取 [**建立實驗**]。 然後輸入**我的第 automl-實驗**作為實驗名稱。

1. 選取 [**建立新的計算**], 並設定此實驗的計算內容。

    欄位| 值
    ---|---
    Compute 名稱| 輸入可識別計算內容的唯一名稱。 在此範例中, 我們使用**automl-compute**。
    虛擬機器大小| 選取您計算的虛擬機器大小。 我們會使用**Standard_DS12_V2**。
    其他設定| *最小節點*:1. 若要啟用資料分析, 您必須有一或多個節點。 <br> *節點上限*:6. 

    若要建立新的計算, 請選取 [**建立**]。 這需要幾分鐘的時間。 

    建立完成時, 從下拉式清單中選取新的計算, 然後選取 **[下一步]** 。

1. 在本教學課程中, 我們會使用以您的新計算建立的預設儲存體帳戶和容器。 它們會自動填入表單。

1. 選取 [**上傳**], 然後從本機電腦選擇**bankmarketing_train** , 以將它上傳到預設容器。 公開預覽僅支援本機檔案上傳和 Azure Blob 儲存體帳戶。 當上傳完成時, 請從清單中選取檔案。 

    [![選取資料檔案](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. [**預覽**] 索引標籤可讓我們進一步設定此實驗的資料。

    在 [**預覽**] 索引標籤上, 表示資料包含頁首。 服務預設會包含所有用於定型的功能 (資料行)。 在此範例中, 請向右滾動, 並**忽略** **day_of_week**功能。

    ![預覽索引標籤設定](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)


    >[!NOTE]
    > 具有零個最少節點的計算無法使用資料分析。

1. 選取 [**分類**] 做為預測工作。

1. 選取 [ **y** ] 做為目標資料行, 我們想要在其中執行預測。 此資料行指出用戶端是否已訂閱詞彙存款。

1. 展開 [ **Advanced Settings** ], 並填入欄位, 如下所示。

    進階設定|值
    ------|------
    主要度量| AUC_weighted 
    結束準則| 符合上述任一條件時, 訓練作業會在完整完成前結束: <br> *訓練作業時間 (分鐘)* :5  <br> *反覆運算次數上限*:10 
    預處理| 啟用由自動化機器學習完成的前置處理。 這包括自動資料清理、準備和轉換來產生綜合功能。
    驗證| 選取 [K-折迭交叉驗證] 和 [ **2** ] 作為交叉驗證的數目。 
    並行| 針對 [最大並行反覆運算數] 選取 [ **5** ]。

   >[!NOTE]
   > 在此實驗中, 我們不會設定 [每次反覆運算的度量] 或 [最大核心數] 閾值。 我們也不會封鎖演算法進行測試。

1. 選取 [**啟動**] 以執行實驗。

   當實驗啟動時, 您會在頂端看到具有下列狀態的空白 [**執行詳細資料**] 畫面。 

      ![執行準備工作](media/tutorial-1st-experiment-automated-ml/run-preparing.png)
      
實驗準備程式需要幾分鐘的時間。 當處理常式完成時, 狀態訊息會變更為 [**執行**中]。

##  <a name="view-experiment-details"></a>查看實驗詳細資料

當實驗進行時, [**執行詳細資料**] 畫面會以執行的不同反復專案 (模型) 來更新反覆運算圖表和清單。 [反復專案] 清單依度量分數排序。 根據預設, 以我們的**AUC_weighted**度量為最高分數的模型會在清單頂端。

>[!TIP]
> 定型作業需要幾分鐘的時間, 每個管線才會完成執行。

[![執行詳細資料儀表板](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>部署模型

在此實驗中, 會根據**AUC_weighted**計量將**VotingEnsemble**視為最佳模型。 藉由使用 Azure 入口網站中的自動化機器學習, 我們可以將此模型部署為 web 服務, 以預測新的資料。 

1. 在 [**執行詳細資料**] 頁面上, 選取 [**部署最佳模型**] 按鈕。

1. 填入 [**部署最佳模型**] 窗格, 如下所示:

    欄位| 值
    ----|----
    部署名稱| 我的 automl-部署
    部署描述| 我的第一個自動化機器學習實驗部署
    計分腳本| 自動產生
    環境腳本| 自動產生
    
1. 選取 [部署]。 部署大約需要20分鐘的時間才能完成。

    當部署成功完成時, 會出現下列訊息:

    ![部署完成](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png)
    
    就這麼容易！ 您擁有可運作的 web 服務來產生預測。

## <a name="clean-up-resources"></a>清除資源

部署檔案比資料和實驗檔案大, 因此儲存的成本更高。 僅刪除部署檔案以將成本降到最低, 或者, 如果您想要保留您的工作區和實驗檔案。 否則, 如果您不打算使用任何檔案, 請刪除整個資源群組。  

### <a name="delete-the-deployment-instance"></a>刪除部署實例

如果您想要保留資源群組和工作區以進行其他教學課程和探索, 請只從 Azure 入口網站刪除部署實例。 

1. 移至左側的 [**資產**] 窗格, 然後選取 [**部署**]。 

1. 選取您想要刪除的部署, 然後選取 [**刪除**]。 

1. 選取 [**繼續**]。

### <a name="delete-the-resource-group"></a>刪除資源群組

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在此自動化機器學習教學課程中, 您使用了 Azure 入口網站來建立和部署分類模型。 如需詳細資訊和後續步驟, 請參閱下列文章:

+ 瞭解如何[使用 web 服務](how-to-consume-web-service.md)。
+ 深入瞭解[前置處理](how-to-create-portal-experiments.md#preprocess)。
+ 深入瞭解[資料分析](how-to-create-portal-experiments.md#profile)。
+ 深入瞭解[自動化機器學習](concept-automated-ml.md)服務。

>[!NOTE]
> 此銀行行銷資料集可在[創意 Commons 下取得 (CCO:公用網域) 授權](https://creativecommons.org/publicdomain/zero/1.0/)。 資料庫的個別內容中的任何許可權都是以[資料庫內容](https://creativecommons.org/publicdomain/zero/1.0/)授權為依據, 並可在[Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset)上取得。 此資料集原本是在[UCI Machine Learning 資料庫](https://archive.ics.uci.edu/ml/datasets/bank+marketing)內提供。<br><br>
> 請引用下列工作: <br> [Moro et al, 2014]Moro、Cortez 和 Rita。 以資料為導向的方法, 預測銀行電話銷售的成功。 決策支援系統, Elsevier, 62:22-31 年6月2014日。
