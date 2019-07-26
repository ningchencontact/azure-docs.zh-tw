---
title: 在入口網站中建立和探索實驗
titleSuffix: Azure Machine Learning service
description: 瞭解如何在入口網站中建立和管理自動化機器學習實驗
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: 1bfc415b2e4dbc66e2afeae73b78079fb027a60c
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358840"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>在 Azure 入口網站中建立及探索自動化機器學習服務實驗 (預覽)

 在本文中, 您將瞭解如何在不需使用任何程式碼的情況下, 建立、執行及探索自動化機器學習實驗 Azure 入口網站。 自動化機器學習服務會自動化選取最佳演算法以用於特定資料的程式, 因此您可以快速產生機器學習模型。 [深入瞭解自動化機器學習](concept-automated-ml.md)服務。

 如果您偏好更以程式碼為基礎的經驗, 您也可以使用[AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py),[在 Python 中設定自動化機器學習實驗](how-to-configure-auto-train.md)。

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* Azure Machine Learning 服務工作區。 請參閱[建立 Azure Machine Learning 服務工作區](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)。

## <a name="get-started"></a>開始使用

流覽至工作區的左窗格。 在 [撰寫 (預覽)] 區段下, 選取 [自動化 Machine Learning]。

![Azure 入口網站流覽窗格](media/how-to-create-portal-experiments/nav-pane.png)

 如果這是您第一次使用自動化 Machine Learning 進行實驗, 您會看到下列內容:

![Azure 入口網站實驗登陸頁面](media/how-to-create-portal-experiments/landing-page.png)

否則, 您會看到自動化機器學習服務儀表板, 並概述所有自動化機器學習服務實驗, 包括以 SDK 建立的測試。 在這裡, 您可以依日期、實驗名稱和執行狀態來篩選和探索您的執行。

