---
title: 此版本有哪些新功能？
titleSuffix: Azure Machine Learning service
description: 了解 Azure Machine Learning 服務的最新更新，以及機器學習和資料準備 Python SDK。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: Blackmist
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 7aedb0804626d1204121568904763bec5e83e858
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786258"
---
# <a name="azure-machine-learning-service-release-notes"></a>Azure Machine Learning 服務版本資訊

本文章會說明 Azure Machine Learning 服務的版本。  如需每個 SDK 的完整說明，請瀏覽參考文件以瞭解：
+ Azure Machine Learning [**主要適用於 Python 的 SDK**](https://aka.ms/aml-sdk)
+ Azure Machine Learning [**Data Prep SDK**](https://aka.ms/data-prep-sdk)

若要了解已知的 Bug 和因應措施，請參閱[已知問題的清單](resource-known-issues.md)。

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>視覺化介面
+ **預覽功能**
  + 加入視覺介面中的 [執行 R 指令碼] 模組。

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK for Python v1.0.48

+ **新功能**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets**目前為可用**azureml opendatasets**。 舊的封裝也可以運作，但我們建議您將使用**azureml opendatasets**往後的更豐富的功能與改良功能。
    + 這個新的套件可讓您註冊為 AML 工作區中的資料集的 開啟資料集，並利用任何資料集所提供的功能。
    + 它也包含現有的功能，例如使用開啟的資料集當做 Pandas/SPARK 資料框架，以及聯結天氣等某些資料集的位置。

+ **預覽功能**
    + HyperDriveConfig 現在可以接受的管線物件做為支援使用管線的超參數微調的參數。

+ **Bug 修正和增強功能**
  + **azureml-train-automl**
    + 已修正遺失的轉換後的資料行類型的相關錯誤。
    + 修正 bug，以允許 y_query 是包含無 (s) 開頭的物件類型。 
    + 即使常數分數，仍然不必要地成長產生的集團集團選取程序中，已修正的問題。
    + 設定為 whitelist_models 與 blacklist_models AutoMLStep 中修正的問題。
    + 已修正問題，防止前置處理 AutoML 會有在 Azure ML 管線環境中使用時的使用方式。
  + **azureml-opendatasets**
    + 移動的 azureml-contrib-opendatasets 到 azureml opendatasets。
    + 允許開啟的資料集類別註冊到 AML 工作區，並順暢地運用 AML 資料集的功能。
    + 改善的 NoaaIsdWeather 大幅擴充非 SPARK 版本中的效能。
  + **azureml-explain-model**
    + Interpretability 物件的有更新的線上文件。
    + 新增模擬說明 batch_size 時 include_local 進行串流處理來改善執行時間 DecisionTreeExplainableModel 的批次中的全域說明 = False。
    + 已修正此問題其中`explanation.expected_values`會在它有時傳回浮點數，而不是浮點數的清單。
    + 已新增的預期值，以模仿說明中的輸出 automl 說明模型文件庫。
    + 若要取得未經處理的功能重要性提供轉換引數時，請修正排列功能重要性。
    + 新增模擬說明 batch_size 時 include_local 進行串流處理來改善 DecisionTreeExplainableModel 模型 explainability 程式庫執行時間的批次中的全域說明 = False。
  + **azureml-core**
    + 新增附加 DBFS AzureML CLI 中的資料存放區的能力。
    + 如果要建立空的資料夾的位置與資料存放區上傳修正的問題`target_path`開始使用`/`。
    + 已啟用的兩個資料集的詳細比較。
    + 刪除模型和映像現在會提供擷取上游物件相依於這些，如果刪除失敗，因為上游的相依性的詳細資訊。
    + 未使用的 RunConfiguration 設定 auto_prepare_environment 中已被取代。
  + **azureml-mlflow**
    + 遠端執行中使用 azureml.mlflow 提高的資源使用率。
    + 改善的 azureml mlflow 套件文件。
    + 已修正問題，其中 mlflow.log_artifacts("my_dir") 會儲存在 「 my_dir/成品-路徑 」 而不是 「 成品路徑 」 的成品。
  + **azureml-dataprep**
    + 資料流程物件現在可以反覆產生一連串的記錄。
    + 已修正的問題所在`Dataflow.read_pandas_dataframe`會失敗時`in_memory`引數設定為 True。
    + 改進處理的 pandas 資料框架，搭配非字串資料行索引。
    + 公開`set_diagnostics_collection()`允許以程式設計方式啟用/停用遙測收集。
    + 加入臨界數值和 bottomValues 彙總。
  + **azureml-pipeline-core**
    + 管線的所有步驟的參數 hash_paths 已被取代，未來將會移除。 依預設內容 source_directory 會雜湊 （除了列在 .amlignore 或.gitignore 檔案）
    + 繼續改善的模組以及 ModuleStep 支援計算類型特定模組，以準備 RunConfiguration 整合和進一步的變更來解除鎖定其在管線中的使用方式。
  + **azureml-pipeline-steps**
    + AzureBatchStep:輸入/輸出方面改良的說明文件。
    + AzureBatchStep:您可以變更 delete_batch_job_after_finish 預設值為 true。
  + **azureml-train-core**
    + 現在接受字串，作為自動化超參數微調的計算目標。
    + 未使用的 RunConfiguration 設定 auto_prepare_environment 中已被取代。
    + 已取代的參數`conda_dependencies_file_path`並`pip_requirements_file_path`的益處`conda_dependencies_file`和`pip_requirements_file`分別。
  + **azureml-opendatasets**
    + 改善 NoaaIsdWeather 大幅擴充非 SPARK 版本中的效能。
    
## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning 資料準備 SDK v1.1.7

因為時會造成問題的一些使用 Azure Databricks 的客戶，我們就會還原更佳的效能，變更。 如果您遇到的問題，在 Azure Databricks 上時，您可以升級至版本 1.1.7 使用其中一種下列方法：
1. 執行此指令碼來升級： `%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. 重新建立叢集，這將會安裝最新的資料準備 SDK 版本。

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK for Python v1.0.45

+ **新功能**
  + 加入決策樹 surrogate 模型來模擬在 azureml 說明模型封裝中的說明
  + 指定在推斷映像上安裝 CUDA 版本的能力。 CUDA 9.0、 9.1 和 10.0 的支援。
  + 基底映像現在已推出 Azure ML 訓練的相關資訊[Azure ML 容器 GitHub 儲存機制](https://github.com/Azure/AzureML-Containers)和[DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + 已新增的 CLI 支援管線的排程。 若要了解更多執行"az ml 管線-h"
  + 加入 AKS web 服務部署組態和 CLI 的自訂 Kubernetes 命名空間參數。
  + 管線的所有步驟的已被取代的 hash_paths 參數
  + Model.register 現在支援註冊多個個別的檔案，即使用單一模型`child_paths`參數。
  
+ **預覽功能**
    + 評分 explainers 現在可以選擇性地儲存 conda 並 pip 更可靠的序列化和還原序列化的資訊。
    + 自動功能選取器的錯誤修正。
    + 已更新至新的 api，新的實作所公開的修補 bug mlflow.azureml.build_image。

+ **重大變更**

+ **Bug 修正和增強功能**
  + 從 azureml 核心移除的 paramiko 相依性。 新增的取代舊版的計算目標的警告會附加方法。
  + 改善效能的 run.create_children
  + 在模擬使用二元分類器的詳細說明，修正老師機率是用來進行調整 shap 值的機率的順序
  + 改良的錯誤處理和自動化機器學習服務的訊息。 
  + 已修正自動化機器學習服務的反覆項目逾時問題。
  + 改善自動 machine learning 時間序列轉換效能。

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning 資料準備 SDK v1.1.6

+ **新功能**
  + 新增彙總函數的上界值 (`SummaryFunction.TOPVALUES`) 下方的值 (`SummaryFunction.BOTTOMVALUES`)。

+ **Bug 修正和增強功能**
  + 大幅改善的效能`read_pandas_dataframe`。
  + 已修正的錯誤，造成`get_profile()`上資料流程指向二進位檔案失敗。
  + 公開`set_diagnostics_collection()`允許以程式設計方式啟用/停用遙測收集。
  + 變更的行為， `get_profile()`。 NaN 值現在會忽略最小值、 平均值、 標準和加總，與 Pandas 的行為一致。


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK for Python v1.0.43

+ **新功能**
  + Azure Machine Learning 現在提供常用的機器學習服務和資料分析 framework scikit-learn 的第一級支援。 使用[`SKLearn`估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)，使用者可以輕鬆地訓練和部署 scikit-learn 模型。
    + 了解如何[執行與 scikit-learn 的超參數微調使用 HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb)。
  + 已新增建立 ModuleStep 管線以及模組和 ModuleVersion 類別，以管理可重複使用的計算單位中支援。
  + ACI webservices 現在支援透過更新持續性 scoring_uri。 Scoring_uri 會從 IP 變更 FQDN。 可以藉由設定 deploy_configuration dns_name_label 設定 FQDN 的 Dns 名稱標籤。 
  + 自動化的機器學習的新功能：
    + STL featurizer 預測
    + KMeans 叢集都可使用功能掃掠
  + AmlCompute 配額核准就變得更快 ！ 我們現在已自動核准您的配額要求的臨界值內的程序。 如需有關配額的運作方式的詳細資訊，了解[如何管理配額](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)。

+ **預覽功能**
    + 與整合[MLflow](https://mlflow.org)透過 azureml mlflow 套件追蹤 1.0.0 ([中的範例筆記本](https://aka.ms/azureml-mlflow-examples))。
    + 提交為一次執行的 Jupyter notebook。 [API 參考文件](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + 公開預覽[資料漂移偵測器](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift?view=azure-ml-py)透過 azureml-contrib-datadrift 套件 ([中的範例筆記本](https://aka.ms/azureml-datadrift-example))。 資料漂移是其中一個主要原因模型精確度會隨著時間的降低。 它會提供資料時模型在生產環境中是模型已定型的資料不同。 AML 資料漂移偵測器可協助監視資料漂移的客戶，並傳送警示，每當偵測到漂移時。 

+ **重大變更**

+ **Bug 修正和增強功能**
  + RunConfiguration 載入，並儲存支援指定的完整檔案路徑與舊有行為的完整後-相容性。
  + 新增快取 ServicePrincipalAuthentication，依預設關閉的。
  + 啟用多個繪圖，在相同的計量名稱底下的記錄。
  + 模型類別現在正確匯入從 azureml.core (`from azureml.core import Model`)。
  + 在管線的步驟，`hash_path`參數現在已被取代。 新的行為是雜湊完整 source_directory，除了.amlignore 或.gitignore 中列出的檔案。
  + 在管線封裝中各種`get_all`並`get_all_*`方法已被取代的益處`list`和`list_*`分別。
  + azureml.core.get_run 不再需要匯入之前傳回原始的執行的類型的類別。
  + 已修正的問題，有些 web 服務更新對呼叫未不會觸發更新。
  + 評分 AKS webservices 上的逾時應該會介於 5 毫秒到 300000ms 之間。 所允許的上限為 5 分鐘 scoring_timeout_ms 評分要求藉從 1 分鐘。
  + LocalWebservice 物件現在具有`scoring_uri`和`swagger_uri`屬性。
  + 移動建立輸出目錄和跨使用者處理序的輸出目錄上傳。 啟用執行歷程記錄每個使用者處理序中執行的 SDK。 如此應能解決執行一些分散式訓練所遇到的同步處理問題。
  + （適用於分散式訓練只） 的處理序名稱和 PID，現在將包含從使用者處理序名稱寫入 azureml 記錄檔的名稱。

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning 資料準備 SDK v1.1.5

+ **Bug 修正和增強功能**
  + 如需有 2 位數年份格式的解譯的 datetime 值，有效的年份範圍已更新為符合 Windows 可能版本。 範圍已從 1930年 2029年變更為 1950年-2049年。
  + 讀取時使用的檔案和設定`handleQuotedLineBreaks=True`，`\r`會被視為新的一行。
  + 已修正的 bug 造成`read_pandas_dataframe`在某些情況下失敗。
  + 改善的效能`get_profile`。
  + 改良的錯誤訊息。

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning 資料準備 SDK v1.1.4

+ **新功能**
  + 您現在可以使用下列的運算式語言函式來擷取及剖析成新的資料行的 datetime 值。
    + `RegEx.extract_record()` 新的資料行中擷取日期時間的項目。
    + `create_datetime()` 從不同的日期時間的項目，會建立 datetime 物件。
  + 當呼叫`get_profile()`，您現在可以看到分位數的資料行已標示為 (est)，以清楚指出值是約略估算而已。
  + 您現在可以使用 * * 從 Azure Blob 儲存體讀取時的萬用字元。
    + 例如，`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **錯誤修正**
  + 已修正從遠端來源 (Azure Blob) 中讀取的 Parquet 檔案與相關的錯誤。

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK for Python v1.0.39
+ **變更**
  + 回合的組態 auto_prepare_environment 選項已被取代，自動準備成為預設值。

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning 資料準備 SDK v1.1.3

+ **新功能**
  + 已新增的支援，來讀取 PostgresSQL 資料庫，藉由呼叫 read_postgresql 或使用資料存放區。
    + 請參閱操作指南中的範例：
      + [資料擷取 notebook](https://aka.ms/aml-data-prep-ingestion-nb)
      + [資料存放區 notebook](https://aka.ms/aml-data-prep-datastore-nb)

+ **Bug 修正和增強功能**
  + 已修正的問題與資料行類型轉換：
  + 現在正確將轉換的布林值或數值資料行的布林資料行。
  + 現在不會失敗時嘗試設定日期資料行是日期類型。
  + 改善的 JoinType 型別和隨附參考文件。 聯結兩個資料流程，您現在可以指定其中一個聯結的類型：
    + 無、 比對、 內部、 UNMATCHLEFT、 LEFTANTI、 LEFTOUTER、 UNMATCHRIGHT、 RIGHTANTI、 RIGHTOUTER、 FULLANTI，完整。
  + 改善的資料型別推斷來辨識更多日期格式。

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站中，您現在可以：
+ 建立及執行自動化的 ML 實驗 
+ 建立 Notebook VM 以試用範例 Jupyter 筆記本或您自己。
+ 包含自動化的機器學習服務、 視覺化介面和裝載 Notebook Vm 的機器學習服務工作區中全新的撰寫區段 （預覽）
    + 自動建立模型，使用自動化機器學習服務 
    + 使用拖曳和卸除視覺化介面，執行實驗
    + 建立 Notebook VM 瀏覽資料、 建立模型，並將服務部署。
+ 即時圖表和更新執行的報表中的計量，然後執行詳細資料頁面
+ 更新的檔案檢視器記錄檔、 輸出和執行詳細資料頁面中的快照集。
+ 全新和改進的報表建立體驗中的 [實驗] 索引標籤。 
+ 已新增的能夠從 Azure 機器學習服務工作區的 [概觀] 頁面下載的 config.json 檔案的詳細資訊。
+ 支援從 Azure Databricks 工作區的機器學習服務工作區建立 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK for Python v1.0.33
+ **新功能**
  + _Workspace.create_方法現在接受 CPU 和 GPU 叢集的預設叢集設定。
  + 如果工作區建立失敗，則會清除相依的資源。
  + 預設 Azure Container Registry SKU 已切換為 basic。
  + 所需的 執行 或 映像建立時，會以延遲的方式，建立 azure Container Registry。
  + 支援環境的定型執行。

### <a name="notebook-virtual-machine"></a>Notebook 的虛擬機器 

使用 Notebook VM 作為安全、 符合企業需求的裝載環境的 Jupyter notebook 在其中設計程式的機器學習服務實驗，模型部署為 web 端點和執行所有支援 Azure 機器學習服務 SDK 使用 Python 的其他作業。 它提供了幾項功能：
+ [快速啟動 VM 的預先設定 notebook](quickstart-run-cloud-notebook.md) 具有最新版的 Azure 機器學習服務 SDK 和相關的封裝。
+ 存取是透過經實證的技術，例如 HTTPS、 Azure Active Directory 驗證和授權來保護。
+ 筆記本與您 Azure Machine Learning 工作區的 blob 儲存體帳戶中的程式碼的可靠的雲端儲存體。 您可以放心刪除您的 notebook VM，而不會遺失您的工作。
+ 預先安裝探索和實驗使用 Azure Machine Learning 服務功能的範例筆記本。
+ Azure Vm、 任何 VM 類型、 任何套件的任何驅動程式的完整自訂功能。 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure 機器學習服務 SDK for Python v1.0.33 發行。

+ Azure ML 硬體加速模型上[Fpga](concept-accelerate-with-fpgas.md)已正式推出。
  + 您現在可以[使用 azureml 加速度模型封裝](how-to-deploy-fpga-web-service.md)來：
    + 訓練支援的深度類神經網路 （ResNet 50、 ResNet 152、 DenseNet 121、 VGG-16 和 SSD VGG） 的加權
    + 使用轉送學習與支援的 DNN
    + 模型管理服務註冊模型，並將容器化模型
    + 將模型部署到 Azure VM 中使用 Azure Kubernetes Service (AKS) 叢集中 FPGA
  + 部署容器[Azure 資料方塊邊緣](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)伺服器裝置
  + 評分與 gRPC 端點與此資料[範例](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>自動化 Machine Learning

+ 掃掠，以啟用動態新增 featurizers 效能最佳化的功能。 新的 featurizers： 工作內嵌項目，辨識項、 目標編碼、 文字目標版本編碼方式、 群集距離的權數
+ 處理有效的訓練/智慧型的 CV 分割內自動化 ML
+ 幾個記憶體最佳化的變化和執行階段效能改進
+ 模型說明中的效能改進
+ 本機執行的 ONNX 模型轉換
+ 已新增的 Subsampling 支援
+ 智慧型停止時定義不允出準則
+ 整體堆疊的項目

+ 時間序列預測
  + 新的預測趨勢預測函式   
  + 您現在可以使用輪流原點交叉驗證對時間序列資料
  + 新增用來設定的時間序列會延遲的新功能 
  + 加入以支援輪流視窗彙總功能的新功能
  + 新的假日偵測和 featurizer 國家/地區的程式碼中定義時嘗試設定

+ Azure Databricks
  + 啟用時間序列預測和模型 explainabilty/interpretability 功能
  + 您現在可以取消和繼續 （繼續） 自動化的 ML 實驗
  + 多核心處理新增的支援

### <a name="mlops"></a>MLOps
+ **本機部署和評分容器偵錯**<br/> 您現在可以部署在本機的 ML 模型，並快速反覆執行計分的檔案和相依性，以確保它們如預期般運作。

+ **Introduced InferenceConfig & Model.deploy()**<br/> 模型的部署現在支援使用 RunConfig 相同的項目指令碼中指定的來源資料夾。  此外，已簡化模型部署，以單一命令。

+ **追蹤的 Git 參考**<br/> 客戶一直要求基本的 Git 整合功能的一些時間，因為它可協助維護的端對端稽核記錄。 我們已實作追蹤跨 Azure ML 中的 Git 相關中繼資料 （儲存機制、 認可、 初始狀態） 的主要實體。 這項資訊會由 SDK 和 CLI 會自動收集。

+ **模型的程式碼剖析和驗證服務**<br/> 客戶經常抱怨的困難度，正確大小其推斷服務相關聯的計算。 與我們的模型分析服務，客戶可以提供的範例輸入，以及我們將會分析跨 16 不同的 CPU / 記憶體設定來判斷最佳的部署大小調整。

+ **推斷的自備您自己的基底映像**<br/> 另一個常見抱怨無法從測試移至推斷重新共用相依性。 與共用功能我們新基底映像，您可以立即重複使用測試基底映像、 相依性以及所有推斷。 這應該加速部署，並減少外部迴圈的內部間距。

+ **改善的 Swagger 結構描述產生體驗**<br/> 我們先前的 swagger 產生，方法是很容易出錯且無法自動化。 我們有新的內建方法，從任何 Python 函式，透過裝飾項目產生 swagger 結構描述。 我們有開放原始碼這段程式碼，我們的結構描述產生通訊協定不會連結至 Azure ML 平台。

+ **Azure ML CLI 是正式推出 (GA)**<br/> 現在可以使用單一 CLI 命令部署模型。 我們共同客戶的意見反應，沒有人會從 Jupyter notebook 的 ML 模型的部署。 [ **CLI 參考文件**](https://aka.ms/azmlcli)已更新。


## <a name="2019-04-22"></a>2019-04-22

Azure 機器學習服務 SDK for Python v1.0.30 發行。

[ `PipelineEndpoint` ](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py)介紹中加入新版本的發行管線的同時保有相同的端點。

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning 資料準備 SDK v1.1.2

注意:資料準備 Python SDK 將不會再安裝`numpy`和`pandas`封裝。 請參閱[更新的安裝指示](https://aka.ms/aml-data-prep-installation)。

+ **新功能**
  + 您現在可以使用樞紐轉換。
    + 操作說明指南：[Pivot notebook](https://aka.ms/aml-data-prep-pivot-nb)
  + 您現在可以在原生函式中使用規則運算式。
    + 範例：
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + 您現在可以使用`to_upper` 並`to_lower` 運算式語言中的函式。
  + 您現在可以看到每個資料行資料設定檔中的唯一值數目。
  + 對於某些常用的讀取器的步驟，您現在可以傳入`infer_column_types`引數。 如果設定為`True`，資料準備會嘗試偵測並自動將資料行類型的轉換。
    + `inference_arguments` 現在已被取代。
  + 您現在可以呼叫`Dataflow.shape`。

+ **Bug 修正和增強功能**
  + `keep_columns` 現在接受一個額外的選擇性引數`validate_column_exists`，它會檢查的結果`keep_columns`會包含任何資料行。
  + 所有讀取器步驟 （從檔案讀取） 現在接受一個額外的選擇性引數`verify_exists`。
  + 讀取 pandas 資料框架，以及資料設定檔的提升的效能。
  + 修正的 bug，其中配量從資料流程的單一步驟失敗，單一索引。

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure 入口網站
  + 您現在可以重新提交在現有的遠端計算叢集上執行的現有指令碼。 
  + 您現在可以使用新的參數執行發行的管線，在 [管線] 索引標籤上。 
  + 執行詳細資料現在支援新的快照集檔案檢視器。 當您送出特定的執行，您可以檢視快照集的目錄。 您也可以下載已提交給啟動執行的 notebook。
  + 您現在可以取消父執行從 Azure 入口網站。

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK for Python v1.0.23

+ **新功能**
  + Azure 機器學習服務 SDK 現在支援 Python 3.7。
  + Azure Machine Learning DNN 估算現在提供內建的多個版本支援。 例如， `TensorFlow` 估算器現在接受`framework_version`參數，而使用者可以指定 '1.10' 或 '1.12' 的版本。 如需目前的 SDK 版本所支援的版本中，呼叫`get_supported_versions()`上所需的架構類別 (例如`TensorFlow.get_supported_versions()`)。
  如需最新的 SDK 版本所支援的版本，請參閱[DNN 估算器文件](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)。

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning 資料準備 SDK v1.1.1

+ **新功能**
  + 您可以閱讀使用 read_ * 轉型的多個資料存放區/資料路徑/DataReference 來源。
  + 您可以執行下列作業來建立新的資料行的資料行： 部門、 樓層、 模數電源，長度。
  + 資料準備現在是 Azure ML 診斷套件的一部分，而且會依預設記錄診斷資訊。
    + 若要關閉這個功能，設定這個環境變數設為 true:DISABLE_DPREP_LOGGER

+ **Bug 修正和增強功能**
  + 改進程式碼文件常使用的類別和函式。
  + 無法讀取 Excel 檔案的 auto_read_file 中修正的 bug。
  + 已覆寫 read_pandas_dataframe 中的資料夾的選項。
  + 已新增的支援的 Fedora 27/28 和 Ubuntu 1804 dotnetcore2 相依性安裝，以及提升的效能。
  + 改善從 Azure Blob 的讀取效能。
  + 資料行類型偵測現在支援類型的資料行的長時間。
  + 修正的 bug，其中有些日期值顯示為時間戳記，而不是 Python datetime 物件。
  + 修正了其中一些類型的計數正顯示為雙精度浮點數，而不是整數。

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK for Python v1.0.21

+ **新功能**
  + *Azureml.core.Run.create_children*方法可讓低度延遲建立多個子執行透過單一呼叫。

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning 資料準備 SDK 1.1.0 版

+ **重大變更**
  + 資料準備套件的概念已被取代，不再支援。 而不是保存在單一套件中的多個資料流程，您可以個別保存資料流程。
    + 操作說明指南：[開啟並儲存資料流程的 notebook](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **新功能**
  + 資料準備現可辨識的比對特定的語意型別，並據以分割資料行。 目前支援 STypes 包括： 傳送電子郵件位址 」、 「 地理的座標 （緯度與經度） 」、 「 IPv4 和 IPv6 位址 」、 「 美國電話號碼和 「 美國郵遞區號。
    + 操作說明指南：[語意類型 notebook](https://aka.ms/aml-data-prep-semantic-types-nb)
  + 資料準備現在支援從兩個數值資料行中產生結果的資料行的下列作業： 減、 乘、 分割，和模數。
  + 您可以呼叫`verify_has_data()`上檢查執行時，資料流程是否會產生記錄的資料流程。

+ **Bug 修正和增強功能**
  + 您現在可以指定要在長條圖中用於數值資料行設定檔的分類收納數目。
  + `read_pandas_dataframe`轉換現在需要 DataFrame，以具備字串或位元組型別資料行名稱。
  + 修正在`fill_nulls`轉換，其中值未正確填入如果資料行遺漏。

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK for Python v1.0.18

 + **變更**
   + Azureml tensorboard 封裝會取代 azureml-contrib-tensorboard。
   + 此版本中，您可以設定使用者帳戶在您的受控的計算叢集 (amlcompute)，建立它時。 這可以由傳入佈建設定的這些屬性。 您可以找到更多詳細資料，在[SDK 參考文件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-)。

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning 資料準備 SDK v1.0.17

+ **新功能**
  + 現在支援加入兩個數值的資料行，來產生結果的資料行，使用運算式語言。

+ **Bug 修正和增強功能**
  + 改善的文件和 random_split 的參數檢查。
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning 資料準備 SDK v1.0.16

+ **Bug 修正**
  + 修正服務主體驗證問題所造成的 API 變更。

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK for Python v1.0.17

+ **新功能**

  + Azure Machine Learning 現在提供熱門 DNN 架構 Chainer 一流的支援。 使用[ `Chainer` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)類別的使用者可以輕鬆地訓練並部署 Chainer 模型。
    + 了解如何[ChainerMN 以執行分散式的訓練](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + 了解如何[執行超參數微調與使用 HyperDrive Chainer](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure 機器學習服務管線新增資料存放區修改為基礎的管線執行的能力觸發程序。 管線[排程 notebook](https://aka.ms/pl-schedule)會更新，以展示此功能。

+ **Bug 修正和增強功能**
  + 我們新增了支援 Azure 機器學習服務管線將 source_directory_data_store 屬性設定為 「 所需的資料存放區 （例如 blob 儲存體） 上[RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)的值提供給[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)。 依預設步驟使用 Azure 檔案存放區的備份資料存放區，可能會遇到大量的步驟會同時執行時的節流問題。

### <a name="azure-portal"></a>Azure 入口網站

+ **新功能**
  + 新的拖曳和卸除資料表報表編輯器的體驗。 使用者可以將資料行從井拖曳到資料表區域會顯示資料表的預覽。 可以重新排列資料行。
  + 新的記錄檔檢視器
  + 執行、 計算、 模型、 映像，以及從 活動 索引標籤的 部署實驗的連結

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning 資料準備 SDK v1.0.15

+ **新功能**
  + 資料準備目前支援從資料流程寫入檔案資料流。 也提供可操作檔案資料流名稱，以建立新的檔案名稱的功能。
    + 操作說明指南：[使用與檔案資料流 notebook](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Bug 修正和增強功能**
  + 改善的 T-digest 對大型資料集的效能。
  + 資料準備現在支援從資料路徑的讀取資料。
  + 一種熱門編碼現在適用於布林值和數值資料行。
  + 其他其他的 bug 修正。

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>適用於 Python 的 Azure Machine Learning SDK v1.0.15

+ **新功能**
  + Azure 機器學習服務管線新增 AzureBatchStep ([notebook](https://aka.ms/pl-azbatch))，HyperDriveStep (notebook)，並以時間為基礎來排程功能 ([notebook](https://aka.ms/pl-schedule))。
  +  DataTranferStep 已更新成可與 Azure SQL Server 和「適用於 PostgreSQL 的 Azure 資料庫」([otebook](https://aka.ms/pl-data-trans)) 搭配運作。

+ **變更**
  + 即將淘汰 `PublishedPipeline.get_published_pipeline` 而改用 `PublishedPipeline.get`。
  + 即將淘汰 `Schedule.get_schedule` 而改用 `Schedule.get`。

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning 資料準備 SDK v1.0.12

+ **新功能**
  + 「資料準備」現在支援使用「資料存放區」從 Azure SQL 資料庫讀取資料。
 
+ **變更**
  + 改善記憶體效能的特定作業的大型資料。
  + `read_pandas_dataframe()` 現在會要求必須指定 `temp_folder`。
  + `ColumnProfile` 上的 `name` 屬性即將淘汰，請改用 `column_name`。

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK for Python v1.0.10

+ **變更**： 
  + Azure ML SDK 不再具有 azure-cli 套件作為相依性。 明確的說，azure-cli-core 與 azure-cli-profile 相依性已從 azureml-core 移除。 以下是使用者影響的變更：
    + 如果您是執行 「 az 登入 」，然後使用 azureml sdk，SDK 會在一次執行瀏覽器或裝置程式碼記錄檔。 它不會使用由「az login」建立的任何認證狀態。
    + 對於 Azure CLI 驗證，例如使用「az login」，請使用 _azureml.core.authentication.AzureCliAuthentication_ 類別。 對於 Azure CLI 驗證，請在您已安裝 azureml-sdk 的 Python 環境中執行 _pip install azure-cli_。
    + 如果您正在使用服務主體執行「az login」以進行自動化，建議您使用 _azureml.core.authentication.ServicePrincipalAuthentication_ 類別，因為 azureml-sdk 不會使用 azure CLI 建立的認證狀態。 

+ **錯誤修正**：此版本主要包含次要錯誤修正

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **錯誤修正**
  + 改善效能的資料設定檔。
  + 已修正與錯誤報告相關的輕微錯誤。
  
### <a name="azure-portal-new-features"></a>Azure 入口網站：新功能
+ 全新的拖放圖表報告製作體驗。 使用者可以將資料行或屬性從 well 拖曳至圖表區域，在此區域系統將根據資料類型自動為使用者選取適當的圖表類型。 使用者可以將圖表類型變更為其他適用的類型，或新增其他屬性。

    支援的圖表類型：
    - 折線圖
    - 長條圖
    - 堆疊橫條圖
    - 盒狀圖
    - 散佈圖
    - 泡泡圖
+ 入口網站現在會動態地產生實驗報告。 當使用者將回合提交給實驗後，具有計量和圖表記錄的報告就會自動產生，以便您比較每個不同的回合。 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK for Python v1.0.8

+ **錯誤修正**：此版本主要包含次要錯誤修正

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK v1.0.7

+ **新功能**
  + 資料存放區改善 (記載於[資料存放區操作指南](https://aka.ms/aml-data-prep-datastore-nb))
    + 已新增以相應增加方式從中讀取和寫入至 Azure 檔案共用和 ADLS 資料存放區的功能。
    + 使用資料存放區時，Data Prep 現在支援使用服務主體驗證，而非互動式驗證。
    + 已新增 wasb 和 wasbs url 的支援。

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning 資料準備 SDK v1.0.6

+ **錯誤修正**
  + 已修正與從 Spark 上公開可讀取的 Azure Blob 容器讀取相關的 Bug

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK for Python v1.0.6
+ **錯誤修正**：此版本主要包含次要錯誤修正

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **新功能**
  + `to_bool` 函式現在允許將不相符的值轉換成 Error 值。 這是 `to_bool` 和 `set_column_types` 新的預設不相符行為，先前的預設行為會將不相符的值轉換為 False。
  + 在呼叫 `to_pandas_dataframe` 時，有新的選項可將數字資料行中的 null/遺失值解譯為 NaN。
  + 新增了功能，會檢查某些運算式的傳回類型，以確保類型一致性，並及早失敗。
  + 您現在可以呼叫 `parse_json`，以將資料行中的值剖析為 JSON 物件，並將其展開成多個資料行。

+ **錯誤修正**
  + 已修正錯誤，該錯誤會讓 Python 3.5.2 中的 `set_column_types` 損毀。
  + 已修正會在使用 AML 映像連線至資料存放區時當機的錯誤。

+ **更新**
  * 使用者入門教學課程、案例研究和操作指南的[範例 Notebook](https://aka.ms/aml-data-prep-notebooks)。

## <a name="2018-12-04-general-availability"></a>2018-12-04：正式運作

Azure Machine Learning 服務現已正式運作。

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
在此版本中，我們透過 [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute) 來宣布新的受控計算體驗。 這個計算目標會取代適用於 Azure Machine Learning 的 Azure Batch AI 計算。 

這個計算目標：
+ 用於模型定型和批次推斷/計分
+ 是單一對多個節點的計算
+ 會為使用者執行叢集管理和作業排程
+ 會依預設自動調整
+ 同時支援 CPU 和 GPU 資源 
+ 可讓您使用低優先順序的 VM 來降低成本

您可以在 Python 中，使用 Azure 入口網站或 CLI 建立 Azure Machine Learning Compute。 它必須建立於您工作區所在的區域，而且無法附加至任何其他工作區。 這個計算目標會針對您的回合使用 Docker 容器，並封裝您的相依性，以便在您的所有節點上複寫相同的環境。

> [!Warning]
> 我們建議您建立新的工作區來使用 Azure Machine Learning Compute。 使用者嘗試從現有的工作區建立 Azure Machine Learning Compute 可能會看到錯誤，但機率很低。 您工作區中現有的計算應能持續運作而不會受到影響。

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK for Python v1.0.2
+ **重大變更**
  + 在此版本中，我們會移除從 Azure Machine Learning 中建立 VM 的支援。 您仍然可以附加現有的雲端 VM 或遠端內部部署伺服器。 
  + 我們也會移除對 BatchAI 的支援，這些功能現在全都應透過 Azure Machine Learning Compute 來支援。

+ <bpt id="p1">**</bpt>New<ept id="p1">**</ept>
  + 針對機器學習管線：
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py) \(英文\)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py) \(英文\)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py) \(英文\)


+ **已更新**
  + 針對機器學習管線：
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) \(英文\) 現在接受 runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) \(英文\) 現在可從 SQL 資料來源複製或複製到其中
    + 排程 SDK 中的功能，以建立和更新執行已發佈管線的排程

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning 資料準備 SDK v0.5.2
+ **重大變更** 
  * `SummaryFunction.N` 已重新命名為 `SummaryFunction.Count`。
  
+ **錯誤修正**
  * 讀取自和寫入至遠端回合上的資料存放區時，請使用最新 AML 執行權杖 以前，如果在 Python 中更新 AML 執行權杖，將不會使用已更新的 AML 執行權杖來更新資料準備執行階段。
  * 其他更清楚的錯誤訊息
  * 當 Spark 使用 `Kryo` 序列化時，to_spark_dataframe() 將不再損毀
  * 值計數偵測器現在可以顯示 1000 個以上的唯一值
  * 如果原始的資料流程沒有名稱，隨機分割就不再失敗  

+ **詳細資訊**
  * [Azure Machine Learning 資料準備 SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>文件和 Notebook
+ ML 管線
  + 適用於開始使用管線、批次範圍和樣式移轉範例之全新和更新的 Notebook： https://aka.ms/aml-pipeline-notebooks
  + 了解如何[建立您的第一個管線](how-to-create-your-first-pipeline.md)
  + 了解如何[使用管線執行批次預測](how-to-run-batch-predictions.md)
+ Azure Machine Learning 計算目標
  + [範例 Notebook](https://aka.ms/aml-notebooks) 現在已更新為使用新的受控計算。
  + [了解這個計算](how-to-set-up-training-targets.md#amlcompute)。

### <a name="azure-portal-new-features"></a>Azure 入口網站：新功能
+ 在入口網站中建立和管理 [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute) 類型。
+ 監視 Azure Machine Learning Compute 的配額使用量和[要求配額](how-to-manage-quotas.md)。
+ 即時檢視 Azure Machine Learning Compute 叢集狀態。
+ 已新增對於建立 Azure Machine Learning Compute 和 Azure Kubernetes Service 的虛擬網路支援。
+ 使用現有的參數，重新執行您已發佈的管線。
+ 新的[自動化機器學習圖表](how-to-track-experiments.md#auto)，適用於分類模型 (升力、增益、校正、具備模型說明能力的特徵重要性圖表) 和迴歸模型 (殘差，以及具備模型說明能力的特徵重要性圖表)。 
+ 您可以在 Azure 入口網站中檢視管線。




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK for Python v0.1.80

+ **重大變更** 
  * azureml.train.widgets  命名空間已移至 azureml.widgets  。
  * azureml.core.compute.AmlCompute  取代了下列類別 - azureml.core.compute.BatchAICompute  和 azureml.core.compute.DSVMCompute  。 第二個類別將會在後續版本中移除。 AmlCompute 類別現在有更簡易的定義，只需 vm_size 和 max_nodes 即可，並且會在提交作業時自動將叢集規模調整為 0 到 max_nodes。 我們已對[範例 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) 更新此資訊，這應該能提供使用範例給您參考。 希望您喜歡此簡化功能和後續版本中的許多有趣功能！

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning Data Prep SDK v0.5.1 

若要深入了解 Data Prep SDK，請閱讀[參考文件](https://aka.ms/data-prep-sdk)。
+ **新功能**
   * 建立新的 DataPrep CLI 來執行 DataPrep 套件，並檢視資料集或資料流程的資料設定檔
   * 重新設計 SetColumnType API 以改善可用性
   * 將 smart_read_file 重新命名為 auto_read_file
   * 資料設定檔現在包含偏度和峰度
   * 可透過分層取樣來取樣
   * 可從包含 CSV 檔案的 zip 檔案讀取
   * 可使用隨機劃分來分割資料列取向的資料集 (例如，測試-訓練集)
   * 可以藉由呼叫 `.dtypes` 從資料流程或資料設定檔取得所有資料行的資料類型
   * 可以藉由呼叫 `.row_count` 從資料流程或資料設定檔取得資料列計數

+ **錯誤修正**
   * 修正長時間的雙重轉換 
   * 修正新增任何資料行之後的判斷提示 
   * 修正 FuzzyGrouping 在某些情況下無法偵測到群組的問題
   * 修正排序函式以遵循多欄排列順序
   * 將 and/or 運算式修正為類似於 `pandas` 處理它們的方式
   * 修正從 dbfs 路徑讀取的方式
   * 讓錯誤訊息變得更容易了解 
   * 現在使用 AML 權杖讀取遠端計算目標時不會再失敗
   * Linux DSVM 現在不會再有失敗狀況
   * 現在字串述詞中有非字串值時不會再發生損毀
   * 現在能在資料流程發生正常的失敗時，處理判斷提示錯誤
   * 現在支援 Azure Databricks 上掛接 dbutils 的儲存位置

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure 入口網站 
Azure Machine Learning 服務的 Azure 入口網站含有下列更新：
  * 用於已發佈管線的新 [管線]  索引標籤。
  * 已增加附加現有的 HDInsight 叢集作為計算目標的支援。

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK for Python v0.1.74

+ **重大變更** 
  * *Workspace.compute_targets、datastores、experiments、images、models 以及 *webservices* 是屬性而非方法。 例如，將 Workspace.compute_targets()  取代為 Workspace.compute_targets  。
  * *Run.get_context* 取代 *Run.get_submitted_run*。 第二個方法將會在後續版本中移除。
  * *PipelineData* 類別現在預期將 datastore 物件而不是 datastore_name 當作參數。 同樣地，*管線* 接受 default_datastore，而不是 default_datastore_name。

+ **新功能**
  * Azure Machine Learning 管線[範例 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) 現在使用 MPI 步驟。
  * Jupyter Notebooks 的 RunDetails 小工具已更新以顯示管線的視覺效果。

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning Data Prep SDK v0.4.0 
 
+ **新功能**
  * 類型計數已新增至資料設定檔 
  * 值計數與長條圖現在可以使用
  * 資料設定檔中有更多百分位數
  * 中間值可以在 Summarize 中使用
  * 現在支援 Python 3.7
  * 當您將包含資料存放區的資料流程儲存至 DataPrep 套件時，資料存放區資訊將保存為 DataPrep 套件的一部分
  * 現在支援寫入至資料存放區 
        
+ **已修正的 Bug**
  * 64 位元不帶正負號的整數溢位現在會在 Linux 上正確處理
  * 已在 smart_read 中修正純文字檔案不正確的文字標籤
  * 字串資料行類型現在會出現在 [計量] 檢視中
  * 類型計數現在已修正，可顯示對應至單一 FieldType，而不是個別項目的 ValueKinds
  * 當路徑以字串方式提供時，Write_to_csv 不會再失敗
  * 使用 Replace 時，將 “find” 留空將不會再失敗 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK for Python v0.1.68

+ **新功能**
  * 建立新工作區時的多個租用戶支援。

+ **已修正的 BUG**
  * 在部署 Web 服務時，您不再需要釘選 pynacl 程式庫版本。

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning 資料準備 SDK v0.3.0

+ **新功能**
  * 已新增 transform_partition_with_file(script_path) 方法，可讓使用者傳入要執行的 Python 檔案路徑

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK for Python v0.1.65
[版本 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) 包含新功能、更多文件、錯誤修正和更多 [Notebook 範例](https://aka.ms/aml-notebooks)。

若要了解已知的 Bug 和因應措施，請參閱[已知問題的清單](resource-known-issues.md)。

+ **重大變更**
  * Workspace.experiments、Workspace.models、Workspace.compute_targets、Workspace.images、Workspace.web_services 會傳回字典，先前傳回清單。 請參閱 [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API 文件。

  * 自動化 Machine Learning 已將正規化均方誤差從主要計量中移除。

+ **HyperDrive**
  * 各種貝氏的 HyperDrive bug 修正，以及取得計量呼叫的效能提升。 
  * Tensorflow 從 1.9 升級至 1.10 
  * 針對冷啟動的 Docker 映像最佳化。 
  * 作業現在會報告正確的狀態，即使是以非 0 的錯誤碼結束。 
  * SDK 中的 RunConfig 屬性驗證。 
  * HyperDrive 執行物件支援類似於一般執行的取消作業：不需要傳遞任何參數。 
  * 改善小工具以維護分散式執行和 HyperDrive 執行的下拉式清單值狀態。 
  * 已修正參數伺服器的 TensorBoard 和其他記錄檔支援。 
  * 服務端上的 Intel(R) MPI 支援。 
  * 修正 BatchAI 驗證期間散發執行修正的參數微調錯誤。 
  * 內容管理員現在會識別主要執行個體。 

+ **Azure 入口網站體驗**
  * 執行詳細資料中支援 log_table() 和 log_row()。 
  * 自動建立有 1、2 或 3 個數值資料行和選擇性類別資料行的資料表和資料列圖形。

+ **自動化 Machine Learning**
  * 改良錯誤處理和文件 
  * 已修正 run 屬性擷取的效能問題。 
  * 已修正繼續執行的問題。 
  * 已修正 Esembling 反覆項目問題。
  * 已修正 MAC OS 上的訓練懸置。
  * 對自訂驗證案例中的巨集平均 PR/ROC 曲線縮小取樣。
  * 已移除額外的索引邏輯。
  * 已從 get_output API 中移除篩選條件。

+ **管線**
  * 已新增 Pipeline.publish() 方法來直接發佈管線，而不需要先啟動執行。   
  * 已新增 PipelineRun.get_pipeline_runs() 方法來擷取從所發佈管線產生的管線執行。

+ **Project Brainwave**
  * 已更新 FPGA 上可用的新 AI 模型支援。

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning 資料準備 SDK v0.2.0
[版本 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) 包含下列功能和錯誤修正：

+ **新功能**
  * One-hot 編碼的支援
  * 分量轉換的支援
   
+ **已修正的 Bug：**
  * 適用於任何 Tornado 版本，不必降級 Tornado 版本
  * 值計數代表所有值，不只是前三個值

## <a name="2018-09-public-preview-refresh"></a>2018-09 (公開預覽刷新版)

Azure Machine Learning 的全新重新整理版本：深入了解此版本： https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>後續步驟

閱讀 [Azure Machine Learning 服務](../service/overview-what-is-azure-ml.md)概觀。
