---
title: "深入學習預測性維護實際案例 - Azure | Microsoft Docs"
description: "了解如何複寫使用 Azure Machine Learning Workbench 之預測性維護深入學習的教學課程。"
services: machine-learning
author: FrancescaLazzeri
ms.author: Lazzeri
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: a55209256c29fa62cc2da72f9653fbc7fc0e7c54
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>深入學習預測性維護實際案例

深入學習是機器學習其中一個最熱門的趨勢。 深入學習可用於許多領域和應用，包括無人駕駛汽車、語音和影像辨識、機器人計數及財務。 深入學習是機器學習的一種，由一組靈感來自大腦形狀 (生物類神經網路) 的演算法所組成。 認知科學家通常將深入學習視為人工類神經網路 (ANN)。

預測性維護也很熱門。 在預測性維護中，設計了許多不同的技術來協助判斷設備的狀況，並預測何時要執行維護。 預測性維護的一些常見用途是故障預測、故障診斷 (根本原因分析)、故障偵測、故障類型分類，以及故障後緩和或維護動作的建議。

在預測性維護案例中，系統會收集一段時間的資料，以監視設備的狀態。 其目標是要找出可以協助預測，且最終能夠防止故障的模式。 使用[長短期記憶 (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) 網路是一種深入學習方法，這種方法在預測性維護中特別吸引人。 LSTM 網路適合從序列學習。 時間序列資料可以用來回溯查看較長的時段以偵測故障模式。

在本教學課程中，我們會為[預測性維護](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3)中所述的資料集和案例建置 LSTM 網路。 我們會使用該網路預測飛機引擎的剩餘使用壽命。 此範本會使用模擬的飛機感應器值來預測飛機引擎將會在未來何時故障。 運用這項預測，我們就可以事先規劃維護，以避免故障。

本教學課程使用 [Keras](https://keras.io/) 深入學習程式庫和 Microsoft 辨識工具組 [CNTK](https://docs.microsoft.com/cognitive-toolkit/Using-CNTK-with-Keras) 作為後端。

具有此教學課程之範例的公用 GitHub 存放庫位於 [https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)。

## <a name="use-case-overview"></a>使用案例概觀

本教學課程會使用模擬飛機引擎「運轉到故障 (run-to-failure)」事件的範例，來示範預測性維護的模型化流程。 

此處所述的模型化資料隱含假設資產具有逐漸降低模式。 此模式會反映在資產的感應器測量。 藉由檢查一段時間的資產感應器值，機器學習演算法可以學習感應器值、感應器值變化和過往故障之間的關聯性。 此關聯性可用來預測未來的故障。 

我們建議您檢查資料格式，並先將範本的三個步驟全都完成，再使用您自己的日期取代範例資料。

## <a name="prerequisites"></a>必要條件

- [Azure 帳戶](https://azure.microsoft.com/free/) (有提供免費試用)。
- Azure Machine Learning Workbench，並已建立工作區。
- 針對模型運作：Azure Machine Learning Operationalization (已設定本機部署環境)，以及 [Azure Machine Learning 模型管理帳戶](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview)。

## <a name="create-a-new-workbench-project"></a>建立新的 Workbench 專案

使用此範例作為範本來建立新專案：

1. 開啟 Machine Learning Workbench。
2. 在 [專案] 頁面上，選取 **+**，然後選取 [新增專案]。
3. 在 [建立新專案] 窗格中，輸入新專案的資訊。
4. 在 [搜尋專案範本] 搜尋方塊中輸入「預測性維護」，然後選取範本。
5. 選取 [建立] 。

## <a name="prepare-the-notebook-server-computation-target"></a>準備筆記本伺服器計算目標

在您的本機電腦上的 Machine Learning Workbench [檔案] 功能表上，選取 [開啟命令提示字元] 或 [開啟 PowerShell]。 在所選選項的命令提示字元視窗中，執行下列命令：

`az ml experiment prepare --target docker --run-configuration docker`

我們建議在 DS4_V2 標準 [Linux (Ubuntu) 資料科學虛擬機器 (DSVM)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) 上執行 Notebook 伺服器。 設定 DSVM 之後，執行下列命令以準備 Docker 映像：

`az ml computetarget attach remotedocker --name [connection_name] --address [VM_IP_address] --username [VM_username] --password [VM_password]`

`az ml experiment prepare --target [connection_name] --run-configuration [connection_name]`

Docker 映像準備好之後，開啟 Jupyter Notebook 伺服器。 若要開啟 Jupyter Notebook 伺服器，請移至 Machine Learning Workbench [Notebook] 索引標籤，或啟動瀏覽器型伺服器：`az ml notebook start`

Notebook 儲存在 Jupyter 環境的 Code 目錄中。 依編號循序執行這些 Notebook，從 Code\1_data_ingestion_and_preparation.ipynb 開始。

選取核心以符合 [project_name]_Template [connection_name]，然後選取 [設定核心]。

## <a name="data-description"></a>資料說明

範本會在檔案 PM_train.txt、PM_test.txt 和 PM_truth.txt 中使用三個資料集作為輸入。

-  **定型資料**：飛機引擎運轉到故障資料。 定型資料 (PM_train.txt) 包含多個多變量時間序列，並以「循環」作為時間單位。 針對每個循環，它包含了 21 個感應器讀數。 

    每個時間序列都可以假設為從相同類型的不同引擎產生。 每個引擎會假設為從不同程度的初始損耗和製造變化開始。 這項資訊對於使用者是未知的。 

    在此模擬資料中，引擎會假設為在每個時間序列的開頭正常運作。 然後開始在運作循環序列期間的某個時間點降低效能。 效能會逐漸降低且降低幅度會慢慢擴大。 

    當到達預先定義的臨界值時，系統就會將引擎視為不再能安全地運作。 每個時間序列的最後一個循環可視為對應引擎的故障點。

-   **測試資料**：飛機引擎運作資料，未記錄故障事件。 測試資料 (PM_test.txt) 具有與定型資料相同的資料結構描述。 唯一的差別在於此資料不會指出故障發生的時間 (最後一個時間期間「不」代表故障點)。 在此引擎故障之前可以再持續運作多少循環不得而知。

- **真實資料**：測試資料中每個引擎真實剩餘循環的資訊。 基本真實資料會提供測試資料中引擎的剩餘工作循環數。

## <a name="scenario-structure"></a>案例結構

案例的內容可於 [GitHub 存放庫] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) 取得。 

在存放庫中，[讀我檔案] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/blob/master/README.md) 檔案會概述從準備資料到建置及運作模型的流程。 三個 Jupyter Notebook 可於存放庫的 [Code] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/tree/master/Code) 資料夾中取得。 

