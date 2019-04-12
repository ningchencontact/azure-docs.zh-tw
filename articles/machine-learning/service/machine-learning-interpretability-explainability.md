---
title: 模型可解釋性
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning Interpretability SDK 來說明為何您的模型進行預測。 它可以用於定型和推斷期間了解您的模型如何進行預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 04/09/2019
ms.openlocfilehash: fbcafb61ecd69f58bb3c14d1b15f36f1b21f2833
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494435"
---
# <a name="azure-machine-learning-interpretability-sdk"></a>Azure Machine Learning Interpretability SDK

在本文中，您將了解如何解釋為什麼您的模型進行預測它使用 Azure Machine Learning Interpretability SDK 所做的。 能夠說明您的模型是很重要，原因如下：

* 客戶和專案關係人想要知道**他們可以相信預測如果您的模型可讓**。
* 資料科學家，為您想要了解**如何查詢以尋找深入剖析模型**。 您也需要工具來做出明智的決策**如何改善您的模型**。
* 為公司中，您必須了解**搭配不同的模型的行為輸入散發**並**解模型的行為分析特定的輸入時發生**。

Machine learning interpretability 來說很重要的機器學習開發週期的兩個階段：**訓練**時間並**推斷**時間：

* 期間**訓練**:模型設計師和評估工具需要 interpretability 工具來建置信任的專案關係人說明模型的輸出。 他們也需要深入了解模型，讓他們可以偵錯模型，並決定行為是否符合其目標。 最後，他們必須確保模型不偏差。
* 期間**推斷**:預測，必須是可以理解的人使用您的模型。 比方說，為什麼沒有模型拒絕抵押貸款，或預測投資組合會帶來更高的風險嗎？

Azure Machine Learning Interpretability SDK 併入技術由 Microsoft 所開發成果，協力廠商程式庫 （例如 SHAP 和淡黃綠色）。 SDK 會在整合式的程式庫之間建立通用的 API，並整合 Azure Machine Learning 服務。 使用此 SDK，您可以說明機器學習服務模型**上的所有資料的全域**，或**在本機上的特定資料點**使用最新技術，以方便使用且可調整的方式。

## <a name="how-does-it-work"></a>運作方式

Azure Machine Learning Interpretability 可以用來了解模型的通用行為或特定的預測。 前者會呼叫全域的說明，後者會呼叫本機說明。

Azure Machine Learning Interpretability 方法可以根據此方法是否為模型無關或特定的模型進行也分類。 某些方法的目標特定類型的模型。 比方說，SHAP 的樹狀結構說明只適用於樹狀結構為基礎的模型。 某些方法會將模型視為黑箱，例如模仿說明或 SHAP 的核心的說明。 Azure Machine Learning Interpretability SDK 運用這些不同的方法，根據資料集、 模型類型和使用案例。

Azure Machine Learning Interpretability 傳回一組資訊模型如何進行其預測。 資訊包括項目，例如：

* 全域/本機相對功能重要性
* 全域/本機功能，並預測關聯性

## <a name="architecture"></a>架構

Azure Machine Learning Interpretability SDK 建構成兩個 Python 套件：

* [azureml.explain.model](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py) -主套件，其中包含 Microsoft 所支援的功能。
* `azureml.contrib.explain.model` -Preview，您可以嘗試的實驗性功能。

    > [!IMPORTANT]
    > 未完全支援 contrib 中的項目。 隨著成熟的實驗性功能，它們會逐漸移到主要封裝。

### <a name="explainers"></a>Explainers

Azure Machine Learning Interpretability SDK 導入了兩組 explainers:直接 Explainers 和中繼 Explainers。

__直接 explainers__來自整合式的程式庫。 SDK 會包裝所有 explainers，使其公開的通用的 API 和輸出格式。 以下是直接的 explainers SDK 中提供的清單：

> [!TIP]
> 如果您可以更輕鬆地直接使用這些 explainers，您可以直接啟動它們而不是使用通用的 API，以及輸出格式。

* **樹狀結構說明**:SHAP 的樹狀結構說明，著重於多項式時間快速 SHAP 值估計演算法特定樹狀結構與整體的樹狀結構。
* **深入說明**:根據從 SHAP，深入說明的說明 」 是使用 DeepLIFT SHAP NIPS 篇文章所述，建置在連線上的高速的近似值演算法 SHAP 深度學習模型中的值。 TensorFlow 模型和使用 TensorFlow 後端的 Keras 模型支援 （另外還有 PyTorch 的初步支援） 」。
* **核心說明**:SHAP 的核心說明使用特別加權的本機線性迴歸，來估計 SHAP 值的任何模型。
* **模擬說明**:模擬的說明是以全域 surrogate 模型的概念為基礎。 全域 surrogate 模型是本質上可解譯的模型，經過定型，可盡量精確地預估黑色方塊模型的預測。 資料科學家可以解譯結論黑色方塊模型的相關的 surrogate 模型。
* **淺綠色說明**:根據淡黃綠色，淡黃綠色說明可以使用的新狀態本機解譯模型無關的說明 （淺綠色） 演算法來建立本機的 surrogate 模型。 不像全域 surrogate 模型中，淺綠色著重於定型本機 surrogate 模型說明個別的預測。
* **HAN 文字說明**:HAN 文字說明可用於階層式注意力網路從文字資料的模型說明取得在指定的黑色方塊文字模型。 我們訓練 HAN surrogate 模型，在給定的老師模型的預測的輸出。 全域在文字語料庫的定型之後, 我們已新增特定文件的 fine-tune 步驟，以改善說明的精確度。 HAN 用於雙向 RNN 其兩個注意層為句子和單字的注意。 一旦 DNN 是於教師模型定型並微調特定文件，我們可以擷取 word importances 從注意圖層。 我們發現 HAN 是更正確地比淺綠色或 SHAP 文字資料，但更昂貴的定型時間也在的條款。 不過，我們已改進定型時間藉由為使用者提供的選項初始化手套字組內嵌項目，與網路雖然仍然很慢。 在遠端 Azure GPU VM 上執行 HAN，並可以大幅提升定型時間。 HAN 實作述 「 階層式注意力網路文件分類 （Yang 要是，2016年） ' ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf))。

