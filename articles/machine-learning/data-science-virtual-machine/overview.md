---
title: 適用於 Linux 和 Windows 的 Azure 資料科學虛擬機器簡介 | Microsoft Docs
description: 適用於 Windows 和 Linux 資料科學虛擬機器的重要分析案例與元件。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/22/2019
ms.author: vijetaj
ms.openlocfilehash: 22192d67a946f9f466848b65b9cd763b16e6c5f0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099376"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>適用於 Linux 和 Windows 的 Azure 資料科學虛擬機器是什麼？

資料科學虛擬機器 (DSVM) 是 Azure 雲端平台上的自訂 VM 映像，專為進行資料科學建置。 它已預先安裝和預先設定許多常用的資料科學和其他工具，以開始建置智慧應用程式進行進階分析。 

工具組態皆已經由 Microsoft 的資料科學家和開發人員，以及由更廣泛的資料科學社群進行完善的測試。 此測試有助於確保穩定性和一般可行性。

下列項目有提供 DSVM：
+ Windows Server 2016、Windows Server 2012
+ Ubuntu 16.04 LTS 和 CentOS 7.4

> [!NOTE]
> 所有適用於深度學習的 VM 工具都已併入資料科學虛擬機器中。 


## <a name="what-can-i-do-with-the-dsvm"></a>DSVM 有何用途？
資料科學虛擬機器的目標在於提供無衝突、預先設定及完全整合的資料科學環境，讓所有技能等級和各個企業的資料專業人員使用。 您可以布建 DSVM，而不是自行推出可比較的工作區。 該選擇可以為您省下幾天或甚至是「幾周」  的安裝、設定和套件管理程序。 配置 DSVM 之後，您可以立即開始進行資料科學專案。

DSVM 是針對使用廣泛用途案例而設計和設定的。 您可以隨著專案需求的變更來相應增加或減少環境。 您也可以使用慣用語言來設計資料科學工作的程式，以及安裝其他工具來自訂系統以符合需求。

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>在雲端中預先設定分析桌面
DSVM 可為想將其本機桌面取代為受控雲端桌面的資料科學小組提供基準組態。 此基準確保小組的所有資料科學家都有一致的設定，用來確認實驗，並提升共同作業。 它也可減輕系統管理員的負擔，進而降低成本。 此負擔減輕後，評估、安裝及維護各種軟體套件以執行進階分析所需的時間，也得以縮短。

### <a name="data-science-training-and-education"></a>資料科學訓練和教育
教導資料科學課程的企業訓練者和教育者通常會提供虛擬機器映像。 此映像可確保他們的學生擁有一致的設定且範例如預期般運作。 

DSVM 會建立具有一致設定的隨選環境，緩解支援和不相容性挑戰。 在必須經常建置這些環境的案例中，特別是針對較短的訓練課程，可以有大幅獲益。

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>大型專案的隨選彈性容量
資料科學黑客松 (Hackathon)/競賽或大型資料模型化和探索需要相應放大硬體容量，通常是很短的期間。 DSVM 可幫助在相應放大的伺服器上快速隨選複寫資料科學環境，允許可執行強大運算資源的實驗。

### <a name="custom-compute-power-for-azure-notebooks"></a>自訂 Azure Notebooks 的計算能力
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) 是免費的託管服務，無需任何安裝程序，就能用來在雲端中開發、執行和共用 Jupyter Notebook。 免費服務層級的限制為 4 GB 記憶體和 1 GB 資料。 

若要解除所有的限制，您可以將 Notebooks 專案附加至 DSVM 或在 Jupyter 伺服器上執行的任何其他 VM。 如果您以使用 Azure Active Directory 的帳戶 (例如公司帳戶) 登入 Azure Notebooks，Notebooks 會自動顯示該帳戶關聯訂用帳戶中的任何資料科學 DSVM。 您可以[將 DSVM 連結至 Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) 以擴充可用的計算能力。

### <a name="short-term-experimentation-and-evaluation"></a>短期實驗和評估
您可以使用 DSVM，以最少的設定工作來評估或了解這類工具：

- Microsoft Machine Learning Server
- SQL Server
- Visual Studio 工具
- Jupyter
- 深度學習和機器學習工具組
- 此社群中的熱門新工具 

由於您可以快速設定 DSVM，因此您可以將其套用到其他短期使用案例中。 這些案例包括複寫已發佈的實驗、執行示範，以及遵循線上課程和會議教學課程的逐步解說。

### <a name="deep-learning"></a>深入學習
在 DSVM 中，您的訓練模型可以在以圖形處理器 (GPU) 為基礎的硬體上使用深度學習演算法。 藉由利用 Azure 平台的 VM 規模調整功能，DSVM 可協助您根據需求在雲端中使用 GPU 型硬體。 當訓練大型模型或需要高速計算同時保留相同的 OS 磁碟時，您可以切換到 GPU 型 VM。  

Windows Server 2016 版的 DSVM 預先安裝了 GPU 驅動程式、架構和 GPU 版本的深入學習架構。 在 Linux 版本上，GPU 的深入學習於 CentOS 和 Ubuntu DSVM 上啟用。 

您可以將 Ubuntu、CentOS 或 Windows-2016 版 DSVM 部署到非 GPU 型的 Azure 虛擬機器。 在此情況下，所有深度學習架構都會回復為 CPU 模式。
 
