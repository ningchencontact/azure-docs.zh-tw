---
title: "深入學習預測性維護實際案例 - Azure | Microsoft Docs"
description: "了解如何複寫使用 Azure Machine Learning Workbench 之預測性維護深入學習的教學課程。"
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 7d4fe98b5c45767fb06391218e80789fc0c96a3b
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenario"></a>深入學習預測性維護實際案例。

深入學習是機器學習其中一個最熱門的趨勢，包含許多領域的應用，包括：
- 無人車和機器人
- 語音和影像辨識
- 財務預測。

還有廣為人知的深度類神經網路 (DNN)，這些方法是受到大腦內的個別神經元 (生物神經網路) 所啟發。

對所有企業來說，設備在非排程的時間停機將會造成不利影響。 務必讓現場設備保持運作，並將代價很高的非排程停機時間降到最低，才能達到最大使用率和效能。 及早發現問題有助於以更符合成本效益的方式配置為數不多的維護資源，並增強品質和供應鏈流程。 

預測性維護 (PM) 策略使用機器學習方法判斷設備狀況以事先執行維護作業，避免發生電腦效能不良的情況。 在 PM 中，系統會收集一段時間的資料以 監視機器的狀態，然後分析資料以找出可用來預測故障的模式。 [長短期記憶 (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) 網路對此設定很有吸引力，因為它們就是針對從資料序列中學習所設計的。

## <a name="link-to-the-gallery-github-repository"></a>連結至資源庫 GitHub 存放庫

以下是問題報告與解答的公用 GitHub 存放庫連結：[https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) 

## <a name="use-case-overview"></a>使用案例概觀

本教學課程會使用模擬飛機引擎「運轉到故障 (run-to-failure)」事件的範例，來示範預測性維護的模型化流程。 [預測性維護](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3)已提供此案例說明

這個設定的主要假設為引擎效能在其存留期期間逐漸降級。 降級狀況可以在引擎感應器測量中進行偵測。 PM 會嘗試為感應器值變更和過往故障之間的關係建立模型。 然後可以透過該模型，依據感應器測量的目前狀態，預測引擎未來可能發生故障的時間。

此案例會建立一個 LSTM 網路，以使用過往感應器值預測飛機引擎剩餘使用年限 (RUL)。 此案例使用 [Keras](https://keras.io/) 搭配 [Tensorflow](https://www.tensorflow.org/) 深入學習架構作為運算引擎，透過一組引擎訓練 LSTM，然後在一組看不見的引擎上測試網路。

## <a name="prerequisites"></a>先決條件
- [Azure 帳戶](https://azure.microsoft.com/free/) (提供免費試用)。
- Azure Machine Learning Workbench，並已建立工作區。
- 針對模型運作：Azure Machine Learning Operationalization (已設定本機部署環境)，以及 [Azure Machine Learning 模型管理帳戶](model-management-overview.md)。

## <a name="create-a-new-workbench-project"></a>建立新的 Workbench 專案

使用此範例作為範本來建立新專案：

1. 開啟 Machine Learning Workbench。
2. 在 [專案] 頁面上，選取 **+**，然後選取 [新增專案]。
3. 在 [建立新專案] 窗格中，輸入新專案的資訊。
4. 在 [搜尋專案範本] 搜尋方塊中輸入「預測性維護」，並選取 [預測性維護的深入學習案例] 範本。
5. 按一下 [建立] 按鈕

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

範本會使用檔案 **PM_train.txt****PM_test.txt** 和 **PM_truth.txt** 中的三個資料集作為輸入。 

-  **定型資料**：飛機引擎運轉到故障資料。 定型資料 (PM_train.txt) 包含多個多變量時間序列，並以「循環」作為時間單位。 針對每個循環，它包含了 21 個感應器讀數。 

    - 每個時間序列都是從相同類型的不同引擎所產生的。 每個引擎啟動時都會具有不同程度的初始損耗和一些獨特的製造變化。 此資訊對於使用者是未知的。 

    - 在此模擬資料中，引擎會假設為在每個時間序列的開頭正常運作。 然後開始在運作循環序列期間的某個時間點降低效能。 效能會逐漸降低且降低幅度會慢慢擴大。 

    - 當到達預先定義的臨界值時，系統就會將引擎視為不再能安全地運作。 每個時間序列的最後一個週期都會是該引擎發生故障的時間點。

-   **測試資料**：飛機引擎運作資料，未記錄故障事件。 測試資料 (PM_test.txt) 具有與定型資料相同的資料結構描述。 唯一的差別在於此資料不會指出故障發生的時間 (最後一個時間期間「不」代表故障點)。 在此引擎故障之前可以再持續運作多少循環不得而知。

- **真實資料**：測試資料中每個引擎真實剩餘循環的資訊。 基本真實資料會提供測試資料中引擎的剩餘工作循環數。

## <a name="scenario-structure"></a>案例結構

案例工作流程已分成三個步驟，每個步驟都會在 Jupyter 筆記本中執行。 每個筆記本都會產生資料構件，這些構件會保存在本機，以供在下列筆記本中使用： 

### <a name="task-1-data-ingestion-and-preparation"></a>工作 1：資料擷取和準備

`Code/1_data_ingestion_and_preparation.ipnyb` 中的 Data Ingestion Jupyter Notebook 會將三個輸入資料集載入 Pandas 資料框架格式。 然後，它會準備資料以進行模型化，並且執行一些初步的資料視覺化。 資料集儲存在本機計算內容中，以供在模型建置筆記本中使用。

### <a name="task-2-model-building-and-evaluation"></a>工作 2：模型建置和評估

`Code/2_model_building_and_evaluation.ipnyb` 中的 Model Building Jupyter Notebook 會從磁碟讀取訓練和測試資料集，並建置訓練資料所設定的 LSTM 網路。 會在測試集上測量模型效能。 產生的模型會序列化並儲存在本機計算內容，以便用於運作工作。

### <a name="task-3-operationalization"></a>工作 3：運作

`Code/3_operationalization.ipnyb` 中的 Operationalization Jupyter Notebook 會使用預存模型來建置函式和結構描述，以便在 Azure 主控的 Web 服務上呼叫模型。 該筆記本會測試函式，然後將資產壓縮到 `LSTM_o16n.zip` 檔案中，該檔案會載入到您的 Azure 儲存體容器中以供部署。

`LSTM_o16n.zip` 部署檔案包含下列構件：

- `webservices_conda.yaml` 定義在部署目標上執行 LSTM 模型時所需的 Python 套件。  
- `service_schema.json` 定義 LSTM 模型所預期的資料結構描述。     
- `lstmscore.py` 定義部署目標為新資料打分數時所執行的函式。    
- `modellstm.json` 定義 LSTM 架構。 lstmscore.py 函式會讀取架構和權數，以將模型初始化。
- `modellstm.h5` 定義模型權數。
- `test_service.py` 為測試指令碼，會呼叫包含測試資料記錄的部署結束點。 
- `PM_test_files.pkl` `test_service.py` 指令碼會從 `PM_test_files.pkl` 檔案讀取歷史引擎資料，並傳送足夠的循環給 Web 服務，讓 LSTM 傳回引擎故障的機率。

該筆記本會先使用模型定義來測試函式，然後才封裝運作資產以供部署。 `Code/3_operationalization.ipnyb` 筆記本的結尾處包含用於設定和測試 Web 服務的指示。

## <a name="conclusion"></a>結論

本教學課程使用簡單的案例運用感應器值進行預測。 更進階的預測維護案例 (例如[預測性維護模型建構指南 R Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1)) 可以使用許多資料來源，例如歷史維護記錄、錯誤記錄檔，以及機器功能。 其他資料來源可能需要經過不同的處理，才能搭配深入學習使用。


## <a name="references"></a>參考

許多不同的平台上也提供了其他預測性維護使用案例範例：

* [預測性維護解決方案範本](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [預測性維護建模指南](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [使用 SQL R 服務的預測性維護建模指南](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [預測性維護建模指南 Python 筆記本](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [使用 PySpark 的預測性維護](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

* [預測性維護實際案例](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-predictive-maintenance)

## <a name="next-steps"></a>後續步驟

Azure Machine Learning Workbench 內有提供許多其他範例案例來示範此產品的其他功能。 