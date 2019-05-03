---
title: 比較 （&) 重現一段時間，與快照集的資料
titleSuffix: Azure Machine Learning service
description: 了解如何比較經過一段時間的資料，並確保資料集的快照集的重現性
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.date: 05/02/2019
ms.openlocfilehash: ed10cb259802321769605bc0399a610131ddb174
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029141"
---
# <a name="compare-data-and-ensure-reproducibility-with-snapshots-preview"></a>比較資料，並確保快照集 （預覽） 的重現性

在本文中，您學會如何建立和管理的快照集您[Azure Machine Learning 資料集](how-to-create-register-datasets.md)（資料集） 讓您可以擷取，或比較經過一段時間的資料。 資料集，讓您更輕鬆地存取和使用您在雲端的各種案例中的資料。 

**資料集的快照集**在建立時儲存的資料設定檔 （摘要統計資料）。 您可以選擇也適用於重現性在快照中儲存資料的複本。 

>[!Important]
> 快照集產生的儲存成本。 在快照中儲存一份資料需要更多的儲存體。 使用[ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-)不再需要。

## <a name="when-to-use-snapshots"></a>使用快照集的時機

有三個主要的用途，快照集：

+ **模型驗證**:比較不同的快照集的訓練執行之間，或針對實際執行資料的資料設定檔。 

+ **模型重現**:藉由呼叫在定型期間包含資料的快照集，重新產生您的結果。 

+ **追蹤資料一段時間**:請參閱 如何將資料集演變的[比較設定檔](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#compare-profiles-rhs-dataset-snapshot--include-columns-none--exclude-columns-none--histogram-compare-method--histogramcomparemethod-wasserstein--0--)
  
## <a name="prerequisites"></a>必要條件

若要建立資料集的快照集，您需要已註冊的 Azure Machine Learning 資料集。 如果您沒有，請參閱[建立和註冊的資料集](how-to-create-register-datasets.md)。

## <a name="create-dataset-snapshots"></a>建立資料集的快照集

若要建立資料集的快照集，請使用[ `dataset.create_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?#create-snapshot-snapshot-name--compute-target-none--create-data-snapshot-false--target-datastore-none-)從 Azure 機器學習服務 SDK。 

根據預設，快照集會儲存資料的最新的設定檔 （摘要統計資料）[資料集定義](how-to-manage-dataset-definitions.md)套用。 資料集定義包含任何資料所定義的轉換步驟的記錄。 它是讓您使用的資料準備可重現的好方法。

（選擇性） 您也可以包含一份資料快照集加入`create_data_snapshot = True`。  這項資料可用於重現性。 

這個範例會使用[crime 資料取樣](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv)和資料集稱為`dataset_crime`使用本文中，建立[「 建立和註冊資料集 」](how-to-create-register-datasets.md)。

```Python
from azureml.core.dataset import Workspace, Dataset
from azureml.data.dataset_snapshot import DatasetSnapshot
import datetime

# get existing workspace
workspace = Workspace.from_config()

# get existing, named dataset:
dataset = workspace.Dataset['dataset_crime']

# assign name to snapshot
snapshot_name = 'snapshot_' + datetime.datetime.today().strftime('%Y%m%d%H%M%S')

# create snapshot to store profile from a remote environment
# and include copy of data
snapshot = dataset.create_snapshot(snapshot_name = snapshot_name,
                                   compute_target = remote_compute_target,
                                   create_data_snapshot = True)
```
 

因為會以非同步方式建立快照集，使用[ `wait_for_completion()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#wait-for-completion-show-output-true--status-update-frequency-10-)監視的程序的方法。

```python
# monitor process every 10 seconds
snapshot.wait_for_completion(show_output=True, status_update_frequency=10)

# return snapshot just created
dataset.get_snapshot(snapshot_name)
```

輸出：

```
Action status: Running
Action status: Running
Action status: Completed


DatasetSnapshot(Name: snapshot_20190411165420,
Dataset Id: 66cd0830-2f72-41e5-b677-d2d953f54821,
Workspace: "your_workspace_name",
Dataset definition version: 1,
Snapshot created date: 2019-05-11 17:24:00+00:00)
```

使用[ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-)不再需要。

## <a name="find-snapshots"></a>找到快照集

若要擷取現有的快照集，請使用[ `get_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-snapshot-snapshot-name-)。

若要取得您指定的資料集的已儲存的快照集的清單，請使用[ `get_all_snapshots()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-all-snapshots--)。 

```Python
# Get named snapshot for this dataset
dataset.get_snapshot(snapshot_name)

# Get all snapshots for this dataset
dataset.get_all_snapshots()
```

## <a name="get-data-and-profiles-from-snapshots"></a>從快照集取得資料和設定檔

每個快照集產生資料集，其中包含摘要統計資料，並可讓您選擇儲存一份您的資料設定的檔。

### <a name="get-the-data-profile"></a>取得資料設定檔

使用[ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-)來存取您的資料設定檔。  要比較訓練課程和實際執行資料，例如，您可以使用此設定檔。

```Python
snapshot.get_profile()
```

||類型|Min|max|Count|遺漏計數|未遺漏計數|遺漏百分比|錯誤計數|空白計數|0.1% 分位數|1% 分位數|5% 分位數|25% 分位數|50% 分位數|75% 分位數|95% 分位數|99% 分位數|99.9% 分位數|平均值|標準差|Variance|偏度|峰度
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e+07|1.05351e+07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
案例編號|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
date|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
區塊|FieldType.STRING|004XX S KILBOURN AVE|113XX S PRAIRIE AVE|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
主要類型|FieldType.STRING|詐騙的作法|遭竊|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
描述|FieldType.STRING|假的核取|透過 $500|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
位置描述|FieldType.STRING||學校、 公用，建置|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
阻止|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
國內|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
活動訊號|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
區域|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
Ward|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
社群區域|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379


### <a name="get-the-data-from-the-snapshot"></a>從快照集取得資料

若要取得的資料集的快照中儲存的資料複本，產生 pandas 資料框架具有[ `to_pandas_dataframe()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#to-pandas-dataframe--)方法。

如果在快照集建立期間未要求資料的複本，這個方法將會失敗。 

```Python
snapshot.to_pandas_dataframe().head(3)
```

||ID|案例編號|date|區塊|IUCR|主要類型|描述|位置描述|阻止|國內|...|Ward|社群區域|FBI 程式碼|X 座標|Y 座標|Year|更新日期|緯度|經度|位置
-|--|-----------|----|-----|----|------------|-----------|--------------------|------|--------|---|----|--------------|--------|------------|------------|----|----------|--------|---------|--------
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|1153|詐騙的作法|透過美金 300 元的金融身分遭竊|OTHER|False|False|...|9|50|11|1183356.0|1831503.0|2016|2016-05-11 15:48:00|41.692834|-87.604319|(41.692833841, -87.60431945)
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|890|遭竊|從建立|居住地|False|False|...|21|71|6|1166776.0|1850053.0|2016|2016-05-12 15:48:00|41.744107|-87.664494|(41.744106973, -87.664494285)
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S 沙加緬度 AVE|1154|詐騙的作法|金融身分遭竊美金 300 元歲以下|居住地|False|False|...|19|74|11|NaN|NaN|2016|2016-05-12 15:50:00|NaN|NaN|


## <a name="next-steps"></a>後續步驟

* 請參閱 SDK[概觀](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)設計模式和使用方式範例。

* 如需使用資料集的快照集的範例，請參閱 <<c0> [ 範例 notebook](https://aka.ms/dataset-tutorial)。
