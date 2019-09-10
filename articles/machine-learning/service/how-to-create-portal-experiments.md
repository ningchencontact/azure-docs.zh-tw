---
title: 使用 Azure 的自動化 ML 介面來定型 & 部署模型
titleSuffix: Azure Machine Learning service
description: 在 Azure 入口網站中建立、管理和部署自動化的機器學習實驗
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 08/02/2019
ms.openlocfilehash: 79632a2b5862538ef702cec01a60aada14d8dbce
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860483"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>在 Azure 入口網站中建立、探索自動化機器學習服務實驗並加以部署（預覽）

 在本文中，您將瞭解如何在不使用任何程式碼的情況下，于 Azure 入口網站中建立、探索及部署自動化的機器學習實驗。 自動化機器學習服務會自動化選取最佳演算法以用於特定資料的程式, 因此您可以快速產生機器學習模型。 [深入瞭解自動化機器學習](concept-automated-ml.md)服務。

 如果您偏好更以程式碼為基礎的經驗, 您也可以使用[AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py),[在 Python 中設定自動化機器學習實驗](how-to-configure-auto-train.md)。


## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* Azure Machine Learning 服務工作區。 請參閱[建立 Azure Machine Learning 服務工作區](how-to-manage-workspace.md)。

## <a name="get-started"></a>開始使用

流覽至工作區的左窗格。 在 [撰寫（預覽）] 區段下，選取 [自動化 Machine Learning]。

![Azure 入口網站導覽窗格](media/how-to-create-portal-experiments/nav-pane.png)

 如果這是您第一次執行任何實驗，您會看到 [**歡迎使用自動 Machine Learning** ] 畫面。 

否則，您會看到**自動化機器學習**服務儀表板，並概述所有自動化機器學習服務實驗，包括以 SDK 建立的測試。 在這裡, 您可以依日期、實驗名稱和執行狀態來篩選和探索您的執行。

