---
title: 建立和瀏覽入口網站中的實驗
titleSuffix: Azure Machine Learning service
description: 了解如何建立和管理自動化的機器學習服務入口網站中的實驗
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: a2a281fda9272fb794692becb0ca08f3cf791458
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65989954"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>建立並探索自動化的機器學習服務實驗，在 Azure 入口網站 （預覽）

 在本文中，您會學習如何建立、 執行和探索自動化的機器學習服務實驗，而單一一行程式碼不需要在 Azure 入口網站。 自動化的機器學習服務會自動選取最佳的演算法，用於您特定的資料，您可以快速產生機器學習服務模型的程序。 [深入了解自動化的機器學習服務](concept-automated-ml.md)。

 如果您偏好更多的程式碼架構體驗，您也可以[設定自動化的機器學習服務實驗以 Python](how-to-configure-auto-train.md)具有[Azure 機器學習服務 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* Azure Machine Learning 服務工作區。 請參閱[建立 Azure 機器學習服務工作區](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)。

## <a name="get-started"></a>開始使用

瀏覽至您的工作區的左窗格。 您可以選取 [自動化的機器學習服務撰寫 （預覽）] 區段下。

![Azure 入口網站的瀏覽窗格](media/how-to-create-portal-experiments/nav-pane.png)

 如果這是您第一次執行與自動化的機器學習服務的任何實驗，您會看到下列項目：

![Azure 入口網站的實驗登陸頁面](media/how-to-create-portal-experiments/landing-page.png)

否則，您會看到您自動化機器學習服務儀表板的所有自動化機器學習服務實驗，包括使用 SDK 的概觀。 您可以在這裡篩選和日期來瀏覽您的執行、 實驗名稱，並執行狀態。

![Azure 入口網站的實驗儀表板](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>建立實驗

選取 [建立實驗] 按鈕來填入下列表單。

![建立實驗表單](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. 輸入您的實驗名稱。

1. 選取的資料分析和訓練作業的計算。 在下拉式清單中使用一份您現有的計算。 若要建立新的計算，請依照下列步驟 3 中的指示。

1. 選取 建立新的 計算 按鈕，開啟下方窗格中，並設定您的計算內容，這項實驗。

    ![建立新實驗的計算](media/how-to-create-portal-experiments/create-new-compute.png)

    欄位|描述
    ---|---
    計算名稱| 輸入可識別您的計算內容的唯一名稱。
    虛擬機器大小| 選取您的計算的虛擬機器大小。
    其他設定| *最小節點*:輸入您的計算節點數目下限。 適用於 AML 計算的節點數目下限為 0。 若要啟用程式碼剖析資料，您必須具有 1 或多個節點。 <br> *最大節點*:輸入您的計算節點數目上限。 預設值為 6 個節點的 AML 計算。

      若要開始建立新的計算，請選取**建立**。 這可能需要一些時間。

      >[!NOTE]
      > 計算名稱會指出您選取/建立的計算是否*啟用分析*。 （資料分析，請參閱 7b 如需詳細資訊。）

1. 選取您的資料的儲存體帳戶。 公開預覽僅支援本機檔案上傳和 Azure Blob 儲存體帳戶。

1. 選取的儲存體容器。

1. 從您的儲存體容器，請選取資料檔案，或上傳至容器的檔案從本機電腦。

    ![選取實驗資料的檔案](media/how-to-create-portal-experiments/select-file.png)

1. 您可以使用預覽和設定檔索引標籤來進一步設定您的資料，這項實驗。

    1. [預覽] 索引標籤，表示是否您的資料包含標頭，然後選取用於定型的特徵 （資料行）**包含**切換按鈕，在每個特徵資料行中。

        ![資料預覽](media/how-to-create-portal-experiments/data-preview.png)

    1. 在 [設定檔] 索引標籤中，您可以檢視[資料設定檔](#profile)的功能，以及發佈、 類型和摘要統計資料 （平均值、 中間值、 max/min、 等等） 的每個。

        ![資料設定檔 索引標籤](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > 如果您的計算內容，將會出現下列錯誤訊息**不**啟用分析：*資料分析 」 僅適用於已經執行的計算目標*。

1. 選取的訓練工作類型： 分類、 迴歸，或預測。

1. 選取目標資料行。 您想要在執行預測資料行。

1. 預測：
    1. 選取時間資料行：此資料行包含要使用的時間資料。
    1. 選取 預測時間範圍：指出多少時間單位 （分鐘/小時/天/週/月/年） 將模型能夠預測未來。 進一步說明模型需要預測未來，它會變成不準確。 [進一步了解預測和預測水平分割](https://docs.microsoft.com/azure/machine-learning/service/how-to-auto-train-forecast#configure-experiment)。

1. （選擇性）進階設定： 您可以用來進一步控制訓練作業的其他設定。

    進階設定|描述
    ------|------
    主要的計量| 用來評分模型的主要度量。 [深入了解模型計量](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#explore-model-metrics)。
    允出準則| 當下列任何準則符合時，則會在完整完成之前結束訓練作業。 <br> *定型作業時間 （分鐘）* :允許執行定型作業的時間長度。  <br> *最大數目的反覆項目*:若要測試訓練作業中的管線 （反覆項目） 的最大數目。 作業不會執行多個指定的反覆運算次數。 <br> *計量分數臨界值*:所有管線的最小度量分數。 這可確保如果您有您想要達到定義的目標度量時，您執行不超過必要訓練作業花更多時間。
    前置處理| 選取此選項，以啟用或停用由自動化的機器學習服務在前置處理。 前置處理包含自動資料清理、 準備，以及轉換來產生綜合的功能。 [深入了解前置處理](#preprocess)。
    驗證| 選取其中一個用於訓練作業的交叉驗證選項。 [深入了解交叉驗證](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#cross-validation-split-options)。
    並行| 選取您想要使用多核心的計算時，使用多核心限制。
    已封鎖的演算法| 選取您想要排除訓練作業的演算法。

   ![進階的設定表單](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> 如需有關欄位的詳細資訊，請按一下 [資訊工具提示]。

<a name="profile"></a>

### <a name="data-profiling"></a>資料分析

您可以在您的資料集，來確認您的資料集是否可供 ML 的取得許多不同的摘要統計資料。 對於非數字資料行，其中包括只可以使用基本統計資料，例如 min、 max 和錯誤計數。 針對數值資料行，您也可以檢閱其統計分鐘的時間和預估的分位數。 具體來說，我們的資料設定檔包含：

* **功能**： 已摘要列出資料行的名稱。

* **設定檔**： 推斷的類型為基礎的內嵌視覺效果。 例如，字串、 布林值，以及日期會有值計數，而小數位數 （數值） 有近似的長條圖。 這可讓您快速了解資料的分佈。

* **輸入散發**: 內建值的計數資料行內的類型。 Null 是他們自己的型別，因此這個視覺效果是適用於偵測奇數或遺漏的值。

* **型別**： 資料行的推斷的型別。 可能的值包括： 字串、 布林值、 日期及小數位數。

* **最小**： 資料行的最小值。 空白的項目會顯示其型別沒有固有順序 （例如布林值） 的功能。

* **最大**： 資料行的最大值。 「 分鐘 」，例如空白項目會出現不相關的類型使用的功能。

* **計數**： 遺漏的和非遺漏資料行中的項目總數。

* **未遺漏計數**： 資料行中不會遺失的項目數目。 請注意，空白字串和錯誤會被視為值，因此它們不會導致 「 未遺漏計數 」。

* **分量**（位於 0.1、 1、 5、 25、 50、 75、 95、 99 和高達 99.9%的時間間隔）： 在提供的資料分佈的每個分位數近似的值。 空白項目會出現不相關的類型使用的功能。

* **表示**: 資料行的算術平均值。 空白項目會出現不相關的類型使用的功能。

* **標準差**： 資料行的標準差。 空白項目會出現不相關的類型使用的功能。

* **變異數**： 資料行的變異數。 空白項目會出現不相關的類型使用的功能。

* **偏態**： 資料行的偏態。 空白項目會出現不相關的類型使用的功能。

* **峰態**： 資料行的峰值。 空白項目會出現不相關的類型使用的功能。

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>進階前置處理

在設定您的實驗時，您可以啟用 [進階] 設定`Preprocess`。 這就表示會自動執行下列的資料前置處理和特徵化步驟。

|前置處理&nbsp;步驟| 描述 |
| ------------- | ------------- |
|卸除高基數或無變異數特徵|卸除從定型和驗證集，包括功能的遺漏，跨所有資料列，或具有極高基數 （例如，雜湊、 識別碼或 Guid） 的相同值的所有值。|
|插補遺漏值|針對數值特徵，推算與資料行中值的平均值。<br/><br/>如需類別的功能，推算最常見的值。|
|產生其他特徵|針對 DateTime 特徵：年、月、日、星期幾、幾月幾日、季、第幾週、小時、分鐘、秒。<br/><br/>針對文字特徵：根據單字母組、 雙字母組和三字元字母組詞彙頻率。|
|轉換及進行編碼 |只有少量唯一值的數值特徵轉換成類別特徵。<br/><br/>One-hot 編碼分類; 較低的基數為執行高基數，一個為經常性存取-雜湊編碼。|
|字組內嵌項目|文字 featurizer 將向量的文字語彙基元轉換成句子向量使用預先定型的模型。 文件中的每個字組內嵌向量彙總在一起以產生文件功能向量。|
|目標編碼|類別的功能，對應每個分類與迴歸問題，以及每個類別分類問題中的類別機率的平均的目標值。 以頻率為基礎的加權和 k 摺疊交叉驗證會套用到透過調整對應和疏鬆資料類別所造成的雜訊減少。|
|文字目標版本編碼方式|文字輸入封包的字堆疊線性模型用來產生每個類別的機率。|
|辨識項 （事後懊悔） 的重量|計算相互關聯的目標資料行的類別資料行的量值的事後懊悔。 它的計算方式的記錄檔中類別與類別外的機率的比率。 此步驟會輸出每個類別的一個數值特徵資料行，並不需要明確推算遺漏值和極端值處理方式。|
|群集距離|定型的 k-means 群集模型的所有數值資料行。  輸出 k 的新功能，一項新的數字功能，每個叢集，其中包含每個範例，以每個群集的距心的距離。|

## <a name="run-experiment-and-view-results"></a>執行實驗並檢視結果

若要執行實驗，按一下 啟動。 實驗準備程序需要幾分鐘的時間。

### <a name="view-experiment-details"></a>檢視實驗的詳細資料

完成這項實驗中準備階段之後，您會看到 [執行詳細資料] 畫面。 這可讓您建立之模型的完整清單。 根據預設，最高評分之模型所根據您的參數清單的頂端。 定型作業嘗試出更多的模型時，它們會加入至反覆項目清單和圖表。 若要取得到目前為止所產生的模型計量的快速比較使用反覆項目圖表。

定型作業可能需要一段時間，每個管線完成執行。

![執行詳細資料的儀表板](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>檢視訓練執行詳細資料

向下切入任何的輸出模型，以查看 定型執行的詳細資訊，例如效能計量和發佈的圖表。 [深入了解圖表](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments#understanding-automated-ml-charts)。

![反覆項目詳細資料](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-model"></a>部署模型

一旦您手邊有最佳的模型，就可以將它部署為 web 服務來預測新資料。

自動化的 ML 可協助您部署模型，而不需要撰寫程式碼：

1. 您有幾個部署選項。 
    1. 如果您想要將計量準則為基礎的最佳模型部署為此實驗中，選取**部署的最佳模型**從**執行詳細資料**頁面。

        ![部署模型按鈕](media/how-to-create-portal-experiments/deploy-model-button.png)

    1. 如果您想要部署特定模型的反覆項目，在向下切入模式以開啟其特定的執行詳細資料頁面，然後選取**部署模型**。

        ![部署模型按鈕](media/how-to-create-portal-experiments/deploy-model-button2.png)

1. 第一個步驟是在服務中註冊的模型。 選取 註冊模型 」，並等待完成註冊程序。

    ![部署模型 刀鋒視窗](media/how-to-create-portal-experiments/deploy-model-blade.png)

1. 一旦註冊模型，您可以下載評分指令碼 (scoring.py) 和環境指令碼 (condaEnv.yml)，以在部署期間使用。

1. 當下載評分指令碼和環境的指令碼時，請移至**資產**刀鋒視窗的左側的導覽窗格，然後選取**模型**。

    ![瀏覽窗格中的模型](media/how-to-create-portal-experiments/nav-pane-models.png)

1. 選取您註冊時，該模型，然後選取 [建立映像]。

    您可以透過它的描述，其中包含執行的識別碼，也就是反覆項目數目，以下列格式來識別模型： *< Run_ID > _ < Iteration_number > （_m)*

    ![模型：建立映像](media/how-to-create-portal-experiments/model-create-image.png)

1. 輸入映像的名稱。 
1. 選取 **瀏覽**上傳您先前下載的評分檔案 (scoring.py) 評分檔案 方塊旁的按鈕。

1. 選取 **瀏覽**上傳您先前下載的環境檔案 (condaEnv.yml) Conda 檔案 方塊旁的按鈕。

    您可以使用您自己的計分指令碼和 conda 檔案，以及上傳其他檔案。 [深入了解計分指令碼](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#script)。

      >[!Important]
      > 檔案名稱必須是在 32 個字元與開頭與結尾必須使用英數字元。 可能包含連字號、 底線、 點和之間的英數字元。 不允許有空格。

    ![建立映像](media/how-to-create-portal-experiments/create-image.png)

1. 選取 建立 按鈕以啟動 建立映像。 這需要幾分鐘才能完成，完成後，您會看到一則訊息，在頂端列中。
1. 移至 「 映像 」 索引標籤，檢查您想要部署的映像旁邊的核取方塊，選取 [建立部署]。 [深入了解部署](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)。

    有 2 個選項進行部署。
     + Azure 容器執行個體 (ACI)-這使用更多用於測試用途，而不是大規模運作的部署。 請確定至少一個核心的值填滿_CPU 保留容量_，和至少 1 gb (GB) 為_記憶體保留容量_
     + Azure Kubernetes Service (AKS))-這個選項是用於大規模的部署。 您必須已準備好 AKS 基礎計算。

     ![映像：建立部署](media/how-to-create-portal-experiments/images-create-deployment.png)

1. 完成後，請選取 [建立]  。 部署模型可能需要幾分鐘的時間才能完成執行每個管線。

1. 就這麼簡單！ 您有操作的 web 服務來產生預測。

## <a name="next-steps"></a>後續步驟

* [深入了解自動化的機器學習服務](concept-automated-ml.md)和 Azure Machine Learning。
* [了解如何使用 web 服務](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service)。
