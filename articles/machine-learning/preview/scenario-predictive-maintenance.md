---
title: "預測性維護實際案例 | Microsoft Docs"
description: "使用 PySpark 的預測性維護實際案例"
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: 21cf8201236224244e6ed34f91f9c5c601ab9a79
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="predictive-maintenance-real-world-scenario"></a>預測性維護實際案例。

對所有企業來說，設備在非排程的時間停機將會造成不利影響。 務必讓現場設備保持運作，並減少代價很高的非排程停機時間，才能達到最大使用率和效能。 及早發現問題有助於以更符合成本效益的方式配置為數不多的維護資源，並增強品質和供應鏈流程。 

此案例探索相對[大規模模擬資料集](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)，以從資料擷取、功能工程、模型建置以及模型運作和部署來逐步解說預測性維護資料科學專案。 整個程序的程式碼是在 Azure ML Workbench 內利用 PySpark 以 Jupyter Notebooks 撰寫的。 最終的模型會使用 Azure Machine Learning 模型管理部署，以進行即時設備故障預測。   

## <a name="link-to-the-gallery-github-repository"></a>連結至資源庫 GitHub 存放庫

以下是問題報告與解答的公用 GitHub 存放庫的連結：[https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>使用案例概觀

在普遍擁有龐大固定資產的產業中，企業所面臨的主要問題在於，如果機械故障而導致生產延遲，將會讓成本大幅上揚。 企業無不極力預測這些問題會在何時發生，以便事先主動避免。 目標是要藉由縮短停機時間及可能提升安全性來降低成本。 

此案例的構想是參考[預測性維護腳本](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)，示範為模擬的資料集建置預測模型。 範例資料是從許多預測性維護使用案例中觀察到的常見元素所衍生的。

此模擬資料的企業問題是要預測元件故障所造成的問題。 因此，企業問題是「機器因為元件故障而當機的機率有多高？」 這個問題會格式化為多級分類問題 (每個機器的多個元件)，並使用機器學習演算法來建立預測性模型。 我們會使用從機器收集到的歷史資料來訓練模型。 在此案例中，使用者會經歷各種步驟，以在 Azure Machine Learning Workbench 環境中實作這類模型。

## <a name="prerequisites"></a>先決條件

* [Azure 帳戶](https://azure.microsoft.com/en-us/free/) (有提供免費試用)。
* 遵循[快速入門安裝指南](./quickstart-installation.md)所安裝的 [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) 版本，以便安裝程式並建立 Workbench。
* 「Azure Machine Learning 操作化」需要一個本機部署環境，以及一個[模型管理帳戶](model-management-overview.md)

此範例可在任何 AML Workbench 計算環境中執行。 不過，建議至少以 16 GB 的記憶體來執行它。 此案例是在執行遠端 DS4_V2 標準[適用於 Linux (Ubuntu) 的資料科學虛擬機器](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)的 Windows 10 電腦上建置並測試的。

模型的操作化是使用 0.1.0a22 版的 Azure ML CLI 來完成的。

## <a name="create-a-new-workbench-project"></a>建立新的 Workbench 專案

使用此範例作為範本來建立新專案：
1.  開啟 Azure Machine Learning Workbench
2.  在 [專案] 頁面上，按一下 **+** 符號，然後選取 [新增專案]
3.  在 [建立新專案] 窗格中，填入新專案的資訊
4.  在 [搜尋專案範本] 搜尋方塊中輸入「預測性維護」並選取 [預測性維護] 範本
5.  按一下 [建立] 按鈕

## <a name="prepare-the-notebook-server-computation-target"></a>準備筆記本伺服器計算目標

若要在您的本機電腦上執行，請從 AML Workbench [`File`] \(檔案\) 功能表中，選取 [`Open Command Prompt`] \(開啟命令提示字元\) 或 [`Open PowerShell CLI`] \(開啟 PowerShell CLI\)。 CLI 介面可讓您使用 `az` 命令存取您的 Azure 服務。 首先，使用以下命令以登入 Azure 帳戶：

```
az login
``` 

這個命令會提供驗證金鑰以與 `https:\\aka.ms\devicelogin` URL搭配使用。 CLI 會等候直到裝置登入作業傳回並提供一些連線資訊。 接下來，如果您已安裝本機 [docker](https://www.docker.com/get-docker)，請使用下列命令備妥本機計算環境：

```
az ml experiment prepare --target docker --run-configuration docker
```

最好是在[適用於 Linux (Ubuntu) 的資料科學虛擬機器](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)上執行，以符合記憶體和磁碟需求。 一旦設定 DSVM，使用以下兩個命令備妥遠端 Docker 環境：

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

一旦連線到遠端 Docker 容器，使用以下命令備妥 DSVM Docker 計算環境： 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

備妥 Docker 計算環境之後，在 AML Workbench [Notebook] 索引標籤內開啟 Jupyter Notebook 伺服器，或者使用以下命令啟動瀏覽器型伺服器： 
```
az ml notebook start
```

範例 Notebook 會儲存在 `Code` 目錄。 Notebook 會設定為循序執行，從第一個 (`Code\1_data_ingestion.ipynb`) Notebook 開始。 當您開啟每個 Notebook 時，系統會提示您選取計算核心。 選擇 `[Project_Name]_Template [Connection_Name]` 核心以在先前設定的 DSVM 上執行。

## <a name="data-description"></a>資料說明

[模擬資料](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)包含五個逗號分隔值 (.csv) 檔案。 請依循下列連結來取得資料集的更詳細描述。

* [機器](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv)：以特徵來區別每一部機器。 例如，存在時間和模型。
* [錯誤](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv)：錯誤記錄包含機器仍在運作時所擲回的非重大錯誤。 系統不會將這些錯誤視為故障，但這些錯誤或許能預測未來的故障事件。 遙測資料是以每小時一次的速率在收集，所以系統會將錯誤的日期時間四捨五入到最接近的整點。
* [維護](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv)：維護記錄同時包含排程和非排程的維護記錄。 排程的維護會與元件的定期檢查對應，非排程的維護則可能來自機械故障或其他效能降低情形。 遙測資料是以每小時一次的速率在收集，所以系統會將維護的日期時間四捨五入到最接近的整點。
* [遙測](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv)：遙測資料包含每個機器內多個感應器的時間序列量值。 資料是藉由平均每小時間隔的感應器值來記錄的。
* [故障](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv)：故障會與維護記錄中的元件更換事件對應。 每一筆記錄都包含機器識別碼、元件類型和更換日期與時間。 我們會使用這些記錄來建立將由模型試著預測的機器學習標籤。

請參閱＜Code＞區段中的[資料擷取](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter 筆記本案例，以從 GitHub 存放庫下載未經處理的資料集，並建立要供此分析使用的 PySpark 資料集。

## <a name="scenario-structure"></a>案例結構
案例內容可於 [GitHub 存放庫](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)取得。 

[讀我檔案](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md)檔案概述準備資料、建置模型，然後部署解決方案來實際執行的工作流程。 工作流程的每個步驟封裝在存放庫內 [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) 資料夾的 Jupyter Notebook 中。   

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb)：此筆記本會下載 5 個輸入 .csv 檔案、執行一些初步的資料清理及視覺化。 此 Notebook 會將每個資料集轉換成 PySpark 格式，然後將其儲存在 Azure Blob 容器中以在功能工程 Notebook 中使用。

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb)：使用 Azure blob 的原始資料集，針對遙測、錯誤及維護資料使用標準時間數列方法來建構模型功能。 失敗相關的元件替換會用來建構描述失敗元件的模型標籤。 已加上標籤的功能資料會儲存在 Azure Blob 中供模型建置 Notebook 使用。

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb)模型化筆記本會使用已加上標籤的功能資料集，將資料沿著時間戳記分割成訓練和開發資料集。 此筆記本是具有 `pyspark.ml.classification` 模型的已設定集合實驗。 訓練資料會向量化，而使用者可以使用 `DecisionTreeClassifier` 或 `RandomForestClassifier` 來進行實驗，藉由操作超參數來尋找效能最佳的模型。 效能是藉由評估開發資料集的相關量值統計資料來判斷的。 這些統計資料會往回記錄到 AML Workbench 執行階段畫面中以供追蹤。 每次執行時，筆記本都會將產生的模型儲存至執行 Jupyter 筆記本核心的本機磁碟。 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb)：此 Notebook 會使用儲存至本機 (Jupyter Notebook 核心) 磁碟的最後一個模型，來建置可透過部署將模型整合至 Azure Web 服務中的元件。 完整的運作資產會壓縮至儲存在另一個 Azure Blob 容器內的 `o16n.zip` 檔案中。 此 ZIP 壓縮檔案包含︰

* `service_schema.json` - 用於部署的結構描述定義檔。 
* `pdmscore.py` - Azure Web 服務所需的 init() 和 run() 函式
* `pdmrfull.model` - 模型定義目錄。
    
 此筆記本會先使用模型定義來測試函式，然後才封裝操作化資產以供部署。 部署指示包含在此筆記本的結尾。

## <a name="conclusion"></a>結論

此案例可讓讀者大致了解如何在 Azure ML Workbench 中，於 Jupyter 筆記本環境內使用 PySpark 來建置完整的預測性維護解決方案。 此範例案例也會詳細描述對 Azure Machine Learning 模型管理環境的模型部署，以進行即時設備故障預測。

## <a name="references"></a>參考

許多不同的平台上也提供了其他預測性維護使用案例：

* [預測性維護解決方案範本](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [預測性維護建模指南](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [使用 SQL R 服務的預測性維護建模指南](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [預測性維護建模指南 Python 筆記本](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [使用 PySpark 的預測性維護](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [深入學習預測性維護](
 https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>後續步驟

Azure Machine Learning Workbench 內有提供許多其他範例案例來示範此產品的其他功能。 