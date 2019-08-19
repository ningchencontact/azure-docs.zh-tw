---
title: 探索和轉換資料 (Dataset 類別)
titleSuffix: Azure Machine Learning service
description: 使用摘要統計資料並透過資料清理、轉換和特徵工程來準備資料, 來探索資料
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: dbdd088e5a78a4f78eec27b5ee74856c6aecc209
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847911"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>使用 Dataset 類別探索和準備資料 (預覽)

瞭解如何使用[AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)中的 azureml 資料集封裝來探索和準備資料。 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)類別 (預覽) 可讓您藉由提供如取樣、摘要統計資料和智慧型轉換等功能, 來探索並準備您的資料。 轉換步驟會儲存在[資料集定義](how-to-manage-dataset-definitions.md)中, 而且能夠以高擴充性的方式處理不同架構的多個大型檔案。

> [!Important]
> 某些資料集類別 (預覽) 在[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)套件 (GA) 上具有相依性。 雖然您可以使用 Batchai[資料準備](how-to-transform-data.md)函式直接完成轉換函式, 但如果您要建立新的解決方案, 我們會建議本文中所述的資料集封裝包裝函式。 Azure Machine Learning 資料集 (預覽) 可讓您不僅轉換資料, 還能將[快照集資料](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py)和儲存為版本的資料[集定義](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)。 資料集是下一版的資料準備 SDK, 提供擴充的功能來管理 AI 解決方案中的資料集。

## <a name="prerequisites"></a>先決條件

若要探索並準備您的資料, 您將需要:

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* Azure Machine Learning 服務工作區。 請參閱[建立 Azure Machine Learning 服務工作區](how-to-manage-workspace.md)。

