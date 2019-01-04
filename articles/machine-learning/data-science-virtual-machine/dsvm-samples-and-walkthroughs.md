---
title: 資料科學虛擬機器的範例和逐步解說 - Azure | Microsoft Docs
description: 了解教導您如何使用「資料科學虛擬機器」來完成常見工作和案例的範例和逐步解說。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: 64be6af340aa02c6c0b094013d2cbd286286aca7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101978"
---
# <a name="samples-on-data-science-virtual-machines"></a>資料科學虛擬機器上的範例

「Azure 資料科學虛擬機器」包含一組完整的範例程式碼。 範例程式碼採用以 Python 和 R 這類語言撰寫的 Jupyter Notebook 及指令碼形式。 
> [!NOTE]
> 如需有關如何在資料科學虛擬機器上執行 Jupyter Notebook 的詳細資訊，請參閱[存取 Jupyter](#access-jupyter) 一節。

## <a name="quick-reference-of-samples"></a>範例的快速參考
| 範例類別 | 說明 | 位置 |
| ------------- | ------------- | ------------- |
| R 語言  | 以 R 撰寫的範例會說明如何與 Azure 雲端資料存放區連線之類的案例。 它們也說明如何比較開放原始碼 R 與 Microsoft R，以及說明如何在「Microsoft R 伺服器」或 SQL Server 上讓模型能夠運作。 <br/> [R 語言](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python 語言  | 以 Python 撰寫的範例會說明如何與 Azure 雲端資料存放區連線並使用 Azure Machine Learning 之類的案例。  <br/> [Python 語言](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia 語言  | 以 Julia 撰寫的範例詳述如何以 Julia 繪圖及進行深度學習。 此外，也說明如何從 Julia 呼叫 C 和 Python。 <br/> [Julia 語言](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux：<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | 使用 Machine Learning 來建置機器學習和深度學習模型。 在任何位置部署模型。 使用自動化機器學習服務和智慧型超參數調整。 此外，也使用模型管理和分散式定型。 <br/> [Machine Learning](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch Notebook  | 運用 PyTorch 型類神經網路的深度學習範例。 Notebook 範圍涵蓋初學者到進階案例。  <br/> [PyTorch Notebook](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  使用 TensorFlow 架構來實作的各種不同類神經網路範例和技術。 <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft 辨識工具組 <br/>   | Microsoft 的辨識工具組小組發佈的深入學習範例。  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux：<br/> `~notebooks/CNTK`<br/> <br/>|
| caffe2 | 運用 caffe2 型類神經網路的深度學習範例。 數個 Notebook 將可讓使用者熟悉 caffe2 及如何有效地使用它。 範例包括映像前處理和資料集建立。 此外，也包括迴歸及如何使用預先定型的模型。 <br/> [caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | 運用 H2O 來處理許多真實案例問題的 Python 型範例。 <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML 語言  | 在 Apache Spark 2.x 上透過 pySpark 和 MMLSpark (適用於 Apache Spark 的 Microsoft Machine Learning) 利用 Spark MLlib 工具組功能的範例。  <br/> [SparkML 語言](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | 以 XGBoost 針對分類和迴歸等案例撰寫的標準機器學習範例。 <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>存取 Jupyter 

若要存取 Jupyter，請選取桌面或應用程式功能表上的 `Jupyter` 圖示。 您也可以在 Linux 版本的「資料科學虛擬機器」上存取 Jupyter。 您可以在 Ubuntu 上從網頁瀏覽器瀏覽 `https://<Full Domain Name or IP Address of the DSVM>:8000` 來進行遠端存取。

若要新增例外並允許透過瀏覽器存取 Jupyter，請參閱下列螢幕擷取畫面。


![啟用 Jupyter 例外](./media/ubuntu-jupyter-exception.png)


使用與「資料科學虛擬機器」登入相同的密碼來進行登入。
<br/>

**Jupyter 首頁**
<br/>![Jupyter 首頁](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R 語言 
<br/>![R 範例](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python 語言
<br/>![Python 範例](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia 語言 
<br/>![Julia 範例](./media/julia-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![AzurekML 範例](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch 範例](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow 範例](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK 範例](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>caffe2 
<br/>![caffe2 範例](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O 範例](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML 範例](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost 範例](./media/xgboost-samples.png)<br/>

