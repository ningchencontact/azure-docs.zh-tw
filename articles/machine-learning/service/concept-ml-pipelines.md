---
title: 什麼是 ML 管線
titleSuffix: Azure Machine Learning service
description: 在本文中，您將深入了解機器學習管線 (使用適用於 Python 的 Azure Machine Learning 來建置)，以及了解使用管線的優點。 資料科學家使用 Machine learning (ML) 管線來建置、管理他們的機器學習工作流程，以及將其最佳化。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 22d1da4c194b392993b37b16ab20673120c3362e
ms.sourcegitcommit: acffa72239413c62662febd4e39ebcb6c6c0dd00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951804"
---
# <a name="what-are-ml-pipelines-in-azure-machine-learning-service"></a>Azure Machine Learning 服務中的 ML 管線為何？

瞭解您可以使用 Azure Machine Learning 服務建立和管理的機器學習管線。 

資料科學家、資料工程師和 IT 專業人員可以使用機器學習 (ML) 管線，共同處理所需的步驟：
+ 資料準備，例如正規化和轉換
+ 模型訓練
+ 模型評估
+ 部署

了解如何[建立第一個管線](how-to-create-your-first-pipeline.md)。

![Azure Machine Learning 服務中的機器學習管線](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>我應該使用哪一種 Azure 管線技術？

Azure 雲端提供數個其他管線, 各有不同的用途。 下表列出不同的管線和它們的用途:

| 管線 | 作用 | 標準管道 |
| ---- | ---- | ---- |
| Azure Machine Learning 管線 | 定義可重複使用的機器學習服務工作流程, 以作為您機器學習服務案例的範本。 | 資料 > 模型 |
| [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | 將執行工作所需的資料移動、轉換和控制活動群組在一起。  | 資料 > 資料 |
| [Azure 管線](https://azure.microsoft.com/services/devops/pipelines/) | 將應用程式持續整合及傳遞至任何平臺/任何雲端  | 程式碼 > 應用程式/服務 |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>為何使用 Azure Machine Learning 來建置管線？

機器學習管線會以速度、可攜性和重複使用來優化您的工作流程, 讓您可以專注于您的專業知識、機器學習服務, 而不是基礎結構和自動化。

管線是由多個**步驟**所構成, 這些步驟在管線中是相異的計算單位。 每個步驟都可以獨立執行, 並使用隔離的計算資源。 這可讓多個資料科學家在同一時間同時處理相同的管線, 而不會有過度負擔的計算資源, 同時也可讓您輕鬆地針對每個步驟使用不同的計算類型/大小。

設計管線之後，在管線的定型迴圈上通常需要更多微調。 當您重新執行管線時, 回合會跳到需要重新執行的不同步驟, 例如更新的訓練腳本, 並略過未變更的內容。 相同範例適用於用來執行步驟的未變更指令碼。 這種功能有助於避免執行成本高昂且耗時的步驟, 例如資料內嵌和轉換 (如果基礎資料尚未變更)。

使用 Azure Machine Learning, 您可以針對管線中的每個步驟使用各種工具組和架構, 例如 PyTorch 或 TensorFlow。 Azure 會協調您使用的各種[計算目標](concept-azure-machine-learning-architecture.md)，讓您的中繼資料可輕易地與下游計算目標共用。

您可以直接在 Azure 入口網站中[追蹤管線實驗的計量](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments)。 發佈管線之後, 您可以設定 REST 端點, 讓您可以從任何平臺或堆疊重新執行管線。

## <a name="key-advantages"></a>主要優點

針對您的機器學習工作流程使用管線的主要優點如下:

|主要優點|描述|
|:-------:|-----------|
|**自動&nbsp;執行**|以可靠且自動的方式, 以平行或依序執行排程步驟。 資料準備和模型化可以過去數天或數周, 而管線可讓您在進程執行時, 將焦點放在其他工作上。 |
|**異構計算**|使用多個在異類和可擴充的計算資源和儲存位置之間可靠地協調的管線。 在不同的計算目標上執行個別的管線步驟, 例如 HDInsight、GPU 資料科學 Vm 和 Databricks。 這可有效地使用可用的計算選項。|
|**再使用性**|建立特定案例的管線範本, 例如重新定型和批次評分。 透過簡單的 REST 呼叫, 從外部系統觸發已發佈的管線。|
|**追蹤和版本控制**|使用管線 SDK 明確地為您的資料來源、輸入及輸出命名與設定版本，而不需在逐一查看時手動追蹤資料和結果路徑。 您也可以個別管理指令碼和資源，以提升產能。|
|**協作**|管線可讓資料科學家在機器學習設計流程的所有區域之間共同作業, 同時能夠同時處理管線步驟。|

## <a name="the-python-sdk-for-pipelines"></a>適用於管線的 Python SDK

[使用 PYTHON SDK](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)在您慣用的 IDE 或 Jupyter 筆記本中建立 ML 管線。 沒有資料相依性存在時，Azure Machine Learning SDK 會提供命令式架構來排序和平行化您管線中的步驟。 

您可以使用宣告式資料相依性來最佳化您的工作。 SDK 包含一般工作適用的預先建置模組架構，這類工作包括資料轉送及模型發佈。 您可以藉由跨管線執行可重複使用的自訂步驟, 來擴充架構以建立您自己的慣例模型。 您也可以直接從 SDK 管理計算目標和儲存體資源。

將您的管線儲存為範本, 並將其部署至 REST 端點, 以進行批次評分或重新定型作業。

管線有兩個 Python 套件, Azure Machine Learning: [azureml-管線-核心](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)和[azureml 管線-步驟](https://docs.microsoft.com/en-us/python/api/azureml-pipeline-steps/?view=azure-ml-py)。

## <a name="next-steps"></a>後續步驟

+ 了解如何[建立第一個管線](how-to-create-your-first-pipeline.md)。

+ 瞭解如何對[大型資料執行批次預測](how-to-run-batch-predictions.md)。

+ 閱讀[管線的 SDK 參考](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)檔

+ 試試看[Azure Machine Learning 管線](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)的範例 Jupyter 筆記本展示。 瞭解如何[執行筆記本以探索此服務](samples-notebooks.md)。
