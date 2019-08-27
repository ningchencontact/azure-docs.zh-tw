---
title: 資料科學虛擬機器的範例和逐步解說 - Azure | Microsoft Docs
description: 透過這些範例和逐步解說, 瞭解如何使用資料科學虛擬機器來處理一般工作和案例。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: vijetaj
ms.openlocfilehash: 628c516553c2304739229c53d725645c799d06ce
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2019
ms.locfileid: "70047846"
---
# <a name="samples-on-data-science-virtual-machines"></a>資料科學虛擬機器上的範例

Azure 資料科學虛擬機器 (Dsvm) 包含一組完整的範例程式碼。 這些範例包括以 Python 和 R 等語言 Jupyter 筆記本和腳本。
> [!NOTE]
> 如需有關如何在資料科學虛擬機器上執行 Jupyter 筆記本的詳細資訊, 請參閱[存取權 Jupyter](#access-jupyter)一節。

## <a name="quick-reference-of-samples"></a>範例的快速參考
| 範例類別 | 描述 | 位置 |
| ------------- | ------------- | ------------- |
| R 語言  | 範例說明案例, 例如如何與以 Azure 為基礎的雲端資料存放區連線, 以及如何比較開放原始碼 R 和 Microsoft Machine Learning Server。 它們也會說明如何在 Microsoft Machine Learning Server 和 SQL Server 上讓模型。 <br/> [R 語言](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python 語言  | 範例說明如何使用以 Azure 為基礎的雲端資料存放區進行連線, 以及如何使用 Azure Machine Learning 的案例。  <br/> [Python 語言](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia 語言  | 提供 Julia 中繪製和深度學習的詳細描述。 同時說明如何從 Julia 呼叫 C 和 Python。 <br/> [Julia 語言](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux：<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | 說明如何使用 Machine Learning 建立機器學習和深度學習模型。 在任何位置部署模型。 使用自動化機器學習服務和智慧型超參數調整。 此外，也使用模型管理和分散式定型。 <br/> [Machine Learning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch Notebook  | 使用以 PyTorch 為基礎的類神經網路的深度學習範例。 Notebook 範圍涵蓋初學者到進階案例。  <br/> [PyTorch Notebook](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  使用 TensorFlow 架構所實行的各種類神經網路範例和技術。 <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft 辨識工具組 <br/>   | Microsoft Cognitive Toolkit 小組所發行的深度學習範例。  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux：<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | 使用以 Caffe2 為基礎的類神經網路的深度學習範例。 數個筆記本讓使用者熟悉 Caffe2, 以及如何有效地使用它。 範例包括影像前置處理和資料集建立。 它們也包括回歸, 以及如何使用預先定型模型。 <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | 以 Python 為基礎的範例, 使用 H2O 來進行實際的問題案例。 <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML 語言  | 透過 pySpark 和 MMLSpark 使用 Apache Spark MLLib 工具組功能的範例:適用于 Apache Spark Apache Spark 2.x 的 Microsoft Machine Learning。  <br/> [SparkML 語言](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | XGBoost 中的標準機器學習範例, 適用于分類和回歸等案例。 <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>存取 Jupyter 

若要存取 Jupyter, 請選取 [桌面] 或 [應用程式] 功能表上的 [ **Jupyter** ] 圖示。 您也可以在 DSVM 的 Linux 版本上存取 Jupyter。 若要從網頁瀏覽器進行遠端存取, `https://<Full Domain Name or IP Address of the DSVM>:8000`請移至 Ubuntu 上的。

若要新增例外狀況, 並透過瀏覽器提供 Jupyter 存取, 請使用下列指導方針:


![啟用 Jupyter 例外](./media/ubuntu-jupyter-exception.png)


使用您用來登入資料科學虛擬機器的相同密碼進行登入。
<br/>

**Jupyter 首頁**
<br/>![Jupyter 首頁](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R 語言 
<br/>![R 範例](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python 語言
<br/>![Python 範例](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia 語言 
<br/>![Julia 範例](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Azure Machine Learning 範例](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch 範例](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow 範例](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK 範例](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![caffe2 範例](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O 範例](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML 範例](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost 範例](./media/xgboost-samples.png)<br/>

