---
title: 此版本有哪些新功能？
titleSuffix: Azure Machine Learning service
description: 了解 Azure Machine Learning 服務的最新更新，以及機器學習和資料準備 Python SDK。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: 2721e134e03d3d622e61085dc39a2914098ba570
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70930731"
---
# <a name="azure-machine-learning-service-release-notes"></a>Azure Machine Learning 服務版本資訊

本文章會說明 Azure Machine Learning 服務的版本。  如需完整的 SDK 參考內容，請造訪 Azure Machine Learning 的[**Python 的主要 SDK**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)參考頁面。 

若要了解已知的 Bug 和因應措施，請參閱[已知問題的清單](resource-known-issues.md)。

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>適用于 Azure Machine Learning 工作區的新 web 體驗（預覽） 
新的 web 體驗可讓資料科學家和資料工程師完成其端對端機器學習服務生命週期，從準備和視覺化資料，到在單一位置訓練和部署模型。 

![Azure Machine Learning 工作區 UI （預覽）](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**主要功能：**

使用這個新的 Azure Machine Learning 介面，您現在可以：
+ 管理您的筆記本或連結至 Jupyter
+ [執行自動化 ML 實驗](tutorial-first-experiment-automated-ml.md)
+ [從本機檔案、資料存放區 & web 檔案建立資料集](how-to-create-register-datasets.md)
+ 探索 & 準備模型建立的資料集
+ 監視模型的資料漂移 
+ 從儀表板中查看最近的資源

在此版本中，支援下列瀏覽器：Chrome、Firefox、Safari 和 Microsoft Edge Preview。

**已知問題︰**

1. 如果您看到「發生錯誤，請重新整理您的瀏覽器！ 當部署正在進行時，載入區塊檔案時發生錯誤。  

1. 無法刪除或重新命名筆記本和檔案中的檔案。 在公開預覽期間，您可以在筆記本 VM 中使用 Jupyter UI 或終端機來執行更新檔案作業。 因為它是已掛接的網路檔案系統，所以您在筆記本 VM 上所做的所有變更都會立即反映在 [筆記本] 工作區中。 

1. 若要透過 SSH 連線到筆記本 VM：
   1. 尋找在 VM 設定期間所建立的 SSH 金鑰。 或者，在 Azure ML Azure 入口網站中尋找金鑰，> 開啟 [計算] 索引標籤，> 在清單中找出 [筆記本 VM] > 開啟其 [屬性]：從對話方塊複製金鑰。
   1. 將這些公開和私人 SSH 金鑰匯入到您的本機電腦。
   1. 使用它們來透過 SSH 連線到筆記本 VM。 

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.60

+ **新功能**
  + 引進了 FileDataset，它會參考您資料存放區或公用 url 中的單一或多個檔案。 檔案可以是任何格式。 FileDataset 可讓您將檔案下載或掛接至您的計算。 若要深入瞭解 FileDataset，請 https://aka.ms/file-dataset 造訪。
  + 已新增 PythonScript 步驟、Adla 步驟、Databricks 步驟、DataTransferStep 和 AzureBatch 步驟的管線 Yaml 支援

+ **Bug 修正和改善**
  + **azureml-automl-核心**
    + AutoArima 現在是僅供預覽的 suggestable 管線。
    + 改善預測的錯誤報表。
    + 在預測工作中使用自訂例外狀況，而不是泛型來改善記錄。
    + 已移除 max_concurrent_iterations 的檢查，以小於反覆運算總數。
    + AutoML 模型現在會傳回 AutoMLExceptions
    + 此版本可改善自動化機器學習本機執行的執行效能。
  + **azureml-core**
    + 引進資料集。取得「上傳」（工作區）， `TabularDataset`其`FileDataset`會傳回以其註冊名稱為索引鍵之和物件的字典。 
    
    ```py 
    workspace = Workspace.from_config() 
    all_datasets = Dataset.get_all(workspace) 
    mydata = all_datasets['my-data'] 
    ```
    
    + 引進`parition_format`做為`Dataset.Tabular.from_delimited_files`和`Dataset.Tabular.from_parquet.files`的引數。 每個資料路徑的分割區資訊將會根據指定的格式，解壓縮到資料行中。 ' {column_name} ' 會建立字串資料行，而 ' {column_name： yyyy/MM/dd/HH/mm/ss} ' 會建立 datetime 資料行，其中 ' yyyy '、' MM '、' dd '、' HH '、' mm ' 和 ' ss ' 是用來針對日期時間類型來解壓縮 year、month、day、hour、minute 和 second Partition_format 應該從第一個分割區索引鍵的位置開始，直到檔路徑結束為止。 例如，假設路徑為 '.。/USA/2019/01/01/data.csv '，其中分割區是依據國家和時間，partition_format = '/{Country}/{PartitionDate： yyyy/MM/dd}/data .csv ' 會建立字串資料行 ' Country '，其值為 ' USA '，而 datetime 資料行 ' PartitionDate ' 的值為 ' 2019-01-01 '。
    + `to_csv_files`和`to_parquet_files`方法已加入至`TabularDataset`。 這些方法會將資料轉換`TabularDataset`成指定`FileDataset`格式的檔案，以啟用與之間的轉換。
    + 儲存 Model. package （）所產生的 Dockerfile 時，會自動登入基底映射登錄。
    + 不再需要 ' gpu_support ';AzureML 現在會自動偵測並使用 nvidia docker 擴充功能（如果有的話）。 它將在未來的版本中移除。
    + 已新增建立、更新和使用 PipelineDrafts 的支援。
    + 此版本可改善自動化機器學習本機執行的執行效能。
    + 使用者可以依名稱查詢執行歷程記錄中的計量。
    + 在預測工作中使用自訂例外狀況，而不是泛型來改善記錄。
  + **azureml-explain-model**
    + 已將 feature_maps 參數新增至新的 MimicWrapper，讓使用者可以取得原始功能的說明。
    + [資料集上傳] 預設會針對 [上傳說明] [已關閉]，而且可以使用 upload_datasets = True 來重新啟用
    + 已將 "is_law" 篩選參數新增至說明清單和下載函數。
    + 將方法`get_raw_explanation(feature_maps)`新增至全域和本機說明物件。
    + 已將版本檢查新增至具有列印警告的 lightgbm （如果支援的版本低於）
    + 已優化批次處理說明時的記憶體使用量
    + AutoML 模型現在會傳回 AutoMLExceptions
  + **azureml-pipeline-core**
    + 已新增建立、更新和使用 PipelineDrafts 的支援-可用來維護可變的管線定義，並以互動方式使用它們來執行
  + **azureml-train-automl**
    + 已建立的功能，可安裝支援 gpu 的特定版本 pytorch v 1.1.0、cuda 工具組9.0、pytorch 轉換器，這是在遠端 python 執行時間環境中啟用經理 BERT/XLNet 的必要項。
  + **azureml-train-core**
    + 直接在 sdk 中（而不是伺服器端）發生某些超參數空間定義錯誤的早期失敗。

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning 資料準備 SDK v 1.1.14
+ **Bug 修正和改善**
  + 已使用原始路徑和認證啟用寫入 ADLS/ADLSGen2。
  + 已修正導致`include_path=True`無法處理的`read_parquet`bug。
  + 已`to_pandas_dataframe()`修正因例外狀況「不正確屬性值： hostSecret」而造成的失敗。
  + 已修正無法在 Spark 模式的 DBFS 上讀取檔案的錯誤。
  
## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.57
+ **新功能**
  + 已`TabularDataset`啟用，可供 AutomatedML 使用。 若要深入瞭解`TabularDataset`，請造訪 https://aka.ms/azureml/howto/createdatasets 。
  
+ **Bug 修正和改善**
  + **automl-用戶端-核心-nativeclient-android**
    + 已修正當定型和/或驗證標籤（y 和 y_valid）以 pandas 資料框架的形式提供，但不是 numpy 陣列時，所引發的錯誤。
    + 已更新介面來建立`RawDataContext` ，只需要資料`AutoMLBaseSettings`和物件。
    +  允許 AutoML 的使用者卸載在預測時不夠長的訓練系列。 -允許 AutoML 使用者在預測時，從測試集卸載不存在於定型集中的粒紋。
  + **azure-cli-ml**
    + 您現在可以針對 Microsoft 產生和客戶憑證的 AKS 叢集上部署的評分端點，更新 SSL 憑證。
  + **azureml-automl-核心**
    + 已修正 AutoML 中的問題，其中遺漏標籤的資料列不會正確移除。
    + 已改善 AutoML 中的錯誤記錄;完整的錯誤訊息現在一律會寫入記錄檔。
    + AutoML 已將其封裝釘選更新`azureml-defaults`為`azureml-explain-model`包含、 `azureml-dataprep`和。 AutoML 不會再警告套件不符（ `azureml-train-automl`套件除外）。
    + 已修正時間序列中的 cv 分割大小不相等的問題，導致 bin 計算失敗。
    + 針對交叉驗證定型類型執行集團反復專案時，如果我們最後無法下載在整個資料集上定型的模型，我們就會在模型權數和正在送入投票的模型之間產生不一致的情況。集團.
    + 已修正當定型和/或驗證標籤（y 和 y_valid）以 pandas 資料框架的形式提供，但不是 numpy 陣列時，所引發的錯誤。
    + 已修正在輸入資料表的布林資料行中遇到 None 時，預測工作的問題。
    + 允許 AutoML 的使用者卸載在預測時不夠長的訓練系列。 -允許 AutoML 使用者在預測時，從測試集卸載不存在於定型集中的粒紋。
  + **azureml-core**
    + 已修正 blob_cache_timeout 參數順序的問題。
    + 已將外部調整和轉換例外狀況類型新增至系統錯誤。
    + 已新增對遠端執行 Key Vault 秘密的支援。 新增 Keyvault 類別，以新增、取得及列出與您的工作區相關聯之 keyvault 中的秘密。 支援的作業包括：
      + azureml. workspace. _default_keyvault （）
      + keyvault. Keyvault. set _secret （name，value）
      + keyvault. Keyvault. set _secrets （secrets_dict）
      + keyvault. Keyvault. get _secret （name）
      + keyvault. Keyvault. get _secrets （secrets_list）
      + keyvault. Keyvault. list_secrets （）
    + 在遠端執行期間取得預設 keyvault 並取得秘密的其他方法：
      + azureml. workspace. _default_keyvault （）
      + 執行. get _secret （name）
      + 執行. get _secrets （secrets_list）
    + 已將其他覆寫參數新增至提交-hyperdrive CLI 命令。
    + 改善 API 呼叫的可靠性會將重試擴充至常見的要求程式庫例外狀況。
    + 新增從已提交的執行提交執行的支援。
    + 已修正 FileWatcher 中即將到期的 SAS 權杖問題，這會導致檔案在其初始權杖過期後停止上傳。
    + 支援匯入資料集 python SDK 中的 HTTP csv/tsv 檔案。
    + 已淘汰工作區. setup （）方法。 向使用者顯示的警告訊息建議改用 create （）或 get （）/from_config （）。
    + 已新增環境。新增 _private_pip_wheel （），可讓您將私人自訂 python 套件（. .whl）上傳至工作區，並安全地使用它們來建立/具體化環境。
    + 您現在可以針對 Microsoft 產生和客戶憑證的 AKS 叢集上部署的評分端點，更新 SSL 憑證。
  + **azureml-explain-model**
    + 已新增參數，以將模型識別碼新增到上傳的說明。
    + 已`is_raw`將標記新增至記憶體中的說明並上傳。
    + 已新增 azureml-說明模型套件的 pytorch 支援和測試。
  + **azureml-opendatasets**
    + 支援偵測和記錄自動測試環境。
    + 已新增類別，讓我們依縣市和郵遞區號取得人口。
  + **azureml-pipeline-core**
    + 已將標籤屬性新增至輸入和輸出埠定義。
  + **azureml-遙測**
    + 已修正不正確的遙測設定。
  + **azureml-train-automl**
    + 修正安裝程式失敗時的錯誤、安裝程式執行的「錯誤」欄位中未記錄錯誤，因此不會儲存在父執行「錯誤」中。
    + 已修正 AutoML 中的問題，其中遺漏標籤的資料列不會正確移除。
    + 允許 AutoML 的使用者卸載在預測時不夠長的訓練系列。
    + 允許 AutoML 使用者在預測時，從測試集卸載不存在於定型集中的粒紋。
    + 現在 AutoMLStep 會將 automl config 傳遞至後端，以避免變更或新增新的設定參數時發生任何問題。
    + AutoML 資料 Guardrail 現已開放公開預覽。 使用者會在定型之後看到資料 Guardrail 報表（適用于分類/回歸工作），而且也可以透過 SDK API 來存取它。
  + **azureml-train-core**
    + 已在 PyTorch 估計工具中新增 torch 1.2 支援。
  + **azureml-widget**
    + 改善分類定型的混淆矩陣圖表。

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning 資料準備 SDK v 1.1.12
+ **新功能**
  + 現在可以將字串清單當做`read_*`方法的輸入傳入。

+ **Bug 修正和改善**
  + 在 Spark 中`read_parquet`執行時，的效能已大幅改善。
  + 已修正在單一`column_type_builder`資料行具有不明確日期格式的情況下失敗的問題。

### <a name="azure-portal"></a>Azure 入口網站
+ **預覽功能**
  + [執行詳細資料] 頁面現在提供記錄檔和輸出檔案串流。 開啟預覽切換時，檔案會即時串流更新。
  + 在工作區層級設定配額的功能會以預覽形式發行。 AmlCompute 配額會在訂用帳戶層級配置，但我們現在可讓您在工作區之間散發該配額，並將其配置給公平共用和治理。 只要按一下工作區左側導覽列中的 [**使用方式 + 配額**] 分頁，然後選取 [**設定配額**] 索引標籤即可。請注意，您必須是訂用帳戶管理員，才能在工作區層級設定配額，因為這是跨工作區的操作。

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.55

+ **新功能**
  + 以權杖為基礎的驗證現在支援對 AKS 上部署之評分端點的呼叫。 我們會繼續支援目前以金鑰為基礎的驗證，而且使用者可以一次使用其中一種驗證機制。
  + 能夠將虛擬網路（VNet）後方的 blob 儲存體註冊為數據存放區。
  
+ **Bug 修正和改善**
  + **azureml-automl-核心**
    + 修正 CV 分割的驗證大小較小的 bug，並導致預測性和預測的事實與 true 圖表不正確。
    + 遠端執行預測工作的記錄已改進，現在使用者會在執行失敗時提供完整的錯誤訊息。
    + 已修正前置處理旗標為 True 時的時間序列失敗。
    + 讓一些預測資料驗證錯誤訊息更容易採取動作。
    + 藉由卸載和/或資料集的消極式載入來減少 AutoML 執行的記憶體耗用量，特別是在處理常式產生之間
  + **azureml-contrib-說明-模型**
    + 已將 model_task 旗標新增至 explainers，以允許使用者覆寫模型類型的預設自動推斷邏輯
    + Widget 變更：使用 contrib 自動安裝，不再有全域功能重要性的 nbextension 安裝/啟用-支援說明（例如 Permutative）
    + 儀表板變更：除了 [摘要] 頁面上的 beeswarm 繪圖之外，也會顯示盒狀圖和 violin 繪圖-更快速地 rerendering beeswarm 繪圖上的 [頂端-k] 滑杆變更-有用的訊息，說明如何計算前 k 個可自訂的訊息，以取代圖表未提供資料
  + **azureml-core**
    + 已新增 Model. package （）方法，以建立可封裝模型和其相依性的 Docker 映射和 Dockerfile。
    + 已更新本機 webservices 以接受包含環境物件的 InferenceConfigs。
    + 已修正模型。當 '. ' 時，register （）產生不正確模型（針對目前目錄）會傳遞做為 model_path 參數。
    + 新增 submit_child，此功能會反映實驗。將執行指定為已提交子回合的父系時提交。
    + 支援來自 Model 的設定選項。請在 register_model 中註冊。
    + 在現有叢集上執行 JAR 作業的能力。
    + 現在支援 instance_pool_id 和 cluster_log_dbfs_path 參數。
    + 已新增在將模型部署至 Webservice 時，使用環境物件的支援。 現在可以提供環境物件做為 InferenceConfig 物件的一部分。
    + 為新區域新增 appinsifht 對應-centralus-westus-northcentralus
    + 已新增所有資料存放區類別中所有屬性的檔。
    + 已將 blob_cache_timeout 參數`Datastore.register_azure_blob_container`新增至。
    + 已將 save_to_directory 和 load_from_directory 方法新增至 azureml. 環境。
    + 已將「az ml 環境下載」和「az ml 環境 register」命令新增至 CLI。
    + 已新增環境。新增 _private_pip_wheel 方法。
  + **azureml-explain-model**
    + 使用資料集服務（預覽）將資料集追蹤新增至說明。
    + 將全域說明從10k 串流至100時，已減少預設批次大小。
    + 已將 model_task 旗標新增至 explainers，以允許使用者覆寫模型類型的預設自動推斷邏輯。
  + **azureml-mlflow**
    + 已修正 mlflow 中已忽略嵌套目錄之 build_image 的錯誤。
  + **azureml-pipeline-steps**
    + 已新增在現有 Azure Databricks 叢集上執行 JAR 作業的功能。
    + 已新增 DatabricksStep 步驟的支援 instance_pool_id 和 cluster_log_dbfs_path 參數。
    + 已新增 DatabricksStep 步驟中管線參數的支援。
  + **azureml-train-automl**
    + 已新增集團相關檔案的 docstrings。
    + 已將檔更新為適用于`max_cores_per_iteration`和的更適當語言`max_concurrent_iterations`
    + 遠端執行預測工作的記錄已改進，現在使用者會在執行失敗時提供完整的錯誤訊息。
    + 已從管線 automlstep 筆記本中移除 get_data。
    + 已開始支援 automlstep 中的 dataprep。

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning 資料準備 SDK v 1.1.10

+ **新功能**
  + 您現在可以要求在特定資料行上執行特定的檢查器（例如長條圖、散佈圖等）。
  + 已將平行處理自`append_columns`變數加入至。 若為 True，則會將資料載入記憶體中，但執行將會平行執行;如果為 False，則執行會串流處理，但會進行單一執行緒。

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.53

+ **新功能**
  + 自動化 Machine Learning 現在支援在遠端計算目標上定型 ONNX 模型
  + Azure Machine Learning 現在提供從先前的執行、檢查點或模型檔案繼續定型的功能。
    + 瞭解如何[使用估算器從上一次執行繼續定型](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Bug 修正和改善**
  + **automl-用戶端-核心-nativeclient-android**
    + 修正轉換（bug 連結）之後遺失資料行類型的 bug; 
    + 允許 y_query 是一種物件類型，在開頭（#459519）包含無（s）。
  + **azure-cli-ml**
    + CLI 命令「模型部署」和「服務更新」現在接受參數、設定檔或兩者的組合。 參數的優先順序高於檔案中的屬性。
    + 現在可以在註冊之後更新模型描述
  + **azureml-automl-核心**
    + 將 NimbusML 相依性更新為1.2.0 版本（目前最新）。
    + 新增要在 AutoML 估算器中使用的 Nimbus ML 估算器 & 管線支援。
    + 修正集團選取程式中的 bug，即使分數保持不變也不必要地增加產生的集團。
    + 啟用跨 CV 分割重複使用某些 featurizations，以進行預測工作。 這可加快執行安裝的執行時間，其方式是 n_cross_validations 昂貴的 featurizations （例如延遲和滾動時段）。
    + 解決時間超出 pandas 支援的時間範圍時的問題。 如果時間小於 pd，我們現在會引發 DataException。Timestamp. min 或大於 pd。時間戳記。最大值
    + 預測現在會在定型和測試集中允許不同的頻率（如果可以對齊的話）。 例如，「每季從一月開始」和「10月開始的季度」可以對齊。
    + 屬性 "parameters" 已加入至 TimeSeriesTransformer。
    + 移除舊的例外狀況類別。
    + 在預測工作`target_lags`中，參數現在會接受單一整數值或整數清單。 如果已提供整數，則只會建立一個 lag。 如果提供了清單，將會採用延遲的唯一值。 target_lags = [1，2，2，4] 將建立延遲1、2和4個期間。
    + 修正轉換（bug 連結）之後遺失資料行類型的 bug;
    + 在`model.forecast(X, y_query)`中，允許 y_query 是一種物件類型，在開頭（#459519）包含無（s）。
    + 將預期的值新增至 automl 輸出
  + **azureml-contrib-datadrift**
    +  範例筆記本的改良功能，包括切換至 azureml-opendatasets，而不是 azureml-contrib opendatasets 和效能改善（在充實資料時）
  + **azureml-contrib-說明-模型**
    + Azureml-contrib 中原始功能重要性的淺轉換引數固定說明-說明-模型套件
    + 已將 segmentations 新增至說明中的映射說明-contrib-說明-模型套件
    + 新增 LimeExplainer 的 scipy sparse 支援
    + 當 include_local = False 時，將 batch_size 新增至模擬說明，以批次處理中的全域說明來改善 DecisionTreeExplainableModel 的執行時間
  + **azureml-contrib-featureengineering**
    + 修正呼叫 set_featurizer_timeseries_params （）： dict 數值型別變更和 null 檢查-加入筆記本 for 時間序列 featurizer
    + 將 NimbusML 相依性更新為1.2.0 版本（目前最新）。
  + **azureml-core**
    + 已新增在 AzureML CLI 中附加 DBFS 資料存放區的功能 
    + 已修正資料存放區上傳的錯誤（如果`target_path`以開頭的位置建立空白資料夾）`/`
    + 已修正 ServicePrincipalAuthentication 中的 deepcopy 問題。
    + 已將「az ml 環境 show」和「az ml 環境 list」命令新增至 CLI。
    + 環境現在支援將 base_dockerfile 指定為已建立之 base_image 的替代方法。
    + 未使用的 RunConfiguration 設定 auto_prepare_environment 已標示為已被取代。
    + 現在可以在註冊之後更新模型描述
    + 錯誤修正[模型] 和 [映射刪除] 現在會提供詳細資訊, 說明如何在因為上游相依性而導致刪除失敗時, 取得相依于它們的上游物件。
    + 已修正為某些環境建立工作區時所發生部署的空白持續時間列印的 bug。
    + 改良的工作區建立失敗例外狀況。 使用者看不到 [無法建立工作區]。 找不到 ...」作為訊息，並改為查看實際的建立失敗。
    + 在 AKS webservices 中新增權杖驗證的支援。 
    + 將`get_token()`方法新增`Webservice`至物件。
    + 已新增 CLI 支援來管理機器學習資料集。
    + `Datastore.register_azure_blob_container`現在可以選擇採用`blob_cache_timeout`值（以秒為單位），以設定 blobfuse 的掛接參數，以啟用此資料存放區的快取到期時間。 預設值為 [沒有超時]，也就是讀取 blob 時，它會保留在本機快取中，直到作業完成為止。 大部分的作業會偏好此設定，但某些作業需要從大型資料集讀取更多資料，而不能容納其節點。 針對這些作業，微調此參數將有助於成功完成。 微調此參數時請注意：設定值過低可能會導致效能不佳，因為 epoch 中使用的資料可能會在再次使用之前過期。 這表示所有讀取作業都是從 blob 儲存體（也就是網路）而不是本機快取完成，這會對定型時間造成負面影響。
    + 現在可以在註冊之後正確地更新模型描述
    + 模型和影像刪除現在提供有關上游物件的詳細資訊，而這些物件會導致刪除失敗
    + 使用 mlflow 來改善遠端執行的資源使用率。
  + **azureml-explain-model**
    + Azureml-contrib 中原始功能重要性的淺轉換引數固定說明-說明-模型套件
    + 新增 LimeExplainer 的 scipy sparse 支援
    + 已新增圖形線性說明包裝函式，以及另一個層級至表格式說明以說明線性模型
    + 如需說明模型程式庫中的模擬說明，請修正 include_local = False 以進行稀疏資料輸入時的錯誤
    + 將預期的值新增至 automl 輸出
    + 已修正提供轉換引數以取得原始功能重要性時的排列功能重要性
    + 當 include_local = False 時，將 batch_size 新增至模擬說明，以批次處理中的全域說明來改善 DecisionTreeExplainableModel 的執行時間
    + 針對模型可解釋性程式庫，已修正黑箱 explainers，其中 pandas 資料框架輸入是預測的必要項
    + 已修正有時`explanation.expected_values`會傳回 float，而不是包含浮點數的清單的錯誤（bug）。
  + **azureml-mlflow**
    + 改善 mlflow 的效能。 set _experiment （experiment_name）
    + 修正使用 InteractiveLoginAuthentication for mlflow tracking_uri 時的錯誤
    + 使用 mlflow 來改善遠端執行的資源使用率。
    + 改善 azureml mlflow 套件的檔
    + Patch bug，其中 mlflow .log _artifacts （"my_dir"）會將成品儲存在 "my_dir/< 成品路徑 >"，而不是 "< 成品路徑 >"
  + **azureml-opendatasets**
    + 將 opendatasets 的 pyarrow 釘選到舊版本（< 0.14.0），因為新引進的記憶體問題。
    +  將 azureml-contrib-opendatasets 移至 azureml-opendatasets。 -允許將開啟的資料集類別註冊到 AML 工作區，並順暢地運用 AML 資料集功能。 -大幅改善非 SPARK 版本中的 NoaaIsdWeather 更豐富效能。
  + **azureml-pipeline-steps**
    + DatabricksStep 中的輸入和輸出現在支援 DBFS 資料存放區。
    + 已更新有關輸入/輸出之 Azure Batch 步驟的檔。
    + 在」已 azurebatchstep 中，將 [ *delete_batch_job_after_finish*預設值] 變更為 [ *true*]。
  + **azureml-遙測**
    +  將 azureml-contrib-opendatasets 移至 azureml-opendatasets。 -允許將開啟的資料集類別註冊到 AML 工作區，並順暢地運用 AML 資料集功能。 -大幅改善非 SPARK 版本中的 NoaaIsdWeather 更豐富效能。
  + **azureml-train-automl**
    + 已更新 get_output 上的檔，以反映實際的傳回類型，並提供有關抓取索引鍵屬性的其他注意事項。
    + 將 NimbusML 相依性更新為1.2.0 版本（目前最新）。
    + 將預期的值新增至 automl 輸出
  + **azureml-train-core**
    + 字串現已接受為自動超參數微調的計算目標
    + 未使用的 RunConfiguration 設定 auto_prepare_environment 已標示為已被取代。

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning 資料準備 SDK v 1.1。9

+ **新功能**
  + 已新增從 HTTP 或 HTTPs url 直接讀取檔案的支援。

+ **Bug 修正和改善**
  + 已改善嘗試從遠端來源（目前不支援）讀取 Parquet 資料集時的錯誤訊息。
  + 修正在 ADLS Gen 2 中寫入至 Parquet 檔案格式，並在路徑中更新 ADLS Gen 2 容器名稱時的錯誤。

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>視覺化介面
+ **預覽功能**
  + 已在視覺化介面中新增「執行 R 腳本」模組。

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.48

+ **新功能**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets**現已提供為**azureml-opendatasets**。 舊的封裝仍然可以使用, 但建議您使用**azureml-opendatasets**向前邁進, 以取得更豐富的功能和改進。
    + 這個新封裝可讓您將開啟的資料集註冊為 AML 工作區中的資料集, 並利用資料集提供的任何功能。
    + 它也包含現有的功能, 例如將開啟的資料集當做 Pandas/SPARK 資料框架使用, 以及某些資料集 (例如氣象) 的位置聯結。

+ **預覽功能**
    + HyperDriveConfig 現在可以接受管線物件做為參數, 以支援使用管線的超參數微調。

+ **Bug 修正和改善**
  + **azureml-train-automl**
    + 修正在轉換之後遺失資料行類型的 bug。
    + 修正 bug，讓 y_query 成為一開始就包含無（s）的物件類型。 
    + 已修正集團選取程式中不必要地增加產生的集團的問題, 即使分數保持不變也一樣。
    + 已修正 AutoMLStep 中的 whitelist_models 和 blacklist_models 設定問題。
    + 已修正在 Azure ML 管線的內容中使用 AutoML 時, 無法使用前置處理的問題。
  + **azureml-opendatasets**
    + 已將 azureml-contrib-opendatasets 移至 azureml-opendatasets。
    + 允許將開啟的資料集類別註冊到 AML 工作區, 並順暢地利用 AML 資料集功能。
    + 大幅改善非 SPARK 版本中的 NoaaIsdWeather 更豐富效能。
  + **azureml-explain-model**
    + 已更新 interpretability 物件的線上檔。
    + 已在 include_local = False 時，將 batch_size 新增至模擬說明，以批次處理中的全域說明來改善 DecisionTreeExplainableModel 的執行時間。
    + 修正了有時候會`explanation.expected_values`傳回浮點數的問題, 而不是包含浮點數的清單。
    + 已在說明模型程式庫中, 將預期的值新增至模擬說明的 automl 輸出。
    + 已修正提供轉換引數以取得原始功能重要性時的排列功能重要性。
    + 已將 include_local = False 的 batch_size 新增至模擬說明，以分批串流全域解釋，以改善模型可解釋性程式庫 DecisionTreeExplainableModel 的執行時間。
  + **azureml-core**
    + 已新增在 AzureML CLI 中附加 DBFS 資料存放區的功能。
    + 已修正資料存放區上傳的問題, 如果`target_path`以啟動, `/`則會建立空的資料夾。
    + 已啟用兩個資料集的比較。
    + [模型] 和 [映射刪除] 現在會提供詳細資訊, 說明如何在因為上游相依性而導致刪除失敗時, 取得相依于它們的上游物件。
    + 已淘汰 auto_prepare_environment 中未使用的 RunConfiguration 設定。
  + **azureml-mlflow**
    + 已改善使用 mlflow 之遠端執行的資源使用率。
    + 已改善 azureml mlflow 套件的檔。
    + 已修正 mlflow .log _artifacts （"my_dir"）會將成品儲存在「my_dir/成品路徑」，而不是「成品-路徑」的問題。
  + **azureml-pipeline-core**
    + 所有管線步驟的參數 hash_paths 已被取代，未來將會移除。 根據預設，會雜湊 source_directory 的內容（除了 amlignore 或. .gitignore 中列出的檔案以外）
    + 繼續改善模組和 ModuleStep 以支援計算類型特定模組, 以準備進行 RunConfiguration 整合和進一步變更以解除鎖定管線中的使用方式。
  + **azureml-pipeline-steps**
    + 」已 azurebatchstep已改善輸入/輸出的相關檔。
    + 」已 azurebatchstep已將 delete_batch_job_after_finish 預設值變更為 true。
  + **azureml-train-core**
    + 字串現已接受為自動超參數微調的計算目標。
    + 已淘汰 auto_prepare_environment 中未使用的 RunConfiguration 設定。
    + 已取代`conda_dependencies_file_path`的`pip_requirements_file_path` `pip_requirements_file`參數, 並分別支援和。 `conda_dependencies_file`
  + **azureml-opendatasets**
    + 大幅提升非 SPARK 版本的 NoaaIsdWeather 豐富效能。

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning 資料準備 SDK v 1.1。8

+ **新功能**
 + 現在可以反復查看資料流程物件, 產生一連串的記錄。 請參閱的`Dataflow.to_record_iterator`檔。

+ **Bug 修正和改善**
 + 提升 DataPrep SDK 的穩定性。
 + 改善 pandas 資料框架與非字串資料行索引的處理。
 + 改善資料集中的`to_pandas_dataframe`效能。
 + 修正了在多節點環境中執行時, Spark 執行資料集失敗的錯誤。

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning 資料準備 SDK v 1.1。7

我們已還原改善效能的變更, 因為這會導致某些客戶使用 Azure Databricks 的問題。 如果您在 Azure Databricks 上遇到問題, 可以使用下列其中一種方法升級至版本 1.1.7:
1. 執行此腳本進行升級:`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. 重新建立叢集, 這將會安裝最新的資料準備 SDK 版本。

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.45

+ **新功能**
  + 新增決策樹代理模型以模擬 azureml 中的說明-說明-模型套件
  + 能夠指定要安裝在推斷映射上的 CUDA 版本。 支援 CUDA 9.0、9.1 和10.0。
  + 您現在可以在[AZURE Ml 容器 GitHub 存放庫](https://github.com/Azure/AzureML-Containers)和[DOCKERHUB](https://hub.docker.com/_/microsoft-azureml)取得 azure ml 訓練基底映射的相關資訊
  + 已新增管線排程的 CLI 支援。 執行「az ml 管線-h」以深入瞭解
  + 已將自訂 Kubernetes 命名空間參數新增至 AKS webservice 部署設定和 CLI。
  + 所有管線步驟的已淘汰 hash_paths 參數
  + Model。 register 現在支援使用`child_paths`參數, 將多個個別檔案註冊為單一模型。
  
+ **預覽功能**
    + 計分 explainers 現在可以選擇性地儲存 conda 和 pip 資訊, 以提供更可靠的序列化和還原序列化。
    + 自動功能選取器的錯誤修正。
    + 已將 mlflow 更新為新的 api，並已修補新的執行所公開的 bug。

+ **Bug 修正和改善**
  + 已從 azureml 核心移除 paramiko 相依性。 已新增舊版計算目標附加方法的取代警告。
  + 改善 create_children 的效能。
  + 在 [模擬具有二元分類器的說明] 中，修正當教師機率用於縮放圖形值時的機率順序。
  + 已改善自動化機器學習的錯誤處理和訊息。 
  + 已修正自動化機器學習的反復專案超時問題。
  + 已改善自動化機器學習的時間序列轉換效能。

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning 資料準備 SDK v 1.1。6

+ **新功能**
  + 已新增 top 值 (`SummaryFunction.TOPVALUES`) 和底端值 (`SummaryFunction.BOTTOMVALUES`) 的摘要函數。

+ **Bug 修正和改善**
  + 大幅改善的效能`read_pandas_dataframe`。
  + 已修正會導致`get_profile()`指向二進位檔案的資料流程失敗的錯誤。
  + 公開`set_diagnostics_collection()`以允許以程式設計方式啟用/停用遙測集合。
  + 已變更的行為`get_profile()`。 現在會忽略 Min、Mean、Std 和 Sum 的 NaN 值, 這與 Pandas 的行為一致。


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.43

+ **新功能**
  + Azure Machine Learning 現在提供熱門機器學習和資料分析架構 Scikit-learn 的一流支援。 使用者可以使用[ `SKLearn`估計工具](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py), 輕鬆地訓練和部署 scikit-learn 學習模型。
    + 瞭解如何使用[scikit-learn 執行超參數微調-瞭解如何使用 HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb)。
  + 已新增在管線中建立 ModuleStep 以及模組和 ModuleVersion 類別的支援, 以管理可重複使用的計算單位。
  + ACI webservices 現在支援透過更新的持續 scoring_uri。 Scoring_uri 會從 IP 變更為 FQDN。 您可以藉由在 deploy_configuration 上設定 dns_name_label 來設定 FQDN 的 Dns 名稱標籤。 
  + 自動化機器學習服務的新功能:
    + 適用于預測的 STL featurizer
    + 已啟用 Kmeans 叢集以進行功能掃描
  + AmlCompute 配額核准的速度變快了! 我們現在已自動化在閾值內核准配額要求的程式。 如需有關配額如何工作的詳細資訊, 請瞭解[如何管理配額](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)。

+ **預覽功能**
    + 透過 azureml MLflow 套件 ([範例筆記本](https://aka.ms/azureml-mlflow-examples)) 與[MLflow](https://mlflow.org) 1.0.0 追蹤進行整合。
    + 提交 Jupyter 筆記本作為執行。 [API 參考檔](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + 透過 azureml-contrib-datadrift package ([範例筆記本](https://aka.ms/azureml-datadrift-example)) 公開預覽[資料漂移](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift?view=azure-ml-py)偵測器。 資料漂移是模型精確度隨著時間而下降的其中一個主要原因。 在生產環境中提供模型的資料與定型模型的資料不同時, 就會發生此情況。 AML 資料漂移偵測器可協助客戶監視資料漂移, 並在偵測到漂移時傳送警示。 

+ **重大變更**

+ **Bug 修正和改善**
  + RunConfiguration 載入和儲存支援針對先前的行為指定具有完整回溯相容性的完整檔案路徑。
  + 已在 ServicePrincipalAuthentication 中新增快取, 預設為關閉。
  + 以相同的計量名稱來啟用多個繪圖的記錄功能。
  + 模型類別現在已從 azureml 核心 (`from azureml.core import Model`) 正確地匯入。
  + 在管線步驟中`hash_path` , 參數現在已被取代。 新的行為是雜湊完成 source_directory，但 amlignore 或. .gitignore 中列出的檔案除外。
  + 在管線封裝中, `get_all`不同`get_all_*`的`list`和方法分別被取代為和`list_*`。
  + azureml。 get 執行時間不再需要匯入類別，再傳回原始的執行類型。
  + 已修正某些 WebService 更新呼叫並未觸發更新的問題。
  + AKS webservices 上的計分超時應該介於5毫秒和300000ms 之間。 評分要求的允許 scoring_timeout_ms 上限已從1分鐘 i 藉為5分鐘。
  + LocalWebservice 物件現在具有`scoring_uri`和`swagger_uri`屬性。
  + 已移動輸出目錄建立, 並從使用者進程輸出目錄上傳。 已啟用執行歷程記錄 SDK, 以在每個使用者進程中執行。 這應該會解決分散式定型執行所遇到的一些同步處理問題。
  + 從使用者進程名稱寫入的 azureml 記錄檔名稱現在會包含進程名稱 (僅適用于分散式訓練) 和 PID。

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning 資料準備 SDK v 1.1。5

+ **Bug 修正和改善**
  + 針對具有2位數年份格式的已轉譯日期時間值, 有效年份的範圍已更新為符合 Windows 可能發行的版本。 範圍已從1930-2029 變更為1950-2049。
  + 當讀取檔案和設定`handleQuotedLineBreaks=True`時, `\r`將會被視為新行。
  + 已修正在某些情況`read_pandas_dataframe`下導致失敗的錯誤 (bug)。
  + 改善的`get_profile`效能。
  + 改善的錯誤訊息。

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning 資料準備 SDK 1.1。4

+ **新功能**
  + 您現在可以使用下列運算式語言函式, 將日期時間值解壓縮並剖析成新的資料行。
    + `RegEx.extract_record()`將 datetime 元素解壓縮至新的資料行。
    + `create_datetime()`從個別的 datetime 元素建立 datetime 物件。
  + 呼叫`get_profile()`時, 您現在可以看到分量資料行標示為 (est.), 以清楚指出這些值是近似值。
  + 從 Azure Blob 儲存體讀取時, 您現在可以使用 * * 萬用字元。
    + 例如，`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **錯誤修正**
  + 已修正與從遠端來源 (Azure Blob) 讀取 Parquet 檔案相關的錯誤。

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.39
+ **變更**
  + [執行設定 auto_prepare_environment] 選項即將淘汰，且 [自動準備] 已成為預設值。

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning 資料準備 SDK v 1.1。3

+ **新功能**
  + 已藉由呼叫 read_postgresql 或使用資料存放區，新增了讀取 PostgresSQL 資料庫的支援。
    + 請參閱操作指南中的範例:
      + [資料內嵌筆記本](https://aka.ms/aml-data-prep-ingestion-nb)
      + [資料存放區筆記本](https://aka.ms/aml-data-prep-datastore-nb)

+ **Bug 修正和改善**
  + 已修正資料行類型轉換的問題:
  + 現在會正確地將布林值或數值資料行轉換成布林資料行。
  + 當嘗試將日期資料行設定為日期類型時, 現在不會失敗。
  + 改良的 JoinType 類型和隨附的參考檔。 聯結兩個數據流時, 您現在可以指定其中一種聯結類型:
    + NONE、MATCH、INNER、UNMATCHLEFT、LEFTANTI、LEFTOUTER、UNMATCHRIGHT、RIGHTANTI、RIGHTOUTER、FULLANTI、FULL。
  + 改良的資料類型推斷來辨識更多的日期格式。

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站中, 您現在可以:
+ 建立並執行自動化 ML 實驗 
+ 建立筆記本 VM, 以試用 Jupyter 筆記本或您自己的範例。
+ Machine Learning 服務工作區中的全新撰寫區段 (預覽), 其中包括自動化 Machine Learning、視覺化介面和託管的筆記本 Vm
    + 使用自動化機器學習服務自動建立模型 
    + 使用拖放視覺介面來執行實驗
    + 建立筆記本 VM 以流覽資料、建立模型及部署服務。
+ [執行報表] 和 [執行詳細資料] 頁面中的即時圖表和計量更新
+ 已更新 [執行詳細資料] 頁面中記錄、輸出和快照集的檔案檢視器。
+ [實驗] 索引標籤中新的和改良的報表建立體驗。 
+ 已新增從 [Azure Machine Learning 服務] 工作區的 [總覽] 頁面下載 config.xml 檔案的功能。
+ 從 Azure Databricks 工作區支援 Machine Learning 服務工作區建立 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.33
+ **新功能**
  + _工作區。 create_方法現在接受 CPU 和 GPU 叢集的預設叢集設定。
  + 如果工作區建立失敗, 則會清除相依的資源。
  + 預設 Azure Container Registry SKU 已切換為 [基本]。
  + 當需要執行或建立映射時, 會延遲建立 Azure Container Registry。
  + 支援定型執行的環境。

### <a name="notebook-virtual-machine"></a>筆記本虛擬機器 

使用筆記本 VM 做為適用于 Jupyter 筆記本的安全、符合企業需求的裝載環境, 您可以在其中編寫機器學習服務實驗、將模型部署為 web 端點, 以及使用 Python Azure Machine Learning SDK 來執行其他所有作業。 它提供數個功能:
+ [快速啟動](tutorial-1st-experiment-sdk-setup.md) 具有最新版 Azure Machine Learning SDK 和相關套件的預先設定筆記本 VM。
+ 存取是透過經過驗證的技術 (例如 HTTPS、Azure Active Directory 驗證和授權) 來保護。
+ 在 Azure Machine Learning 工作區 blob 儲存體帳戶中, 筆記本和程式碼的可靠雲端儲存體。 您可以安全地刪除您的筆記本 VM, 而不會遺失您的工作。
+ 已預先安裝的範例筆記本, 可探索及試驗 Azure Machine Learning 服務功能。
+ Azure Vm 的完整自訂功能、任何 VM 類型、任何封裝、任何驅動程式。 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK for Python v 1.0.33 已發行。

+ [Fpga](concept-accelerate-with-fpgas.md)上的 Azure ML 硬體加速模型已正式推出。
  + 您現在可以[使用 azureml-accel 模型套件](how-to-deploy-fpga-web-service.md)來執行下列動作:
    + 訓練支援的深度類神經網路 (ResNet 50、ResNet 152、DenseNet-121、VGG-16-16 和 SSD-VGG-16) 的權數
    + 搭配支援的 DNN 使用轉移學習
    + 向模型管理服務註冊模型並容器化模型
    + 使用 Azure Kubernetes Service (AKS) 叢集中的 FPGA 將模型部署至 Azure VM
  + 將容器部署至[Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)伺服器裝置
  + 使用此[範例](https://github.com/Azure-Samples/aml-hardware-accelerated-models)的 gRPC 端點來為您的資料評分

### <a name="automated-machine-learning"></a>自動化 Machine Learning

+ 功能優化可動態新增有以進行效能優化。 新有: 工作內嵌、辨識項權數、目標編碼、文字目標編碼、叢集距離
+ 智慧型 CV, 可處理自動化 ML 內的定型/有效分割
+ 少量的記憶體優化變更和執行時間效能改進
+ 模型說明中的效能改進
+ 本機執行的 ONNX 模型轉換
+ 新增的次取樣支援
+ 未定義結束準則時的智慧型停止
+ 堆疊整體

+ 時間序列預測
  + 新增預測預測函數   
  + 您現在可以在時間序列資料上使用輪流的交叉驗證
  + 已新增新功能來設定時間序列延遲 
  + 新增功能以支援滾動視窗匯總功能
  + 在實驗設定中定義國家/地區代碼時的新假日偵測和 featurizer

+ Azure Databricks
  + 啟用時間序列預測和模型 explainabilty/interpretability 功能
  + 您現在可以取消並繼續 (繼續) 自動化 ML 實驗
  + 已新增多核心處理的支援

### <a name="mlops"></a>MLOps
+ **針對評分容器進行本機部署 & 的偵錯工具**<br/> 您現在可以在本機部署 ML 模型, 並快速地逐一查看評分檔案和相依性, 以確保其行為如預期般運作。

+ **Introduced InferenceConfig & Model.deploy()**<br/> 模型部署現在支援使用專案腳本來指定源資料夾, 這與 RunConfig 相同。  此外, 模型部署已簡化為單一命令。

+ **Git 參考追蹤**<br/> 客戶已要求基本的 Git 整合功能一段時間, 因為它有助於維護端對端的審核記錄。 我們已針對 Git 相關的中繼資料 (存放庫、認可、清除狀態), 在 Azure ML 中的主要實體上執行追蹤。 SDK 和 CLI 會自動收集這項資訊。

+ **模型分析 & 驗證服務**<br/> 客戶經常抱怨有困難地調整與推斷服務相關聯的計算大小。 透過我們的模型分析服務, 客戶可以提供範例輸入, 我們會分析16個不同的 CPU/記憶體設定, 以判斷部署的最佳大小。

+ **帶入您自己的基底映射以進行推斷**<br/> 另一個常見的抱怨是, 從實驗移至推斷的困難會重新共用相依性。 使用新的基底映射共用功能, 您現在可以重複使用您的實驗基底映射、相依性和所有專案來進行推斷。 這應該會加速部署, 並減少內部與外部迴圈之間的差距。

+ **改良的 Swagger 架構產生體驗**<br/> 先前的 swagger 產生方法容易發生錯誤, 無法自動化。 我們有新的內嵌方法, 可透過裝飾專案從任何 Python 函式產生 swagger 架構。 我們有開放原始碼這段程式碼, 而我們的架構產生通訊協定並不會與 Azure ML 平臺結合。

+ **Azure ML CLI 已正式推出 (GA)**<br/> 現在可以使用單一 CLI 命令來部署模型。 我們有一般客戶的意見反應, 不會從 Jupyter 筆記本部署 ML 模型。 [**CLI 參考檔**](https://aka.ms/azmlcli)已更新。


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK for Python v 1.0.30 已發行。

[`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py)引進了加入新版本的已發行管線, 同時維持相同的端點。

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning 資料準備 SDK v 1.1。2

注意：資料準備 Python SDK 將不再安裝`numpy`和`pandas`封裝。 請參閱[更新的安裝指示](https://aka.ms/aml-data-prep-installation)。

+ **新功能**
  + 您現在可以使用 [資料透視轉換]。
    + 操作指南:[資料透視筆記本](https://aka.ms/aml-data-prep-pivot-nb)
  + 您現在可以在原生函式中使用正則運算式。
    + 例如：
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + 您現在可以在`to_upper`運算式`to_lower`語言中使用 和 函數。
  + 您現在可以看到資料設定檔中每個資料行的唯一值數目。
  + 對於一些常用的讀取器步驟, 您現在可以傳入`infer_column_types`引數。 如果設定為`True`, 則資料準備會嘗試偵測並自動轉換資料行類型。
    + `inference_arguments`現在已被取代。
  + 您現在可以呼叫`Dataflow.shape`。

+ **Bug 修正和改善**
  + `keep_columns` 現在`keep_columns`會接受額外的選擇性`validate_column_exists`引數, 以檢查的結果是否會包含任何資料行。
  + 所有讀取器步驟 (讀取自檔案) 現在都接受額外的選擇性自`verify_exists`變數。
  + 改善從 pandas 資料框架讀取及取得資料設定檔的效能。
  + 已修正從資料流程切割單一步驟失敗且具有單一索引的 bug。

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure 入口網站
  + 您現在可以在現有的遠端計算叢集上重新提交現有的腳本執行。 
  + 您現在可以在 [管線] 索引標籤上, 使用新的參數來執行已發佈的管線。 
  + [執行詳細資料] 現在支援新的快照集檔案檢視器。 當您提交特定執行時, 可以查看目錄的快照集。 您也可以下載已提交的筆記本以開始執行。
  + 您現在可以從 Azure 入口網站取消父系執行。

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.23

+ **新功能**
  + Azure Machine Learning SDK 現在支援 Python 3.7。
  + Azure Machine Learning DNN 估算器現在提供內建的多版本支援。 例如, `TensorFlow`  估計工具現在接受`framework_version`參數, 而使用者可以指定 ' 1.10 ' 或 ' 1.12 ' 的版本。 如需目前 SDK 版本所支援的版本清單, 請在所`get_supported_versions()`要的 framework 類別上呼叫 (例如, `TensorFlow.get_supported_versions()`)。
  如需最新 SDK 版本所支援的版本清單, 請參閱[DNN 估計工具檔](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)\ (英文 \)。

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning 資料準備 SDK 1.1.1 版

+ **新功能**
  + 您可以使用 read_ * 轉換來讀取多個資料存放區/資料路徑/DataReference 來源。
  + 您可以在資料行上執行下列作業, 以建立新的資料行: 除法、floor、模數、乘冪、長度。
  + 資料準備現在是 Azure ML 診斷套件的一部分, 而且預設會記錄診斷資訊。
    + 若要關閉此功能, 請將此環境變數設定為 true:DISABLE_DPREP_LOGGER

+ **Bug 修正和改善**
  + 已改善常用類別和函式的程式碼檔。
  + 已修正 auto_read_file 中無法讀取 Excel 檔案的錯誤。
  + 已新增覆寫 read_pandas_dataframe 中資料夾的選項。
  + 改善 dotnetcore2 相依性安裝的效能, 並新增 Fedora 27/28 和 Ubuntu 1804 的支援。
  + 改善從 Azure Blob 讀取的效能。
  + 資料行類型偵測現在支援 Long 類型的資料行。
  + 已修正某些日期值顯示為時間戳記而不是 Python datetime 物件的 bug。
  + 已修正某些類型計數顯示為雙精確度 (而不是整數) 的 bug。

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.21

+ **新功能**
  + *Create_children*方法可讓您透過單一呼叫，以低延遲的方式建立多個子回合。

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning 資料準備 SDK v 1.1。0

+ **重大變更**
  + 資料準備套件的概念已被取代, 不再受到支援。 除了在一個封裝中保存多個資料流程, 您也可以個別保存資料流程。
    + 操作指南:[開啟和儲存資料流程筆記本](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **新功能**
  + 資料準備現在可以辨識符合特定語義類型的資料行, 並據以進行分割。 目前支援的 STypes 包括: 電子郵件地址、地理座標 (緯度 & 經度)、IPv4 和 IPv6 位址、美國電話號碼和美國郵遞區號。
    + 操作指南:[語義類型筆記本](https://aka.ms/aml-data-prep-semantic-types-nb)
  + 資料準備現在支援下列作業, 從兩個數值資料行產生結果資料行: 減去、乘、除和模數。
  + 您可以在`verify_has_data()`資料流程上呼叫, 以檢查資料流程是否會在執行時產生記錄。

+ **Bug 修正和改善**
  + 您現在可以在數值資料行設定檔的長條圖中, 指定要使用的 bin 數目。
  + `read_pandas_dataframe`轉換現在需要資料框架具有字串或位元組類型的資料行名稱。
  + 已修正`fill_nulls`轉換中的錯誤, 如果遺漏資料行, 就不會正確填入值。

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.18

 + **變更**
   + Azureml-tensorboard 套件會取代 azureml-contrib-tensorboard。
   + 在此版本中, 您可以在受控計算叢集 (amlcompute) 上設定使用者帳戶, 同時建立它。 這可以藉由在布建設定中傳遞這些屬性來完成。 您可以在[SDK 參考檔](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-)中找到更多詳細資料。

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning 資料準備 SDK v 1.0.17

+ **新功能**
  + 現在支援加入兩個數值資料行, 以使用運算式語言來產生結果資料行。

+ **Bug 修正和改善**
  + 已改善 random_split 的檔和參數檢查。
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning 資料準備 SDK v 1.0.16

+ **Bug 修正**
  + 已修正 API 變更所造成的服務主體驗證問題。

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>適用于 Python 的 Azure Machine Learning SDK 1.0.17

+ **新功能**

  + Azure Machine Learning 現在為熱門的 DNN 架構 Chainer 提供了一流的支援。 使用[`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)類別使用者可以輕鬆地定型和部署 Chainer 模型。
    + 瞭解如何[使用 ChainerMN 執行分散式訓練](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + 瞭解如何[使用 HyperDrive 搭配 Chainer 執行超參數微調](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning 管線新增功能會根據資料存放區的修改來觸發管線執行。 管線[排程筆記本](https://aka.ms/pl-schedule)會更新以展示這項功能。

+ **Bug 修正和改善**
  + 我們已新增支援 Azure Machine Learning 管線，以便在提供給[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)的[RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)上，將 source_directory_data_store 屬性設定為所需的資料存放區（例如 blob 儲存體）。 根據預設, 步驟會使用 Azure 檔案儲存體作為備份資料存放區, 而當同時執行大量步驟時, 可能會遇到節流問題。

### <a name="azure-portal"></a>Azure 入口網站

+ **新功能**
  + 報表的新拖放資料表編輯器體驗。 使用者可以將資料行從適當的位置拖曳至資料表區域, 其中會顯示資料表的預覽。 可以重新排列資料行。
  + 新增記錄檔檢視器
  + [活動] 索引標籤中的實驗執行、計算、模型、映射和部署的連結

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning 資料準備 SDK v 1.0.15

+ **新功能**
  + 資料準備現在支援從資料流程寫入檔案資料流程。 也提供操作檔案資料流程名稱的功能, 以建立新的檔案名。
    + 操作指南:[使用檔案資料流程筆記本](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Bug 修正和改善**
  + 改善大型資料集上的 t 摘要效能。
  + 資料準備現在支援從資料路徑讀取資料。
  + 一個熱編碼現在適用于布林值和數值資料行。
  + 其他錯誤修正。

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>適用於 Python 的 Azure Machine Learning SDK v1.0.15

+ **新功能**
  + Azure Machine Learning 管線已新增」已 azurebatchstep ([筆記本](https://aka.ms/pl-azbatch))、HyperDriveStep (筆記本) 和以時間為基礎的排程功能 ([筆記本](https://aka.ms/pl-schedule))。
  +  DataTranferStep 已更新成可與 Azure SQL Server 和「適用於 PostgreSQL 的 Azure 資料庫」([otebook](https://aka.ms/pl-data-trans)) 搭配運作。

+ **變更**
  + 即將淘汰 `PublishedPipeline.get_published_pipeline` 而改用 `PublishedPipeline.get`。
  + 即將淘汰 `Schedule.get_schedule` 而改用 `Schedule.get`。

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning 資料準備 SDK v1.0.12

+ **新功能**
  + 「資料準備」現在支援使用「資料存放區」從 Azure SQL 資料庫讀取資料。
 
+ **變更**
  + 改善大型資料上某些作業的記憶體效能。
  + `read_pandas_dataframe()` 現在會要求必須指定 `temp_folder`。
  + `ColumnProfile` 上的 `name` 屬性即將淘汰，請改用 `column_name`。

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK for Python v1.0.10

+ **變更**： 
  + Azure ML SDK 不再具有 azure-cli 套件作為相依性。 明確的說，azure-cli-core 與 azure-cli-profile 相依性已從 azureml-core 移除。 以下是使用者影響的變更：
    + 如果您要執行「az login」, 然後再使用 azureml-sdk, SDK 會再一次執行瀏覽器或裝置程式碼記錄檔。 它不會使用由「az login」建立的任何認證狀態。
    + 對於 Azure CLI 驗證，例如使用「az login」，請使用 _azureml.core.authentication.AzureCliAuthentication_ 類別。 對於 Azure CLI 驗證，請在您已安裝 azureml-sdk 的 Python 環境中執行 _pip install azure-cli_。
    + 如果您正在使用服務主體執行「az login」以進行自動化，建議您使用 _azureml.core.authentication.ServicePrincipalAuthentication_ 類別，因為 azureml-sdk 不會使用 azure CLI 建立的認證狀態。 

+ **錯誤修正**：此版本主要包含次要錯誤修正

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **錯誤修正**
  + 改善取得資料設定檔的效能。
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

+ **新增**
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
+ 新的[自動化機器學習圖表](how-to-understand-automated-ml.md)，適用於分類模型 (升力、增益、校正、具備模型說明能力的特徵重要性圖表) 和迴歸模型 (殘差，以及具備模型說明能力的特徵重要性圖表)。 
+ 您可以在 Azure 入口網站中檢視管線。




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK for Python v0.1.80

+ **重大變更** 
  * azureml.train.widgets 命名空間已移至 azureml.widgets。
  * azureml.core.compute.AmlCompute 取代了下列類別 - azureml.core.compute.BatchAICompute 和 azureml.core.compute.DSVMCompute。 第二個類別將會在後續版本中移除。 AmlCompute 類別現在有更簡易的定義，只需 vm_size 和 max_nodes 即可，並且會在提交作業時自動將叢集規模調整為 0 到 max_nodes。 我們已對[範例 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) 更新此資訊，這應該能提供使用範例給您參考。 希望您喜歡此簡化功能和後續版本中的許多有趣功能！

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
  * 用於已發佈管線的新 [管線] 索引標籤。
  * 已增加附加現有的 HDInsight 叢集作為計算目標的支援。

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK for Python v0.1.74

+ **重大變更** 
  * *Workspace.compute_targets、datastores、experiments、images、models 以及 *webservices* 是屬性而非方法。 例如，將 Workspace.compute_targets() 取代為 Workspace.compute_targets。
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
