---
title: 什麼是 ML 環境
titleSuffix: Azure Machine Learning
description: 在本文中，您將瞭解機器學習環境的優點，其可讓您在不同的計算目標上，啟用可重現、可審核的機器學習相依性定義。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: ad520c7e6503f28de0bd5538662c223575a078fa
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692728"
---
# <a name="what-are-azure-machine-learning-environments"></a>Azure Machine Learning 環境是什麼？
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

環境會針對您的訓練和評分腳本，以及執行時間（Python、Spark 或 Docker），指定 Python 套件、環境變數和軟體設定。 它們是您 Azure Machine Learning 工作區內的受控和版本設定實體，可讓您跨不同的計算目標來進行可重現、可審核和便攜的機器學習工作流程。

您可以在本機計算上使用環境物件來開發您的定型腳本、在 Azure Machine Learning 計算上重複使用相同的環境，以進行大規模的模型定型，甚至使用相同的環境部署您的模型。

下圖說明相同的環境物件可用於您的回合設定，以進行定型，以及在您的推斷和部署設定中用於 web 服務部署。

![機器學習服務工作流程中的環境圖表](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>環境的類型

環境大致上可以分成三個類別：**策劃**、**使用者管理**和**系統管理**。

策劃環境是由 Azure Machine Learning 提供，而且預設會在您的工作區中提供。 其中包含 Python 套件和設定的集合，可協助您開始使用不同的機器學習架構。 

針對使用者管理的環境，您需負責設定環境，並在計算目標上安裝定型指令碼所需的每個套件。 Conda 不會檢查您的環境，或為您安裝任何項目。 請注意，如果您要定義自己的環境，則必須列出 `>= 1.0.45` 為 pip 相依性的 `azureml-defaults`。 此套件包含將模型裝載為 web 服務所需的功能。

當您想要讓[Conda](https://conda.io/docs/)為您管理 Python 環境和腳本相依性時，會使用系統管理的環境。 服務預設會假設這種類型的環境，因為它在不是手動設定的遠端計算目標上有其效用。

## <a name="creating-and-managing-environments"></a>建立和管理環境

環境可以透過下列方式建立：

* 使用策劃環境或定義您自己的相依性，以定義新的 `Environment` 物件
* 從您的工作區使用現有的 `Environment` 物件。 這可讓您使用相依性進行一致性和重現性
* 從現有的 Anaconda 環境定義匯入。

如需特定程式碼範例，請參閱操作[說明](how-to-use-environments.md#create-an-environment)。 環境也可以透過您的工作區輕鬆管理，並包含下列功能：

* 當您提交實驗時，環境會自動註冊到您的工作區。 也可以手動註冊
* 從您的工作區提取環境，並將其用於定型、部署，或對環境定義進行編輯
* 版本控制可讓您在一段時間內查看環境的變更，並確保重現性
* 自動從您的環境建立 Docker 映射

請參閱程式碼範例的[管理環境](how-to-use-environments.md#manage-environments)一節。

## <a name="next-steps"></a>後續步驟

* 瞭解如何在 Azure Machine Learning 中[建立和使用環境](how-to-use-environments.md)
* 請參閱適用于[環境類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)的 Python SDK 參考檔。
* 請參閱適用于[環境](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)的 R SDK 參考檔。