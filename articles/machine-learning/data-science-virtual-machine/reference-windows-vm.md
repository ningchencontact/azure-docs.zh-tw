---
title: 參考：Windows DSVM
description: Windows 資料科學 VM 中包含的工具詳細資料
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: be4397de477891f2a698c9f7dcb131da79479529
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200024"
---
# <a name="reference-windows-data-science-virtual-machine"></a>參考：Windows 資料科學虛擬機器

如需 Windows 資料科學虛擬機器上可用工具的清單，請參閱下文。 

## <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer 版本

您可以使用 Microsoft Enterprise Library 進行分析，因為 VM 上已安裝 Machine Learning Server Developer 版本。 Machine Learning Server 先前稱為 Microsoft R Server，是一個能夠廣泛部署的分析平台。 它具備擴充性，且支援商業用途。

Machine Learning Server 支援各種巨量資料統計資料、預測模型和機器學習工作。 它支援全範圍的分析：探索、分析、視覺化和模型化。 藉由使用及擴充開放原始碼的 R 和 Python，Machine Learning Server 可與 R 和 Python 指令碼和函式相容。 它也可與 CRAN、pip 和 Conda 套件相容，以分析企業規模的資料。

Machine Learning Server 會藉由新增資料的平行和區塊處理，解決開放原始碼 R 的記憶體內部限制問題。 這表示您可以對遠大於主記憶體可負荷的資料量執行分析。 

