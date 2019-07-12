---
title: 偵測到 AKS 部署上的資料漂移 （預覽）
titleSuffix: Azure Machine Learning service
description: 偵測資料漂移，Azure Kubernetes Service 上的部署 Azure Machine Learning 服務中的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 07/08/2019
ms.openlocfilehash: 3b8152bde8b7e44dde1b0b9c82216333778f83da
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806014"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>偵測模型部署到 Azure Kubernetes Service (AKS) 上的資料漂移 （預覽）

在本文中，您了解如何監視資料漂移，訓練資料集和推斷之間的已部署的模型資料。 在機器學習服務的內容中，定型的機器學習服務模型可能會遇到降級的預測效能，因為漂移。 使用 Azure Machine Learning 服務時，您可以監視資料漂移，和服務可以傳送電子郵件警示給您時偵測到漂移時。

## <a name="what-is-data-drift"></a>什麼是資料漂移？

不同於用來定型模型的資料提供給模型，以在生產環境中的資料時，會發生資料漂移。 它是其中一個主要原因所在模型的精確度隨著時間逐漸降低，因此監視資料漂移有助於偵測模型的效能問題。 

## <a name="what-can-i-monitor"></a>我可以監視什麼？

使用 Azure Machine Learning 服務時，您可以監視部署在 AKS 上模型的輸入，並比較這項資料給模型的訓練資料集。 推斷資料是定期[快照集，並分析](how-to-explore-prepare-data.md)，然後針對基準資料集，以產生資料漂移分析計算的： 

+ 測量資料漂移，稱為漂移係數的大小。
+ 量值資料漂移貢獻的功能，告知哪些功能造成資料漂移。
+ 量值距離度量。 目前 Wasserstein 和能源距離會計算。
+ 量值散發的功能。 目前核心密度估計和長條圖。
+ 傳送電子郵件漂移的警示資料。

> [!Note]
> 此服務為 （預覽） 和受限制的組態選項。 請參閱我們[API 文件](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py)並[版本資訊](azure-machine-learning-release-notes.md)如需詳細資訊和更新。 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>在 Azure Machine Learning 服務中監視資料漂移的方式

