---
title: 版本及管理 azureml 資料集的資料集定義
titleSuffix: Azure Machine Learning service
description: 了解如何更新資料集定義，以及管理定義的生命週期
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 4476bdd902f054683fa544dc44b548689f3a1881
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241871"
---
# <a name="version-and-manage-your-dataset-definitions"></a>版本及管理您的資料集定義

了解如何更新和管理 Azure Machine Learning 資料集 （預覽） 的資料集定義。

資料集定義是用來準備您的資料集，其中包含這兩個參考到您的來源資料執行的轉換步驟的配方。 一個資料集可以有許多定義，而且每個定義自己的生命週期。

下列案例中是使用資料集定義的範例：

1. 您建置機器學習服務會使用目前的資料集定義的管線。
1. 基礎資料變更;已加入新的屬性。
1. 您建立新的版本，新增額外的轉換，以處理新的屬性定義。

在此範例中，現有的管線會繼續使用原始的定義版本。 新的定義版本可以用於新的案例，例如定型模型，或建立管線。

## <a name="prerequisites"></a>必要條件

您需要有 Azure 訂用帳戶和工作區以註冊您的資料集，以管理資料集定義的生命週期。

在這份文件中的範例中使用的範例檔案會位於[ https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv ](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv)。

## <a name="create-and-update-definitions"></a>建立和更新定義

首先，我們會建立，並向您的工作區中的資料集。

```python
from azureml.core import Workspace, Datastore, Dataset

# change the configuration for workspace and Datastore
subscription_id = 'your subscription id'
resource_group = 'your resource group name'
workspace_name = 'your workspace name'
datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace(subscription_id, resource_group, workspace_name)

# get a Datastore that has already been created in the workspace
dstore = Datastore.get(workspace, datastore_name)

# create an in-memory Dataset from Datastore
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# register the Dataset with the workspace. if a Dataset with the same name already exists, retrieve it by turning `exist_ok = True`
dataset_name = 'crime dataset'
dataset = dataset.register(workspace = workspace, 
                 name = dataset_name, 
                 description = 'crime dataset for demo', 
                 tags = {'year':'2019', 'month':'Apr'},
                 exist_ok = True)
```

第一個資料集定義 (`version_id` = 1) 在建立資料集時所建立。 然後每次您更新您的資料集定義時，新的 version_id 將指派給最新的定義。

```python
# get the Dataset from the workspace by name
dataset = workspace.datasets['dataset_name']

# get the latest Dataset definition
ds_def = dataset.get_definition()

# update the Dataset definition to select only the columns I'm interested in
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])

# with this update, the latest definition for dataset now has `version_id = 2`
dataset = dataset.update_definition(ds_def, 'select useful column')

dataset = workspace.datasets['dataset_name']
dataset.head(5)
```
<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>阻止</th>
      <th>緯度</th>
      <th>經度</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>FALSE</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>FALSE</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

此定義更新不會清除先前的資料集定義。 您仍然可以存取，並使用先前的定義。

```python
# specify `version_id` to get a previous definition 
ds_def_old = dataset.get_definition(version_id = 1)
ds_def_old.head(5)
```
<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>案例編號</th>
      <th>Date</th>
      <th>區塊</th>
      <th>IUCR</th>
      <th>主要類型</th>
      <th>描述</th>
      <th>位置描述</th>
      <th>阻止</th>
      <th>國內</th>
      <th>...</th>
      <th>Ward</th>
      <th>社群區域</th>
      <th>FBI 程式碼</th>
      <th>X 座標</th>
      <th>Y 座標</th>
      <th>Year</th>
      <th>更新日期</th>
      <th>緯度</th>
      <th>經度</th>
      <th>位置</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>HZ239907</td>
      <td>4/4/2016 23:56</td>
      <td>007XX E 111TH ST</td>
      <td>1153</td>
      <td>詐騙的作法</td>
      <td>透過美金 300 元的金融身分遭竊</td>
      <td>OTHER</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>50</td>
      <td>11</td>
      <td>1183356</td>
      <td>1831503</td>
      <td>2016</td>
      <td>5/11/2016 15:48</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
      <td>(41.692833841, -87.60431945)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>HZ258664</td>
      <td>4/15/2016 17:00</td>
      <td>082XX S MARSHFIELD AVE</td>
      <td>890</td>
      <td>遭竊</td>
      <td>從建立</td>
      <td>居住地</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>21</td>
      <td>71</td>
      <td>6</td>
      <td>1166776</td>
      <td>1850053</td>
      <td>2016</td>
      <td>5/12/2016 15:48</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
      <td>(41.744106973, -87.664494285)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>HZ261252</td>
      <td>4/15/2016 10:00</td>
      <td>104XX S 沙加緬度 AVE</td>
      <td>1154</td>
      <td>詐騙的作法</td>
      <td>金融身分遭竊美金 300 元歲以下</td>
      <td>居住地</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>19</td>
      <td>74</td>
      <td>11</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/12/2016 15:50</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>HZ261534</td>
      <td>4/15/2016 9:00</td>
      <td>113XX S PRAIRIE AVE</td>
      <td>1120</td>
      <td>詐騙的作法</td>
      <td>偽造</td>
      <td>居住地</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>49</td>
      <td>10</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/13/2016 15:51</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>HZ277630</td>
      <td>4/15/2016 10:00</td>
      <td>055XX N KEDZIE AVE</td>
      <td>890</td>
      <td>遭竊</td>
      <td>從建立</td>
      <td>學校、 公用，建置</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>40</td>
      <td>13</td>
      <td>6</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/25/2016 15:59</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

