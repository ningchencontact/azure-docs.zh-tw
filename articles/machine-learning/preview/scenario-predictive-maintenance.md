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
ms.openlocfilehash: d8e34924cb29e2e6469d009e40b04d5cee8930a6
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2017
---
# <a name="predictive-maintenance-real-world-scenario"></a>預測性維護實際案例。

對所有企業來說，設備在非排程的時間停機將會造成不利影響。 因此，務必要讓現場設備保持運作，並減少代價很高的非排程停機時間，才能達到最大使用率和效能。 及早發現問題有助於以更符合成本效益的方式配置為數不多的維護資源，並增強品質和供應鏈流程。 

此案例中瀏覽相對[大規模的模擬的資料集](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)中引導進行預測性維護資料科學專案從資料擷取，功能工程、 模型建立及實施模型和部署。 整個程序的程式碼是以使用 Azure ML 工作臺內 PySpark Jupyter 筆記本撰寫。 最終的模型部署使用 Azure 機器學習模型管理進行即時設備故障的預測。   

## <a name="link-to-the-gallery-github-repository"></a>連結至資源庫 GitHub 存放庫

以下是公用 GitHub 存放庫的連結：[https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>使用案例概觀

在普遍擁有龐大固定資產的產業中，企業所面臨的主要問題在於，如果機械故障而導致生產延遲，將會讓成本大幅上揚。 企業無不極力預測這些問題會在何時發生，以便事先主動避免。 目標是要藉由縮短停機時間及可能提升安全性來降低成本。 

此案例會從構想[預測性維護腳本](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)示範建立模擬的資料集的預測模型。 這些範例資料被衍生自許多的預測性維護使用案例中觀察到的常見項目。

此模擬資料的企業問題是要預測元件故障所造成的問題。 因此，企業問題是「機器因為元件故障而當機的機率有多高？」 這個問題會格式化為多級分類問題 (每個機器的多個元件)，並使用機器學習演算法來建立預測性模型。 我們會使用從機器收集到的歷史資料來訓練模型。 在此案例中，使用者會經歷各種步驟，以在 Azure Machine Learning Workbench 環境中實作這類模型。

## <a name="prerequisites"></a>必要條件

* [Azure 帳戶](https://azure.microsoft.com/en-us/free/) (有提供免費試用)。
* 已安裝的版本[Azure 機器學習 Workbench](./overview-what-is-azure-ml.md)下列[快速入門安裝指南](./quickstart-installation.md)安裝程式，並建立工作區。
* 「Azure Machine Learning 操作化」需要一個本機部署環境，以及一個[模型管理帳戶](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview)

此範例可在任何 AML Workbench 計算環境中執行。 不過，建議至少以 16 GB 的記憶體來執行它。 此案例是在執行遠端 DS4_V2 標準[適用於 Linux (Ubuntu) 的資料科學虛擬機器](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)的 Windows 10 電腦上建置並測試的。

模型的操作化是使用 0.1.0a22 版的 Azure ML CLI 來完成的。

## <a name="create-a-new-workbench-project"></a>建立新的 Workbench 專案

使用此範例作為範本來建立新專案：
1.  開啟 Azure Machine Learning Workbench
2.  在 [專案] 頁面上，按一下 **+** 符號，然後選取 [新增專案]
3.  在 [建立新專案] 窗格中，填入新專案的資訊
4.  在 [搜尋專案範本] 搜尋方塊中輸入「預測性維護」並選取範本
5.  按一下 [建立] 

## <a name="prepare-the-notebook-server-computation-target"></a>準備筆記本伺服器計算目標

若要在您的本機電腦上執行，請從 AML Workbench [`File`] \(檔案\) 功能表中，選取 [`Open Command Prompt`] \(開啟命令提示字元\) 或 [`Open PowerShell CLI`] \(開啟 PowerShell CLI\)。 CLI 介面可讓您存取您的 Azure 服務使用`az`命令。 首先，登入您的 Azure 帳戶與命令：

```
az login
``` 

這個命令會提供驗證金鑰來搭配`https:\\aka.ms\devicelogin`URL。 CLI 會等到裝置登入作業會傳回並提供一些連接資訊。 下一步，如果您有本機[docker](https://www.docker.com/get-docker)安裝，請使用下列命令將本機計算環境備妥：

```
az ml experiment prepare --target docker --run-configuration docker
```

最好是在執行[Data Science 虛擬機器，適用於 Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)記憶體和磁碟需求。 DSVM 設定之後，將環境備妥遠端 docker 與下列兩個命令：

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

連接到遠端 docker 容器後，準備 DSVM docker 計算環境使用： 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

使用 docker 計算環境備妥，開啟 Jupyter 筆記本伺服器內 AML Workbench 筆記本 索引標籤，或啟動瀏覽器為基礎的伺服器： 
```
az ml notebook start
```

範例筆記本會儲存在`Code`目錄。 筆記本設定為連續執行，在第一個啟動 (`Code\1_data_ingestion.ipynb`) 筆記型電腦。 當您開啟每個筆記本時，系統會提示您選取計算核心。 選擇`[Project_Name]_Template [Connection_Name]`先前設定 DSVM 上執行的核心。

## <a name="data-description"></a>資料說明

[模擬資料](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)包含五個逗號分隔值 (.csv) 檔案。 請依循下列連結來取得資料集的更詳細描述。

* [機器](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv)：以特徵來區別每一部機器。 例如，存在時間和模型。
* [錯誤](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv)：錯誤記錄包含機器仍在運作時所擲回的非重大錯誤。 系統不會將這些錯誤視為故障，但這些錯誤或許能預測未來的故障事件。 遙測資料是以每小時一次的速率在收集，所以系統會將錯誤的日期時間四捨五入到最接近的整點。
* [維護](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv)：維護記錄同時包含排程和非排程的維護記錄。 排程的維護會與元件的定期檢查對應，非排程的維護則可能來自機械故障或其他效能降低情形。 遙測資料是以每小時一次的速率在收集，所以系統會將維護的日期時間四捨五入到最接近的整點。
* [遙測](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv)： 從每個機器內的多個感應器的遙測資料包含時間序列的量值。 記錄資料，以減低每一小時間隔內的感應器值。
* [故障](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv)：故障會與維護記錄中的元件更換事件對應。 每一筆記錄都包含機器識別碼、元件類型和更換日期與時間。 我們會使用這些記錄來建立將由模型試著預測的機器學習標籤。

請參閱＜Code＞區段中的[資料擷取](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter 筆記本案例，以從 GitHub 存放庫下載未經處理的資料集，並建立要供此分析使用的 PySpark 資料集。

## <a name="scenario-structure"></a>案例結構
案例內容可於 [GitHub 存放庫](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)取得。 

[讀我檔案](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md)檔案概述準備資料、 建立模型，然後部署解決方案來實際執行工作流程。 每個步驟的工作流程封裝在 Jupyter 筆記本中[程式碼](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code)儲存機制內的資料夾。   

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb)：此筆記本會下載 5 個輸入 .csv 檔案、執行一些初步的資料清理及視覺化。 筆記本會將每個資料集轉換為 PySpark 格式，並將它儲存到 Azure blob 容器功能工程筆記本中使用。

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb)： 使用 Azure blob，使用標準時間數列方法遙測、 錯誤及維護資料所建構功能的模型中的原始資料集。 失敗相關的元件取代項目用來建構模型標籤描述哪些元件失敗。 加上標籤的功能資料會儲存在 Azure blob 建立筆記本的模型。

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb)模型化筆記本會使用已加上標籤的功能資料集，將資料沿著時間戳記分割成訓練和開發資料集。 此筆記本是具有 `pyspark.ml.classification` 模型的已設定集合實驗。 訓練資料會向量化，而使用者可以使用 `DecisionTreeClassifier` 或 `RandomForestClassifier` 來進行實驗，藉由操作超參數來尋找效能最佳的模型。 效能是藉由評估開發資料集的相關量值統計資料來判斷的。 這些統計資料會往回記錄到 AML Workbench 執行階段畫面中以供追蹤。 每次執行時，筆記本都會將產生的模型儲存至執行 Jupyter 筆記本核心的本機磁碟。 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb)： 使用最後一個模型儲存至本機磁碟中 （Jupyter 筆記本核心），筆記本會建置部署該模型到 Azure web 服務的元件。 完整的運作資產會壓縮至儲存在另一個 Azure Blob 容器內的 `o16n.zip` 檔案中。 此 ZIP 壓縮檔案包含︰

* `service_schema.json` - 用於部署的結構描述定義檔。 
* `pdmscore.py` - Azure Web 服務所需的 init() 和 run() 函式
* `pdmrfull.model` - 模型定義目錄。
    
 此筆記本會先使用模型定義來測試函式，然後才封裝操作化資產以供部署。 部署指示包含在此筆記本的結尾。

## <a name="conclusion"></a>結論

此案例可讓讀者大致了解如何在 Azure ML Workbench 中，於 Jupyter 筆記本環境內使用 PySpark 來建置完整的預測性維護解決方案。 此範例案例還詳細描述模型部署到 Azure 機器學習模型管理環境，以進行即時設備故障的預測。

## <a name="references"></a>參考

此使用案例先前已在多個平台上開發過：

* [預測性維護解決方案範本](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [預測性維護建模指南](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [使用 SQL R 服務的預測性維護建模指南](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [預測性維護建模指南 Python 筆記本](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [使用 PySpark 的預測性維護](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

## <a name="next-steps"></a>後續步驟

Azure Machine Learning Workbench 內有提供許多其他範例案例來示範此產品的其他功能。 