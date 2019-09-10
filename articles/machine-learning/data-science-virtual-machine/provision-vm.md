---
title: 快速入門：建立 Windows
titleSuffix: Azure Data Science Virtual Machine
description: 在 Azure 上設定和建立資料科學虛擬機器以進行分析和機器學習。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 02/22/2019
ms.openlocfilehash: ac4b9b4d32d05083ceabd41207243eb483648baa
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278604"
---
# <a name="quickstart-set-up-a-windows-data-science-virtual-machine-on-azure"></a>快速入門：在 Azure 上設定 Windows 資料科學虛擬機器

Microsoft Windows 資料科學虛擬機器 (DSVM) 是 Azure 上的 Windows Server 2016 虛擬機器 (VM) 映像。 它會預先安裝並設定用於資料分析和機器學習的工具。

## <a name="included-data-science-tools"></a>內含的資料科學工具

DSVM 中包含下列工具：

* [Azure Machine Learning 服務](../index.yml)的 Python SDK。
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer 版本。
* Anaconda Python 散發套件。
* Jupyter Notebook (使用 R、Python、PySpark 核心)。
* Microsoft Visual Studio Community。
* Microsoft Power BI Desktop。
* Microsoft SQL Server 2017 Developer 版本。
* 用於本機開發與測試的獨立 Apache Spark 執行個體。
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)。
* 機器學習與資料分析工具：
  * 深度學習架構：[TensorFlow](https://www.tensorflow.org/)、[ Chainer](https://chainer.org/)、MXNet 和 Keras AI 架構都包含在 VM 上。
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit)：快速的機器學習系統，支援線上雜湊，allreduce、簡化、learning2search、主動式和互動式學習等技巧。
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/)：此工具可提供快速且正確的推進式決策樹實作。
  * [Rattle](https://togaware.com/rattle/)。 R 分析工具可讓您輕鬆地使用 R 架構開始進行資料分析與機器學習。其中包含 GUI 型資料探索，以及自動產生 R 程式碼來建立模型。
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/)：Java 中的視覺化資料採礦和機器學習服務軟體。
  * [Apache Drill](https://drill.apache.org/)：適用於 Hadoop、NoSQL 和雲端儲存體的無結構描述 SQL 查詢引擎。 可支援 ODBC 和 JDBC 介面，以便從 PowerBI、Microsoft Excel 和 Tableau 等標準 BI 工具查詢 NoSQL 和檔案。
* R 和 Python 語言的程式庫，可用於 Azure Machine Learning 和其他 Azure 服務。
* 包括 Git Bash 的 Git，可搭配原始程式碼存放庫 (例如 GitHub 和 Azure DevOps) 運作。 Git 會提供數個可在 Git Bash 和命令提示字元中存取的常用 Linux 命令列工具。 範例包括 awk、sed、perl、grep、find、wget 和 curl。
* 開發工具和編輯器 (RStudio、PyCharm)。

### <a name="about-data-science"></a>關於資料科學

資料科學涉及反覆進行一連串的工作︰

1. 尋找、載入及前置處理資料。
1. 建置與測試模型。
1. 部署要在智慧型應用程式中使用的模型。

資料科學家會使用各種工具來進行這些工作。 尋找適當的軟體版本，然後加以下載並安裝是耗費時間的工作。 DSVM 提供可佈建於 Azure 上的現成映像，其中含有已預先安裝和設定的數種常用工具，有助於節省時間。

DSVM 可快速啟動分析專案。 您可以用各種語言處理工作，包含 R、Python、SQL 與 C#。 Visual Studio 提供容易使用的整合式開發環境 (IDE) 來開發和測試您的程式碼。 Azure SDK 包含在 VM 中，以便您使用 Microsoft 雲端平台上的多種服務來建置應用程式。

此 DSVM 映像不會產生軟體費用。 您只需支付 Azure 使用費用。 費用取決於您佈建的虛擬機器大小。 如需詳細資訊，請參閱[資料科學虛擬機器頁面](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows)。

### <a name="other-dsvm-versions"></a>其他 DSVM 版本

* [Ubuntu](dsvm-ubuntu-intro.md) 映像。 其中含有類似 DSVM 的工具，以及一些深度學習架構。
* [Linux CentOS](linux-dsvm-intro.md) 映像。
* 資料科學虛擬機器的 [Windows Server 2012 版本](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows)。 有一些工具僅適用於 Windows Server 2016 版。 除此之外，本文也適用於 Windows Server 2012 版本。

## <a name="prerequisite"></a>必要條件

若要建立 Microsoft 資料科學虛擬機器，您必須具有 Azrue 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.com/free)。