VM 隨附 Visual Studio Community。 它具有 Visual Studio R 工具和 Visual Studio Python 工具 (PTVS) 擴充功能，提供搭配 R 或 Python 使用的完整整合式開發環境 (IDE)。 我們也提供其他 IDE，例如 VM 上的 [RStudio](https://www.rstudio.com) 和 [PyCharm Community 版本](https://www.jetbrains.com/pycharm/)。

## <a name="python"></a>Python

為了能夠使用 Python 進行開發，已安裝 Anaconda Python 散發套件 2.7 與 3.6。 這些散發套件具有基底 Python 以及大約 300 個最受歡迎的數學運算、工程設計和資料分析封裝。 您可以使用 Visual Studio Community 2017 中安裝的 PTVS。 或者，您可以使用其中一個隨附於 Anaconda 的 IDE，像是 IDLE 或 Spyder。 搜尋並開啟其中一個套件 (Windows 標誌鍵 + S)。

> [!NOTE]
> 若要將「適用於 Visual Studio 的 Python 工具」指向 Anaconda Python 2.7，您必須為每個版本建立自訂的環境。 若要在 Visual Studio 2017 Community 中設定這些環境路徑，請移至 [工具] > [Python 工具] > [Python 環境]。 然後選取 [+ 自訂]。

Anaconda Python 3.6 安裝於 C:\Anaconda 下。 Anaconda Python 2.7 則安裝在 C:\Anaconda\envs\python2 下。 如需詳細步驟，請參閱 [PTVS 文件](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters)。

## <a name="the-jupyter-notebook"></a>Jupyter Notebook

Anaconda 散發套件也隨附 Jupyter Notebook，這是一個共用程式碼與分析的環境。 Jupyter Notebook 伺服器已預先設定 Python 2.7、Python 3.x、PySpark、Julia 及 R 核心。 若要啟動 Jupyter 伺服器並開啟瀏覽器以存取 Notebook 伺服器，請使用 **Jupyter Notebook** 桌面圖示。

我們在 Python 和 r 中封裝數個範例 Notebook。在您存取 Jupyter 之後，Notebook 會示範如何使用下列技術：

* Machine Learning Server
* SQL Server 機器學習服務、資料庫內分析
* Python
* Microsoft 辨識工具組
* TensorFlow
* 其他 Azure 技術

當您使用先前建立的密碼向 Jupyter Notebook 驗證之後，您可在 Notebook 首頁看到範例的連結。

## <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM 包含 Visual Studio Community。 這是 Microsoft 提供的熱門 IDE 的免費版本，可供用於進行評估，且適合小型團隊。 請參閱 [Microsoft 軟體授權條款](https://www.visualstudio.com/support/legal/mt171547)。

使用桌面圖示或 [開始] 功能表來開啟 Visual Studio。 搜尋程式 (Windows 標誌鍵 + S)，後面接著 **Visual Studio**。 在該處，您可以使用像是 C#、Python、R 及 Node.js 等語言來建立專案。 已安裝的外掛程式方便您使用下列 Azure 服務：

* Azure 資料目錄
* Azure HDInsight for Hadoop/Spark
* Azure Data Lake

名為 Azure Machine Learning for Visual Studio Code 的外掛程式也可與 Azure Machine Learning 整合，並協助您快速建置 AI 應用程式。

> [!NOTE]
> 您可能會收到一則訊息，表示您的評估期間已過期。 請輸入您的 Microsoft 帳戶認證。 或建立新的免費帳戶，以取得 Visual Studio Community 的存取權。

## <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition

DSVM 隨附於具有機器學習服務的 SQL Server 2017 開發人員版本。 這個 SQL Server 版本是以 R 或 Python 提供，而且可以執行資料庫內分析。 

機器學習服務提供用於開發和部署智慧型應用程式的平台。 您可以使用這些語言和社群的許多封裝來建立模型，並為 SQL Server 資料產生預測。 您可以讓分析貼近資料，因為機器學習服務(資料庫內) 會整合 R 和 Python 語言與 SQL Server。 此整合可免除與資料移動相關聯的成本和安全性風險。

> [!NOTE]
> SQL Server Developer 版本只能用於進行開發和測試。 您需要授權，才能在生產環境中執行它。

您可藉由開啟 Microsoft SQL Server Management Studio 來存取 SQL Server。 您的 VM 名稱會填入為 [伺服器名稱]。 在 Windows 上以系統管理員身分登入時，請使用 Windows 驗證。 當您在 SQL Server Management Studio 中，您可以建立其他使用者、建立資料庫、匯入資料以及執行 SQL 查詢。

若要使用 SQL Server 機器學習服務來啟用資料庫內分析，請以伺服器系統管理員身分登入，然後在 SQL Server Management Studio 中執行下列命令 (一次性動作)：

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

以您的 VM 名稱取代 `%COMPUTERNAME%`。

## <a name="azure"></a>Azure

有數個 Azure 工具會安裝於 VM 上：

* 前往 Azure SDK 文件的桌面捷徑。
* 使用 AzCopy 在 Azure 儲存體帳戶的內部和外部複製資料。 若要查看使用情況，在命令提示字元中輸入 **Azcopy**。
* 使用 Azure 儲存體總管來瀏覽您儲存在 Azure 儲存體帳戶中的物件。 它也會從 Azure 儲存體來回複製資料。 若要存取此工具，請在 [搜尋] 欄位中輸入**儲存體總管**。 或在 Windows [開始] 功能表上找到。
* Adlcopy 會將資料複製到 Azure Data Lake。 若要查看使用情況，請在命令提示字元中輸入 **adlcopy**。
* dtui 工具可從 Azure Cosmos DB (雲端中的 NoSQL 資料庫) 往返複製資料。 請在命令提示字元中輸入**dtui**。
* 整合執行階段可在內部部署資料來源與雲端之間複製資料。 其使用於 Azure Data Factory 之類的工具。
* 使用 Azure PowerShell，以 Powershell 指令碼語言管理 Azure 資源。 它也會安裝在 VM 上。

## <a name="power-bi"></a>Power BI

DSVM 已安裝 Power BI Desktop，以協助您建置儀表板和視覺效果。 使用此工具以從不同來源提取資料，來撰寫您的儀表板和報告並發佈至雲端。 如需詳細資訊，請參閱 [Power BI 網站](https://powerbi.microsoft.com)。 您可以在 [開始] 功能表上找到 Power BI 桌面。

> [!NOTE]
> 您需要 Microsoft Office 365 帳戶才能存取 Power BI。

## <a name="azure-machine-learning-sdk-for-python"></a>適用於 Python 的 Azure Machine Learning SDK

資料科學家和 AI 開發人員可使用 [Azure Machine Learning 服務](../service/overview-what-is-azure-ml.md)搭配適用於 Python 的 Azure Machine Learning SDK，來建置及執行機器學習工作流程。 您可以在 Jupyter Notebook 或其他 Python IDE 中，使用開放原始碼架構 (例如 TensorFlow 和 scikit-learn) 與該服務進行互動。

Python SDK 會預先安裝在「Microsoft 資料科學虛擬機器」上。 若要開始使用 Python SDK，請參閱[使用 Python 來開始使用 Azure Machine Learning](../service/quickstart-create-workspace-with-python.md)。

## <a name="more-microsoft-development-tools"></a>其他 Microsoft 開發工具

您可以使用 [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) 來尋找並下載其他 Microsoft 開發工具。 Microsoft 資料科學虛擬機器桌面上也有工具的捷徑。  

## <a name="important-directories-on-the-virtual-machine"></a>虛擬機器上的重要目錄

| 項目 | 目錄 |
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

有其他問題嗎？ 請考慮建立[支援票證](https://azure.microsoft.com/support/create-ticket/)。
