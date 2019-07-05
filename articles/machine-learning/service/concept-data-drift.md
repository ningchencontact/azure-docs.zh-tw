---
title: 資料漂移監視 （預覽）
titleSuffix: Azure Machine Learning service
description: 了解 Azure Machine Learning 服務如何監視資料漂移。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: a03e3124647869e7148f271810bb523986a851c6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442389"
---
# <a name="what-is-data-drift-monitoring-preview"></a>什麼是資料漂移監視 （預覽） 嗎？

資料漂移是資料分佈的變化。 在機器學習服務的內容中，定型的機器學習服務模型可能會遇到降級的預測效能，因為漂移。 監視訓練資料和用來進行預測的資料之間漂移，可協助偵測效能問題。

機器學習服務模型，才適合用來為它們定型的資料。 將模型部署到生產環境中，但不要監視其效能可能會導致未偵測到並不利的影響。 與資料漂移監視，您可以偵測並適應資料漂移。 

## <a name="when-to-monitor-for-data-drift"></a>監視資料漂移的時機？

我們可以監視的度量包括：

+ 漂移 （漂移係數） 的大小
+ 漂移 （漂移貢獻功能） 的原因
+ 距離度量 （Wasserstein、 能源、 等）。

與此監視機制，發出警示，或動作可以設定時偵測到漂移時，資料科學家可以調查問題的根本原因。 

如果您認為可能會變更已部署模型的輸入的資料，您應該考慮使用資料漂移偵測。

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>在 Azure Machine Learning 服務中監視資料漂移的方式

使用**Azure Machine Learning 服務**，透過資料集或部署監視資料漂移。 若要監視資料漂移，被指定的基準資料集-通常為訓練資料集模型。 通常模型的輸入的資料蒐集從部署-測試第二個資料集-，原因是對基礎資料集。 這兩個資料集[分析](how-to-explore-prepare-data.md#explore-with-summary-statistics)和輸入資料漂移監視服務。 機器學習模型訓練，會偵測到兩個資料集之間的差異。 模型的效能會轉換成漂移決定係數，測量兩個資料集之間漂移的大小。 使用[模型 interpretability](machine-learning-interpretability-explainability.md)計算提供給漂移係數的功能。 從資料集的設定檔，會追蹤每項功能的統計資訊。 

## <a name="data-drift-metric-output"></a>資料漂移計量輸出

有多種方式可以檢視漂移計量：

* 使用 Jupyter 小工具。
* 使用`get_metrics()`函式，任何`datadriftRun`物件。
* 在您的模型在 Azure 入口網站中檢視度量

下列度量資訊會儲存在資料漂移工作的每個執行反覆項目：

|計量|描述|
--|--|
wasserstein_distance|定義的一維的數值分佈的統計距離。|
energy_distance|定義的一維的數值分佈的統計距離。|
datadrift_coefficient|正式騏宇相互關聯係數，範圍從-1 到 1 的實數。 在內容中漂移，0 表示沒有漂移而 1 表示最大漂移。|
datadrift_contribution|功能提供給漂移的功能重要性。|

## <a name="next-steps"></a>後續步驟

請參閱範例，以及了解如何監視資料漂移：

+ [了解如何監視部署到 Azure Kubernetes Service (AKS) 中的模型上的資料漂移](how-to-monitor-data-drift.md)
+ 試試看[Jupyter Notebook 範例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)