__中繼 explainers__自動選取適當的直接說明，並產生根據給定的模型和資料集的最佳說明資訊。 中繼 explainers 運用所有的程式庫 （SHAP、 淺綠色、 模擬等），我們有整合式或開發。 SDK 中提供的中繼 explainers 如下：

* **表格式說明**:搭配使用表格式資料集。
* **文字說明**:搭配使用的文字資料集。

除了為中繼選取的直接 explainers、 中繼 explainers 開發其他功能的基礎程式庫及直接 explainers 中改善的速度和延展性。

目前`TabularExplainer`會採用下列邏輯來叫用直接 Explainers:

1. 如果它是樹狀結構為基礎的模型時，套用`TreeExplainer`、 其他
2. 如果它是 DNN 模型時，套用`DeepExplainer`、 其他
3. 將它視為黑箱模型，並套用 `KernelExplainer`

內建的智慧`TabularExplainer`會變成更複雜，因為多個程式庫都已整合至 SDK，而且我們了解每個說明的優缺點。

`TabularExplainer` 透過直接 Explainers 也將重要的功能和效能增強功能：

* **初始化資料集的摘要**。 在其中說明的速度是最重要的情況下，我們彙總初始化資料集，並產生較少的代表性的範例，以加快全域和本機說明。
* **評估資料集取樣**。 如果使用者會將一組大型評估範例中，但實際上不需要所有這些評估，就可以設定取樣參數為 true，以加速全球的說明。

下圖顯示兩組直接與中繼 explainers 之間的關聯性。

[![Machine 學習 Interpretability 架構](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>支援的模型

任何在 Python 中的資料集定型的模型`numpy.array`， `pandas.DataFrame`， `iml.datatypes.DenseData`，或`scipy.sparse.csr_matrix`機器學習服務 Interpretability SDK 所支援的格式。

說明函式會接受模型和管線做為輸入。 如果提供的模型，模型必須實作的預測函數`predict`或`predict_proba`符合 Scikit-learn 慣例。 如果提供的管線 （管線指令碼的名稱），則說明函式會假設執行的管線指令碼傳回的預測。

### <a name="local-and-remote-compute-target"></a>本機和遠端計算目標

機器學習服務 Interpretability SDK 被設計用於搭配這兩個本機和遠端計算目標。 如果在本機執行 SDK 函式將不會連絡任何 Azure 服務。 您可以從遠端執行說明，在 Azure Machine Learning 計算，並登入 Azure Machine Learning 執行歷程記錄服務的說明資訊。 之後會記錄這些資訊，報表和視覺效果從說明可供使用 Azure Machine Learning 工作區入口網站上供使用者分析。

## <a name="train-and-explain-locally"></a>定型，並在本機說明

1. 訓練您的模型，在本機的 Jupyter notebook。 

    ``` python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. 呼叫的說明：若要起始說明物件，您需要將模型定型資料、 功能感興趣 （選擇性），並輸出類別名稱 (如果分類) 來說明。 以下是如何具現化並說明物件使用[TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py)， [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)，和`LimeExplainer`在本機。 `TabularExplainer` 呼叫其中一個之下的三個 explainers (`TreeExplainer`， `DeepExplainer`，或`KernelExplainer`)，並會自動選取最適合您的使用案例。 不過，您可以直接呼叫每個其三個基礎 explainers。

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```
    或
    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. 取得通用的功能重要性的值。

    ```python
    # You can use the training data or the test data here. 
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. 本機的功能重要性的值： 使用下列函式呼叫來說明個別的執行個體或執行個體群組。

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```
    或
    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

## <a name="train-and-explain-remotely"></a>定型，並說明遠端

雖然您可以在 Azure Machine Learning 服務支援的各種計算目標上進行訓練，這一節中的範例會示範如何使用 AMLCompute 執行此動作。

1. 在本機的 Jupyter notebook (比方說，run_explainer.py) 中的定型指令碼。

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    # Train your model here

    # explain predictions on your local machine    
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0,:])
    # upload global and local explanation objects to Run History
    upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. 遵循上的指示[設定用於定型模型的計算目標](how-to-set-up-training-targets.md#amlcompute)若要了解如何設定 Azure Machine Learning 計算作為計算目標並提交您的訓練執行。

3. 下載您的本機 Jupyter notebook 中的說明。 
    > [!IMPORTANT]
    > 未完全支援 contrib 中的項目。 隨著成熟的實驗性功能，它們會逐漸移到主要封裝。

    ``` python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="next-steps"></a>後續步驟

若要查看集合的 Jupyter notebook 會示範上述的指示，請參閱[Azure Machine Learning Interpretability 範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)。