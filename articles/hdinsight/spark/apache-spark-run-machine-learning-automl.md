---
title: 以自動化機器學習 (AutoML) 在 Azure HDInsight 中的 Apache Spark 上執行 Azure Machine Learning 工作負載
description: 了解如何以自動化機器學習 (AutoML) 在 Azure HDInsight 中的 Apache Spark 上執行 Azure Machine Learning 工作負載。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: howto
ms.date: 01/14/2019
ms.openlocfilehash: 58852ee5de97bef043f8c0b67d9e90cdc057912a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440078"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>以自動化機器學習 (AutoML) 在 Azure HDInsight 中的 Apache Spark 上執行 Azure Machine Learning 工作負載

Azure Machine Learning 是共同作業式的拖放工具，您可以用來依據您的資料建置、測試及部署預測分析解決方案。 Azure Machine Learning 會以 Web 服務方式發佈模型，讓自訂應用程式或 BI 工具 (例如 Excel) 都能夠很容易地使用。 自動化機器學習 (AutoML) 可協助您使用智能式自動化和最佳化來建立高品質的機器學習模型。 AutoML 會針對特定問題類型，決定要使用的正確演算法和超參數。

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>在 HDInsight 叢集上安裝 Azure Machine Learning

> [!Note]
> Azure ML 工作區目前可在下列區域中使用：eastus、eastus2 和 westcentralus。 HDInsight 叢集也應該建立在這些區域中的其中一個。

如需 Azure Machine Learning 和自動化機器學習的一般教學課程，請參閱[教學課程：在 Azure Machine Learning Studio 中建立您的第一個資料科學實驗](../../machine-learning/studio/create-experiment.md)和[教學課程：使用自動化機器學習建置迴歸模型](../../machine-learning/service/tutorial-auto-train-models.md)。
若要在 Azure HDInsight 叢集上安裝 AzureML，請在 HDInsight 3.6 Spark 2.3.0 叢集 (建議) 的前端節點和背景工作角色節點上執行指令碼動作：[install_aml](https://commonartifacts.blob.core.windows.net/automl/install_aml.sh)。 此指令碼動作可作為叢集建立程序的一部份來執行，或透過 Azure 入口網站在現有叢集上執行。

如需指令碼動作的詳細資訊，請參閱[使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](../hdinsight-hadoop-customize-cluster-linux.md)。 在安裝 Azure Machine Learning 套件和相依性時，指令碼也會下載 Jupyter Notebook 範例 (下載到 `HdiNotebooks/PySpark` 路徑的預設存放區)。 此 Jupyter Notebook 會示範如何針對簡單的分類問題，使用自動化機器學習分類器。

> [!Note]
> Azure Machine Learning 套件會安裝到 Python3 Conda 環境。 已安裝的 Jupyter Notebook 應使用 PySpark3 核心來執行。

## <a name="authentication-for-workspace"></a>工作區的驗證

建立工作區和提交實驗都需要驗證權杖。 您可以使用 [Azure AD 應用程式](../../active-directory/develop/app-objects-and-service-principals.md)來產生此權杖。 如果帳戶未啟用多重要素驗證，則 [Azure AD 使用者](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python)也可用來產生必要的驗證權杖。  

下列程式碼片段會使用 **Azure AD 應用程式**來建立驗證權杖。

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                username = '<Azure AD Application ID>',
                password = '<Azure AD Application Key>'
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

在自動化機器學習組態中，應設定 `spark_context` 屬性，才能讓套件在分散模式下執行。 `concurrent_iterations` 屬性會決定可平行執行的反覆項目數量上限，其值應設為小於 Spark 應用程式的執行程式核心數目。

## <a name="next-steps"></a>後續步驟

* 如需有關自動化機器學習背後動機的詳細資訊，請參閱[以使用 Microsoft 自動化機器學習的步調發行模型！](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)。
* [Microsoft Research 中的 AutoML 專案](https://www.microsoft.com/research/project/automl/)