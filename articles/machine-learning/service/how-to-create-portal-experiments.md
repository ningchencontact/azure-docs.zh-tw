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
ms.openlocfilehash: 8b6d7f791300a970e71fda4f1d56354a45d07afd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029891"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>建立並探索自動化的機器學習服務實驗，在 Azure 入口網站 （預覽）

 在本文中，您會學習如何建立、 執行和探索自動化的機器學習服務實驗，而單一一行程式碼不需要在 Azure 入口網站。 自動化的機器學習服務會自動選取最佳的演算法，用於您特定的資料，您可以快速產生機器學習服務模型的程序。 [深入了解自動化的機器學習服務](https://docs.microsoft.com/azure/machine-learning/service/concept-automated-ml)。

 如果您偏好更多的程式碼基礎經驗，您也可以[設定自動化的機器學習服務實驗以 Python](how-to-configure-auto-train.md)具有[Azure 機器學習服務 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* Azure Machine Learning 服務工作區。 請參閱[建立 Azure 機器學習服務工作區](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)。

## <a name="get-started"></a>開始使用

瀏覽至您的工作區的左窗格。 您可以選取 [自動化的機器學習服務撰寫 （預覽）] 區段下。

![Azure 入口網站的瀏覽窗格](media/how-to-create-portal-experiments/nav-pane.png)

 如果這是您第一次執行與自動化的機器學習服務的任何實驗，您會看到下列項目：

![Azure 入口網站的實驗登陸頁面](media/how-to-create-portal-experiments/landing-page.png)

否則，您會看到自動化機器學習服務儀表板的所有自動化機器學習服務實驗，並執行，包括執行使用 SDK 的概觀。 您可以在這裡篩選和日期來瀏覽您的執行、 實驗名稱，並執行狀態。

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
    Compute 名稱| 輸入可識別您的計算內容的唯一名稱。
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
    允出準則| 當下列任何準則符合時，則會在完整完成之前結束訓練作業。 <br> *定型作業時間 （分鐘）*:允許執行定型作業的時間長度。  <br> *最大數目的反覆項目*:若要測試訓練作業中的管線 （反覆項目） 的最大數目。 作業不會執行多個指定的反覆運算次數。 <br> *計量分數臨界值*:所有管線的最小度量分數。 這可確保如果您有您想要達到定義的目標度量時，您執行不超過必要訓練作業花更多時間。
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

此外，您可以使用這些統計資料來決定是否要包含或排除特定資料行。 若切換每個資料行選取器，您可以控制的範圍的資料行都將使用自動化機器學習服務實驗中。

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

## <a name="run-experiment"></a>執行實驗

若要執行實驗，請按一下 [開始] 按鈕。
  
實驗準備程序需要幾分鐘的時間。

## <a name="view-results"></a>檢視結果

完成這項實驗中準備階段之後，您會看到 [執行詳細資料] 畫面。 這會讓您建立之模型的完整清單。 根據預設，最高評分之模型所根據您的參數，將位於清單頂端。 當訓練作業嘗試出更多模型，您會看到它們加入至清單和圖表。
使用圖表來取得度量資訊的快速比較到目前為止所產生的模型。

![執行詳細資料的儀表板](media/how-to-create-portal-experiments/run-details.png)

您可向下切入任何的輸出模型，這會開啟該模型，包括效能和分配圖表和計量的詳細資料。 [深入了解圖表](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments#understanding-automated-ml-charts)。

![反覆項目詳細資料](media/how-to-create-portal-experiments/dashboard.png)

定型作業可能需要一段時間，每個管線完成執行。

## <a name="deploy-model"></a>部署模型

一旦您手邊有最佳的模型，就可以將它部署為 web 服務來預測新資料。

自動化的 ML 可協助您部署模型，而不需要撰寫程式碼：

1. 從 右側的 執行摘要 窗格中，選取 註冊模型 」。

    ![註冊模型](media/how-to-create-portal-experiments/register-model.png)

1. 一旦註冊模型，您可以下載要在部署期間使用的計分指令碼。

    ![下載的計分指令碼](media/how-to-create-portal-experiments/download-scoring-script.png)

1. 評分指令碼之後，請移至 < 模型 > 頁面 (在左側的導覽窗格下**資產**)。

    ![模型瀏覽窗格](media/how-to-create-portal-experiments/nav-pane-models.png)

1. 檢查您註冊時，模型旁的核取方塊，然後選取 [建立映像]。

    您可以透過它的描述，其中包含執行的識別碼和反覆項目編號，以下列格式來識別模型： **< Run_ID > _ < Iteration_number > （_m)**。

1. 輸入映像的名稱，然後上傳評分您先前下載的檔案。 [深入了解評分指令碼](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where.md#script)。

    您可以使用您自己的計分指令碼和 Conda 檔案。 如果您沒有 Conda 檔案[建立您自己](tutorial-deploy-models-with-aml.md#create-environment-file)並將它上傳以及其他您可能想要使用的其他檔案。

    ![建立映像表單](media/how-to-create-portal-experiments/create-image.png)

1. 選取 建立 按鈕以啟動 建立映像。 這需要幾分鐘才能完成，完成後，您會看到一則訊息，在頂端列中。

1. 移至 「 映像 」 索引標籤，檢查您想要部署的映像旁邊的核取方塊，選取 [建立部署]。

    ![建立部署畫面影像。](media/how-to-create-portal-experiments/images-create-deployment.png)

1. 輸入唯一的部署名稱。

1. （選擇性）輸入部署的描述。

1. 選取要使用的目標計算類型。 

    ![建立部署的表單](media/how-to-create-portal-experiments/create-deployment.png)

1. 選取 [建立] 以啟動部署程序，將會需要幾分鐘才能完成。

1. 就這麼簡單！ 您有操作的 web 服務來產生預測。

## <a name="next-steps"></a>後續步驟

* [如何使用已部署的模型](how-to-consume-web-service.md)。
* [收集資料，在生產環境中的模型](how-to-enable-data-collection.md)。