---
title: 建立與部署互通的 ONNX 模型
titleSuffix: Azure Machine Learning service
description: 深入了解 ONNX 以及如何使用 Azure Machine Learning 來建立及部署 ONNX 模型
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 12/3/2018
ms.custom: seodec18
ms.openlocfilehash: 97464115b87ca5facdc055e0031bc5fc4e962a22
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295652"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX 與 Azure Machine Learning：建立與部署互通的 AI 模型

[開放神經網路交換](https://onnx.ai) (ONNX) 格式是代表機器學習模型的開放標準。 ONNX 受到[合作夥伴社群](https://onnx.ai/supported-tools)的支援，例如 Microsoft 建立了相容的架構和工具。 Microsoft 致力於開發開放且可交互作用的 AI，讓資料科學家和開發人員可以：

+ 使用自己所選擇的架構來建立模型並加以定型
+ 以最少的整合工作在各個平台之間部署模型

Microsoft 會讓其產品 (包括 Azure 和 Windows) 支援 ONNX，以協助您達成這些目標。  

## <a name="why-choose-onnx"></a>為何選擇 ONNX？
您透過 ONNX 所取得的互通性，可以讓絕佳的想法更快付諸實行。 使用了 ONNX，資料科學家可以選擇他們喜好的架構來進行作業。 同樣地，開發人員可以耗費較少的時間讓模型準備好用於生產環境，並且跨雲端與邊線進行部署。  

您可以從許多架構建立 ONNX 模型，這些架構包括 PyTorch、Chainer、Microsoft Cognitive Toolkit (CNTK)、MXNet、ML.Net、TensorFlow、Keras、SciKit-Learn 等等。

另外還有工具的生態系統，可用來視覺化及加速 ONNX 模型。 許多預先定型的 ONNX 模型也適用於常見案例。

您可以使用 Azure Machine Learning 和 ONNX Runtime 將 [ONNX 模型可以部署](#deploy)到雲端。 也可以使用 [Windows ML](https://docs.microsoft.com/windows/ai/)，將其部署到 Windows 10 裝置。 甚至可以使用從 ONNX 社群取得的轉換器，將其部署到其他平台。 

[![顯示訓練、 轉換和部署的 ONNX 流程圖](media/concept-onnx/onnx.png) ](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>取得 ONNX 模型

您可以透過數種方式來取得 ONNX 模型：
+ 從 [ONNX Model Zoo](https://github.com/onnx/models) 取得預先定型的 ONNX 模型 (請參閱本文底部的範例)
+ 從 [Azure 自訂視覺服務](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)產生自訂 ONNX 模型 
+ 將現有模型從另一種格式轉換成 ONNX (請參閱本文底部的範例) 
+ 在 Azure Machine Learning services 中將新的 ONNX 模型定型 (請參閱本文底部的範例)

## <a name="saveconvert-your-models-to-onnx"></a>將您的模型儲存/轉換為 ONNX

您可以將現有的模型轉換成 ONNX，或在定型結束時將它們儲存成 ONNX。

|模型的架構|轉換範例或工具|
|-----|-------|
|PyTorch|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)|
|Microsoft&nbsp;Cognitive&nbsp;Toolkit&nbsp;(CNTK)|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)|
|TensorFlow|[tensorflow-onnx 轉換器](https://github.com/onnx/tensorflow-onnx)|
|Chainer|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)|
|MXNet|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)|
|Keras、ScitKit-Learn、CoreML<br/>XGBoost 和 libSVM|[WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools)|

您可以在 [ONNX 教學課程網站](https://github.com/onnx/tutorials)找到支援架構和轉換器的最新清單。

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>在 Azure 中部署 ONNX 模型

使用 Azure Machine Learning 服務，您可以部署、管理及監視 ONNX 模型。 使用標準[部署工作流程](concept-model-management-and-deployment.md)和 ONNX Runtime，您可以建立裝載於雲端中的 REST 端點。 請參閱本文結尾的 Jupyter Notebook 完整範例，自行嘗試看看。 

### <a name="install-and-configure-onnx-runtime"></a>安裝和設定 ONNX Runtime

ONNX Runtime 是適用於 ONNX 模型的開放原始碼高效能推斷引擎。 其可提升 CPU 與 GPU 硬體速度，具有可供 Python、C# 和 C. ONNX Runtime 使用的 API，且支援 ONNX 1.2+ 模型，並能在 Linux、Windows 和 Mac 上執行。 Python 套件位於 [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime)[GPU](https://pypi.org/project/onnxruntime-gpu))，[C# 套件](https://www.nuget.org/packages/Microsoft.ML.OnnxRuntime/)位於 [Nuget.org](https://www.nuget.org)。請至 [GitHub](https://github.com/Microsoft/onnxruntime) 參閱專案的詳細資訊。 請閱讀[系統需求](https://github.com/Microsoft/onnxruntime#system-requirements)才能進行安裝。

若要安裝 Python 適用的 ONNX Runtime，請使用：
```python
pip install onnxruntime
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

### <a name="example-deployment-steps"></a>部署步驟範例

以下是部署 ONNX 模型的範例：

1. 初始化您的 Azure Machine Learning 服務工作區。 如果您還沒有工作區，請在[本快速入門](quickstart-get-started.md)中深入了解如何建立工作區。

   ```python
   from azureml.core import Workspace

   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
   ```

2. 向 Azure Machine Learning 註冊模型。

   ```python
   from azureml.core.model import Model

   model = Model.register(model_path = "model.onnx",
                          model_name = "MyONNXmodel",
                          tags = ["onnx"],
                          description = "test",
                          workspace = ws)
   ```

3. 建立具有模型和任何相依性的映像。

   ```python
   from azureml.core.image import ContainerImage

   image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                     runtime = "python",
                                                     conda_file = "myenv.yml",
                                                     description = "test",
                                                     tags = ["onnx"]
                                                    )

   image = ContainerImage.create(name = "myonnxmodelimage",
                                 # this is the model object
                                 models = [model],
                                 image_config = image_config,
                                 workspace = ws)

   image.wait_for_creation(show_output = True)
   ```

   `score.py` 檔案包含評分邏輯，並且必須包含在映像中。 此檔案是用來在映像中執行模型。 如需如何建立評分指令碼的指示，請參閱本[教學課程](tutorial-deploy-models-with-aml.md#create-scoring-script)。 ONNX 模型的範例檔案如下所示：

   ```python
   import onnxruntime
   import json
   import numpy as np
   import sys
   from azureml.core.model import Model

   def init():
       global session
       model = Model.get_model_path(model_name = 'MyONNXModel')
       session = onnxruntime.InferenceSession(model)

   def preprocess(input_data_json):
       # convert the JSON data into the tensor input
       return np.array(json.loads(input_data_json)['data']).astype('float32')

   def postprocess(result):
       return np.array(result).tolist()

   def run(input_data_json):
       try:
           start = time.time()   # start timer
           input_data = preprocess(input_data_json)
           input_name = session.get_inputs()[0].name  # get the id of the first input of the model   
           result = session.run([], {input_name: input_data})
           end = time.time()     # stop timer
           return {"result": postprocess(result),
                   "time": end - start}
       except Exception as e:
           result = str(e)
           return {"error": result}
   ```

   `myenv.yml` 檔案說明映像所需的相依性。 請參閱本[教學課程](tutorial-deploy-models-with-aml.md#create-environment-file)，以取得如何建立環境檔案的指示，例如此範例檔案：

   ```python
   from azureml.core.conda_dependencies import CondaDependencies 

   myenv = CondaDependencies.create(pip_packages=["numpy","onnxruntime","azureml-core"])

   with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
   ```

4. 若要部署模型，請參閱[部署方式及位置](how-to-deploy-and-where.md)文件。


## <a name="examples"></a>範例

請參閱 [how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx)，以取得建立與部署 ONNX 模型的範例筆記本。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>其他資訊

深入了解 ONNX 或參與專案：
+ [ONNX 專案網站](https://onnx.ai)

+ [GitHub 上的 ONNX 程式碼](https://github.com/onnx/onnx)

深入了解 ONNX Runtime 或參與專案：
+ [ONNX Runtime GitHub 存放庫](https://github.com/Microsoft/onnxruntime)