* 適用于 Python 的 Azure Machine Learning SDK (1.0.21 版或更新版本), 其中包括 azureml 資料集封裝。 若要安裝或更新為最新版本的 SDK, 請參閱[安裝或更新 sdk](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* Azure Machine Learning 資料準備 SDK。 若要安裝或更新為最新版本, 請參閱[安裝或更新資料準備 SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py)。

* 下載範例檔案, 以遵循範例:[犯罪](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv)和[city. json](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json)。

## <a name="sampling"></a>取樣

取得您資料的範例, 以初步瞭解您的資料結構和內容。 目前, Dataset 類別中[`sample()`](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-)的方法支援前 N 個、簡單的隨機和階層式取樣策略。

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>前 N 個範例

針對前 N 個取樣, 您的資料集的前 n 筆記錄是您的範例。 如果您只是想要瞭解資料記錄的外觀, 或查看資料中有哪些欄位, 這會很有説明。

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||id|案例編號|Date|封鎖|IUCR|主要類型|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|欺騙實務|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD 平均|890|遭竊|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO 平均|1154|欺騙實務|...
3|10519591|HZ261534|4/15/2016 9:00|113XX S 對勁平均|1120|欺騙實務|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE 平均|890|遭竊|...

### <a name="simple-random-sample"></a>簡單的隨機範例

在簡單的隨機取樣中, 資料擴展的每個成員都有相同的機會選取為範例的一部分。 `simple_random`在範例策略中, 會根據指定的機率來選取資料集中的記錄, 並傳回修改過的資料集。 種子參數是選擇性的。

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||id|案例編號|Date|封鎖|IUCR|主要類型|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD 平均|890|遭竊|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO 平均|1154|欺騙實務|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE 平均|890|遭竊|...

### <a name="stratified-sample"></a>分層範例

分層範例可確保擴展中的特定群組會在範例中表示。 在範例策略中, 填入會根據相似程度分割成分層或子群組, 並根據`fractions`參數所指示的分層權數, 從每個分層隨機選取記錄。 `stratified`

在下列範例中, 我們會依指定的資料行將每一筆記錄分組, 並根據中`fractions`的分層 X 權數資訊來包含記錄。 如果未指定分層或無法將記錄分組, 則取樣的預設權數為0。

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||id|案例編號|Date|封鎖|IUCR|主要類型|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD 平均|890|遭竊|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE 平均|890|遭竊|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN 平均|810|遭竊|...

## <a name="explore-with-summary-statistics"></a>探索摘要統計資料

 使用[`get_profile()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-)方法偵測異常、遺漏值或錯誤計數。 此函式會取得資料的設定檔和摘要統計資料, 進而協助判斷所需的資料準備作業。

```Python
dataset.get_profile()
```

||Type|Min|max|Count|遺漏計數|未遺漏計數|遺漏百分比|錯誤計數|空白計數|0.1% 分位數|1% 分位數|5% 分位數|25% 分位數|50% 分位數|75% 分位數|95% 分位數|99% 分位數|99.9% 分位數|平均值|標準差|Variance|偏度|峰度
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType.INTEGER|1.04986e+07|1.05351e+07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
案例編號|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Date|FieldType.DATE|2016-04-04 23:56:00 + 00:00|2016-04-15 17:00:00 + 00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
封鎖|FieldType.STRING|004XX S KILBOURN 平均|113XX S 對勁平均|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
主要類型|FieldType.STRING|欺騙實務|遭竊|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
描述|FieldType.STRING|假檢查|超過 $500|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
位置描述|FieldType.STRING||學校, 公用, 大樓|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
阻止|FieldType.BOOLEAN|偽|偽|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
國內|FieldType.BOOLEAN|偽|偽|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
節拍|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
區域|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
Ward|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
社群區域|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379
FBI 程式碼|FieldType.INTEGER|6|11|10.0|0.0|10.0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
X 座標|FieldType.INTEGER|1.16309e+06|1.18336e+06|10.0|7.0|3.0|0.7|0.0|0.0|1.16309e+06|1.16309e+06|1.16309e+06|1.16401e+06|1.16678e+06|1.17921e+06|1.18336e+06|1.18336e+06|1.18336e+06|1.17108e+06|10793.5|1.165e+08|0.335126|-2.33333
Y 座標|FieldType.INTEGER|1.8315e+06|1.908e+06|10.0|7.0|3.0|0.7|0.0|0.0|1.8315e+06|1.8315e+06|1.8315e+06|1.83614e+06|1.85005e+06|1.89352e+06|1.908e+06|1.908e+06|1.908e+06|1.86319e+06|39905.2|1.59243e+09|0.293465|-2.33333
年份|FieldType.INTEGER|2016|2016|10.0|0.0|10.0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
更新日期|FieldType.DATE|2016-05-11 15:48:00 + 00:00|2016-05-27 15:45:00 + 00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
緯度|FieldType.DECIMAL|41.6928|41.9032|10.0|7.0|3.0|0.7|0.0|0.0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
經度|FieldType.DECIMAL|-87.6764|-87.6043|10.0|7.0|3.0|0.7|0.0|0.0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
Location|FieldType.STRING||(41.903206037、-87.676361925)|10.0|0.0|10.0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>插補遺漏值

在資料集、null 值、NaN 的和不包含任何內容的值中, 都會被視為遺漏值。 這些可能會影響機器學習模型的效能, 或導致無效結論。 插補是用來處理遺漏值的常見方法, 當您的遺漏值記錄的百分比很高時, 您無法直接刪除, 這會很有用。

從上一節產生的資料集設定檔中, 我們`Latitude`看到`Longitude`和資料行的遺漏值百分比偏高。 在此範例中, 我們會計算這兩個數據行的平均值和插補遺漏值。

首先, 使用[`get_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-)來取得[`keep_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow)資料集的最新定義, 並向下削減資料, 因此我們只會看到我們想要處理的資料行。

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||id|阻止| 緯度|經度|
-|---------|-----|---------|----------|
|0|10498554|偽|41.692834|-87.604319|
|1|10516598|偽| 41.744107 |-87.664494|
|2|10519196|偽| NaN|NaN|

接下來, [`summarize()`](/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-ml-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow--summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow)使用`MEAN`函數來檢查 [緯度] 資料行的值。 此函式接受 `group_by_columns` 參數中的資料行陣列來指定彙總層級。 參數會接受函式, 此函式會指定目前的資料行名稱、新的匯出功能變數名稱, 以及要執行的`SummaryFunction`。 `SummaryColumnsValue` `summary_columns`

```Python
lat_mean = ds_def.summarize(group_by_columns = ['Arrest'],
                            summary_columns = [dprep.SummaryColumnsValue(column_id = 'Latitude',
                                                                         summary_column_name = 'Latitude_MEAN',
                                                                         summary_function = dprep.SummaryFunction.MEAN)])
lat_mean = lat_mean.filter(lat_mean['Arrest'] == False)
lat_mean.head(1)
```

||阻止|Latitude_MEAN|
--|-----|--------|
|0|偽|41.780049|

確認要插補的值之後, 請使用[`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py)來學習以計算`MIN`的、 `MAX` `MEAN`值或`CUSTOM`值插補資料行的固定運算式。 未指定 `group_by_columns` 時，會使用各群組計算出的 `MIN`、`MAX` 和 `MEAN` 按群組插補遺漏值。

接受 column_id 字串, 並使用`ReplaceValueFunction`來指定插補類型。 [`ImputeColumnArguments`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) 如需遺漏經度值, 請插補以-87 為基礎的外部知識。

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

您可以`ImputeMissingValuesBuilder` [`Builders`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py)使用[類別`impute_missing_values()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder)函式, 將插補步驟連結在一起的物件中。 `impute_columns` 參數可接受 `ImputeColumnArguments` 物件的陣列。

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

呼叫函式以儲存插補步驟, 並使用[`to_dataflow()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow)將它們套用至資料流程物件。 [`learn()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none)

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

如下列輸出資料表所示, 遺漏的緯度是以`MEAN` `Arrest==False` group 的值插補, 而且遺漏的經度是以-87 插補。

||id|阻止|緯度|經度
-|---------|-----|---------|----------
0|10498554|偽|41.692834|-87.604319
1|10516598|偽|41.744107|-87.664494
2|10519196|偽|41.780049|-87.000000

使用更新資料集定義, [`update_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-)以保留執行的轉換步驟。

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||id|阻止|緯度|經度
-|---------|-----|---------|----------
0|10498554|偽|41.692834|-87.604319
1|10516598|偽|41.744107|-87.664494
2|10519196|偽|41.780049|-87.000000

## <a name="create-assertion-rules"></a>建立判斷提示規則

在清除和準備資料時, 我們所使用的資料通常只是生產環境所需的總數據子集。 因此, 我們在清理過程中所做的一些假設可能會變成 false。 例如, 在連續更新的資料集中, 原本只包含特定範圍內數位的資料行, 在稍後的執行中可能會包含更大範圍的值。 這些錯誤通常會導致管線中斷或資料錯誤。

資料集支援在資料上建立判斷提示, 這會在管線執行時進行評估。 這些判斷提示可讓我們驗證我們對資料的假設是否持續正確, 而不會適當地處理失敗。

例如, 如果您想要將資料`Latitude`集`Longitude`內的和值限制為[`assert_value()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow)特定的數值範圍, 此方法會確保一律會發生這種情況。

```Python
from azureml.dataprep import value
from azureml.core.dataset import Dataset

# get the latest definition of the Dataset
ds_def = dataset.get_definition()

# set assertion rules for `Latitude` and `Longitude` columns
ds_def = ds_def.assert_value('Latitude', (value <= 90) & (value >= -90), error_code='InvalidLatitude')
ds_def = ds_def.assert_value('Longitude', (value <= 180) & (value >= -87), error_code='InvalidLongitude')

ds_def.get_profile()
```

||Type|Min|max|Count|遺漏計數|未遺漏計數|遺漏百分比|錯誤計數|空白計數|0.1% 分位數|1% 分位數|5% 分位數|25% 分位數|50% 分位數|75% 分位數|95% 分位數|99% 分位數|99.9% 分位數|平均值|標準差|Variance|偏度|峰度
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType.INTEGER|1.04986e+07|1.05351e+07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
阻止|FieldType.BOOLEAN|偽|偽|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
緯度|FieldType.DECIMAL|41.6928|41.9032|10.0|0.0|10.0|0.0|0.0|0.0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1.05
經度|FieldType.INTEGER|-87|-87|10.0|0.0|10.0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

在設定檔中, 您會看到`Error Count`資料`Longitude`行的是3。 下列程式碼會篩選資料集、抓取錯誤, 並查看會導致判斷提示失敗的值。 從這裡調整您的程式碼, 並適當地清理您的資料。

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>依範例衍生資料行

資料集的更先進工具之一, 就是使用所需結果的範例衍生資料行的能力。 這可讓您為 SDK 提供一個範例, 因此它可以產生程式碼來達成預定的轉換。

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||id|案例編號|Date|封鎖|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD 平均|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO 平均|...

假設您需要將日期和時間格式轉換為 ' 2016-04-04 10PM-12AM-上午 12 '。 在 [ `derive_column_by_example()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow)引數，提供您想要的輸出中的範例`example_data`參數，格式如下： *(所需的輸出中的 (原始輸出）* 。

下列程式碼提供兩個所需輸出的範例: ("2016-04-04 23:56:00"、"2016-04-04 10PM-12AM-上午 12") 和 ("2016-04-15 17:00:00"、"2016-04-15 4PM-下午 6:)

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

在下表中, 請注意新的資料行 Date_Time_Range 包含指定格式的記錄。

||id|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM-12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 4PM-6PM
2|10519196|2016-04-15 10:00:00|2016-04-15 10AM-12PM

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>模糊群組

當您從不同來源收集資料時, 可能會遇到相同實體的拼寫、大小寫或縮寫的變化。 使用 SDK 的模糊群組或文字叢集化功能, 自動標準化並協調這些變體。

例如, 資料行`inspections.business.city`包含數種形式的城市名稱「三藩市」。

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|...|
-|-----|-------------------------|------------|--|---
0|16162|快速 Ezee 印度食物|3861 24 日 St|SF|...
1|67565|泰文面咖啡廳的王|1541 TARAVAL St|三藩市|...
2|67565|泰文面咖啡廳的王|1541 TARAVAL St|三藩市|...
3|68701|Grindz|832 Clement St|SF|...
4|69186|Premier 餐 & 活動, Inc。|1255 22 日 St|S.F.|...

讓我們使用[`fuzzy_group_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow)方法, 將具有自動偵測到之標準格式「三藩市」的資料行加入。 引數`source_column`和`new_column_name`是必要的。 您也可以選擇:

* 建立新的資料行`similarity_score_column_name`, 它會顯示每一對原始和標準值之間的相似性分數。

* `similarity_threshold`提供, 這是將值分組在一起的最低相似性分數。

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|快速 Ezee 印度食物|3861 24 日 St|SF|三藩市|0.814806|...
1|67565|泰文面咖啡廳的王|1541 TARAVAL St|三藩市|三藩市|1.000000|...
2|67565|泰文面咖啡廳的王|1541 TARAVAL St|三藩市|三藩市|1.000000|...
3|68701|Grindz|832 Clement St|SF|三藩市|0.814806|...
4|69186|Premier 餐 & 活動, Inc。|1255 22 日 St|S.F.|三藩市|0.814806|...

在產生的資料集定義中, 資料中代表「三藩市」的所有不同變化都會正規化為相同的字串「三藩市」。

使用`update_definition()`將這個模糊群組步驟儲存到最新的資料集定義。

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>後續步驟

* 如需回歸模型範例，請參閱自動化機器學習[教學課程](tutorial-auto-train-models.md)。

* 如需設計模式和使用方式範例, 請參閱 SDK[總覽](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。

* 如需使用資料集的範例, 請參閱[範例筆記本](https://aka.ms/dataset-tutorial)。