## <a name="create-your-dsvm"></a>建立您的 DSVM

若要建立 DSVM 執行個體：

1. 移至 [Azure 入口網站](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)上的虛擬機器清單。 如果您尚未登入 Azure 帳戶，系統可能會提示您登入。
1. 選取底部的 [建立]  按鈕。

   ![Azure 入口網站上含有 [建立] 按鈕的虛擬機器清單](./media/provision-vm/configure-data-science-virtual-machine.png)

1. 輸入下列資訊，以設定螢幕擷取畫面的右窗格中顯示的每個步驟：

   1. **基本**：
      * **名稱**：輸入 DSVM 的名稱。
      * **VM 磁碟類型**：選取 [SSD]  或 [HDD]  。 針對 NC_v1 GPU 執行個體 (像是 Nvidia Tesla K80 型)，請選擇 [HDD]  作為磁碟類型。
      * **使用者名稱**：輸入管理帳戶識別碼。
      * **密碼**：輸入管理員帳戶密碼。
      * 訂用帳戶  ：如果您有多個訂用帳戶，請選取要在其中建立機器及計費的訂用帳戶。
      * **資源群組**：建立新的群組或使用現有群組。
      * **位置**：選取最適合的資料中心。 如需最快速的網路存取，請選取擁有您大部分資料或是最接近您實際位置的資訊中心。
   1. **大小**：選取符合您的功能需求和成本限制的伺服器類型。 如需更多 VM 大小的選項，請選取 [檢視全部]  。
   1. **設定**：  
      * **使用受控磁碟**。 如果您想要 Azure 來管理 VM 的磁碟，則選擇 [受控]  。 否則，您必須指定新的或現有的儲存體帳戶。  
      * **其他參數**。 您可以使用預設值。 如果您想要使用非預設值，可將滑鼠停留在特定欄位的資訊連結上以取得說明。
   1. **摘要**：請確認您輸入的所有資訊都正確無誤。 選取 [建立]  。

> [!NOTE]
> * 除了您在 [大小]  步驟中所選伺服器大小的計算成本之外，VM 不會產生任何其他費用。
> * 佈建需要 10 到 20 分鐘的時間。 您可以在 Azure 入口網站上檢視 VM 的狀態。

## <a name="access-the-dsvm"></a>存取 DSVM

建立並佈建 VM 之後，您可以透過遠端桌面連線加以存取。 請使用您在建立虛擬機器的**基本**步驟中設定的管理員帳戶憑證。 

您已準備好開始使用在 VM 上安裝及設定的工具。 許多工具都可透過 [開始]  功能表圖格和桌面圖示來存取。

您也可以將 DSVM 連結至 Azure Notebooks，以在 VM 上執行 Jupyter Notebook，並忽略免費服務層的限制。 如需詳細資訊，請參閱[管理和設定 Notebooks 專案](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier)。

<a name="tools"></a>

## <a name="tools-installed-on-the-dvsm"></a>DVSM 上安裝的工具

在下列各節中，請深入了解資料科學虛擬機器上所安裝的工具。

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer 版本

您可以使用 Microsoft Enterprise Library 進行分析，因為 VM 上已安裝 Machine Learning Server Developer 版本。 Machine Learning Server 先前稱為 Microsoft R Server，是一個能夠廣泛部署的分析平台。 它具備擴充性，且支援商業用途。

Machine Learning Server 支援各種巨量資料統計資料、預測模型和機器學習工作。 它支援全範圍的分析：探索、分析、視覺化和模型化。 藉由使用及擴充開放原始碼的 R 和 Python，Machine Learning Server 可與 R 和 Python 指令碼和函式相容。 它也可與 CRAN、pip 和 Conda 套件相容，以分析企業規模的資料。

Machine Learning Server 會藉由新增資料的平行和區塊處理，解決開放原始碼 R 的記憶體內部限制問題。 這表示您可以對遠大於主記憶體可負荷的資料量執行分析。 