誠如所見的結果，資料集定義的第一個版本仍可防止所有資料行的犯罪資料，不論更新版的更新。 例如，如果您有機器學習服務模型目前耗用的資料集的第一個版本，並使用`Date`從做為其中一個功能集的資料行，不會發生任何錯誤的最新的定義更新，其中保存唯一`ID`， `Arrest`， `Latitude`， `Longitude` crime 資料的資料行。

## <a name="how-to-access-dataset-definitions"></a>如何存取資料集定義

若要取得的所有定義的清單，請使用`get_definitions()`。 若要取得特定版本的定義，請使用`get_definition()`。 下列範例示範如何擷取一份所有定義，以及然後擷取第 1 版：

```python
# list all definitions for the dataset
dataset.get_definitions()
# get version ID 1
dataset.get_definition(version_id=1)
```

輸出`get_definitions()`類似下列的範例：

```text
{'2': VersionID: 2, State: active, Created: 2019-04-19 16:43:52.439890+00:00, Modified: 2019-04-19 16:43:52.439890+00:00, Notes: select useful column,
 '1': VersionID: 1, State: active, Created: 2019-04-19 16:39:14.112046+00:00, Modified: 2019-04-19 16:44:12.912663+00:00, Notes: None}
```

一旦您已擷取的定義，您可以使用它，您的機器學習模型或機器學習服務管線中。

## <a name="manage-lifecycle-of-dataset-definitions"></a>管理資料集定義生命週期

您可以獨立管理生命週期的每個資料集定義。 有三個階段的生命週期： 作用中，已被取代，或保存。

### <a name="active"></a>Active

建立新的資料集定義時，它是設為預設。 

### <a name="deprecate"></a>取代

資料集定義可以取代，不再建議使用，且可用來取代時。 在機器學習服務管線中使用已被取代的資料集定義時，傳回一則警告訊息，但不是會封鎖執行。

當取代資料集定義，指定資料集識別碼和資料集定義的版本識別碼，取代定義。 這項資訊會儲存，而且當您嘗試使用已被取代的資料集定義時，警告訊息中使用。

```python
# get the definition that you want to deprecate
ds_def = dataset.get_definition(version_id = 1)

# deprecate it by providing the information for the replacement definition, which is recommended to be used in machine learning scenarios
ds_def.deprecate(deprecate_by_dataset_id=dataset.id, deprecated_by_definition_version=2)
```

### <a name="archive"></a>封存

定義不應該用於任何原因 （例如基礎資料無法再使用） 時，可保存資料集定義。 在機器學習服務管線中使用封存的資料集定義時，將會封鎖執行，發生錯誤。

```python
# archive the definition with `version_id = 1`
ds_def = dataset.get_definition(version_id = 1)
ds_def.archive()
```

### <a name="reactivate"></a>重新啟動

您可以輕鬆地重新啟動任何已被取代或已封存的資料集定義。

```python
# reactivate Dataset definition with `version_id =1` which was archived in the previous step
ds_def = dataset.get_definition(version_id = 1)
ds_def.reactivate()
```

## <a name="next-steps"></a>後續步驟

如需有關使用資料集的詳細資訊，請參閱[建立及註冊 Azure Machine Learning 資料集](how-to-create-register-datasets.md)。

如需使用資料集的範例，請參閱 <<c0> [ 範例 notebook](https://aka.ms/dataset-tutorial)。
