---
title: 深度學習 & AI 架構
titleSuffix: Azure Data Science Virtual Machine
description: Azure 資料科學虛擬機器上可用的深度學習架構和工具。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/1/2019
ms.openlocfilehash: fd38bf1f7741c4d610ef43a12d90533d4ac7b703
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802420"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>適用于 Azure 資料科學 VM 的深度學習和 AI 架構
下面列出 DSVM 的深度學習架構。

## <a name="caffehttpsgithubcombvlccaffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| 支援的版本 | |
| 支援的 DSVM 版本      | Linux (Ubuntu)     |
| 它是如何在 DSVM 上設定/安裝的？  | Caffe 是安裝在 `/opt/caffe` 中。   範例位於`/opt/caffe/examples`。|
| 如何執行它      | 使用 X2Go 登入您的 VM，然後啟動新的終端機並輸入下列內容：<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>具有範例筆記本的新瀏覽器視窗將會開啟。 二進位檔安裝在 /opt/caffe/build/install/bin。<br/><br/>已安裝的 Caffe 版本需要 Python 2.7，而且無法與預設啟用的 Python 3.5 搭配使用。 若要切換至 Python 2.7， `source activate root`請執行以切換至 Anaconda 環境。|    

## <a name="caffe2httpsgithubcomcaffe2caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| 支援的版本 | |
| 支援的 DSVM 版本      | Linux (Ubuntu)     |
| 它是如何在 DSVM 上設定/安裝的？  | Caffe2 會安裝在 [Python 2.7 （root） conda 環境中。 |
| 如何執行它      | 終端機啟動 Python，然後匯入 Caffe2。 <br/> JupyterHub[連接到 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然後移至 Caffe2 目錄以尋找範例筆記本。 某些筆記本會要求以 Python 程式碼設定 Caffe2 根，請輸入 /opt/caffe2。 |

## <a name="chainerhttpschainerorg"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| 支援的版本 | 5.2 |
| 支援的 DSVM 版本      | Linux (Ubuntu)     |
| 它是如何在 DSVM 上設定/安裝的？  | Chainer 安裝在 Python 3.5 中。 |
| 如何執行它      | 終端機啟用 Python 3.5 環境，執行 `python`，然後 `import chainer`。 <br/> JupyterHub[連接到 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然後移至 Chainer 目錄以尋找範例筆記本。| 

## <a name="cuda-cudnn-nvidia-driverhttpsdevelopernvidiacomcuda-toolkit"></a>[CUDA、cuDNN、NVIDIA 驅動程式](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| 支援的版本 | 10.0.130|
| 支援的 DSVM 版本      | Windows 和 Linux   |
| 它是如何在 DSVM 上設定/安裝的？  |_nvidia-smi_ 可於系統路徑上取得。  |
| 如何執行它      | 開啟命令提示字元（在 Windows 上）或終端機（在 Linux 上），然後執行_nvidia-smi-s_。 |


## <a name="horovodhttpsgithubcomuberhorovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| 支援的版本 | 0.16.1|
| 支援的 DSVM 版本      | Linux (Ubuntu)   |
| 它是如何在 DSVM 上設定/安裝的？  | Horovod 安裝在 Python 3.5 中 |
| 如何執行它      | 在終端機上啟用正確的環境，然後執行 Python。 |

## <a name="kerashttpskerasio"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| 支援的版本 | 2.2.4 |
| 支援的 DSVM 版本      | Windows 和 Linux   |
| 它是如何在 DSVM 上設定/安裝的？  | Keras 安裝在 Windows 上的 Python 3.6 和 Linux 中的 Python 3。5 |
| 如何執行它      | 在終端機上啟用正確的環境，然後執行 Python。 |

## <a name="microsoft-cognitive-toolkit-cntkhttpsdocsmicrosoftcomcognitive-toolkit"></a>[Microsoft Cognitive Toolkit （CNTK）](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| 支援的版本 | 2.5.1 |
| 支援的 DSVM 版本      | Windows 和 Linux   |
| 它是如何在 DSVM 上設定/安裝的？  | CNTK 安裝在[Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)上的 python 3.6 和[Linux](./dsvm-languages.md#python-linux-edition)上的 python 3.5 中 |
| 如何執行它      | 終端機啟動正確的環境，然後執行 Python。 <br/>Jupyter連接到[Jupyter](provision-vm.md)或[JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然後開啟 CNTK 目錄來尋找範例。 |

## <a name="pytorchhttpspytorchorg"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| 支援的版本 | 1.2.0 |
| 支援的 DSVM 版本      | Linux |
| 它是如何在 DSVM 上設定/安裝的？  | 安裝在[Python 3.5](dsvm-languages.md#python-linux-edition)中。 包含範例 Jupyter 筆記本，範例位於/dsvm/samples/pytorch。 |
| 如何執行它      | 終端機啟用正確的環境，然後執行 Python。<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)：連接]，然後開啟 [PyTorch] 目錄以取得範例。  |

## <a name="tensorflowhttpswwwtensorfloworg"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| 支援的版本 | 1.13 |
| 支援的 DSVM 版本      | Windows、Linux |
| 它是如何在 DSVM 上設定/安裝的？  | 安裝在[Linux](dsvm-languages.md#python-linux-edition)上的 python 3.5 和[Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)上的 python 3。6 |
| 如何執行它      | 終端機啟用正確的環境，然後執行 Python。 <br/> Jupyter連接到[Jupyter](provision-vm.md)或[JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然後開啟 TensorFlow 目錄來尋找範例。   |

## <a name="tensorflow-servinghttpswwwtensorfloworgserving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| 支援的版本 | 1.12 |
| 支援的 DSVM 版本      | Linux |
| 它是如何在 DSVM 上設定/安裝的？  | tensorflow_model_server 可在終端機上使用。 |
| 如何執行它      |  可從[線上](https://www.tensorflow.org/serving/)取得範例。   |


## <a name="theanohttpsgithubcomtheanotheano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| 支援的版本 | 1.0.3 |
| 支援的 DSVM 版本      | Linux |
| 它是如何在 DSVM 上設定/安裝的？  |Theano 安裝在 Python 2.7 （_root_）和 python 3.5 （_py35_）環境中。 |
| 如何執行它      |  終端機啟用您想要的 Python 版本（root 或 py35），執行 Python，然後匯入 Theano。<br/>Jupyter選取 Python 2.7 或3.5 核心，然後匯入 Theano。  <br/>若要解決最近的數學核心程式庫（MKL）錯誤，您必須先設定 MKL 執行緒層級，如下所示：<br/><br/>`export MKL_THREADING_LAYER=GNU`  |