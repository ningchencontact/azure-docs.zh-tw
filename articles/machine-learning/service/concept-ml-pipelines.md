---
title: 管線：最佳化機器學習服務工作流程
titleSuffix: Azure Machine Learning service
description: 在本文中，您將深入了解機器學習管線 (使用適用於 Python 的 Azure Machine Learning 來建置)，以及了解使用管線的優點。 資料科學家使用 Machine learning (ML) 管線來建置、管理他們的機器學習工作流程，以及將其最佳化。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: ff693ac34382ea5673989ecb6cbb38e19e176ad3
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65801070"
---
# <a name="build-machine-learning-pipelines-with-the-azure-machine-learning-service"></a>使用 Azure Machine Learning 服務建置機器學習管線

在本文中，了解機器學習管線 (您可以使用適用於 Python 的 Azure Machine Learning SDK 來建置)，以及使用管線的優點。

## <a name="what-are-machine-learning-pipelines"></a>什麼是機器學習管線？

資料科學家、資料工程師和 IT 專業人員可以使用機器學習 (ML) 管線，共同處理所需的步驟：
+ 資料準備，例如正規化和轉換
+ 模型訓練
+ 模型評估
+ 部署 

下圖顯示管線範例：

![Azure Machine Learning 服務中的機器學習管線](./media/concept-ml-pipelines/pipelines.png)

### <a name="which-azure-pipeline-technology-should-i-use"></a>我應該使用哪一種 Azure 管線技術？

Azure 雲端提供數個其他管線，各有不同的用途。 下表列出不同的管線，以及使用如：

| 管線 | 作用 | 標準管道 |
| ---- | ---- | ---- |
| Azure 機器學習服務管線 | 定義可重複使用的機器學習服務可以用做為範本，為您的機器學習案例的工作流程。 | 資料]-> [模型 |
| [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | 群組資料移動、 轉換和控制活動才能執行工作。  | 資料]-> [資料 |
| [Azure 的管線](https://azure.microsoft.com/services/devops/pipelines/) | 持續整合與傳遞到任何應用程式的任何平台/雲端  | 程式碼]-> [應用程式/服務 |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>為何使用 Azure Machine Learning 來建置管線？

您可以使用[適用於 Python 的 Azure Machine Learning SDK](#the-python-sdk-for-pipelines) 來建立 ML 管線，以及送出和追蹤個別管線執行。

透過管線，您可以從簡易性、速度、可攜性和重複使用率方面來最佳化工作流程。 使用 Azure Machine Learning 建置管線時，您可以專注於您的專業知識 (機器學習)，而非基礎結構。

使用不同步驟，便可在調整和測試工作流程時，只重新執行所需的步驟。 步驟是在管線中的計算單位。 如上圖所示，準備資料的工作可能涉及許多步驟。 這些步驟包括但不限於正規化、 轉換、 驗證和功能。 資料來源和中繼資料會在管線上重複使用，如此可節省計算時間和資源。 

設計管線之後，在管線的定型迴圈上通常需要更多微調。 當您重新執行管線時，該執行會跳至需要重新執行的步驟，例如更新的訓練指令碼，並略過未變更的項目。 相同範例適用於用來執行步驟的未變更指令碼。 

使用 Azure Machine Learning，您可以使用各種不同的工具組和架構，例如 PyTorch 或 TensorFlow、 您的管線中每個步驟。 Azure 會協調您使用的各種[計算目標](concept-azure-machine-learning-architecture.md)，讓您的中繼資料可輕易地與下游計算目標共用。 

您可以直接在 Azure 入口網站中[追蹤管線實驗的計量](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments)。 

## <a name="key-advantages"></a>主要優點

為機器學習工作流程建置管線的主要優點如下：

|主要優點|說明|
|:-------:|-----------|
|**自動&nbsp;執行**|排程一些步驟以可靠且自動地平行或依序執行。 由於資料準備和模型化可持續數天或數週，因此，您現在可以在管線執行的同時專注於其他工作。 |
|**混合和多元計算**|您可以使用多個管線，這些管線可在異質且可調整的計算和儲存體中可靠地進行協調。 您可以在不同計算目標上執行個別的管線步驟，例如 HDInsight、GPU 資料科學 VM 及 Databricks。 這可有效地使用可用的計算選項。|
|**再使用性**|您可以針對特定案例 (例如重新定型和批次評分) 將管線範本化。 您可以透過簡單的 REST 呼叫從外部系統觸發這些管線。|
|**追蹤和版本控制**|使用管線 SDK 明確地為您的資料來源、輸入及輸出命名與設定版本，而不需在逐一查看時手動追蹤資料和結果路徑。 您也可以個別管理指令碼和資源，以提升產能。|

## <a name="the-python-sdk-for-pipelines"></a>適用於管線的 Python SDK

使用 Python 來建立您的 ML 管線。 沒有資料相依性存在時，Azure Machine Learning SDK 會提供命令式架構來排序和平行化您管線中的步驟。 您可以在 Jupyter Notebook 或另一個慣用的整合式開發環境中與之互動。 

您可以使用宣告式資料相依性來最佳化您的工作。 SDK 包含一般工作適用的預先建置模組架構，這類工作包括資料轉送及模型發佈。 您可以藉由實作可在管線間重複使用的自訂步驟，來延伸架構以建立您自己的慣例模型。 您也可以直接從 SDK 管理計算目標和儲存體資源。

您可以將管線儲存成範本，並將它們部署到 REST 端點，以便您排程批次評分或重新定型作業。

若要查看如何建置您自己的管線，請參閱[管線的 Python SDK 參考文件](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)和下一節中的 Notebook。

## <a name="example-notebooks"></a>Notebook 範例
 
下列 Notebook 示範使用 Azure Machine Learning 的管線：[how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)。
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

了解如何[建立第一個管線](how-to-create-your-first-pipeline.md)。
