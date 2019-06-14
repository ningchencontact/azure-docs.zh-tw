---
title: 以自動化機器學習 (AutoML) 在 Azure HDInsight 中的 Apache Spark 上執行 Azure Machine Learning 工作負載
description: 了解如何以自動化機器學習 (AutoML) 在 Azure HDInsight 中的 Apache Spark 上執行 Azure Machine Learning 工作負載。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 5135de0fc87af227073f96c653d928ace1a50fd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64917043"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>以自動化機器學習 (AutoML) 在 Azure HDInsight 中的 Apache Spark 上執行 Azure Machine Learning 工作負載

Azure Machine Learning 可以簡化並加速建置、 訓練和部署機器學習服務模型。 在自動化的機器學習 (AutoML) 中，您可以開始使用已定義的目標功能的定型資料，，然後逐一查看的演算法和自動選取最佳的模型，為您的資料根據訓練分數的功能選取項目組合。 HDInsight 可讓客戶佈建具有數百個節點的叢集。 在 HDInsight 叢集中的 Spark 上所執行的 AutoML 可讓使用者於這些節點使用計算容量，以向外延展的方式，執行定型工作，並以平行方式執行多個訓練作業。 這可讓使用者執行 AutoML 實驗時分享其巨量資料工作負載中的計算。
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>在 HDInsight 叢集上安裝 Azure Machine Learning

如需一般自動化的機器學習服務的教學課程，請參閱[教學課程：使用自動化機器學習建置迴歸模型](../../machine-learning/service/tutorial-auto-train-models.md)。
所有新的 HDInsight Spark 叢集是使用 AzureML AutoML SDK 預先安裝。 您可以開始使用 HDInsight 上的 AutoML 以此[Jupyter notebook 範例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi)。 此 Jupyter Notebook 會示範如何針對簡單的分類問題，使用自動化機器學習分類器。

> [!Note]
> Azure Machine Learning 套件會安裝到 Python3 Conda 環境。 已安裝的 Jupyter Notebook 應使用 PySpark3 核心來執行。

您也可以使用 Zeppelin notebook 也使用 AutoML。

> [!Note]
> 具有 Zeppelin[已知問題](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html)PySpark3 不選取正確的 Python 版本的地方。 請使用記載的解決辦法。

## <a name="authentication-for-workspace"></a>工作區的驗證

建立工作區和提交實驗都需要驗證權杖。 您可以使用 [Azure AD 應用程式](../../active-directory/develop/app-objects-and-service-principals.md)來產生此權杖。 如果帳戶未啟用多重要素驗證，則 [Azure AD 使用者](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python)也可用來產生必要的驗證權杖。  

下列程式碼片段會使用 **Azure AD 應用程式**來建立驗證權杖。

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                service_principal_id = '<Azure AD Application ID>',
                service_principal_password = '<Azure AD Application Key>'
                )
```
下列程式碼片段會使用 **Azure AD 使用者**來建立驗證權杖。

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com','my_smart_password')
```

## <a name="loading-dataset"></a>載入資料集

Spark 上的自動化機器學習會使用**資料流程**，這是延遲評估且不可變動的資料作業。  資料流程可以透過公用讀取從 Blob 載入資料集，或透過 SAS 權杖從 Blob URL 載入資料。

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

您也可以使用單次註冊來註冊具有工作區的資料存放區。

## <a name="experiment-submission"></a>實驗提交

在 [自動化的機器學習服務設定](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig)，屬性`spark_context`應設為要在分散模式下執行的封裝。 `concurrent_iterations` 屬性會決定可平行執行的反覆項目數量上限，其值應設為小於 Spark 應用程式的執行程式核心數目。

## <a name="next-steps"></a>後續步驟

* 如需有關自動化的機器學習服務背後的動機的詳細資訊，請參閱[發行模型上使用 Microsoft 的自動化機器學習服務 ！](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* 如需使用 Azure ML 自動化 ML 功能的詳細資訊，請參閱[新增自動化 Azure Machine Learning 服務中的機器學習服務功能](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Microsoft Research 中的 AutoML 專案](https://www.microsoft.com/research/project/automl/)
