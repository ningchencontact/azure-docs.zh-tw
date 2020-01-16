---
title: 關於 Azure Machine Learning 環境
titleSuffix: Azure Machine Learning
description: 在本文中，您將瞭解機器學習環境的優點，其可讓您在各種計算目標上，提供可重現、可審核及可移植的機器學習相依性定義。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: 8906299cc9e2c000dab2ac9d2a345d9aaf238260
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045855"
---
# <a name="what-are-azure-machine-learning-environments"></a>Azure Machine Learning 環境是什麼？
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning 環境會針對您的訓練和評分腳本指定 Python 套件、環境變數和軟體設定。 它們也會指定執行時間（Python、Spark 或 Docker）。 它們是您 Machine Learning 工作區內的受控和版本設定實體，可讓您在各種計算目標上進行可重現、可審核和便攜的機器學習工作流程。

您可以使用本機計算上的 `Environment` 物件來執行下列動作：
* 開發您的訓練腳本。
* 在 Azure Machine Learning 計算中重複使用相同的環境，以進行大規模的模型定型。
* 使用同一個環境部署您的模型。

下圖說明如何在您的回合設定中使用單一 `Environment` 物件，以進行訓練，以及針對 web 服務部署使用您的推斷和部署設定。

![機器學習服務工作流程中環境的圖表](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>環境的類型

環境大致上可以分成三個類別：*策劃*、*使用者管理*和*系統管理*。

策劃環境是由 Azure Machine Learning 提供，而且預設會在您的工作區中提供。 其中包含 Python 套件和設定的集合，可協助您開始使用各種機器學習架構。 

在使用者管理的環境中，您必須負責設定環境，並在計算目標上安裝定型腳本所需的每個套件。 Conda 不會檢查您的環境，也不會為您安裝任何專案。 如果您要定義自己的環境，則必須列出 `>= 1.0.45` 為 pip 相依性的 `azureml-defaults`。 此套件包含將模型裝載為 web 服務所需的功能。

當您想要讓[Conda](https://conda.io/docs/)為您管理 Python 環境和腳本相依性時，可以使用系統管理的環境。 服務預設會假設這種類型的環境，因為它在不是手動設定的遠端計算目標上有其效用。

## <a name="create-and-manage-environments"></a>建立和管理環境

您可以透過下列方式建立環境：

* 藉由使用策劃環境或定義您自己的相依性，來定義新的 `Environment` 物件。
* 從您的工作區使用現有的 `Environment` 物件。 這種方法可讓您使用相依性進行一致性和重現性。
* 從現有的 Anaconda 環境定義匯入。
* 使用 Azure Machine Learning CLI

如需特定程式碼範例，請參閱[重複使用環境以進行定型和部署](how-to-use-environments.md#create-an-environment)的「建立環境」一節。 環境也可以透過您的工作區輕鬆管理。 其中包括下列功能：

* 當您提交實驗時，環境會自動註冊到您的工作區。 也可以手動註冊。
* 您可以從工作區提取環境，以用於定型或部署，或對環境定義進行編輯。
* 有了版本設定，您就可以查看您的環境在一段時間內的變更，以確保重現性。
* 您可以從您的環境自動建立 Docker 映射。

如需程式碼範例，請參閱[重複使用環境以進行定型和部署](how-to-use-environments.md#manage-environments)的「管理環境」一節。

## <a name="next-steps"></a>後續步驟

* 瞭解如何在 Azure Machine Learning 中[建立和使用環境](how-to-use-environments.md)。
* 請參閱[環境類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)的 Python SDK 參考檔。
* 請參閱[環境](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)的 R SDK 參考檔。