![Azure 入口網站實驗儀表板](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>建立實驗

選取 [建立實驗] 按鈕以填入下列表單。

![建立實驗表單](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. 輸入您的實驗名稱。

1. 選取資料分析和訓練作業的計算。 您現有計算的清單可在下拉式清單中取得。 若要建立新的計算, 請遵循步驟3中的指示。

1. 選取 [建立新的計算] 按鈕以開啟下方窗格, 並設定此實驗的計算內容。

    ![建立新的實驗計算](media/how-to-create-portal-experiments/create-new-compute.png)

    欄位|描述
    ---|---
    Compute 名稱| 輸入可識別計算內容的唯一名稱。
    虛擬機器大小| 選取您計算的虛擬機器大小。
    其他設定| *最小節點*:輸入您的計算節點數目下限。 AML 計算的節點數目下限為0。 若要啟用資料分析, 您必須有一或多個節點。 <br> *節點上限*:輸入您計算的節點數目上限。 AML 計算的預設值是6個節點。

      若要開始建立新的計算, 請選取 [**建立**]。 這可能需要幾分鐘的時間。

      >[!NOTE]
      > 您的計算名稱會指出您選取/建立的計算是否已啟用程式碼*剖析*。 (如需資料分析的詳細資訊, 請參閱 7b)。

1. 為您的資料選取儲存體帳戶。 公開預覽僅支援本機檔案上傳和 Azure Blob 儲存體帳戶。

1. 選取儲存體容器。

1. 選取儲存體容器中的資料檔案, 或從您的本機電腦將檔案上傳至容器。

    ![選取用於實驗的資料檔案](media/how-to-create-portal-experiments/select-file.png)

1. 使用 [預覽] 和 [設定檔] 索引標籤, 進一步設定此實驗的資料。

    1. 在 [預覽] 索引標籤上, 指出您的資料是否包含標題, 並使用每個功能資料行中**包含**的切換按鈕, 選取定型的功能 (資料行)。

        ![資料預覽](media/how-to-create-portal-experiments/data-preview.png)

    1. 在 [設定檔] 索引標籤上, 您可以依功能來查看[資料設定檔](#profile), 以及每個的散發、類型和摘要統計資料 (mean、中位數、最大/最小值等等)。

        ![資料設定檔索引標籤](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > 如果您的計算內容**未**啟用分析, 將會出現下列錯誤訊息:*資料分析僅適用于已在執行中的計算目標*。

1. 選取定型作業類型: [分類]、[回歸] 或 [預測]。

1. 選取目標資料行。 您想要對其執行預測的資料行。

1. 針對預測:
    1. 選取時間資料行:此資料行包含要使用的時間資料。
    1. 選取預測範圍:指出模型能夠預測未來的時間單位數 (分鐘/小時/天/周/月/年)。 需要進一步預測模型, 使其變得更不精確。 [深入瞭解預測和水準預測](how-to-auto-train-forecast.md)。

1. 選擇性[高級設定]: 您可以用來更有效控制訓練作業的其他設定。

    進階設定|描述
    ------|------
    主要度量| 用來評分模型的主要度量。 [深入瞭解模型計量](how-to-configure-auto-train.md#explore-model-metrics)。
    結束準則| 符合上述任一條件時, 訓練作業會在完整完成前結束。 <br> *訓練作業時間 (分鐘)* :允許執行定型作業的時間長度。  <br> *反覆運算次數上限*:要在定型作業中測試的管線數目上限 (反覆運算)。 作業不會執行超過指定的反覆運算次數。 <br> *度量分數閾值*:所有管線的最小度量分數。 這可確保如果您有想要觸達的已定義目標計量, 則不需要花費更多時間來進行定型作業。
    預處理| 選取以啟用或停用自動化機器學習完成的前置處理。 前置處理包含自動資料清理、準備和轉換來產生綜合功能。 [深入瞭解](#preprocess)前置處理。
    驗證| 選取要在定型作業中使用的其中一個交叉驗證選項。 [深入瞭解交叉驗證](how-to-configure-auto-train.md)。
    並行| 選取您想要在使用多核心計算時使用的多核心限制。
    封鎖的演算法| 選取您想要從定型作業中排除的演算法。

   ![Advanced 設定表單](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> 如需有關欄位的詳細資訊, 請按一下資訊工具提示。

<a name="profile"></a>

### <a name="data-profiling"></a>資料分析

您可以在資料集內取得各式各樣的摘要統計資料, 以確認您的資料集是否為 ML 備妥。 若是非數值資料行, 則只會包含基本統計資料, 例如最小值、最大值和錯誤計數。 針對數值資料行, 您也可以查看其統計時間和預估分量。 具體而言, 我們的資料設定檔包括:

* **功能**: 摘要資料行的名稱。

* **設定檔**: 根據所推斷之類型的內嵌視覺效果。 例如, 字串、布林值和日期會有數值計數, 而小數 (數值) 則具有近似長條圖。 這可讓您快速瞭解資料的散發。

* **類型散發**: 資料行內類型的內建值計數。 Null 是其本身的類型, 因此此視覺效果適用于偵測奇數或遺漏值。

* **類型**: 資料行的推斷類型。 可能的值包括: 字串、布林值、日期和小數。

* **Min**: 資料行的最小值。 如果功能的類型沒有固有的順序 (例如布林值), 則會出現空白專案。

* **Max**: 資料行的最大值。 就像「最小」, 空白專案會針對具有無關類型的功能出現。

* **Count**: 資料行中遺漏和非遺漏專案的總數。

* **未遺漏計數**: 資料行中沒有遺漏的專案數。 請注意, 空字串和錯誤會被視為值, 因此它們不會參與「未遺漏計數」。

* **分量**(0.1、1、5、25、50、75、95、99和 99.9% 間隔): 每個分量的近似值, 以提供資料分佈的意義。 具有無關類型的功能會顯示空白專案。

* **Mean**: 資料行的算術平均值。 具有無關類型的功能會顯示空白專案。

* **標準差**: 資料行的標準差。 具有無關類型的功能會顯示空白專案。

* **差異**: 資料行的變異數。 具有無關類型的功能會顯示空白專案。

* **偏斜**: 資料行的偏斜度。 具有無關類型的功能會顯示空白專案。

* **峰值**: 資料行的峰度。 具有無關類型的功能會顯示空白專案。

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>先進前置處理

在設定您的實驗時, 您可以啟用 [ `Preprocess`advanced] 設定。 這麼做表示會自動執行下列資料前置處理和特徵化步驟。

|前置&nbsp;處理步驟| 描述 |
| ------------- | ------------- |
|卸除高基數或無變異數特徵|從定型和驗證集卸載這些, 包括所有遺漏值的功能、所有資料列中的相同值, 或非常高的基數 (例如, 雜湊、識別碼或 Guid)。|
|插補遺漏值|對於數值特徵, 請插補資料行中的平均值。<br/><br/>針對類別特徵, 插補最常的值。|
|產生其他特徵|針對 DateTime 特徵：年、月、日、星期幾、幾月幾日、季、第幾週、小時、分鐘、秒。<br/><br/>針對文字特徵：以 unigrams、bi 字母和三字元-克為基礎的詞彙頻率。|
|轉換和編碼 |具有少數唯一值的數值特徵會轉換成類別特徵。<br/><br/>針對低基數類別目錄執行一次熱編碼;適用于高基數、一次熱雜湊編碼。|
|Word 內嵌|文字 featurizer 會使用預先定型的模型, 將文字標記的向量轉換成句子向量。 檔中每個單字的內嵌向量會匯總在一起, 以產生檔功能向量。|
|目標編碼|針對分類功能, 會將每個類別對應至回歸問題的平均目標值, 並針對分類問題的每個類別, 將其設為類別的機率。 以頻率為基礎的加權和 k 折迭交叉驗證會套用, 以減少因稀疏資料類別而造成的對應和雜訊的過度調整。|
|文字目標編碼|若為文字輸入, 則會使用具有文字包裝的堆疊線性模型來產生每個類別的機率。|
|辨識項的權數 (WoE)|將 WoE 計算為分類資料行與目標資料行相互關聯的量值。 其計算方式是以類別與外類別機率的比率為記錄。 此步驟會輸出每個類別的一個數值特徵資料行, 而不需要明確插補遺漏值和極端值處理。|
|叢集距離|在所有數值資料行上訓練 k 表示叢集模型。  輸出 k 個新功能, 每個叢集一個新的數值功能, 其中包含每個樣本與每個叢集距心的距離。|

## <a name="run-experiment-and-view-results"></a>執行實驗並查看結果

若要執行實驗, 請按一下 [啟動]。 實驗準備程式需要幾分鐘的時間。

### <a name="view-experiment-details"></a>查看實驗詳細資料

實驗準備階段完成後, 您會看到 [執行詳細資料] 畫面。 這會提供您所建立之模型的完整清單。 根據預設, 以您的參數為最高分數的模型會在清單頂端。 當定型作業嘗試多個模型時, 它們會加入至反復專案清單和圖表中。 使用反復專案圖表來快速比較目前為止所產生之模型的計量。

定型作業可能需要一段時間, 每個管線才會完成執行。

![執行詳細資料儀表板](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>View 定型執行詳細資料

向下切入任何輸出模型, 以查看定型執行詳細資料, 例如效能計量和散發圖表。 [深入瞭解圖表](how-to-understand-automated-ml.md)。

![反復專案詳細資料](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-model"></a>部署模型

當您手邊擁有最佳模型之後, 就可以將它部署為 web 服務, 以預測新的資料。

自動化 ML 可協助您部署模型, 而不需要撰寫程式碼:

1. 您有幾個部署選項。 
    1. 如果您想要根據您為實驗設定的計量準則來部署最佳模型, 請從 [**執行詳細資料**] 頁面選取 [**部署最佳模型**]。

        ![[部署模型] 按鈕](media/how-to-create-portal-experiments/deploy-model-button.png)

    1. 如果您想要部署特定的模型反復專案, 請向下切入模型以開啟其特定的執行詳細資料頁面, 然後選取 [**部署模型**]。

        ![[部署模型] 按鈕](media/how-to-create-portal-experiments/deploy-model-button2.png)

1. 第一個步驟是在服務中註冊模型。 選取 [註冊模型], 並等候註冊程式完成。

    ![部署模型分頁](media/how-to-create-portal-experiments/deploy-model-blade.png)

1. 註冊模型之後, 您將能夠下載評分腳本 (scoring.py) 和環境腳本 (condaEnv. yml), 以在部署期間使用。

1. 下載評分腳本和環境腳本時, 請移至左側流覽窗格的 [**資產**] 分頁, 然後選取 [**模型**]。

    ![流覽窗格模型](media/how-to-create-portal-experiments/nav-pane-models.png)

1. 選取您註冊的模型, 然後選取 [建立映射]。

    您可以依其描述來識別模型, 其中會包含執行識別碼、反復專案編號, 格式如下: *< Run_ID > _ < Iteration_number > _Model*

    ![型號:建立映像](media/how-to-create-portal-experiments/model-create-image.png)

1. 輸入影像的名稱。 
1. 選取 [評分檔案] 方塊旁的 **[流覽]** 按鈕, 上傳您先前下載的評分檔案 (scoring.py)。

1. 選取 [Conda 檔案] 方塊旁的 **[流覽]** 按鈕, 上傳您先前下載的環境檔案 (condaEnv. yml)。

    您可以使用自己的評分腳本和 conda 檔, 以及上傳其他檔案。 [深入瞭解評分腳本](how-to-deploy-and-where.md#script)。

      >[!Important]
      > 檔案名的長度必須是32個字元, 而且必須以英數位元開頭和結尾。 可能包含虛線、底線、點和之間的英數位元。 不允許使用空格。

    ![建立映像](media/how-to-create-portal-experiments/create-image.png)

1. 選取 [建立] 按鈕以開始建立映射。 這需要幾分鐘的時間才能完成, 完成後, 您會在頂端列看到一則訊息。
1. 移至 [映射] 索引標籤, 勾選您要部署之映射旁的核取方塊, 然後選取 [建立部署]。 [深入瞭解部署](how-to-deploy-and-where.md)。

    有2個選項可供部署。
     + Azure 容器實例 (ACI)-這可用於測試用途, 而不是大規模的營運部署。 請務必至少針對_CPU 保留容量_填入一個核心的值, 並至少為_記憶體保留容量_填入一個 gb
     + Azure Kubernetes Service (AKS))-這個選項適用于大規模部署。 您必須準備好以 AKS 為基礎的計算。

     ![圖像建立部署](media/how-to-create-portal-experiments/images-create-deployment.png)

1. 完成後，請選取 [建立]。 部署模型可能需要幾分鐘的時間, 每個管線才會完成執行。

1. 就這麼容易！ 您擁有可運作的 web 服務來產生預測。

## <a name="next-steps"></a>後續步驟

* [深入瞭解自動化機器學習](concept-automated-ml.md)和 Azure Machine Learning。
* [瞭解自動化的機器學習結果](how-to-understand-automated-ml.md)。
* [瞭解如何使用 web 服務](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service)。
