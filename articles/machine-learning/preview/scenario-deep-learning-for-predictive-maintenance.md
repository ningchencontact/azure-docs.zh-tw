---
title: "預測性維護真實世界案例 Azure 深入學習 |Microsoft 文件"
description: "了解如何複寫深入學習教學課程中的 Azure 機器學習 Workbench 與預測性維護。"
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
ms.openlocfilehash: 6019437763f82fa14b8677b6c7ec7f0c6938fda7
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>預測性維護真實世界案例的深入學習

深入學習是其中一個在機器學習中最受歡迎的趨勢。 在許多欄位和應用程式，包括無驅動程式的汽車、 語音及圖像辨識、 機器人及財務中用於深入學習。 深入學習是一組啟發由大腦 （生物類神經網路） 和機器學習的圖形的演算法。 認知科學家通常是指深入學習以人為的類神經網路 (ANNs)。

預測性維護很受歡迎。 在預測性維護，許多不同技術的設計會協助您判斷設備的條件，並預測時應該執行維護。 預測性維護的一些常見用法是失敗預測、 失敗診斷 （根本原因分析）、 失敗偵測、 失敗類型分類，以及建議的緩和或維護動作失敗之後。

在預測性維護的情況下，資料會收集一段時間，以監視狀態的設備。 目標是要找出模式，以預測，並最終以防發生失敗。 使用[長簡短詞彙記憶體 (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/)網路是深層學習是預測性維護中特別吸引人的方法。 LSTM 網路是適合學習，從序列。 時間序列資料可以用來查看較長的時間來偵測失敗模式。

在本教學課程中，我們建立資料集和案例所述在 LSTM 網路[預測性維護](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3)。 我們可以使用網路來預測飛機引擎的剩餘有用的生命週期。 範本會使用模擬的飛機感應器值來預測時飛機引擎將會在未來失敗。 使用這項預測，維護可以事先規劃，以避免發生失敗。

本教學課程使用[Keras](https://keras.io/)深入學習程式庫，以及 Microsoft 認知 Toolkit [CNTK](https://docs.microsoft.com/en-us/cognitive-toolkit/Using-CNTK-with-Keras)做為後端。

此教學課程中有範例公用 GitHub 儲存機制位於[https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)。

## <a name="use-case-overview"></a>使用案例概觀

本教學課程會使用模擬的飛機引擎執行-失敗事件的範例示範預測模型的程序的維護。 

此處所述的模型資料的隱含假設是資產已處理的降級模式。 此模式會反映在 資產的感應器度量。 藉由檢查資產的感應器值經過一段時間，機器學習演算法可以了解感應器值、 感應器值中的變更和歷程記錄的失敗數之間的關聯性。 此關聯性用來預測未來的失敗。 

我們建議您檢查資料格式，並完成範本的所有三個步驟之前的範例資料取代您自己的日期。

## <a name="prerequisites"></a>必要條件

- [Azure 帳戶](https://azure.microsoft.com/free/) (有提供免費試用)。
- Azure 機器學習工作，與建立工作區。
- 針對模型實施： Azure 機器學習實施，請使用本機部署環境設定，和[Azure 機器學習模型管理帳戶](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-overview)。

## <a name="create-a-new-workbench-project"></a>建立新的 Workbench 專案

使用此範例作為範本來建立新專案：

1. 開啟 Machine Learning Workbench。
2. 在**專案**頁面上，選取 **+** ，然後選取**新專案**。
3. 在**建立新專案** 窗格中，輸入新的專案資訊。
4. 在**搜尋專案範本**搜尋方塊中，輸入**預測性維護**，然後選取範本。
5. 選取 [建立] 。

## <a name="prepare-the-notebook-server-computation-target"></a>準備筆記本伺服器計算目標

在您的本機電腦上電腦學習 Workbench**檔案**功能表上，選取 **開啟命令提示字元**或**開啟 PowerShell**。 在您選擇的選項的命令提示字元視窗中，執行下列命令：

`az ml experiment prepare --target docker --run-configuration docker`

我們建議您執行筆記本伺服器 DS4_V2 標準[資料科學虛擬機器 (DSVM) 適用於 Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)。 DSVM 設定之後，執行下列命令，以準備 Docker 映像：

`az ml computetarget attach remotedocker --name [connection_name] --address [VM_IP_address] --username [VM_username] --password [VM_password]`

`az ml experiment prepare --target [connection_name] --run-configuration [connection_name]`

準備 Docker 的映像，以開啟 Jupyter 筆記本伺服器。 若要開啟 Jupyter 筆記本伺服器，請移至機器學習 Workbench**筆記本**索引標籤，或啟動瀏覽器為基礎的伺服器：`az ml notebook start`

筆記本會儲存在程式碼中的目錄 Jupyter 環境。 執行這些筆記本開頭 Code\1_data_ingestion_and_preparation.ipynb 循序為編號。

選取以符合您 [project_name] （_t） [connection_name]，然後選取核心**設定核心**。

## <a name="data-description"></a>資料說明

範本會使用三個資料集做為輸入，在 PM_train.txt、 PM_test.txt 和 PM_truth.txt 檔案。

-  **定型資料**: 飛機引擎執行-失敗的資料。 定型資料 (PM_train.txt) 與包含多個，多變量的時間序列，*循環*做為時間單位。 它包含 21 感應器讀數的每個循環。 

    每個時間序列可以假設產生的相同類型的不同的引擎。 每個引擎會假設不同程度的初始損耗和製造變化的開頭。 這項資訊是未知的使用者。 

    在此模擬資料，引擎會假設為正常運作，每個時間序列的開頭。 它會啟動一系列作業系統的循環期間降低在某個時間點。 效能衰退進展，並擴大範圍內。 

    當到達預先定義的臨界值時，引擎會被視為不安全的進一步的作業。 每個時間序列中的最後一個週期可視為對應的引擎失敗點。

-   **測試資料**: 飛機引擎操作的資料，而不記錄失敗事件。 測試資料 (PM_test.txt) 具有相同的資料結構描述，做為定型資料。 唯一的差別在於資料並不表示發生失敗 (最後一個時間週期沒有*不*代表失敗點)。 不知道多少循環的更多這個引擎可能會持續失敗之前。

- **真資料**： 測試資料的每個引擎的循環的則為 true 所剩餘的資訊。 真資料提供測試資料引擎其餘的工作循環的次數。

## <a name="scenario-structure"></a>案例結構

案例中的內容中 [GitHub 儲存機制] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)。 

在儲存機制中，[讀我檔案] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/blob/master/README.md) 檔案會概述的處理程序，從準備資料、 建立和運用模型。 三個 Jupyter 筆記本可用的儲存機制中的 [程式碼] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/tree/master/Code) 資料夾中。 

