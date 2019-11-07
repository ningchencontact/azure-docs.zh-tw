---
title: Azure SQL Database Edge 預覽版中的機器學習服務和 AI 搭配 ONNX |Microsoft Docs
description: Azure SQL Database Edge 預覽版中的機器學習支援 Open Neural Network Exchange （ONNX）格式的模型。 ONNX 是一種開放格式，可讓您用來在各種機器學習架構和工具之間交換模型。
keywords: 部署 sql database edge
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/04/2019
ms.openlocfilehash: 4771db21a0ea5e841dbe12c8ce915b3833a8a30d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692316"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>SQL Database Edge 預覽版中的機器學習服務和 AI 搭配 ONNX

Azure SQL Database Edge 預覽版中的機器學習支援[Open Neural Network Exchange （ONNX）](https://onnx.ai/)格式的模型。 ONNX 是一種開放格式，可讓您用來在各種[機器學習架構和工具](https://onnx.ai/supported-tools)之間交換模型。

## <a name="supported-tool-kits"></a>支援的工具套件

ONNXMLTools 可讓您將模型從不同的機器學習工具套件轉換成 ONNX 模型。 目前，對於數值資料類型和單一資料行輸入，支援下列工具套件：

* [scikit-learn](https://github.com/onnx/sklearn-onnx)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)
* [Spark ML （實驗性）](https://github.com/onnx/onnxmltools/tree/master/onnxmltools/convert/sparkml)
* [LightGBM](https://github.com/onnx/onnxmltools)
* [libsvm](https://github.com/onnx/onnxmltools)
* [XGBoost](https://github.com/onnx/onnxmltools)

## <a name="get-onnx-models"></a>取得 ONNX 模型

有數種方式可讓您以 ONNX 格式取得模型：

- [ONNX 模型 Zoo](https://github.com/onnx/models)：包含數個預先定型的 ONNX 模型，適用于不同類型的工作。 您可以下載並使用 Windows Machine Learning 支援的版本。

- [從機器學習訓練架構原生匯出](https://onnx.ai/supported-tools)：數個定型架構支援 ONNX 的原生匯出功能，可讓您將定型的模型儲存至特定版本的 ONNX 格式。 例如，Chainer、Caffee2 和 PyTorch。 此外，服務（例如[Azure Machine Learning 服務](https://azure.microsoft.com/services/machine-learning-service/)和[Azure 自訂視覺](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)）也會提供原生 ONNX 匯出。

  - 若要瞭解如何使用自訂視覺在雲端中定型及匯出 ONNX 模型，請參閱[教學課程：搭配使用自訂視覺與 WINDOWS ML 的 ONNX 模型（預覽）](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/custom-vision-onnx-windows-ml)。

- [使用 WinMLTools 轉換現有的模型](https://docs.microsoft.com/windows/ai/windows-ml/convert-model-winmltools)：此 Python 封裝可讓模型從數種定型架構格式轉換成 ONNX。 您可以指定您想要將模型轉換成哪個版本的 ONNX，視您應用程式的目標 Windows 組建而定。 如果您不熟悉 Python，您可以使用[Windows MACHINE LEARNING UI 式儀表板](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLDashboard)來轉換您的模型。

> [!IMPORTANT]
> Azure SQL database Edge 並不支援所有的 ONNX 版本。 目前僅支援透過 ONNX 模型預測數值資料類型。

擁有 ONNX 模型之後，您就可以在 Azure SQL Database Edge 中部署模型。 然後，您可以使用[原生計分搭配 PREDICT t-sql 函數](/sql/advanced-analytics/sql-native-scoring/)。

## <a name="limitations"></a>限制

目前，這種支援僅限於具有**數值資料類型**的模型：

- [int 和 Bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [real 和 float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql)。
  
您可以使用 CAST 和 CONVERT [cast 和 convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)，將其他數數值型別轉換成支援的類型。

模型輸入應該是結構化的，讓模型的每個輸入都對應到資料表中的單一資料行。 例如，如果您使用 pandas 資料框架來定型模型，則每個輸入都應該是模型的個別資料行。

## <a name="next-steps"></a>後續步驟

- [透過 Azure 入口網站部署 SQL Database Edge](deploy-portal.md)
- [在 Azure SQL Database Edge 預覽上部署 ONNX 模型](deploy-onnx.md)
