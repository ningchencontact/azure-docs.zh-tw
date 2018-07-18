---
title: Microsoft 的其他機器學習產品 - Azure Machine Learning | Microsoft Docs
description: 除了 Azure Machine Learning，Microsoft 還提供多種不同的選項，可用來建置、部署及管理您的機器學習模型。
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: overview
ms.date: 04/11/2018
ms.openlocfilehash: 20fd51f8906861e725b80202235e1684c31fd562
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268394"
---
# <a name="other-machine-learning-products-and-services-from-microsoft"></a>Microsoft 的其他機器學習產品和服務

除了 [Azure Machine Learning](overview-what-is-azure-ml.md)，Microsoft 還提供了多種不同的選項，可用來建置、部署及管理您的機器學習模型。 
* SQL Server Machine Learning 服務
* Microsoft Machine Learning Server
* Azure 資料科學虛擬機器
* HDInsight 中的 Spark MLLib
* Batch AI 定型服務
* Microsoft Cognitive Toolkit (CNTK)
* Azure 認知服務


## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning 服務
[SQL Server Microsoft Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) 可讓您使用 R 或 Python 執行、定型和部署機器學習模型。 您可以使用位於內部部署與位於 SQL Server 資料庫的資料。 

當您需要在內部部署或 Microsoft SQL Server 內定型或部署模型時，請使用 Microsoft Machine Learning Services。 使用機器學習服務建置的模型可使用 Azure Machine Learning 模型管理來部署。 

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server 
[Microsoft Machine Learning 伺服器](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)是企業伺服器，用來裝載及管理 R 及 Python 處理程序的並行和分散式工作負載。 Microsoft Machine Learning 伺服器可在 Linux、Windows、Hadoop 和 Apache Spark 上執行。 也可在 [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/) 上取得。 它會為使用 [Microsoft Machine Learning 套件](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)建置的解決方案提供執行引擎，並利用對下列案例的支援延伸開放原始碼 R 和 Python：

- 高效能分析
- 統計分析
- 機器學習服務
- 大型資料集

透過隨著伺服器安裝的專屬套件提供其他功能。 對於開發，您可以使用 IDE，例如 [Visual Studio R 工具](https://www.visualstudio.com/vs/rtvs/)和[適用於 Visual Studio 的 Python 工具](https://www.visualstudio.com/vs/python/)。

需要執行下列動作時，請使用 Microsoft Machine Learning 伺服器：

- 在伺服器上建置和部署使用 R 和 Python 建置的模型
- 在 Hadoop 或 Spark 叢集上大規模散發 R 和 Python 定型

## <a name="azure-data-science-virtual-machine"></a>Azure 資料科學虛擬機器
[資料科學虛擬機器](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)是 Microsoft Azure 雲端上的自訂虛擬機器環境，專為進行資料科學建置。 它已預先安裝和預先設定許多常用的資料科學和其他工具，以開始建置智慧應用程式進行進階分析。 資料科學 VM 可在 Windows Server 2016 和 2012 版中使用，以及在以 Ubuntu 16.04 LTS 和 OpenLogic CentOS 7.4 為基礎的發行版上的 Linux VM 中使用。 

當您需要在單一節點上執行或裝載您的作業時，請使用資料科學 VM。 或者，如果您需要從遠端相應增加在單一機器上的處理。 支援將資料科學虛擬機器作為 Azure Machine Learning 測試和 Azure Machine Learning 模型管理的目標。 

## <a name="spark-mllib-in-hdinsight"></a>HDInsight 中的 Spark MLLib
[HDInsight 中的 Spark MLLib](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) 可讓您隨著執行巨量資料的 Spark 作業建立模型。 Spark 可讓您在單一作業中輕鬆地轉換及準備資料，然後相應放大模型建立。 透過 Spark MLLib 建立的模型可以透過 Azure Machine Learning 模型管理來部署、管理和監視。 可以使用 Azure Machine Learning 測試來分派和管理定型執行。 Spark 也可用來將在 Machine Learning Workbench 中建立的資料準備作業向外延展。 

當您需要相應放大資料處理並隨著資料管線建立模型時，請使用 Spark。 您可以在 Scala、Java、Python 或 R 中撰寫 Spark 作業。 

## <a name="batch-ai-training"></a>Batch AI 定型 
[Azure Batch AI 定型](https://aka.ms/batchaitraining)可協助您使用任何架構搭配您的 AI 模型並行測試，然後在叢集化的 GPU 之間大規模定型。 描述執行所需的作業需求和設定，我們會處理其餘部分。 

Batch AI 定型可讓您相應放大叢集化的 GPU 之間的深入學習作業，使用架構如：

- 辨識工具組
- Caffe
- Chainer
- TensorFlow

Azure Machine Learning 模型管理可用來取得來自 Batch AI 定型的模型，以部署、管理和監視模型。  Batch AI 定型未來將會與 Azure Machine Learning 測試整合。 

## <a name="microsoft-cognitive-toolkit-cntk"></a>Microsoft Cognitive Toolkit (CNTK)
[Microsoft 辨識工具組](https://www.microsoft.com/en-us/cognitive-toolkit/)是深入學習工具組，在有向圖中以計算步驟形式說明類神經網路。 在有向圖中，分葉節點代表輸入值或網路參數，而其他節點則代表其輸入的矩陣作業。 辨識工具組可讓您輕鬆地了解並結合受歡迎的模型類型，例如摘要轉寄 DNN、卷積類神經網路 (CNN) 以及循環式網路 (RNN/LSTM)。 它使用學習自跨多個 GPU 和伺服器的自動差異化和平行化來實作隨機梯度下降 (SGD，錯誤反向傳播)。

當您想要使用深入學習建置模型時，請使用辨識工具組。  辨識工具組可以用於任何先前的服務。

## <a name="azure-cognitive-services"></a>Azure 認知服務
[Azure 認知服務](https://docs.microsoft.com/azure/#pivot=products&panel=ai)是有大約 30 個 API 的組合，可讓您建置使用自然通訊方法的應用程式。 只要幾行程式碼，這些 API 即讓您的應用程式查看、聽取、說出、了解並解譯使用者的需求。 輕鬆地將智慧型功能新增至您的應用程式，例如： 

- 情緒和人氣偵測
- 願景與語音辨識
- Language Understanding (LUIS)
- 知識和搜尋

認知服務可以用來開發跨裝置及平台的應用程式。 API 會持續改進，且易於設定。 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>後續步驟

閱讀 [Azure Machine Learning](overview-what-is-azure-ml.md) 概觀。