VM 隨附 Visual Studio Community。 它具有 Visual Studio R 工具和 Visual Studio Python 工具 (PTVS) 擴充功能，提供搭配 R 或 Python 使用的完整整合式開發環境 (IDE)。 我們也提供其他 IDE，例如 VM 上的 [RStudio](https://www.rstudio.com) 和 [PyCharm Community 版本](https://www.jetbrains.com/pycharm/)。

### <a name="python"></a>Python

為了能夠使用 Python 進行開發，已安裝 Anaconda Python 散發套件 2.7 與 3.6。 這些散發套件具有基底 Python 以及大約 300 個最受歡迎的數學運算、工程設計和資料分析封裝。 您可以使用 Visual Studio Community 2017 中安裝的 PTVS。 或者，您可以使用其中一個隨附於 Anaconda 的 IDE，像是 IDLE 或 Spyder。 搜尋並開啟其中一個套件 (Windows 標誌鍵 + S)。

> [!NOTE]
> 若要將「適用於 Visual Studio 的 Python 工具」指向 Anaconda Python 2.7，您必須為每個版本建立自訂的環境。 若要在 Visual Studio 2017 Community 中設定這些環境路徑，請移至 [工具]   > [Python 工具]   > [Python 環境]  。 然後選取 [+ 自訂]  。

Anaconda Python 3.6 安裝於 C:\Anaconda 下。 Anaconda Python 2.7 則安裝在 C:\Anaconda\envs\python2 下。 如需詳細步驟，請參閱 [PTVS 文件](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters)。

### <a name="the-jupyter-notebook"></a>Jupyter Notebook

Anaconda 散發套件也隨附 Jupyter Notebook，這是一個共用程式碼與分析的環境。 Jupyter Notebook 伺服器已預先設定 Python 2.7、Python 3.x、PySpark、Julia 及 R 核心。 若要啟動 Jupyter 伺服器並開啟瀏覽器以存取 Notebook 伺服器，請使用 **Jupyter Notebook** 桌面圖示。

我們在 Python 和 r 中封裝數個範例 Notebook。在您存取 Jupyter 之後，Notebook 會示範如何使用下列技術：

* Machine Learning Server
* SQL Server 機器學習服務、資料庫內分析
* Python
* Microsoft 辨識工具組
* TensorFlow
* 其他 Azure 技術

當您使用先前建立的密碼向 Jupyter Notebook 驗證之後，您可在 Notebook 首頁看到範例的連結。

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM 包含 Visual Studio Community。 這是 Microsoft 提供的熱門 IDE 的免費版本，可供用於進行評估，且適合小型團隊。 請參閱 [Microsoft 軟體授權條款](https://www.visualstudio.com/support/legal/mt171547)。

使用桌面圖示或 [開始]  功能表來開啟 Visual Studio。 搜尋程式 (Windows 標誌鍵 + S)，後面接著 **Visual Studio**。 在該處，您可以使用像是 C#、Python、R 及 Node.js 等語言來建立專案。 已安裝的外掛程式方便您使用下列 Azure 服務：

* Azure 資料目錄
* Azure HDInsight for Hadoop/Spark
* Azure Data Lake

名為 Azure Machine Learning for Visual Studio Code 的外掛程式也可與 Azure Machine Learning 整合，並協助您快速建置 AI 應用程式。

> [!NOTE]
> 您可能會收到一則訊息，表示您的評估期間已過期。 請輸入您的 Microsoft 帳戶認證。 或建立新的免費帳戶，以取得 Visual Studio Community 的存取權。

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition

DSVM 隨附於具有機器學習服務的 SQL Server 2017 開發人員版本。 這個 SQL Server 版本是以 R 或 Python 提供，而且可以執行資料庫內分析。 

機器學習服務提供用於開發和部署智慧型應用程式的平台。 您可以使用這些語言和社群的許多封裝來建立模型，並為 SQL Server 資料產生預測。 您可以讓分析貼近資料，因為機器學習服務(資料庫內) 會整合 R 和 Python 語言與 SQL Server。 此整合可免除與資料移動相關聯的成本和安全性風險。

> [!NOTE]
> SQL Server Developer 版本只能用於進行開發和測試。 您需要授權，才能在生產環境中執行它。

您可藉由開啟 Microsoft SQL Server Management Studio 來存取 SQL Server。 您的 VM 名稱會填入為 [伺服器名稱]  。 在 Windows 上以系統管理員身分登入時，請使用 Windows 驗證。 當您在 SQL Server Management Studio 中，您可以建立其他使用者、建立資料庫、匯入資料以及執行 SQL 查詢。

若要使用 SQL Server 機器學習服務來啟用資料庫內分析，請以伺服器系統管理員身分登入，然後在 SQL Server Management Studio 中執行下列命令 (一次性動作)：

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

以您的 VM 名稱取代 `%COMPUTERNAME%`。

### <a name="azure"></a>Azure

有數個 Azure 工具會安裝於 VM 上：

* 前往 Azure SDK 文件的桌面捷徑。
* 使用 AzCopy 在 Azure 儲存體帳戶的內部和外部複製資料。 若要查看使用情況，在命令提示字元中輸入 **Azcopy**。
* 使用 Azure 儲存體總管來瀏覽您儲存在 Azure 儲存體帳戶中的物件。 它也會從 Azure 儲存體來回複製資料。 若要存取此工具，請在 [搜尋]  欄位中輸入**儲存體總管**。 或在 Windows [開始]  功能表上找到。
* Adlcopy 會將資料複製到 Azure Data Lake。 若要查看使用情況，請在命令提示字元中輸入 **adlcopy**。
* dtui 工具可從 Azure Cosmos DB (雲端中的 NoSQL 資料庫) 往返複製資料。 請在命令提示字元中輸入**dtui**。
* 整合執行階段可在內部部署資料來源與雲端之間複製資料。 其使用於 Azure Data Factory 之類的工具。
* 使用 Azure PowerShell，以 Powershell 指令碼語言管理 Azure 資源。 它也會安裝在 VM 上。

### <a name="power-bi"></a>Power BI

DSVM 已安裝 Power BI Desktop，以協助您建置儀表板和視覺效果。 使用此工具以從不同來源提取資料，來撰寫您的儀表板和報告並發佈至雲端。 如需詳細資訊，請參閱 [Power BI 網站](https://powerbi.microsoft.com)。 您可以在 [開始]  功能表上找到 Power BI 桌面。

> [!NOTE]
> 您需要 Microsoft Office 365 帳戶才能存取 Power BI。

### <a name="azure-machine-learning-sdk-for-python"></a>適用於 Python 的 Azure Machine Learning SDK

資料科學家和 AI 開發人員可使用 [Azure Machine Learning 服務](../service/overview-what-is-azure-ml.md)搭配適用於 Python 的 Azure Machine Learning SDK，來建置及執行機器學習工作流程。 您可以在 Jupyter Notebook 或其他 Python IDE 中，使用開放原始碼架構 (例如 TensorFlow 和 scikit-learn) 與該服務進行互動。

Python SDK 會預先安裝在「Microsoft 資料科學虛擬機器」上。 若要開始使用 Python SDK，請參閱[使用 Python 來開始使用 Azure Machine Learning](../service/quickstart-create-workspace-with-python.md)。

## <a name="more-microsoft-development-tools"></a>其他 Microsoft 開發工具

您可以使用 [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) 來尋找並下載其他 Microsoft 開發工具。 Microsoft 資料科學虛擬機器桌面上也有工具的捷徑。  

## <a name="important-directories-on-the-vm"></a>VM 上的重要目錄

| Item | 目錄 |
| --- | --- |
| Jupyter Notebook 伺服器組態 | C:\ProgramData\jupyter |
| Jupyter Notebook 範例的主目錄 | C:\dsvm\notebooks 和 c:\users\\<username\>\notebooks |
| 其他範例 | C:\dsvm\samples |
| Anaconda (預設值)︰Python 3.6 | C:\Anaconda |
| Anaconda Python 2.7 環境 | C:\Anaconda\envs\python2 |
| 適用於 Python 的 Microsoft Machine Learning Server (獨立式) | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| 預設 R 執行個體、Machine Learning Server (獨立式) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL Server 機器學習服務資料庫內執行個體目錄 | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| 其他工具 | C:\dsvm\tools |

> [!NOTE]
> 在 Windows Server 2012 版本的 DSVM 及 2018 年 3 月之前的 Windows Server 2016 版本上，預設的 Anaconda 環境是 Python 2.7。 次要環境是位於 C:\Anaconda\envs\py35 的 Python 3.5。

## <a name="next-steps"></a>後續步驟

* 開啟 [開始]  功能表，探索 DSVM 上的工具。
* 閱讀[什麼是 Azure Machine Learning 服務？](../service/overview-what-is-azure-ml.md)並嘗試使用[教學課程](../index.yml)，以了解 Azure Machine Learning 服務。
* 在檔案總管中，瀏覽至 C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts，以取得在 R 中使用 RevoScaleR 程式庫的範例，其支援企業規模的資料分析。 
* 閱讀文章：[您可以在資料科學虛擬機器上做的十件事](https://aka.ms/dsvmtenthings)。
* 了解如何使用 [Team Data Science Process](../team-data-science-process/index.yml)，以系統化方式建置端對端分析方案。
* 瀏覽 [Azure AI 資源庫](https://gallery.cortanaintelligence.com)，可取得在 Azure 上使用 Azure Machine Learning 和相關資料服務的機器學習和資料分析範例。 我們也已在虛擬機器的 [開始]  功能表和桌面上提供此資源庫的圖示。
