---
title: 偵測到 AKS 部署上的資料漂移 （預覽）
titleSuffix: Azure Machine Learning service
description: 了解如何偵測資料漂移，Azure Kubernetes Service 上的部署 Azure Machine Learning 服務中的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 06/20/2019
ms.openlocfilehash: c446c8236ca64948f0bb6a8354a83579cc6ff24c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443946"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service"></a>偵測資料漂移模型部署到 Azure Kubernetes Service （預覽）
在本文中，您了解如何監視資料漂移，訓練資料集和推斷之間的已部署的模型資料。 

## <a name="what-is-data-drift"></a>什麼是資料漂移？

資料漂移，也稱為概念漂移，是其中一個主要原因模型精確度會隨著時間的降低。 它會提供給模型，以在生產環境中的資料是用來定型模型的資料不同。 Azure Machine Learning 服務可以監視資料漂移，並在偵測到漂移時，服務可以傳送電子郵件警示給您。  

> [!Note]
> 此服務為 （預覽） 和受限制的組態選項。 請參閱我們[API 文件](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py)並[版本資訊](azure-machine-learning-release-notes.md)如需詳細資訊和更新。 

## <a name="what-can-i-monitor"></a>我可以監視什麼？
使用 Azure Machine Learning 服務時，您可以監視部署在 AKS 上模型的輸入，並比較這項資料給模型的訓練資料集。 推斷資料是定期[快照集，並分析](how-to-explore-prepare-data.md)，然後針對基準資料集，以產生資料漂移分析計算的： 

+ 測量資料漂移，稱為漂移係數的大小。
+ 量值資料漂移貢獻的功能，告知哪些功能造成資料漂移。
+ 量值距離度量。 目前 Wasserstein 和能源距離會計算。
+ 量值散發的功能。 目前核心密度估計和長條圖。
+ 傳送電子郵件漂移的警示資料。

如需如何計算這些度量的詳細資訊，請參閱[資料漂移概念](concept-data-drift.md)文章。

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

- 已安裝 Azure Machine Learning services 工作區與適用於 Python 的 Azure Machine Learning SDK。 了解如何使用[如何設定開發環境](how-to-configure-environment.md)文件來取得這些必要條件。

- [設定您的環境](how-to-configure-environment.md)，然後再安裝資料漂移 SDK 使用下列命令：

    ```
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

## <a name="import-dependencies"></a>匯入相依性 
本指南中使用的匯入相依性：

```python
# Azure ML service packages 
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration
``` 

## <a name="configure-data-drift"></a>設定資料漂移 

下列 Python 範例示範如何設定`DataDriftDetector`物件：

```python
# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

如需詳細資訊，請參閱`[DataDrift](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py)`類別參考文件。

## <a name="submit-a-datadriftdetector-run"></a>提交 DataDriftDetector 執行

具有`DataDriftDetector`設定的物件，您可以提交[執行的資料漂移](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-)模型在特定日期。 

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

下列 Python 範例示範如何繪製相關資料漂移度量。 您可以使用傳回的度量，以建置自訂的視覺效果：

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![請參閱資料漂移偵測到的 Azure Machine Learning](media/how-to-monitor-data-drift/drift_show.png)

如需計算的度量的詳細資訊，請參閱[資料漂移概念](concept-data-drift.md)文章。

## <a name="schedule-data-drift-scans"></a>排程資料漂移掃描 

當您啟用資料漂移偵測時，DataDriftDetector 是執行指定的排程頻率。 如果漂移係數大於給定的臨界值時，會傳送一封電子郵件。 

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

設定警示臨界值，並提供電子郵件地址的漂移係數[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)漂移係數高於閾值時，會自動傳送電子郵件警示。 為了讓您設定自訂警示和動作，所有資料漂移度量資訊會都儲存在 Azure Machine Learning 服務工作區以及建立的 Application Insights 資源。 您可以依照電子郵件警示中的連結至 Application Insights 查詢。

![資料漂移電子郵件警示](media/how-to-monitor-data-drift/drift_email.png)

## <a name="next-steps"></a>後續步驟

* 使用資料漂移的完整範例，請參閱 < [Azure ML 資料漂移 notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb)。 此 Jupyter Notebook 會示範如何使用[Azure 開啟的資料集](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets)來定型模型來預測天氣，將它部署到 AKS，以及監視資料漂移。 

* 我們非常感謝您的問題、 意見或建議資料漂移移向一般可用性。 使用下方的產品意見反應按鈕 ！ 
