---
title: 資料漂移監視 (預覽)
titleSuffix: Azure Machine Learning service
description: 瞭解 Azure Machine Learning 服務可以如何監視資料漂移。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: 13f73718fabd711e9c71a56ac4537b2ebef8a411
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371095"
---
# <a name="what-is-data-drift-monitoring-preview"></a>什麼是資料漂移監視 (預覽)？

資料漂移是資料分佈的變更。 在機器學習環境中, 定型的機器學習模型可能會因為漂移而遇到降低的預測效能。 監視定型資料和用於進行預測之資料之間的漂移, 有助於偵測效能問題。

機器學習模型只與用來定型它們的資料一樣好。 將模型部署到生產環境而不監視其效能, 可能會導致未偵測到和不利的影響。 透過資料漂移監視, 您可以偵測並調整資料漂移。 

## <a name="when-to-monitor-for-data-drift"></a>監視資料漂移的時機？

我們可以監視的計量包括:

+ 漂移的程度 (漂移係數)
+ 漂移的原因 (依功能的漂移比重)
+ 距離度量 (Wasserstein、能源等等)

進行此監視時, 可以在偵測到漂移時設定警示或動作, 而資料科學家可以調查問題的根本原因。 

如果您認為已部署模型的輸入資料可能會變更, 您應該考慮使用資料漂移偵測。

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>如何在 Azure Machine Learning 服務中監視資料漂移

使用**Azure Machine Learning 服務**, 資料漂移會透過資料集或部署進行監視。 若要監視資料漂移, 基準資料集-通常會指定模型的訓練資料集。 第二個資料集 (通常是從部署收集的模型輸入資料) 會針對基準資料集進行測試。 這兩個資料集都會進行[分析](how-to-explore-prepare-data.md#explore-with-summary-statistics), 並輸入至資料漂移監視服務。 機器學習模型已定型, 可偵測這兩個資料集之間的差異。 模型的效能會轉換成漂移係數, 測量兩個資料集之間的漂移程度。 使用[模型 interpretability](machine-learning-interpretability-explainability.md)時, 會計算提供給漂移係數的功能。 從資料集設定檔中, 會追蹤每項功能的統計資訊。 

## <a name="data-drift-metric-output"></a>資料漂移度量輸出

有多種方式可查看漂移計量:

* 使用 Jupyter [widget。](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) `RunDetails`
* 在任何`datadriftRun`物件上使用函數。`get_metrics()`
* 查看模型 Azure 入口網站中的計量

系統會針對資料漂移工作, 在每次執行反復專案中儲存下列計量:

|度量|描述|
--|--|
wasserstein_distance|為一維數值散發定義的統計距離。|
energy_distance|為一維數值散發定義的統計距離。|
datadrift_coefficient|正式 Matthews 相互關聯係數, 範圍從-1 到1的實數。 在漂移的內容中, 0 表示沒有漂移, 而1表示最大漂移。|
datadrift_contribution|功能對漂移的重要性。|

## <a name="next-steps"></a>後續步驟

請參閱範例, 並瞭解如何監視資料漂移:

+ [瞭解如何在透過 Azure Kubernetes Service (AKS) 部署的模型上監視資料漂移](how-to-monitor-data-drift.md)
+ 試用[Jupyter Notebook 範例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)