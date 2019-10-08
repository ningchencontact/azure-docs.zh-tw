---
title: DSVM 中包含的工具
titleSuffix: Azure Data Science Virtual Machine tools
description: Windows 和 Ubuntu DSVM 映像上包含的工具清單
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: overview
ms.date: 09/27/2019
ms.openlocfilehash: 79de3406e47b84a6120496acce5bba948fbb1a6d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803544"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Azure 資料科學虛擬機器上包含哪些工具？

以下列出資料科學虛擬機器上包含的最新工具清單，以及可讓您了解如何設定各項工具的連結。


| **工具**                                                           | **Windows DSVM** | **Linux DSVM** | **使用注意事項** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| 已預先安裝熱門套件的 [Microsoft R Open](https://mran.microsoft.com/open/)   |<span class='green-check'>&#9989;</span>                     |<span class='green-check'>&#9989;</span>  | [在 DSVM 上使用 R](./dsvm-languages.md#r)           |
| [Microsoft Machine Learning Server (R、Python)](https://docs.microsoft.com/machine-learning-server/) Developer 版本包括： <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) 平行和分散式高效能架構 (R 及 Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package)，Microsoft 的最新機器學習演算法 <br />  &nbsp;&nbsp;&nbsp;&nbsp; [R 和 Python 運算化](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span> | |
| 具有共用啟用的 [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) 專業增強版：Excel、Word 和 PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| 已預先安裝熱門套件的 [Anaconda Python](https://www.continuum.io/) 2.7 及 3.5    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | |
| 已預先安裝 Julia 語言熱門套件的 [JuliaPro](https://juliacomputing.com/products/juliapro.html)                         |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | [在 DSVM 上使用 Julia](./dsvm-languages.md#julia) |
| 關聯式資料庫                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS)、<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | (./dsvm-data-platforms#sql-server-2016-developer-edition.md) |
| 資料庫工具                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [bcp、sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC/JDBC 驅動程式|  [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (查詢工具)， <br />  bcp、sqlcmd <br />  ODBC/JDBC 驅動程式| |
| 可調整的資料庫內分析與 SQL Server 機器學習服務 (R、Python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| 採用下列核心的 [Jupyter Notebook Server](https://jupyter.org/)：                                  |<span class='green-check'>&#9989;</span>     |<span class='green-check'>&#9989;</span> | [Jupyter Notebook 範例](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp;&nbsp;&nbsp;&nbsp; R |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [R Jupyter 範例](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Python Jupyter 範例](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Julia Jupyter 範例](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [pySpark Jupyter 範例](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (僅限 Ubuntu) | |
|     &nbsp;&nbsp;&nbsp;&nbsp; SparkR     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (多使用者 Notebook 伺服器)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (多使用者 Notebook 伺服器) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (僅限 Ubuntu) | |
| 開發工具、IDE 和程式碼編輯器：| | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/)，含 Git Plugin、Azure HDInsight (Hadoop)、Azure Data Lake、SQL Server Data Tools、[Node.js](https://github.com/Microsoft/nodejstools)、[Python](https://aka.ms/ptvs) 和 [Visual Studio R 工具 (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 上的 Visual Studio 2019](./dsvm-tools-development.md#visual-studio-2019) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上的 Visual Studio Code](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上的 RStudio Desktop](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [DSVM 上的 RStudio Server](./dsvm-tools-development.md#rstudio--server)
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [DSVM 上的 PyCharm](./dsvm-tools-development.md#pycharm)
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (Julia IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上的 Juno](./dsvm-tools-development.md#juno)
| &nbsp;&nbsp;&nbsp;&nbsp; Vim 和 Emacs |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; Git 和 Git Bash |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; OpenJDK |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| Power BI Desktop |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   |
| SDK，用以存取服務的 Azure 和 Cortana Intelligence Suite |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上的 Power BI Desktop](./dsvm-tools-development.md#power-bi-desktop) |
| 資料移動和管理工具： | | |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure 儲存體總管 |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure CLI](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure PowerShell |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 上的 Azcopy](./dsvm-tools-ingestion.md#azcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Blob FUSE 驅動程式](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [DSVM 上的 blobfuse](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 上的 Adlcopy](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Cosmos DB 資料移轉工具](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 上的 Cosmos DB](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft 資料管理閘道](https://msdn.microsoft.com/library/dn879362.aspx)：在內部部署環境和雲端之間移動資料 |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 使用方式](./dsvm-tools-ingestion.md#microsoft-data-management-gateway) |
| &nbsp;&nbsp;&nbsp;&nbsp; Unix/Linux 命令列工具 |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| 用於資料探索的 [Apache Drill](https://drill.apache.org) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上的 Apache Drill](./dsvm-tools-explore-and-visualize.md#apache-drill)
| 機器學習工具： ||||
| &nbsp;&nbsp;&nbsp;&nbsp; 與 [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) 整合 (R、Python) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Azure ML SDK](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上的 XGBoost](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上的 Vowpal Wabbit](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp;&nbsp;&nbsp;&nbsp; [Weka](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上的 Weka](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (僅限 Ubuntu) | [DSVM 上的 LightGBM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (僅限 Ubuntu) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/)、[Sparkling Water](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (僅限 Ubuntu) | [DSVM 上的 H20](./dsvm-ml-data-science-tools.md#h2o) |
| 適用於 GPU 或 CPU 的深度學習工具： |  |  | 如需使用方式的詳細資訊，請按一下以下工具名稱 |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) (Windows 2016) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |<span class='green-check'>&#9989;</span> (Windows 2016) |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](./dsvm-deep-learning-ai-frameworks.md#horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (Ubuntu) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe 和 Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](./dsvm-deep-learning-ai-frameworks.md#chainer) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](./dsvm-deep-learning-ai-frameworks.md#keras)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](./dsvm-deep-learning-ai-frameworks.md#pytorch)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow Serving](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [CUDA、cuDNN、NVIDIA 驅動程式](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
