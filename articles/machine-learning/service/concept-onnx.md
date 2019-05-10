---
title: 高效能、 跨平台 ONNX 別推斷
titleSuffix: Azure Machine Learning service
description: 深入了解 ONNX 和 ONNX 執行階段，以加速模型
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: a8bc46011b00a0c63eddd2799ac1309b5754472e
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442414"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX 與 Azure Machine Learning：建立及加速 ML 模型

了解如何使用[開啟類神經網路交換](https://onnx.ai)(ONNX) 能協助您最佳化您的機器學習模型。

最佳化的機器學習服務模型的推斷並不容易，因為您需要調整模型和推斷程式庫，以充分利用的硬體功能。 問題會變得非常困難的是如果您想要取得最佳效能，在各種平台 (雲端/edge，CPU/GPU 等) 上的，由於每個有不同的功能和特性。 如果您有各式各樣的架構，需要在不同平台上執行的模型，則會增加複雜度。 它是非常耗費時間最佳化的架構和硬體的所有不同組合。 需要訓練一次在您慣用的架構，並在雲端或 edge 上的任何位置執行的解決方案。 這是 ONNX 之處。

Microsoft 與合作夥伴社群建立 ONNX 為一項開放標準來表示機器學習服務模型。 從模型[許多架構](https://onnx.ai/supported-tools)包括 TensorFlow、 PyTorch、 Scikit-learn、 Keras、 Chainer、 MXNet、 和 MATLAB 可以匯出或轉換成標準的 ONNX 格式。 一旦模型 ONNX 格式，它們可以各種不同的平台和裝置上執行它。

[ONNX Runtime](https://github.com/Microsoft/onnxruntime)是高效能推斷引擎，可部署至生產環境的 ONNX 模型。 它最適合用於雲端與邊緣，並適用於 Linux、 Windows、 和 mac。 以C++，它也有 C、 Python 和C#Api。 ONNX 執行階段會提供所有 ONNX ML 規格的支援，並同時整合了在不同的硬體，例如 TensorRT 上 NVidia Gpu 加速器。

ONNX 執行階段會在高延展性 Microsoft 服務，例如 Bing、 Office 和認知服務。 效能提升取決於許多因素，但這些 Microsoft 服務所見__平均 2 倍的 CPU 上的效能改善__。 ONNX 執行階段也會在數百台數百萬個裝置上的 Windows ML 的一部分。 您可以使用 Azure Machine Learning 服務的執行階段。 藉由使用 ONNX 執行階段，您可以從廣泛的生產等級最佳化、 測試和持續改進獲益。

[![顯示訓練、 轉換和部署的 ONNX 流程圖](media/concept-onnx/onnx.png)](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>取得 ONNX 模型

您可以透過數種方式來取得 ONNX 模型：
+ 訓練新的 ONNX 模型，在 Azure Machine Learning 服務 （請參閱這篇文章底部的範例）
+ 將從另一種格式的現有模型轉換成 ONNX (請參閱[教學課程](https://github.com/onnx/tutorials)) 
+ 取得預先定型的 ONNX 模型，從[ONNX 模型 Zoo](https://github.com/onnx/models) （請參閱這篇文章底部的範例）
+ 從 [Azure 自訂視覺服務](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)產生自訂 ONNX 模型 

包括影像分類、 物體偵測和文字處理的許多模型可以表示成 ONNX 模型。 不過某些模型可能無法成功轉換。 如果您遇到這種情況時，請在您使用個別的轉換子的 GitHub 中提出問題。 您可以繼續使用您現有的格式模式，直到解決問題。

## <a name="deploy-onnx-models-in-azure"></a>在 Azure 中部署 ONNX 模型

使用 Azure Machine Learning 服務，您可以部署、管理及監視 ONNX 模型。 使用標準[部署工作流程](concept-model-management-and-deployment.md)和 ONNX Runtime，您可以建立裝載於雲端中的 REST 端點。 試用此功能，為您自己的這篇文章結尾處的範例 Jupyter notebook，請參閱。 

### <a name="install-and-use-onnx-runtime-with-python"></a>安裝和搭配 Python 使用 ONNX 執行階段

ONNX 執行階段的 Python 套件位於[PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime)， [GPU](https://pypi.org/project/onnxruntime-gpu))。 請閱讀[系統需求](https://github.com/Microsoft/onnxruntime#system-requirements)才能進行安裝。 

 若要安裝適用於 Python 的 ONNX 執行階段，使用下列命令之一： 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
``` 

若要在您的 Python 指令碼中呼叫 ONNX Runtime，請使用：    
```python   
import onnxruntime  
session = onnxruntime.InferenceSession("path to model") 
``` 

模型隨附的文件通常會告訴您使用模型所需的輸入和輸出。 您也可以使用視覺效果工具 (例如 [Netron](https://github.com/lutzroeder/Netron)) 來檢視模型。 ONNX Runtime 也可讓您查詢模型中繼資料、輸入和輸出：    
```python   
session.get_modelmeta() 
first_input_name = session.get_inputs()[0].name 
first_output_name = session.get_outputs()[0].name   
``` 

若要推斷您的模型，請使用 `run`，傳入您想要系統傳回的輸出清單 (如果您想要傳回全部則留空)，以及傳入輸入值的對應。 結果是輸出清單。  
```python   
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})   
results = session.run([], {"input1": indata1, "input2": indata2})   
``` 

如需完整的 Python API 參考，請參閱 [ONNX Runtime 參考文件](https://aka.ms/onnxruntime-python) (英文)。    

## <a name="examples"></a>範例

請參閱 [how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx)，以取得建立與部署 ONNX 模型的範例筆記本。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>其他資訊

深入了解 ONNX 或參與專案：
+ [ONNX 專案網站](https://onnx.ai)
+ [GitHub 上的 ONNX 程式碼](https://github.com/onnx/onnx)

深入了解 ONNX Runtime 或參與專案：
+ [ONNX Runtime GitHub 存放庫](https://github.com/Microsoft/onnxruntime)


