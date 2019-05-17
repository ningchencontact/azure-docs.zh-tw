---
title: 探索及準備資料 （資料集類別）
titleSuffix: Azure Machine Learning service
description: 使用摘要統計資料探索資料並準備透過資料清理、 轉換和特徵工程設計的資料
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/19
ms.openlocfilehash: 70712605cc97670b625d32052bb79b4a666e4281
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2019
ms.locfileid: "65603147"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>探索及準備資料的資料集類別 （預覽）

了解如何探索及準備資料[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。 [資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)類別 （預覽） 可讓您探索及準備您的資料，例如提供函式： 取樣、 摘要統計資料和智慧型的轉換。 轉換步驟就會存入[資料集定義](how-to-manage-dataset-definitions.md)高度可調整的方式處理不同的結構描述的多個大型檔案的能力。

> [!Important]
> 某些資料集類別 （預覽） 有相依性[azureml dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)封裝 (GA)。 雖然轉換函式可以直接使用 GA'ed[資料準備函式](how-to-transform-data.md)，我們建議您在本文中所述，如果您要建立新方案的資料集封裝包裝函式。 Azure Machine Learning 資料集 （預覽） 可讓您不只將您的資料，但也[快照集資料](how-to-create-dataset-snapshots.md)並儲存[建立版本的資料集定義](how-to-manage-dataset-definitions.md)。 資料集是資料準備 SDK 中，供應項目擴充的功能管理 AI 解決方案中的資料集的下一個版本。

## <a name="prerequisites"></a>必要條件

若要探索及準備您的資料，您將需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* Azure Machine Learning 服務工作區。 請參閱[建立 Azure 機器學習服務工作區](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)。

* Azure 機器學習服務 SDK for Python (版本 1.0.21 或更新版本)。 若要安裝或更新至最新版的 sdk，請參閱[安裝或更新 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* Azure Machine Learning 資料準備 SDK。 若要安裝或更新為最新版本，請參閱[安裝或更新 Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install)。

* 下載範例檔案，若要依照範例： [crime.csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv)並[city.json](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json)。

## <a name="sampling"></a>取樣

需要您的資料，以取得初始了解您的資料架構和內容的範例。 在此階段中， [ `sample()` ](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-)從資料集類別的方法支援 Top N、 簡單隨機和 Stratified 取樣策略。

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>前 n 個範例

前 n 個取樣的資料集的前 n 筆記錄會是您的範例。 這是有幫助，如果只想要了解哪些您的資料記錄外觀或若要查看欄位會在您的資料等。

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||ID|案例編號|Date|封鎖|IUCR|主要類型|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|詐騙的作法|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|遭竊|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S 沙加緬度 AVE|1154|詐騙的作法|...
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE AVE|1120|詐騙的作法|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|遭竊|...

### <a name="simple-random-sample"></a>簡單的隨機取樣

在簡單隨機取樣，填入資料的每個成員已被選取做為範例一部分的機會。 在 `simple_random`取樣策略，從您的資料集的記錄會根據指定的可能性，並傳回已修改的資料集。 種子參數是選擇性的。

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||ID|案例編號|Date|封鎖|IUCR|主要類型|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|遭竊|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S 沙加緬度 AVE|1154|詐騙的作法|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|遭竊|...

### <a name="stratified-sample"></a>分層的取樣

分層的範例會確保在此範例中，會表示特定群組的母體擴展。 在 `stratified`取樣策略，母體分成分層或子群組，根據相似之處，並從根據所指定的分層加權的每個分層中隨機選取記錄`fractions`參數。

在下列範例中，我們分組所指定的資料行中的每一筆記錄，並包含該的記錄中的分層 X 權數資訊`fractions`。 如果未指定的分層，或記錄無法進行分組，範例的預設權數為 0。

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||ID|案例編號|Date|封鎖|IUCR|主要類型|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|遭竊|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|遭竊|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN AVE|810|遭竊|...

## <a name="explore-with-summary-statistics"></a>瀏覽之摘要統計資料

 偵測異常狀況，遺漏值，或使用的錯誤計數[ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-)方法。 此函式取得的設定檔，並摘要統計資料的資料，從而決定要套用的所需的資料準備作業。

```Python
dataset.get_profile()
```

||Type|Min|max|Count|遺漏計數|未遺漏計數|遺漏百分比|錯誤計數|空白計數|0.1% 分位數|1% 分位數|5% 分位數|25% 分位數|50% 分位數|75% 分位數|95% 分位數|99% 分位數|99.9% 分位數|平均值|標準差|Variance|偏度|峰度
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e+07|1.05351e+07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
案例編號|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Date|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
封鎖|FieldType.STRING|004XX S KILBOURN AVE|113XX S PRAIRIE AVE|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
主要類型|FieldType.STRING|詐騙的作法|遭竊|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
說明|FieldType.STRING|假的核取|透過 $500|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
位置描述|FieldType.STRING||學校、 公用，建置|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
阻止|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
國內|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
活動訊號|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
區域|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
Ward|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
社群區域|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379
FBI 程式碼|FieldType.INTEGER|6|11|10.0|0.0|10.0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
X 座標|FieldType.INTEGER|1.16309e+06|1.18336e+06|10.0|7.0|3.0|0.7|0.0|0.0|1.16309e+06|1.16309e+06|1.16309e+06|1.16401e+06|1.16678e+06|1.17921e+06|1.18336e+06|1.18336e+06|1.18336e+06|1.17108e+06|10793.5|1.165e+08|0.335126|-2.33333
Y 座標|FieldType.INTEGER|1.8315e+06|1.908e+06|10.0|7.0|3.0|0.7|0.0|0.0|1.8315e+06|1.8315e+06|1.8315e+06|1.83614e+06|1.85005e+06|1.89352e+06|1.908e+06|1.908e+06|1.908e+06|1.86319e+06|39905.2|1.59243e+09|0.293465|-2.33333
Year|FieldType.INTEGER|2016|2016|10.0|0.0|10.0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
更新日期|FieldType.DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
緯度|FieldType.DECIMAL|41.6928|41.9032|10.0|7.0|3.0|0.7|0.0|0.0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
經度|FieldType.DECIMAL|-87.6764|-87.6043|10.0|7.0|3.0|0.7|0.0|0.0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
位置|FieldType.STRING||(41.903206037, -87.676361925)|10.0|0.0|10.0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>插補遺漏值

在資料集中，null 值 NaN 的包含沒有內容的值視為遺漏的值。 這些可以影響您的機器學習模型的效能，或指向無效的結論。 插補是一個常見的方法，來處理遺漏值，而且適用於您有高百分比的遺漏值只是無法刪除的記錄。

從上一節中產生資料集設定檔的情況下，我們看到`Latitude`和`Longitude`資料行具有高百分比的遺漏值。 在此範例中，我們要計算平均值，並插補遺漏值的兩個資料行。

首先，取得最新的資料集定義[ `get_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-)和削減資料[ `keep_columns()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#keep-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow)，因此我們只能檢視我們想要討論的資料行。

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||ID|阻止| 緯度|經度|
-|---------|-----|---------|----------|
|0|10498554|False|41.692834|-87.604319|
|1|10516598|False| 41.744107 |-87.664494|
|2|10519196|False| NaN|NaN|

接下來，檢查`MEAN`緯度資料行使用的值[ `summarize()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow)函式。 此函式接受 `group_by_columns` 參數中的資料行陣列來指定彙總層級。 `summary_columns`參數可接受`SummaryColumnsValue`函式，指定目前的資料行名稱，新的導出的欄位名稱，而`SummaryFunction`來執行。

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
|0|False|41.780049|

一旦我們確認要插補的值，請使用[ `ImputeMissingValuesBuilder` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py)若要了解 imputes 任一個導出資料行就是 fixed 的運算式`MIN`， `MAX`，`MEAN`的值，或`CUSTOM`值。 未指定 `group_by_columns` 時，會使用各群組計算出的 `MIN`、`MAX` 和 `MEAN` 按群組插補遺漏值。

[ `ImputeColumnArguments` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction)接受 column_id 的字串，並有`ReplaceValueFunction`指定 impute 型別。 針對遺漏的經度值，請使用外部知識為基礎的-87 推算。

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

插補步驟可以鏈結在一起成`ImputeMissingValuesBuilder`物件使用[ `Builders` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py)類別的函式[ `impute_missing_values()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder)。 `impute_columns` 參數可接受 `ImputeColumnArguments` 物件的陣列。

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

呼叫[ `learn()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none)來儲存 impute 步驟中，函式，並將它們套用至使用資料流程物件[ `to_dataflow()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow)。

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

使用下列的輸出資料表所示，遺失的緯度已插補`MEAN`的值`Arrest==False`群組，以及遺失的經度已插補-87 使用。

||ID|阻止|緯度|經度
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

更新資料集定義，具有[ `update_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-)保持執行的轉換步驟。

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||ID|阻止|緯度|經度
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

## <a name="create-assertion-rules"></a>建立判斷提示規則

通常，資料處理時清除和準備資料只是我們用於生產環境所需的總資料。 如此一來，我們讓我們清除假設的一些可能會發現設為 false。 例如，在持續更新資料集中，原本只包含在特定範圍內的數字的資料行可能包含更廣泛的後續執行中的值。 這些錯誤通常會導致中斷的管線或不正確的資料。

在 管線執行評估的資料上建立判斷提示的資料集支援。 這些判斷提示，讓我們驗證我們假設資料繼續為精確時，據以處理失敗。

比方說，如果您想要限制`Latitude`並`Longitude`值在特定的數值範圍，資料集中[ `assert_value()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow)方法可確保一律是如此。

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
ID|FieldType.INTEGER|1.04986e+07|1.05351e+07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
阻止|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
緯度|FieldType.DECIMAL|41.6928|41.9032|10.0|0.0|10.0|0.0|0.0|0.0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1.05
經度|FieldType.INTEGER|-87|-87|10.0|0.0|10.0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

從設定檔，您會看到`Error Count`針對`Longitude`資料行是 3。 下列程式碼會篩選資料集、 擷取錯誤，並看到哪些值會導致失敗的判斷提示。 從這裡開始，請調整您的程式碼，並適當地清理資料。

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>衍生資料行實例化

資料集的更進階的工具之一是能夠衍生資料行使用的所需的結果範例。 這可讓您提供給 SDK 範例中，因此，它可以產生程式碼，以達到預期的轉換。

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||ID|案例編號|Date|封鎖|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S 沙加緬度 AVE|...

假設您要轉換日期和時間格式 ' 2016年-04-04 下午 10 點-上午 12 點 '。 在 [ `derive_column_by_example()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow)引數，提供您想要的輸出中的範例`example_data`參數，格式如下： *(所需的輸出中的 (原始輸出）*。

下列程式碼提供兩個範例所需的輸出，("2016年-04-04 23:56:00"，"2016年-04-04 下午 10-12 AM") 和 ("2016年-04-15 17:00:00"，"2016年-04-15 下午 4 點-下午 6 」)

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

下表中，請注意，新的資料行，Date_Time_Range 包含指定之格式的記錄。

||ID|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM-12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 4PM-6PM
2|10519196|2016-04-15 10:00:00|2016-04-15 10AM-12PM

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>模糊群組

當您從不同來源收集資料時可能會遇到拼字、 大小寫或縮寫的相同實體中的變化。 自動標準化，並協調這些變異 SDK 的 「 模糊群組 」 或文字叢集功能。

例如，資料行`inspections.business.city`包含數種形式的縣 （市) 名稱"San Francisco"。

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|...|
-|-----|-------------------------|------------|--|---
0|16162|快速-N-Ezee 印度食物|3861 24th St|SF|...
1|67565|國王的泰文吱咖啡館|1541 TARAVAL St|SAN FRANCISCO|...
2|67565|國王的泰文吱咖啡館|1541 TARAVAL St|SAN FRANCISCO|...
3|68701|Grindz|832 clement St|SF|...
4|69186|主要根據與事件，inc.|1255 22 St|S.F.|...

讓我們使用[ `fuzzy_group_column()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow)方法，以新增具有會自動偵測到的標準格式"San francisco"的資料行。 引數`source_column`和`new_column_name`所需。 您也會有的選項：

* 建立新的資料行， `similarity_score_column_name`，它會顯示每一對原始值和標準值之間的相似度分數。

* 提供`similarity_threshold`，這是要分組在一起的值的最小相似度分數。

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
0|16162|快速-N-Ezee 印度食物|3861 24th St|SF|San Francisco|0.814806|...
1|67565|國王的泰文吱咖啡館|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
2|67565|國王的泰文吱咖啡館|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
3|68701|Grindz|832 clement St|SF|San Francisco|0.814806|...
4|69186|主要根據與事件，inc.|1255 22 St|S.F.|San Francisco|0.814806|...

在產生的資料集定義中，相同的字串"San Francisco"正規化的資料中表示 「 San Francisco"的所有不同變化。

儲存的最新的資料集定義，使用此模糊群組進入`update_definition()`。

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>後續步驟

* [管理資料集定義的生命週期](how-to-manage-dataset-definitions.md)。

* 將自動化的機器學習[教學課程](tutorial-auto-train-models.md)迴歸模型範例。

* 請參閱 SDK[概觀](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)設計模式和使用方式範例。

* 如需使用資料集的範例，請參閱 <<c0> [ 範例 notebook](https://aka.ms/dataset-tutorial)。