您也可以從[工作區登陸頁面（預覽）](https://ml.azure.com)存取自動化的 Machine Learning。

## <a name="create-an-experiment"></a>建立實驗

選取 [**建立實驗**]，並填入 [**建立新的自動化機器學習服務實驗**] 表單。

1. 輸入唯一的實驗名稱。

1. 選取資料分析和訓練作業的計算。 您現有計算的清單可在下拉式清單中取得。 若要建立新的計算, 請遵循步驟3中的指示。

1. 選取 [**建立新的計算**]，以設定此實驗的計算內容。

    欄位|描述
    ---|---
    Compute 名稱| 輸入可識別您計算內容的唯一名稱。
    虛擬機器大小| 為您的計算選取虛擬機器大小。
    其他設定| 最小節點：輸入您的計算節點數目下限。 AML 計算的節點數目下限為0。 若要啟用資料分析, 您必須有一或多個節點。 <br> 最大節點：輸入您計算的節點數目上限。 AML 計算的預設值是6個節點。

      選取 [建立]。 建立新的計算可能需要幾分鐘的時間。

      >[!NOTE]
      > 您的計算名稱會指出您選取/建立的計算是否已啟用程式碼*剖析*。 (如需資料分析的詳細資訊, 請參閱 7b)。

1. 為您的資料選取儲存體帳戶。 

1. 選取儲存體容器。

1. 選取儲存體容器中的資料檔案, 或從您的本機電腦將檔案上傳至容器。 公開預覽僅支援本機檔案上傳和 Azure Blob 儲存體帳戶。
    >[!Important]
    > 定型資料的需求：
    >* 資料必須是表格式格式。
    >* 您想要預測的值（目標資料行）必須存在於資料中。

    [![選取資料檔案](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. 使用 [預覽] 和 [設定檔] 索引標籤, 進一步設定此實驗的資料。

    1. 在 [**預覽**] 索引標籤上，指出您的資料是否包含標題，並使用每個功能資料行中**包含**的切換按鈕，選取定型的功能（資料行）。

    1. 在 [**設定檔**] 索引標籤上，您可以依功能來查看[資料設定檔](#profile)，以及每個的散發、類型和摘要統計資料（mean、中位數、最大/最小值等等）。

        >[!NOTE]
        > 如果您的計算內容**未**啟用分析, 將會出現下列錯誤訊息:*資料分析僅適用于已在執行中的計算目標*。

1. 選取定型作業類型: [分類]、[回歸] 或 [預測]。

1. 選取目標資料行;這是您想要對其執行預測的資料行。

1. 針對預測:
    1. 選取時間資料行:此資料行包含要使用的時間資料。

    1. 選取預測範圍:指出模型能夠預測未來的時間單位數 (分鐘/小時/天/周/月/年)。 需要進一步預測模型, 使其變得更不精確。 [深入瞭解預測和水準預測](how-to-auto-train-forecast.md)。

1. 選擇性[高級設定]: 您可以用來更有效控制訓練作業的其他設定。

    進階設定|描述
    ------|------
    主要計量| 用來評分模型的主要度量。 [深入瞭解模型計量](how-to-configure-auto-train.md#explore-model-metrics)。
    允出準則| 符合上述任一條件時, 訓練作業會在完整完成前結束。 <br> 訓練作業時間 (分鐘)：允許執行定型作業的時間長度。  <br> 反覆運算的次數上限：要在定型作業中測試的管線數目上限 (反覆運算)。 作業不會執行超過指定的反覆運算次數。 <br> *度量分數閾值*:所有管線的最小度量分數。 這可確保如果您有想要觸達的已定義目標計量, 則不需要花費更多時間來進行定型作業。
    前置處理| 選取以啟用或停用自動化機器學習完成的前置處理。 前置處理包含自動資料清理、準備和轉換來產生綜合功能。 [深入瞭解](#preprocess)前置處理。
    驗證| 選取要在定型作業中使用的其中一個交叉驗證選項。 [深入瞭解交叉驗證](how-to-configure-auto-train.md)。
    並行| 選取您想要在使用多核心計算時使用的多核心限制。
    封鎖的演算法| 選取您想要從定型作業中排除的演算法。

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>資料分析 & 摘要統計資料

您可以在資料集內取得各式各樣的摘要統計資料, 以確認您的資料集是否為 ML 備妥。 若是非數值資料行, 則只會包含基本統計資料, 例如最小值、最大值和錯誤計數。 針對數值資料行, 您也可以查看其統計時間和預估分量。 具體而言, 我們的資料設定檔包括:

>[!NOTE]
> 具有無關類型的功能會顯示空白專案。

統計資料|描述
------|------
功能| 摘要資料行的名稱。
個人資料| 以推斷的型別為基礎的內嵌視覺效果。 例如, 字串、布林值和日期會有數值計數, 而小數 (數值) 則具有近似長條圖。 這可讓您快速瞭解資料的散發。
類型散發| 資料行內類型的內建數值計數。 Null 是其本身的類型, 因此此視覺效果適用于偵測奇數或遺漏值。
Type|推斷的資料行類型。 可能的值包括: 字串、布林值、日期和小數。
Min| 資料行的最小值。 如果功能的類型沒有固有的順序 (例如布林值), 則會出現空白專案。
max| 資料行的最大值。 
Count| 資料行中遺漏和不遺失的專案總數。
未遺漏計數| 資料行中未遺漏的專案數。 空字串和錯誤會被視為值，因此它們不會參與「未遺漏計數」。
分量| 每個分量的近似值可讓您瞭解資料的分佈。
平均值| 資料行的算術平均值或平均值。
標準差| 此資料行資料的散佈或變化量的測量單位。
Variance| 量值分佈在此資料行中的值是來自其平均值。 
偏度| 測量此資料行的資料與一般散發的不同之處。
峰度| 測量此資料行的資料與一般散發的比較程度。

<a name="preprocess"></a>

## <a name="advanced-preprocessing-options"></a>Advanced 前置處理選項

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

選取 [**啟動**] 以執行您的實驗。 實驗準備程式需要幾分鐘的時間。

### <a name="view-experiment-details"></a>檢視實驗詳細資料

實驗準備階段完成後，您會看到 [執行詳細資料] 畫面開始填入。 此畫面會提供您所建立之模型的完整清單。 根據預設，以所選計量為最高分數的模型會在清單頂端。 當定型作業嘗試多個模型時, 它們會加入至反復專案清單和圖表中。 使用反復專案圖表來快速比較目前為止所產生之模型的計量。

定型作業可能需要一段時間, 每個管線才會完成執行。

[![執行詳細資料儀表板](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>View 定型執行詳細資料

向下切入任何輸出模型, 以查看定型執行詳細資料, 例如效能計量和散發圖表。 [深入瞭解圖表](how-to-understand-automated-ml.md)。

![反復專案詳細資料](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-your-model"></a>部署模型

當您手邊擁有最佳模型之後, 就可以將它部署為 web 服務, 以預測新的資料。

自動化 ML 可協助您部署模型, 而不需要撰寫程式碼:

1. 您有幾個部署選項。 

    + 選項 1：若要部署最佳模型（根據您所定義的度量準則），請從 [執行詳細資料] 頁面選取 [部署最佳模型]。

    + 選項 2：若要從此實驗部署特定模型反復專案，請向下切入模型以開啟其 [執行詳細資料] 頁面，然後選取 [部署模型]。
1. 填入 [**部署模型**] 窗格，

    欄位| 值
    ----|----
    部署名稱| 為您的部署輸入唯一的名稱。
    部署描述| 輸入描述，以更清楚地識別此部署的用途。
    評分指令碼| 自動產生或上傳您自己的評分檔案。 [深入瞭解評分腳本](how-to-deploy-and-where.md#script)
    環境指令碼| 自動產生或上傳您自己的環境檔案。
    >[!Important]
    > 檔案名的長度必須是32個字元, 而且必須以英數位元開頭和結尾。 可能包含虛線、底線、點和之間的英數位元。 不允許使用空格。

1. 選取 [部署]。 部署可能需要大約20分鐘的時間才能完成。

    當部署成功完成時，會出現下列訊息。

    ![部署完成](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png) 

現在您已有可運作的 web 服務來產生預測！

## <a name="next-steps"></a>後續步驟

* 嘗試[使用 Azure Machine Learning 建立您的第一個自動化 ML 實驗](tutorial-first-experiment-automated-ml.md)的端對端教學課程。 
* [深入瞭解自動化機器學習](concept-automated-ml.md)和 Azure Machine Learning。
* [瞭解自動化的機器學習結果](how-to-understand-automated-ml.md)。
* [瞭解如何使用 web 服務](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service)。
