---
title: 範例 Jupyter Notebook
titleSuffix: Azure Machine Learning service
description: 尋找並使用 Jupyter Notebook 範例探索 Azure Machine Learning 服務 Python SDK。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: 2ea124ecf6786142ac1703dab1e267b225751079
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035934"
---
# <a name="explore-azure-machine-learning-service-with-jupyter-notebooks"></a>使用 Jupyter Notebook 探索 Azure Machine Learning 服務

[範例 Azure Machine Learning Notebook 存放庫](https://github.com/azure/machinelearningnotebooks)包含最新的 Azure Machine Learning Python SDK 範例。 這些 Juypter Notebook 可協助您探索 SDK，並且可供您自己的機器學習服務專案作為模型。

本文說明如何從下列環境存取存放庫：

- [Azure Machine Learning Notebook VM](#notebookvm)
- [自攜 Notebook 伺服器](#byo)
- [資料科學虛擬機器](#dsvm)
- [Azure Notebooks](#aznb)

> [!NOTE]
> 在複製存放庫之後，您可在 **tutorials** 資料夾中找到教學課程 Notebook，並且可在 **how-to-use-azureml** 資料夾中找到特定功能的 Notebook。

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-notebook-vm"></a>在 Azure Machine Learning Notebook VM 上取得範例

要開始使用範例，最簡單的方式是完成[教學課程：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)。 完成後，您將會有已預先載入 SDK 與範例存放庫的專用 Notebook 伺服器。 您不需要下載或安裝。

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>在 Notebook 伺服器上取得範例

如果您想要使用攜帶的 Notebook 伺服器進行本機開發，請遵循下列步驟：

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

這些指示會安裝快速入門和教學課程 Notebook 所需的基底 SDK 套件。 若要使用其他範例 Notebook，可能需要安裝額外的元件。 如需詳細資訊，請參閱[安裝適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install)。

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>在 DSVM 上取得範例

資料科學虛擬機器 (DSVM) 是專為進行資料科學而建置的自訂 VM 映像。 如果您[建立 DSVM](how-to-configure-environment.md#dsvm)，系統將為您安裝及設定 SDK 和 Notebook 伺服器。 不過，您仍須建立工作區並複製範例存放庫。

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

<a name="aznb"></a>
## <a name="get-samples-on-azure-notebooks"></a>在 Azure Notebooks 上取得範例

在 [Azure Notebooks](https://notebooks.azure.com/) 上，系統會為您安裝及設定 SDK 和 Notebook 伺服器。 Azure Notebook 提供受到完整管理的輕量型 Notebook 環境讓您探索。

若要存取 Azure Notebooks 上的範例存放庫，請透過 [Azure 入口網站](https://portal.azure.com)瀏覽至您的 Azure Machine Learning 工作區。 從 [概觀]  區段中選取 [開始使用 Azure Notebooks]  。

## <a name="next-steps"></a>後續步驟

瀏覽[範例 Notebook](https://aka.ms/aml-notebooks) 以探索 Azure Machine Learning 服務的功用，或嘗試進行下列教學課程：

- [使用 MNIST 定型和部署影像分類模型](tutorial-train-models-with-aml.md)

- [準備資料並使用自動化機器學習服務以 NYC 計程車資料集定型迴歸模型](tutorial-auto-train-models.md)
