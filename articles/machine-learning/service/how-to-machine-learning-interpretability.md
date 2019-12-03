---
title: Azure Machine Learning 服務中的模型 interpretability
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning SDK 來說明您的模型為何會進行預測。 它可以在定型和推斷期間用來瞭解模型如何進行預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 14118098e5d476dc07b21462180673b942a6224d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672560"
---
# <a name="model-interpretability-in-azure-machine-learning-service"></a>Azure Machine Learning 服務中的模型 interpretability
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>模型 interpretability 總覽

Interpretability 對資料科學家和商務決策者而言非常重要，這是為了確保符合公司政策、業界標準和政府法規：
+ 資料科學家需要能夠向主管和專案關係人說明其模型，讓他們能夠瞭解其發現的價值和精確度 
+ 商務決策者必須安心地提供透明功能，讓使用者能夠取得及維護其信任

在模型開發的兩個主要階段中，啟用說明機器學習模型的功能非常重要：
+ 在機器學習模型開發週期的訓練階段。 模型設計師和評估人員可以使用模型的 interpretability 輸出來驗證假設，並與專案關係人建立信任關係。 它們也會使用模型中的深入解析來進行偵錯工具、驗證模型行為是否符合其目標，以及檢查偏差或不重要的功能。
+ 在推斷階段中，由於已部署模型的透明度，讓主管能夠瞭解模型的運作方式，以及其決定如何在真實生活中進行處理和影響人員。 

## <a name="interpretability-with-azure-machine-learning"></a>具有 Azure Machine Learning 的 Interpretability

在本文中，您將瞭解如何在 SDK 中執行模型 interpretability 概念。

使用 SDK 中的類別和方法，您可以取得：
+ 原始和工程功能的功能重要性值
+ 在定型和推斷期間，大規模 Interpretability 真實世界的資料集。
+ 互動式視覺效果可協助您探索資料中的模式，以及定型時間的說明


在機器學習中，**功能**是用來預測目標資料點的資料欄位。 例如，若要預測信用風險，可能會使用年齡、帳戶大小和帳戶存留期的資料欄位。 在此情況下，年齡、帳戶大小和帳戶存留期都是**功能**。 功能重要性告訴您每個資料欄位如何影響模型的預測。 例如，當帳戶大小和年齡不會大幅影響預測精確度時，在預測中可能會耗用大量時間。 此程式可讓資料科學家說明產生的預測，讓專案關係人能夠看到模型中最重要的資料點。

使用這些工具，您可以使用簡單易用且可調整的方式，在**所有資料上**或在**特定資料點上**，以最先進的技術來說明機器學習模型。