接下來，我們將說明逐步操作案例的工作流程。

### <a name="task-1-data-ingestion-and-preparation"></a>工作 1：資料擷取和準備

Code/1_data_ingestion_and_preparation.ipnyb 中的 Data Ingestion Jupyter Notebook 會將三個輸入資料集載入 Pandas 資料框架格式。 然後，它會準備資料以進行模型化，並且執行一些初步的資料視覺化。 然後資料會轉換成 PySpark 格式，並儲存在您的 Azure 訂用帳戶之 Azure Blob 儲存體容器中，以便用於下一個模型化工作。

### <a name="task-2-model-building-and-evaluation"></a>工作 2：模型建置和評估

Code/2_model_building_and_evaluation.ipnyb 中的 Model Building Jupyter Notebook 會從 Blob 儲存體讀取 PySpark 定型和測試資料集。 然後，會使用定型資料集建置 LSTM 網路。 會在測試集上測量模型效能。 產生的模型會序列化並儲存在本機計算內容，以便用於運作工作。

### <a name="task-3-operationalization"></a>工作 3：運作

Code/3_operationalization.ipnyb 中的 Operationalization Jupyter Notebook 會使用預存模型來建置所需的函式和結構描述，以便在 Azure 主控的 Web 服務上呼叫模型。 Notebook 會測試函式，然後將運作資產 zip (壓縮) 到 .zip 檔案。

此 ZIP 檔案包含以下檔案︰

- **modellstm.json**：用於部署的結構描述定義檔。 
- **lstmscore.py**：Azure Web 服務所需的 **init()** 和 **run()** 函式。
- **lstm.model**：模型定義目錄。

此 Notebook 會先使用模型定義來測試函式，然後才封裝運作資產以供部署。 部署指示包含在此筆記本的結尾。


## <a name="conclusion"></a>結論

本教學課程會使用簡單案例，只使用一個資料來源 (感應器值) 來進行預測。 在更進階的預測性維護案例中，例如[預測性維護模型化指南 R Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1)，則會使用多個資料來源。 其他資料來源可能包含歷史維護記錄、錯誤記錄檔及機器和運算子功能。 其他資料來源可能需要在深入學習網路中使用不同類型的處理。 針對正確的參數 (例如視窗大小) 調整模型也很重要。 

您可以編輯此案例的相關部分，並且嘗試不同的問題案例，例如[預測性維護模型化指南](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)中所述的案例，它牽涉到多個其他資料來源。


## <a name="references"></a>參考

- [預測性維護解決方案範本](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)
- [預測性維護建模指南](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
- [預測性維護建模指南 Python 筆記本](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
- [使用 PySpark 的預測性維護](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