接下來，我們將描述逐步案例的工作流程。

### <a name="task-1-data-ingestion-and-preparation"></a>工作 1： 擷取資料和準備

資料擷取 Jupyter 筆記本中 Code/1_data_ingestion_and_preparation.ipnyb 載入熊資料框架格式的三個的輸入的資料集。 然後，其會用於模型化，準備資料，並會一些初步資料視覺效果。 然後轉換成 PySpark 格式並儲存在 Azure Blob 儲存體容器中的下一個工作中，模型中使用您 Azure 訂用帳戶資料。

### <a name="task-2-model-building-and-evaluation"></a>工作 2： 模型建立和評估

模型建置 Jupyter 筆記本中 Code/2_model_building_and_evaluation.ipnyb 讀取 PySpark 培訓及測試資料集，從 Blob 儲存體。 然後，LSTM 網路是定型資料集所建立。 測試集被以模型效能。 所產生之模型會序列化並儲存在實施工作中使用的本機計算內容。

### <a name="task-3-operationalization"></a>工作 3： 實施

實施 Jupyter 筆記本 Code/3_operationalization.ipnyb 中會使用預存的模型來建置函式和所需的 Azure 裝載的 web 服務上呼叫此模型的結構描述。 筆記本會測試函式，並且然後送達 （壓縮） 實施資產到.zip 檔案。

Zip 壓縮的檔案包含下列檔案：

- **modellstm.json**： 部署的結構描述定義檔。 
- **lstmscore.py**: **init （)**和**run （)**函式，所需的 Azure web 服務。
- **lstm.model**： 模型定義目錄。

筆記本會測試之前已部署的實施資產封裝使用的模型定義函式。 部署指示包含在此筆記本的結尾。


## <a name="conclusion"></a>結論

本教學課程使用簡單的案例中只有一個資料來源 （感應器值） 用來進行預測。 在更進階預測性維護案例，例如[預測維護模型化指南 R 筆記本](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1)，可能會使用多個資料來源。 歷程記錄的維護記錄 」、 「 錯誤記錄檔，以及 「 電腦和運算子的功能，可能包含其他資料來源。 其他資料來源可能需要不同類型會以用於深層的了解網路。 它也是重要模型來進行正確的參數，例如調整視窗大小。 

您可以編輯此案例中，相關部分，並請嘗試不同的問題案例，例如中所述的[預測維護模型化指南](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)，牽涉到多個其他資料來源。


## <a name="references"></a>參考

- [預測性維護解決方案範本](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)
- [預測性維護建模指南](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
- [預測性維護建模指南 Python 筆記本](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
- [使用 PySpark 的預測性維護](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

