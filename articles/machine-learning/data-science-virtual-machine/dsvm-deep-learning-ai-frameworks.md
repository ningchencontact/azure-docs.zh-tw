---
title: 深度學習 & AI 架構
titleSuffix: Azure Data Science Virtual Machine
description: Azure 資料科學虛擬機器上可用的深度學習架構和工具, 包括 TensorFlow、PyTorch、Keras、Caffe、MXNet、Horovod、Theano、Chainer 等等。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 08/11/2019
ms.openlocfilehash: f71a2cc5c0a430037b429aac4f3ec4dc5f5f933d
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208114"
---
# <a name="deep-learning-and-ai-frameworks-for-azure-data-science-vm"></a>適用于 Azure 資料科學 VM 的深度學習和 AI 架構
[資料科學虛擬機器](https://aka.ms/dsvm)(DSVM) 支援多個深度學習架構, 可協助建立人工智慧 (AI) 應用程式, 並提供預測性分析和認知功能, 例如影像和語言理解。

透過 DSVM 提供的深度學習架構包括:

+ TensorFlow
+ PyTorch
+ Keras
+ Caffe
+ Caffe2
+ Torch
+ MXNet 模型伺服器
+ MXNet
+ Horovod
+ Theano
+ Chainer
+ Deep Water
+ NVIDIA DIGITS
+ nvidia-smi
+ TensorFlow 服務
+ TensorRT
+ Microsoft 辨識工具組

|DSVM 上&nbsp; &nbsp;的&nbsp;深度學習工具|Windows|Linux|使用&nbsp;注意事項|
|---------|-------------------|------------------|-----|
|[TensorFlow](https://www.tensorflow.org/) | 是 (Windows 2016) | 是 |安裝在 Linux 上的 Python 3.5[和 windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , 以及[windows 2016](dsvm-languages.md#python-windows-server-2016-edition)上的 python 3.6。 DSVM 上包含範例 Jupyter 筆記本。<br/><br/>**若要執行它**:<br/>終端機啟用正確的環境, 然後執行 Python。 <br/> Jupyter連接到[Jupyter](provision-vm.md)或[JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), 然後開啟 TensorFlow 目錄來尋找範例。  |
|[PyTorch](https://pytorch.org/)| 否 | 是 |安裝在[Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)中。 包含範例 Jupyter 筆記本, 範例位於/dsvm/samples/pytorch。    <br/><br/>**若要執行它**:<br/>終端機啟用正確的環境, 然後執行 Python。<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux):連接, 然後開啟 [PyTorch] 目錄以取得範例。  |
|[Keras](https://keras.io/)| 是 | 是 |API 安裝在[Linux 和 windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition)上的 python 3.5, 以及[windows 2016](dsvm-languages.md#python-windows-server-2016-edition)上的 python 3.6 中。 [請參閱範例](https://github.com/fchollet/keras/tree/master/examples)。<br/><br/>**若要執行它**:<br/>終端機啟用正確的環境, 然後執行 Python。 <br/> Jupyter從 GitHub 位置下載範例、連接到[Jupyter](provision-vm.md)或[JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), 然後開啟範例目錄。 |
|[Caffe](https://github.com/caffe2/caffe2) | 否 |是 (Ubuntu)|Caffe 是安裝在 `/opt/caffe` 中。   範例位於`/opt/caffe/examples`。 <br/><br/>**若要執行它**, 請使用 X2Go 登入您的 VM, 然後啟動新的終端機並輸入下列內容:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>具有範例筆記本的新瀏覽器視窗將會開啟。 二進位檔安裝在 /opt/caffe/build/install/bin。<br/><br/>已安裝的 Caffe 版本需要 Python 2.7, 而且無法與預設啟用的 Python 3.5 搭配使用。 若要切換至 Python 2.7, `source activate root`請執行以切換至 Anaconda 環境。|
|[Caffe2](https://github.com/caffe2/caffe2) | 否 |是 (Ubuntu)|Caffe2 安裝在 [Python 2.7 (root) conda 環境](dsvm-languages.md#python-linux-and-windows-server-2012-edition)。 原始檔在 `/opt/caffe2`。<br/>JupyterHub 中包含範例筆記本。<br/><br/>**若要執行它**:<br/>終端機啟用[根 python 環境](dsvm-languages.md#python-linux-and-windows-server-2012-edition), 啟動 Python, 然後匯入 Caffe2。 <br/> JupyterHub[連接到 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), 然後移至 Caffe2 目錄以尋找範例筆記本。 某些筆記本會要求以 Python 程式碼設定 Caffe2 根，請輸入 /opt/caffe2。 |
|[Torch](http://torch.ch/) | 否 |是 (Ubuntu)|Torch 是安裝在 `/dsvm/tools/torch` 中。 PyTorch 是安裝在 Python 2.7 (_root_) 及 Python 3.5 (_py35_) 環境中。 Torch 範例位於中`/dsvm/samples/torch` , 而 PyTorch 範例`/dsvm/samples/pytorch`位於。 |
|[MXNet](https://mxnet.io/) | 是 (Windows 2016) | 是|MXNet 是安裝在 Windows 上的 `C:\dsvm\tools\mxnet` 中，以及 Linux 上的 `/dsvm/tools/mxnet` 中。 Python 繫結安裝在 [Linux 和 Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 的 Python 3.5 中，以及安裝在 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) 的 Python 3.6 中。 R 繫結也會安裝在 Ubuntu 上。<br/><br/>已包含範例 Jupyter 筆記本。 <br/><br/>**若要執行它**:<br/>終端機啟用正確的環境, 然後執行 Python。 <br/> Jupyter連接到[Jupyter](provision-vm.md)或[JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), 然後開啟 mxnet 目錄來尋找範例。|
|[MXNet 模型伺服器](https://github.com/awslabs/mxnet-model-server) | 否 | 是 |用來建立 MXNet 和 ONNX 模型之 HTTP 端點的伺服器。 _Mxnet-模型-伺服器_可在終端機上使用。 [MXNet 模型伺服器頁面](https://github.com/awslabs/mxnet-model-server)上的範例。|
|[Horovod](https://github.com/uber/horovod) | 否 | 是 (Ubuntu) |適用于 TensorFlow 的分散式深度學習架構。 Horovod 安裝在 [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 的 Python 3.5 中。  [請參閱範例](https://github.com/uber/horovod/tree/master/examples)。<br/><br/>**若要執行它**, 請在終端機上啟用正確的環境, 然後執行 Python。 |
|[Theano](https://github.com/Theano/Theano) | 否 | 是 (Ubuntu) |Theano 安裝在 Python 2.7 (_root_) 和 python 3.5 (_py35_) 環境中。<br/><br/>**若要執行它**: <br/>終端機啟用您想要的 Python 版本 (root 或 py35), 執行 Python, 然後匯入 Theano。<br/>Jupyter選取 Python 2.7 或3.5 核心, 然後匯入 Theano。  <br/>若要解決最近的數學核心程式庫 (MKL) 錯誤, 您必須先設定 MKL 執行緒層級, 如下所示:<br/><br/>`export MKL_THREADING_LAYER=GNU`|
|[Chainer](https://chainer.org/) |否 | 是 |Chainer 安裝在 [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 中。 也會安裝 ChainerRL 和 ChainerCV。 <br/><br/>JupyterHub 中包含範例筆記本。<br/><br/>**若要執行它**: <br/>終端機啟用[python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)環境、執行_python_, 然後匯入 chainer。 <br/> JupyterHub[連接到 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), 然後移至 Chainer 目錄以尋找範例筆記本。|
|[NVidia 數位](https://github.com/NVIDIA/DIGITS) | 否 | 是 (Ubuntu) |NVIDIA 的深度學習系統, 用於快速訓練深度學習模型。 數位會安裝在`/dsvm/tools/DIGITS`中, 並以名為「_數位_」的服務形式提供。  <br/><br/>**若要執行它**: <br/>使用 X2Go 登入 VM。 在終端機上執行, 以啟動服務```sudo systemctl start digits```。 <br/><br/>服務需要約一分鐘才能啟動。 開啟網頁瀏覽器，然後前往 `http://localhost:5000`。 請注意，DIGITS 不提供安全登入，請勿在虛擬機器外公開。|
|[CUDA、cuDNN、NVIDIA 驅動程式](https://developer.nvidia.com/cuda-toolkit) |是 | 是 | |
|nvidia-smi|是 | 是 |用於查詢 GPU 活動的 NVIDIA 工具;_nvidia-smi-s_可在系統路徑上取得。 <br/><br/>開啟命令提示字元 (在 Windows 上) 或終端機 (在 Linux 上), 然後執行_nvidia-smi-s_。|
|[TensorFlow Serving](https://www.tensorflow.org/serving/) | 否 | 是 |要在 TensorFlow 模型上推斷的伺服器;tensorflow_model_server 可在終端機上使用。 可從[線上](https://www.tensorflow.org/serving/)取得範例。|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  否 | 是 (Ubuntu) |NVIDIA 的深度學習推斷伺服器。 TensorRT 是安裝為 _apt_ 套件。 可從[線上](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples)取得範例。|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|是 | 是 | 安裝在 Linux 上的 Python 3.5[和 windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , 以及[windows 2016](dsvm-languages.md#python-windows-server-2016-edition)上的 python 3.6。 DSVM 上包含範例 Jupyter 筆記本。 <br/><br/>**若要執行它**: <br/>終端機啟動正確的環境, 然後執行 Python。 <br/>Jupyter連接到[Jupyter](provision-vm.md)或[JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), 然後開啟 CNTK 目錄來尋找範例。 |
|Deep Water|否 | 是 (Ubuntu) |H2O 的深度學習架構, 在[Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)中安裝了 deep, 而且也可在中`/dsvm/tools/deep_water`使用。 JupyterHub 中包含範例筆記本。 Deep Water 需要 CUDA 8 搭配 cuDNN 5.1。 根據預設, 這不在程式庫路徑中, 因為其他深度學習架構會使用 CUDA 9 和 cuDNN 7。 若要使用, 請針對 Deep 水安裝 CUDA 8 + cuDNN 5.1:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>使用 Deep Water：<br/>終端機啟用[python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)環境, 然後執行_python_。 <br/>JupyterHub[連接到 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), 然後移至 deep_water 目錄以尋找範例筆記本。|
