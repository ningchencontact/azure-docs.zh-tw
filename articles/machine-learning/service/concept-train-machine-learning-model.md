---
title: 模型定型方法
titleSuffix: Azure Machine Learning
description: 瞭解您可以用來將模型定型 Azure Machine Learning 的不同方法。 估算器提供簡單的方式來使用熱門的架構，例如 Scikit-learn、學習、TensorFlow、Keras、PyTorch 和 Chainer。 Machine Learning 管線可讓您輕鬆排程自動執行、使用異類計算環境，以及重複使用工作流程的各部分。 和執行設定可讓您更精確地控制定型進程執行所在的計算目標。
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: a755fe1607e581cb0a25eb9bd90c2ba223829a46
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350585"
---
# <a name="train-models-with-azure-machine-learning"></a>使用 Azure Machine Learning 將模型定型

Azure Machine Learning 提供數種方式來定型您的模型，從程式碼優先解決方案使用 SDK 到低程式碼解決方案，例如自動化機器學習和視覺化介面。 使用下列清單來判斷適合您的訓練方法：

+ [適用于 Python 的 AZURE MACHINE LEARNING SDK](#python-sdk)：Python SDK 提供數種方式來定型模型，每個都有不同的功能。

    | 訓練方法 | 描述 |
    | ----- | ----- |
    | [執行設定](#run-configuration) | 將**模型定型的一般方法**是使用定型腳本並執行設定。 回合設定會提供必要的資訊，以設定用來定型模型的定型環境。 您可以執行「執行設定」、「訓練腳本」和「計算目標」（訓練環境），並執行訓練作業。 |
    | [自動化機器學習服務](#automated-machine-learning) | 自動化機器學習可讓您將**模型定型，而不需要大量的資料科學或程式設計知識**。 針對具有資料科學和程式設計背景的人員，它提供了一種方式，可透過自動化演算法選取和超參數微調來節省時間和資源。 使用自動化機器學習時，您不需要擔心定義執行設定。 |
    | [估算器](#estimators) | 估計工具類別**可讓您輕鬆地根據熱門的機器學習架構來定型模型**。 Scikit-learn 的估計工具類別包括 **：學習**、 **PyTorch**、 **TensorFlow**和**Chainer**。 另外還有一個泛型估計工具，可以搭配尚未擁有專用估計工具類別的架構來使用。 使用估算器時，您不需要擔心定義執行設定。 |
    | [機器學習管線](#machine-learning-pipeline) | 管線並不是不同的定型方法，而是**使用模組化、可重複使用的步驟來定義工作流程的一種方式**，其中包括訓練做為工作流程的一部分。 機器學習管線支援使用自動化機器學習、估算器和執行設定來定型模型。 由於管線並不是特別著重于定型，因此使用管線的原因會比其他定型方法更為不同。 一般而言，您可能會在下列情況使用管線：<br>* 您想要**排程**自動執行的程式，例如長時間執行的定型作業或資料準備。<br>* 使用跨異類計算資源和儲存位置協調的**多個步驟**。<br>* 在特定案例中使用管線作為**可重複**使用的範本，例如重新定型或批次評分。<br>* **追蹤和版本資料來源、輸入**和工作流程的輸出。<br>* 您的工作流程是**由不同的小組所實行，這些團隊會獨立處理特定步驟**。 然後，可以在管線中將步驟聯結在一起，以執行工作流程。 |

+ **視覺化介面**：Azure Machine Learning__視覺化介面__為機器學習提供了簡單的進入點，可用於建立概念證明，或用於幾乎編碼經驗的使用者。 它可讓您使用拖放 web 型 UI 來定型模型。 您可以使用 Python 程式碼作為設計的一部分，或在不撰寫任何程式碼的情況下訓練模型。

+ **CLI**：Machine learning CLI 會使用 Azure Machine Learning 提供一般工作的命令，而且通常用於**腳本和自動化**工作。 例如，當您建立定型腳本或管線之後，您可以使用 CLI 來根據排程啟動定型執行，或更新用於定型的資料檔案。 針對定型模型，它會提供可提交定型作業的命令。 它可以使用執行設定或管線提交作業。

每一種訓練方法都可以使用不同類型的計算資源來進行定型。 這些資源統稱為[__計算目標__](concept-azure-machine-learning-architecture.md#compute-targets)。 計算目標可以是本機電腦或雲端資源，例如 Azure Machine Learning 計算、Azure HDInsight 或遠端虛擬機器。

## <a name="python-sdk"></a>Python SDK

適用于 Python 的 Azure Machine Learning SDK 可讓您使用 Azure Machine Learning 來建立及執行機器學習工作流程。 您可以從互動式的 Python 會話、Jupyter 筆記本、Visual Studio Code 或其他 IDE 與服務互動。

* [什麼是適用于 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [安裝/更新 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [設定 Azure Machine Learning 的開發環境](how-to-configure-environment.md)

### <a name="run-configuration"></a>回合組態

您可以使用[RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)來定義具有 Azure Machine Learning 的一般定型作業。 接著會使用回合設定，連同您的定型腳本來訓練計算目標上的模型。

您可以從本機電腦的執行設定開始，然後視需要切換至雲端式計算目標的一個。 變更計算目標時，您只會變更所使用的回合設定。 執行也會記錄訓練作業的相關資訊，例如輸入、輸出和記錄。

* [什麼是執行設定？](concept-azure-machine-learning-architecture.md#run-configurations)
* [教學課程：訓練您的第一個 ML 模型 @ no__t-0
* [Examples：定型模型的 Jupyter Notebook 範例 @ no__t-0
* [如何：設定及使用計算目標以用於模型定型 @ no__t-0

### <a name="automated-machine-learning"></a>自動化 Machine Learning

定義 [反復專案]、[超參數設定]、[特徵化] 及其他設定。 在定型期間，Azure Machine Learning 會平行嘗試不同的演算法和參數。 定型會在達到您定義的結束準則之後停止。 使用估算器時，您不需要擔心定義執行設定。

> [!TIP]
> 在除了至 Python SDK 時，您也可以透過[工作區登陸頁面（預覽）](https://ml.azure.com)使用自動化 ML。

* [什麼是自動化機器學習服務？](concept-automated-ml.md)
* [教學課程：使用自動化機器學習來建立您的第一個分類模型 @ no__t-0
* [教學課程：使用自動化機器學習來預測出租車車資 @ no__t-0
* [Examples：自動化機器學習服務 @ no__t 的 Jupyter Notebook 範例-0
* [如何：在 Python @ no__t 中設定自動化 ML 實驗-0
* [如何：自動定型時間序列預測模型 @ no__t-0
* [如何：使用 Azure Machine Learning 的工作區登陸頁面（預覽），建立、探索及部署自動化的機器學習實驗 ](how-to-create-portal-experiments.md)

### <a name="estimators"></a>估算器

估算器可讓您輕鬆地使用熱門的 ML 架構來定型模型。 如果您使用**scikit-learn-學習**、 **PyTorch**、 **TensorFlow**或**Chainer**，您應該考慮使用估計工具來進行定型。 另外還有一個泛型估計工具，可以搭配尚未擁有專用估計工具類別的架構來使用。 使用估算器時，您不需要擔心定義執行設定。

* [什麼是估算器？](concept-azure-machine-learning-architecture.md#estimators)
* [教學課程：使用 MNIST 資料和 scikit-learn 訓練影像分類模型-瞭解如何使用 Azure Machine Learning @ no__t-0
* [Examples：使用估算器 @ no__t-0 的 Jupyter Notebook 範例
* [如何：在訓練 @ no__t 中建立估算器-0

### <a name="machine-learning-pipeline"></a>機器學習管線

機器學習管線可以使用先前提到的訓練方法（執行設定、估算器和自動化機器學習）。 管線是有關建立工作流程的詳細資訊，因此它們不僅包含模型的定型。 在管線中，您可以使用自動化機器學習、估算器或執行設定來定型模型。

* [Azure Machine Learning 中的 ML 管線為何？](concept-ml-pipelines.md)
* [使用 Azure Machine Learning SDK 建立及執行機器學習管線](how-to-create-your-first-pipeline.md)
* [教學課程：使用 Azure Machine Learning 管線進行批次評分 @ no__t-0
* [Examples：機器學習管線的 Jupyter Notebook 範例 @ no__t-0
* [Examples：具有自動化機器學習的管線 @ no__t-0
* [Examples：具有估算器 @ no__t-0 的管線

## <a name="visual-interface"></a>視覺化介面

視覺化介面（預覽）可讓您在網頁瀏覽器中使用拖放介面來定型模型。

+ [什麼是視覺化介面？](ui-concept-visual-interface.md)
+ [Tutorial：預測汽車價格 @ no__t-0
+ [Regression：預測價格](how-to-ui-sample-regression-predict-automobile-price-basic.md)
+ [Classification：預測信用風險](how-to-ui-sample-classification-predict-credit-risk-basic.md)
+ [Classification：預測流失、appetency 和向上銷售](how-to-ui-sample-classification-predict-churn.md)

## <a name="cli"></a>CLI

機器學習 CLI 是 Azure CLI 的延伸模組。 它提供跨平臺 CLI 命令，以使用 Azure Machine Learning。 一般而言，您會使用 CLI 來自動化工作，例如訓練機器學習服務模型。

* [使用適用于 Azure Machine Learning 的 CLI 擴充功能](reference-azure-machine-learning-cli.md)
* [Azure 上的 MLOps](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>後續步驟

瞭解如何[設定定型環境](how-to-set-up-training-targets.md)。