使用 Azure Machine Learning 服務，透過資料集或部署監視資料漂移。 若要監視資料漂移，被指定的基準資料集-通常為訓練資料集模型。 通常模型的輸入的資料蒐集從部署-測試第二個資料集-，原因是對基礎資料集。 這兩個資料集[分析](how-to-explore-prepare-data.md#explore-with-summary-statistics)和輸入資料漂移監視服務。 機器學習模型訓練，會偵測到兩個資料集之間的差異。 模型的效能會轉換成漂移決定係數，測量兩個資料集之間漂移的大小。 使用[模型 interpretability](machine-learning-interpretability-explainability.md)，計算參與漂移係數的功能。 從資料集的設定檔，會追蹤每項功能的統計資訊。 

## <a name="prerequisites"></a>先決條件

- Azure 訂用帳戶。 如果您沒有帳戶，請在您開始前建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

- 已安裝 Azure Machine Learning services 工作區與適用於 Python 的 Azure Machine Learning SDK。 依照[建立 Azure Machine Learning 服務工作區](setup-create-workspace.md#sdk)中的指示執行下列工作：

    - 建立 Miniconda 環境
    - 安裝適用於 Python 的 Azure Machine Learning SDK
    - 建立工作區
    - 撰寫工作區的組態檔 (aml_config/config.json)。

- 安裝資料漂移 SDK 使用下列命令：

    ```shell
    pip install azureml-contrib-datadrift
    ```

- 建立[資料集](how-to-create-register-datasets.md)從您的模型訓練資料。

- 指定定型資料集時[註冊](concept-model-management-and-deployment.md)模型。 下列範例示範如何使用`datasets`參數來指定定型資料集：

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [啟用模型資料收集](how-to-enable-data-collection.md)AKS 部署的模型中收集資料，並確認資料收集在`modeldata`blob 容器。

## <a name="configure-data-drift"></a>設定資料漂移
若要設定您的實驗的資料漂移，匯入相依性，如下列的 Python 範例所示。 

此範例示範如何設定[ `DataDriftDetector` ](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py)物件：

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>提交 DataDriftDetector 執行

具有`DataDriftDetector`設定的物件，您可以提交[執行的資料漂移](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-)模型在特定日期。 在執行，藉此啟用 DataDriftDetector 警示`drift_threshold`參數。 如果[datadrift_coefficient](#metrics)高於指定`drift_threshold`，會傳送一封電子郵件。

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>以視覺化方式檢視漂移計量

<a name="metrics"></a>

提交您 DataDriftDetector 執行之後，您就可以看到資料漂移工作的每個執行反覆項目中儲存的漂移度量：


|計量|描述|
--|--|
wasserstein_distance|定義的一維的數值分佈的統計距離。|
energy_distance|定義的一維的數值分佈的統計距離。|
datadrift_coefficient|計算方式十分類似，為 Matthew 的相互關聯係數，但此輸出為實數，範圍從 0 到 1。 在內容中漂移，0 表示沒有漂移而 1 表示最大漂移。|
datadrift_contribution|功能提供給漂移的功能重要性。|

有多種方式可以檢視漂移計量：

* 使用 Jupyter 小工具。
* 使用[ `get_metrics()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-)函式，任何`datadrift`執行物件。
* 在您的模型在 Azure 入口網站中檢視度量

下列 Python 範例示範如何繪製相關資料漂移度量。 您可以使用傳回的度量，以建置自訂的視覺效果：

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![請參閱資料漂移偵測到的 Azure Machine Learning](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>排程資料漂移掃描 

當您啟用資料漂移偵測時，DataDriftDetector 是執行指定的排程頻率。 如果 datadrift_coefficient 達到指定`drift_threshold`，每一次排程執行傳送電子郵件。 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

在 Azure 入口網站中的 [模型] 詳細資料頁面上，可以看到資料漂移偵測器的組態。

![Azure 入口網站資料漂移組態](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>在 Azure ML 工作區 UI 中檢視結果

若要在 Azure ML 工作區 UI 中檢視結果，請瀏覽至 [模型] 頁面。 在模型的 [詳細資料] 索引標籤會顯示資料漂移組態。 現在可使用視覺化資料漂移計量 ' 資料漂移 （預覽） 索引標籤。 

![Azure 入口網站資料漂移](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="receiving-drift-alerts"></a>接收漂移的警示

設定警示臨界值，並提供電子郵件地址的漂移係數[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)漂移係數高於閾值時，會自動傳送電子郵件警示。 

為了讓您設定自訂警示和動作，所有資料漂移度量資訊會都儲存在[Application Insights](how-to-enable-app-insights.md)以及 Azure Machine Learning 服務工作區所建立的資源。 您可以依照電子郵件警示中的連結至 Application Insights 查詢。

![資料漂移電子郵件警示](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>重新訓練模型之後漂移

當資料漂移對已部署模型的效能受到負面的影響時，就可以重新定型模型。 下列[ `diff()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#diff-rhs-dataset--compute-target-none--columns-none-
)方法可讓您初始的有意義的舊和新的訓練資料集之間變更的項目。 

```python
from azureml.core import Dataset

old_training_dataset.diff(new_training_dataset)
```

根據先前的程式碼的輸出，您可能想要重新訓練模型。 若要這樣做，請繼續進行下列步驟。

* 調查收集的資料，並準備資料以訓練新的模型。
* 請將它分割為訓練/測試資料。
* 定型模型一次使用新的資料。
* 評估新產生之模型的效能。
* 如果效能優於的生產模型，請部署新的模型。

## <a name="next-steps"></a>後續步驟

* 使用資料漂移的完整範例，請參閱 < [Azure ML 資料漂移 notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb)。 此 Jupyter Notebook 會示範如何使用[Azure 開啟的資料集](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets)來定型模型來預測天氣，將它部署到 AKS，以及監視資料漂移。 

* 我們非常感謝您的問題、 意見或建議資料漂移移向一般可用性。 使用下方的產品意見反應按鈕 ！ 