Interpretability 類別可透過多個 SDK 套件取得。 瞭解如何[安裝適用于 Azure Machine Learning 的 SDK 套件](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* `azureml.interpret`是主要套件，其中包含 Microsoft 支援的功能。

* 您可以嘗試的 `azureml.contrib.interpret`、預覽和實驗性功能。

* 用來解讀自動化機器學習模型的 `azureml.train.automl.automlexplainer` 套件。

> [!IMPORTANT]
> 未完全支援 `contrib` 命名空間中的內容。 當實驗性功能變得成熟時，它們會逐漸移至主要命名空間。

## <a name="how-to-interpret-your-model"></a>如何解讀您的模型

您可以套用 interpretability 類別和方法，以瞭解模型的全域行為或特定的預測。 前者稱為「全域說明」，後者稱為「本機說明」。

方法也可以根據該方法是模型中立或模型特定來分類。 某些方法會以特定類型的模型為目標。 例如，SHAP 的樹狀說明僅適用于以樹狀結構為基礎的模型。 某些方法會將模型視為黑色方塊，例如模擬說明或 SHAP 的核心說明。 `interpret` 套件會根據資料集、模型類型和使用案例，來運用這些不同的方法。

輸出是一組有關給定模型如何進行預測的資訊，例如：
* 全域/本機相對功能重要性
* 全域/本機功能和預測關聯性

### <a name="explainers"></a>Explainers

此套件使用在[解讀-社區](https://github.com/interpretml/interpret-community/)中開發的 interpretability 技術，這是用來定型可解譯模型並協助說明黑箱 AI 系統的開放原始碼 python 套件。 [解讀-社區](https://github.com/interpretml/interpret-community/)作為此 SDK 支援 explainers 的主機，目前支援下列 interpretability 技術：

* **SHAP Tree 說明**： [SHAP](https://github.com/slundberg/shap)的樹狀結構說明，著重于多項式時間快速 SHAP 針對樹狀結構和整體樹狀結構所特有的值估計演算法。
* **SHAP Deep 說明**：根據[SHAP](https://github.com/slundberg/shap)的說明，deep 說明 "是一種高速的近似值演算法，可在深度學習模型中，根據 SHAP [DeepLIFT 論文](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)中所述的 SHAP 連接來 NIPS 值。 支援使用 TensorFlow 後端的 TensorFlow 模型和 Keras 模型（也有 PyTorch 的初步支援）」。
* **SHAP 線性說明**： [SHAP](https://github.com/slundberg/shap)的線性說明會計算線性模型的 SHAP 值，並選擇性地會計入功能間的相互關聯。

* **SHAP 核心說明**： [SHAP](https://github.com/slundberg/shap)的核心說明會使用特殊加權的區域線性回歸來估計任何模型的 SHAP 值。
* 模擬**說明**：模擬說明是以定型[全域代理模型](https://christophm.github.io/interpretable-ml-book/global.html)來模擬黑箱模型的概念為基礎。 全域代理模型是一個本質上的可解譯模型，它會定型以盡可能精確地大致估計黑色方塊模型的預測。 資料科學家可以解讀代理模型，以繪製有關黑色箱模型的結論。 您可以使用下列其中一個可解譯模型做為您的代理模型： LightGBM （LGBMExplainableModel）、線性回歸（LinearExplainableModel）、隨機梯度下降 explainable 模型（SGDExplainableModel）和決策樹（DecisionTreeExplainableModel).


* **排列功能重要性說明**：排列功能重要性是用來說明[Breiman 的隨機](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf)樹系紙張所能啟發的分類和回歸模型的技術（請參閱第10節）。 概括而言，它的運作方式是針對整個資料集，一次隨機亂數據一項功能，並計算感利率的效能計量有多大的變化。 變更愈大，此功能的重要性就愈高。

* **酸橙說明**（`contrib`）：根據有[酸](https://github.com/marcotcr/lime)的酸橙色說明，會使用最新的本機可解譯模型中立說明（酸）演算法來建立本機代理模型。 不同于全域代理模型，「酸」會著重于訓練本機代理模型來說明個別的預測。
* **漢字文字說明**（`contrib`）：漢字文字說明會使用階層式的注意網路，從文字資料取得給定黑色方塊文字模型的模型說明。 它會在指定的黑色方塊模型預測輸出上訓練漢字代理模型。 在文字主體上全域定型之後，它會為特定檔新增微調步驟，以改善說明的正確性。 漢字使用雙向 RNN 和兩個注意層，以進行句子和單字注意。 一旦 DNN 在黑色方塊模型上進行定型，並在特定檔上進行微調，使用者就可以從注意層中解壓縮 importances 一字。 漢字的顯示比文字資料的酸綠色或 SHAP 更精確，但是在定型時間方面也會更昂貴。 已改善，可讓使用者選擇使用手套 word 內嵌來初始化網路，以減少定型時間。 藉由在遠端 Azure GPU VM 上執行漢字，可以大幅改善定型時間。 漢字的執行會在「[檔分類的階層式注意網路（最強烈 et al，2016）](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification)」中說明。


* **表格式說明**： `TabularExplainer` 採用下列邏輯來叫用 Direct [SHAP](https://github.com/slundberg/shap) Explainers：

    1. 如果它是以樹狀結構為基礎的模型，請將 SHAP `TreeExplainer`，否則
    2. 如果它是 DNN 模型，請套用 SHAP `DeepExplainer`，否則
    3. 如果它是線性模型，請將 SHAP `LinearExplainer`，否則
    3. 將它視為黑色箱模型並套用 SHAP `KernelExplainer`


`TabularExplainer` 也對直接 SHAP Explainers 進行了重大的功能和效能增強：

* **初始化資料集的摘要**。 在說明速度最重要的情況下，我們會摘要初始設定資料集，並產生一小組代表性的範例，以加速全域和本機的說明。
* **取樣評估資料集**。 如果使用者傳入大量的評估範例，但實際上並不需要進行評估，則可以將取樣參數設定為 true，以加速全域說明。

下圖顯示直接和中繼 explainers 的目前結構。

[![Machine Learning Interpretability 架構](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>支援的模型

以 Python 中的資料集定型的任何模型 `numpy.array`、`pandas.DataFrame`、`iml.datatypes.DenseData`或 `scipy.sparse.csr_matrix` 格式，都受到 SDK 的 interpretability `explain` 套件支援。

說明函式接受模型和管線做為輸入。 如果提供了模型，模型就必須 `predict` 或符合 Scikit-learn 慣例的 `predict_proba` 來實作為預測函數。 如果提供管線（管線腳本的名稱），說明函式會假設執行中的管線腳本傳回預測。 我們支援透過 PyTorch、TensorFlow 和 Keras 深度學習架構訓練的模型。

### <a name="local-and-remote-compute-target"></a>本機和遠端計算目標

`explain` 套件是設計來搭配本機和遠端計算目標使用。 如果在本機執行，SDK 函式將不會與任何 Azure 服務連線。 您可以在 Azure Machine Learning 計算中從遠端執行說明，並將說明資訊記錄到 Azure Machine Learning 執行歷程記錄服務。 記錄這項資訊之後，說明中的報告和視覺效果就會立即在 Azure Machine Learning 工作區入口網站上提供，供使用者分析之用。


## <a name="next-steps"></a>後續步驟

請參閱[如何](how-to-machine-learning-interpretability-aml.md)在本機和 Azure Machine Learning 遠端計算資源上啟用模型定型的 interpretability。 如需其他案例，請參閱[範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)。