[深入了解可用的深度學習和 AI 架構](dsvm-deep-learning-ai-frameworks.md)。

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>DSVM 包含哪些內容？
資料科學虛擬機器已安裝和設定許多常用的資料科學和深入學習工具。 它也包含工具，可讓您輕鬆地使用各種 Azure 資料與分析產品。 這些產品包括用來建立預測模型的 Microsoft Machine Learning Server (R、Python)，以及用來大規模探索資料集的 SQL Server 2017。 DSVM 包含來自開放原始碼社群及 Microsoft 的許多其他工具，以及[範例程式碼和筆記本](dsvm-samples-and-walkthroughs.md)。 

以下是工具和平台的清單：
+ [支援的程式設計語言](dsvm-languages.md)

+ [支援的資料平台](dsvm-data-platforms.md)

+ [開發工具和 IDE](dsvm-tools-development.md)

+ [深度學習和 AI 架構](dsvm-deep-learning-ai-frameworks.md)

+ [機器學習與資料科學工具](dsvm-ml-data-science-tools.md)

+ [資料擷取工具](dsvm-tools-ingestion.md)

+ [資料探索和虛擬化工具](dsvm-tools-explore-and-visualize.md)

下表列舉並比較 Windows 和 Linux 版本的資料科學虛擬機器所包含的主要元件。

| **工具**                                                           | **Windows 版本** | **Linux 版本** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| 已預先安裝熱門套件的 [Microsoft R Open](https://mran.microsoft.com/open/)   |Y                      | Y             |
| [Microsoft Machine Learning Server (R、Python)](https://docs.microsoft.com/machine-learning-server/) Developer 版本包括： <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) 平行和分散式高效能架構 (R 及 Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package)，Microsoft 的最新機器學習演算法 <br />  &nbsp;&nbsp;&nbsp;&nbsp; [R 和 Python 運算化](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |Y                      | Y |
| 具有共用啟用的 [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) 專業增強版：Excel、Word 和 PowerPoint   |Y                      |N              |
| 已預先安裝熱門套件的 [Anaconda Python](https://www.continuum.io/) 2.7 及 3.5    |Y                      |Y              |
| 已預先安裝 Julia 語言熱門套件的 [JuliaPro](https://juliacomputing.com/products/juliapro.html)                         |Y                      |Y              |
| 關聯式資料庫                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS)、<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| 資料庫工具                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [bcp、sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC/JDBC 驅動程式|  [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (查詢工具)， <br />  bcp、sqlcmd <br />  ODBC/JDBC 驅動程式|
| 可調整的資料庫內分析與 SQL Server 機器學習服務 (R、Python) | Y     |N              |
| 採用下列核心的 [Jupyter Notebook Server](https://jupyter.org/)：                                  | Y     | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; R | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (僅限 Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp; SparkR     | N | Y |
| JupyterHub (多使用者 Notebook 伺服器)| N | Y |
| JupyterLab (多使用者 Notebook 伺服器) | N | Y (僅限 Ubuntu) |
| 開發工具、IDE 和程式碼編輯器：| | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/)，含 Git Plugin、Azure HDInsight (Hadoop)、Azure Data Lake、SQL Server Data Tools、[Node.js](https://github.com/Microsoft/nodejstools)、[Python](https://aka.ms/ptvs) 和 [Visual Studio R 工具 (RTVS)](https://microsoft.github.io/RTVS-docs/) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (Julia IDE)](https://junolab.org/)| Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; Vim 和 Emacs | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; Git 和 Git Bash | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; OpenJDK | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework | Y | N |
| Power BI Desktop | Y | N |
| SDK，用以存取服務的 Azure 和 Cortana Intelligence Suite | Y | Y |
| 資料移動和管理工具： | | |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure 儲存體總管 | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure CLI](https://docs.microsoft.com/cli/azure) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure PowerShell | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Blob FUSE 驅動程式](https://github.com/Azure/azure-storage-fuse) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Cosmos DB 資料移轉工具](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft 資料管理閘道](https://msdn.microsoft.com/library/dn879362.aspx)：在內部部署環境和雲端之間移動資料 | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; Unix/Linux 命令列工具 | Y | Y |
| 用於資料探索的 [Apache Drill](https://drill.apache.org) | Y | Y |
| 機器學習工具： |||
| &nbsp;&nbsp;&nbsp;&nbsp; 與 [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) 整合 (R、Python) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (僅限 Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) | N | Y (僅限 Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/)、[Sparkling Water](https://www.h2o.ai/sparkling-water/) | N | Y (僅限 Ubuntu) |
| 適用於 GPU 或 CPU 的深度學習工具： |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](https://www.tensorflow.org/) | Y (Windows 2016) | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](https://github.com/uber/horovod) | N | Y (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet](https://mxnet.io/) | Y (Windows 2016) | Y|
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe 和 Caffe2](https://github.com/caffe2/caffe2) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](https://chainer.org/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Torch](http://torch.ch/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Theano](https://github.com/Theano/Theano) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](https://keras.io/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](https://pytorch.org/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet Model Server](https://github.com/awslabs/mxnet-model-server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow Serving](https://www.tensorflow.org/serving/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorRT](https://developer.nvidia.com/tensorrt) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [CUDA、cuDNN、NVIDIA 驅動程式](https://developer.nvidia.com/cuda-toolkit) | Y | Y |

## <a name="next-steps"></a>後續步驟

請透過下列文章來深入了解：

+ Windows:
  + [設定 Windows DSVM](provision-vm.md)
  + [您可以在 Windows DSVM 上進行的十件事](vm-do-ten-things.md)

+ Linux：
  + [設定 Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [設定 Linux DSVM (CentOS)](linux-dsvm-intro.md)
  + [Linux DSVM 上的資料科學](linux-dsvm-walkthrough.md)
