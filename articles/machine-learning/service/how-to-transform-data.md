---
title: 轉換：資料準備 Python SDK
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 資料準備 SDK 來轉換和清理資料。 用轉換方法可用來加入資料行、篩選不必要的資料列或資料行，以及插補遺漏的值。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: db23c8af7eaa4a86691ccb0bb831ce2cc28d635c
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65471841"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>使用 Azure Machine Learning 資料準備 SDK 來轉換資料

在本文中，您將了解不同的方法，將轉換的資料使用`azureml-dataprep`封裝。 封裝提供了讓您輕鬆加入資料行，篩選掉不必要的資料列或資料行，並插補遺漏值的函式。 請參閱完整的參考文件[azureml dataprep 套件](https://aka.ms/data-prep-sdk)。

> [!Important]
> 如果您要建立新的方案，請嘗試[Azure Machine Learning 資料集](how-to-explore-prepare-data.md)（預覽） 來轉換您的資料、 快照集資料和儲存已建立版本的資料集定義。 資料集是資料準備供應項目來管理 AI 解決方案中的資料集的擴充的功能 SDK 的下一個版本。 如果您使用`azureml-dataprep`套件來建立資料流程，與您的轉換，而不是使用`azureml-datasets`封裝來建立資料集，您將無法供日後使用快照集或已建立版本的資料集。

此操作說明會顯示下列工作的範例：

- 加入使用運算式的資料行
- [插補遺漏值](#impute-missing-values)
- [衍生資料行實例化](#derive-column-by-example)
- [篩選](#filtering)
- [自訂 Python 轉換](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>加入使用運算式的資料行

Azure Machine Learning 資料準備 SDK 包含 `substring` 運算式，可以使用這些運算式以現有的資料行來計算值，然後在新的資料行中輸入該值。 在此範例中，您要載入資料，然後嘗試將資料行新增到該輸入資料。

```Python
import azureml.dataprep as dprep

# loading data
dflow = dprep.read_csv(path=r'data\crime0-10.csv')
dflow.head(3)
```

||ID|案例編號|date|封鎖|IUCR|主要類型|說明|位置描述|阻止|國內|...|Ward|社群區域|FBI 程式碼|X 座標|Y 座標|Year|更新日期|緯度|經度|位置|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|遭竊|$500 以下|街|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|電池|簡單|街|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017、-87.65955018)|
|2|10140270|HY329253|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|電池|簡單的國產電池|街|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|


使用 `substring(start, length)` 運算式擷取 [案例編號] 資料行中的前置詞，並將該字串放在新的資料行 `Case Category` 中。 將 `substring_expression` 變數傳遞至 `expression` 參數可建立新的導出資料行，此資料行會針對每一筆記錄執行運算式。

```Python
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID|案例編號|案例類別|date|封鎖|IUCR|主要類型|說明|位置描述|阻止|國內|...|Ward|社群區域|FBI 程式碼|X 座標|Y 座標|Year|更新日期|緯度|經度|位置|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|HY|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|遭竊|$500 以下|街|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|HY|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|電池|簡單|街|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017、-87.65955018)|
|2|10140270|HY329253|HY|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|電池|簡單的國產電池|街|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|


使用 `substring(start)` 運算式僅擷取 [案例編號] 資料行中的數字，然後建立新的資料行。 使用 `to_number()` 函式將其轉換為數值資料類型，並將字串資料行名稱當作參數傳遞。

```Python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
```

## <a name="impute-missing-values"></a>插補遺漏值

SDK 可以在指定的資料行中插補遺漏值。 在此範例中，您要載入緯度和經度值，然後嘗試在輸入資料中插補遺漏值。

```python
import azureml.dataprep as dprep

# loading input data
dflow = dprep.read_csv(r'data\crime0-10.csv')
dflow = dflow.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
dflow = dflow.to_number(['Latitude', 'Longitude'])
dflow.head(3)
```

||ID|阻止|緯度|經度|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|NaN|NaN|

第三個記錄遺漏了緯度和經度值。 若要插補那些遺漏的值，您使用[ `ImputeMissingValuesBuilder` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py)若要了解已修正的運算式。 其可使用計算出的 `MIN`、`MAX` 或 `MEAN` 值或 `CUSTOM` 值來插補資料行。 未指定 `group_by_columns` 時，會使用各群組計算出的 `MIN`、`MAX` 和 `MEAN` 按群組插補遺漏值。

請檢查`MEAN`緯度資料行使用的值[ `summarize()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow)函式。 此函式接受 `group_by_columns` 參數中的資料行陣列來指定彙總層級。 `summary_columns` 參數可接受 `SummaryColumnsValue` 呼叫。 此函式呼叫會指定目前的資料行名稱、新的導出欄位名稱，以及要執行的 `SummaryFunction`。

```python
dflow_mean = dflow.summarize(group_by_columns=['Arrest'],
                       summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                 summary_column_name='Latitude_MEAN',
                                                                 summary_function=dprep.SummaryFunction.MEAN)])
dflow_mean = dflow_mean.filter(dprep.col('Arrest') == 'false')
dflow_mean.head(1)
```

||阻止|Latitude_MEAN|
|-----|-----|----|
|0|false|41.878961|

緯度的 `MEAN` 值看起來正確，請使用 `ImputeColumnArguments` 函式推算它。 此函式接受 `column_id` 字串和 `ReplaceValueFunction` 來指定插補類型。 至於遺漏的經度值，則要根據外部知識插補 42。

插補步驟可以使用產生器函式 `impute_missing_values()` 鏈結在一起，成為 `ImputeMissingValuesBuilder` 物件。 `impute_columns` 參數可接受 `ImputeColumnArguments` 物件的陣列。 呼叫 `learn()` 函式來儲存插補步驟，然後使用 `to_dataflow()` 套用至資料流程物件。

```python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = dflow.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])

impute_builder.learn()
dflow_imputed = impute_builder.to_dataflow()
dflow_imputed.head(3)
```

||ID|阻止|緯度|經度|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|41.878961|42.000000|

如上述結果所示，已使用 `Arrest=='false'` 群組的 `MEAN` 值插補遺漏緯度。 已對遺漏經度插補 42。

```python
imputed_longitude = dflow_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>依範例衍生資料行

Azure Machine Learning 資料準備 SDK 的其中一個更為進階的工具，即是使用所需結果的範例衍生資料行的能力。 這可讓您提供一個範例給 SDK，因此 SDK 可以產生程式碼以達到所需的轉換。

```python
import azureml.dataprep as dprep
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dflow.head(4)
```

||DATE|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00|FM-12|22|50|10|
|2|1/1/2015 1:54|FM-15|22|50|10|
|3|1/1/2015 2:54|FM-15|22|50|11|

假設您需要將這個檔案加入到日期和時間格式為「Mar 10, 2018 | 2AM-4AM」的資料集。

```python
builder = dflow.builders.derive_column_by_example(source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(source_data=dflow.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview(count=5) 
```

||DATE|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|Jan 1, 2015 12AM-2AM|
|1|1/1/2015 1:00|Jan 1, 2015 12AM-2AM|
|2|1/1/2015 1:54|Jan 1, 2015 12AM-2AM|
|3|1/1/2015 2:54|Jan 1, 2015 2AM-4AM|
|4|1/1/2015 3:54|Jan 1, 2015 2AM-4AM|

上述程式碼會先建立衍生的資料行的建立器。 您可以提供要納入考慮的來源資料行陣列 (`DATE`)，以及要新增的新資料行名稱。 如同第一個範例，您會傳入第二個資料列 (索引 1)，並提供衍生的資料行的預期值。

最後要呼叫 `builder.preview(skip=30, count=5)`，您就可以看到來源資料行旁邊出現衍生的資料行。 格式看起來正確，但是您只看到相同日期「Jan 1, 2015」的值。

現在，從頂端將所需的資料列數目傳入到 `skip` 查看更往下的資料列。

> [!NOTE]
> Preview() 函式會略過資料列，但不是重新編號的輸出索引。 在下列範例中，資料表中的索引 0 對應到資料流程中的索引 30。

```python
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|1|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|2|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|3|1/2/2015 0:54|Feb 1, 2015 12AM-2AM|
|4|1/2/2015 1:00|Feb 1, 2015 12AM-2AM|

在這裡可以發現產生的程式發生了問題。 衍生程式完全根據您之前提供的範例，選擇將日期剖析為「日/月/年」，但這不是您在這裡所要的結果。 若要修正此問題，特定記錄的索引為目標並提供另一個範例中使用`add_example()`函式`builder`變數。

```python
builder.add_example(source_data=dflow.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|1|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|2|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|3|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|
|4|1/2/2015 1:00|Jan 2, 2015 12AM-2AM|

資料列正確地處理現在 '2015 年 1 月 2 日' 為 '2015 年 1 月 2 日' 但如果您看超出索引 76 衍生的資料行，您會看到結尾的值，在衍生的資料行中有執行任何動作。

```python
builder.preview(skip=75, count=5)
```


||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|2015 年 1 月 3 日上午 6 點-8 AM|
|1|1/3/2015 7:54|2015 年 1 月 3 日上午 6 點-8 AM|
|2|1/29/2015 6:54|None|
|3|1/29/2015 7:00|None|
|4|1/29/2015 7:54|None|

```python
builder.add_example(source_data=dflow.iloc[77], example_value='Jan 29, 2015 6AM-8AM')
builder.preview(skip=75, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|2015 年 1 月 3 日上午 6 點-8 AM|
|1|1/3/2015 7:54|2015 年 1 月 3 日上午 6 點-8 AM|
|2|1/29/2015 6:54|2015 年 1 月 29 日上午 6 點-8 AM|
|3|1/29/2015 7:00|2015 年 1 月 29 日上午 6 點-8 AM|
|4|1/29/2015 7:54|2015 年 1 月 29 日上午 6 點-8 AM|

 若要查看呼叫目前的範例衍生清單`list_examples()`上產生器物件。

```python
examples = builder.list_examples()
```

| |DATE|範例|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|Jan 1, 2015 12AM-2AM|-1|
|1|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|-2|
|2|1/29/2015 20:54|Jan 29, 2015 8PM-10PM|-3|


在某些情況下如果您想要刪除範例不正確，您可以傳入其中一個`example_row`pandas 資料框架，或`example_id`值。 例如，如果您執行`builder.delete_example(example_id=-1)`，它會刪除第一個轉換範例。


呼叫`to_dataflow()`上產生器中，它會傳回資料流與所需的衍生資料行，以新增。

```python
dflow = builder.to_dataflow()
df = dflow.to_pandas_dataframe()
```

## <a name="filtering"></a>正在篩選

SDK 包含方法[ `drop_columns()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#drop-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow)並[ `filter()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py)可讓您篩選出資料行或資料列。

### <a name="initial-setup"></a>初始設定

```python
import azureml.dataprep as dprep
from datetime import datetime
dflow = dprep.read_csv(path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|None|None|None|None|None|None|None|None|None|None|None|None|None|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|N|1|0|0|0|0|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|N|1|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|N|5|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|N|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>篩選資料行

若要篩選資料行，請使用 `drop_columns()`。 這個方法會採用要卸除的資料行清單或更複雜的引數呼叫[ `ColumnSelector` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.columnselector?view=azure-dataprep-py)。

#### <a name="filtering-columns-with-list-of-strings"></a>使用字串的清單篩選資料行

在此範例中，`drop_columns()` 使用的是字串清單。 每個字串應該完全符合要置放的所需資料行。

```python
dflow = dflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|None|None|None|None|None|None|None|None|None|None|None|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|0|0|0|0|1|.00|0|0|21.25|

#### <a name="filtering-columns-with-regex"></a>使用 regex 篩選資料行

或者，使用 `ColumnSelector` 運算式來置放符合 regex 運算式的資料行。 在此範例中，您會置放所有符合運算式 `Column*|.*longitude|.*latitude` 的資料行。

```python
dflow = dflow.drop_columns(dprep.ColumnSelector('Column*|.*longitud|.*latitude', True, True))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|None|None|None|None|None|None|None|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|1|.00|0|0|21.25|

## <a name="filtering-rows"></a>篩選資料列

若要篩選資料列，請使用 `filter()`。 這個方法會將 Azure Machine Learning 資料準備 SDK 運算式做為引數，並傳回新的資料流，內有資料運算式得出為 True 的資料行。 運算式是使用使用運算式建立器 (`col`、`f_not`、`f_and`、`f_or`) 和規則運算子 (>、<、>=、<=、==、!=) 建置而成。

### <a name="filtering-rows-with-simple-expressions"></a>使用簡單運算式篩選資料列

使用運算式產生器 `col`，將資料行名稱指定為字串引數 `col('column_name')`。 搭配下列其中一個標準運算子的組合使用此運算式 >、<、>=、<=、==、!= 來建置運算式，例如 `col('Tip_amount') > 0`。 最後，將內建的運算式傳遞至 `filter()` 函式。

在此範例中，`dflow.filter(col('Tip_amount') > 0)` 傳回資料列的值 `Tip_amount` 大於 0 的新資料流程。

> [!NOTE] 
> `Tip_amount` 會先轉換為數值，以便我們建立比較運算式，針對其他的數值比較該數值。

```python
dflow = dflow.to_number(['Tip_amount'])
dflow = dflow.filter(dprep.col('Tip_amount') > 0)
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01 19:33:28|2013-08-01 19:35:21|5|.00|0.08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|.00|0.30|0|3.8|

### <a name="filtering-rows-with-complex-expressions"></a>使用複雜運算式篩選資料列

若要使用複雜運算式篩選，請結合一個或多個簡單運算式與運算式建立器 `f_not`、`f_and` 或 `f_or`。

在此範例中，`dflow.filter()` 傳回的新資料流中，資料列的 `'Passenger_count'` 小於 5 而 `'Tolls_amount'` 大於 0。

```python
dflow = dflow.to_number(['Passenger_count', 'Tolls_amount'])
dflow = dflow.filter(dprep.f_and(dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:16:00|2013-08-08 12:16:00|1.0|.00|2.25|5.00|19.75|
|1|2013-08-12 14:43:53|2013-08-12 15:04:50|1.0|5.28|6.46|5.33|32.29|

也可結合多個運算式建立器篩選資料列，以建立巢狀運算式。

> [!NOTE]
> `lpep_pickup_datetime` 和 `Lpep_dropoff_datetime` 會先轉換為日期時間，以便建立比較運算式，將該日期時間值與其他的日期時間值做比較。

```python
dflow = dflow.to_datetime(['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dflow = dflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013,7,1)
dflow = dflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06+00:00|2013-08-13 06:30:28+00:00|1.0|9.57|7.47|5.33|44.80|
|1|2013-08-23 12:28:20+00:00|2013-08-23 12:50:28+00:00|2.0|8.22|8.08|5.33|40.41|

## <a name="custom-python-transforms"></a>自訂 Python 轉換

永遠都會有進行轉換最簡單的選項就是撰寫自己的指令碼的情況。 SDK 提供您可以用於自訂 Python 指令碼的三個擴充點。

- 新的指令碼資料行
- 新的指令碼篩選條件
- 轉換分割區

相映增加和相映放大的執行階段均支援每個擴充。 使用這些擴充點的主要優點是，您不需要提取所有資料即可建立資料框架。 您的自訂 Python 程式碼只會和其他轉換一樣以大規模的型態、按分割，並且通常以平行的方式執行。

### <a name="initial-data-preparation"></a>初期資料準備

首先從 Azure Blob 載入一些資料。

```python
import azureml.dataprep as dprep
col = dprep.col

dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
dflow.head(2)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|阿拉巴馬州|1|101710|Hale County|10171002158| |
|1|阿拉巴馬州|1|101710|Hale County|10171002162| |

精簡資料集，並執行一些基本的轉換，包括移除資料行、 取代值，以及轉換類型。

```python
dflow = dflow.keep_columns(['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
dflow = dflow.replace_na(columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
dflow = dflow.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
dflow.head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|阿拉巴馬州|Hale County|1.017100e+10|None|
|1|阿拉巴馬州|Hale County|1.017100e+10|None|

使用下列篩選條件尋找 null 值。

```python
dflow.filter(col('MAM_MTH00numvalid_1011').is_null()).head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|阿拉巴馬州|Hale County|1.017100e+10|None|
|1|阿拉巴馬州|Hale County|1.017100e+10|None|

### <a name="transform-partition"></a>轉換分割區

使用[ `transform_partition()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#transform-partition-script--str-----azureml-dataprep-api-dataflow-dataflow)到所有的 null 值取代為 0。 此程式碼會分區執行，而不是一次在整個資料集上執行。 也就是說，在大型資料集上，此程式碼會在執行階段處理資料時，同時逐區平行執行。

Python 指令碼必須定義稱為 `transform()`，且接受兩個引數 `df` 和 `index` 的函式。 `df` 引數將是包含資料分割資料的 pandas 資料框架，而 `index` 引數則是資料分割的唯一識別碼。 轉換函式可以完整編輯傳入的資料框架，但必須傳回資料框架。 Python 指令碼匯入的任何程式庫都必須存在於執行資料流程所在的環境。

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(2)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|阿拉巴馬州|Hale County|1.017100e+10|0.0|
|1|阿拉巴馬州|Hale County|1.017100e+10|0.0|

### <a name="new-script-column"></a>新的指令碼資料行

您可以使用 Python 指令碼，來建立新的資料行具有國家/地區的名稱和狀態名稱，以及狀態名稱改成大寫。 若要這樣做，請使用[ `new_script_column()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-column-new-column-name--str--insert-after--str--script--str-----azureml-dataprep-api-dataflow-dataflow)上的資料流的方法。

Python 指令碼必須定義稱為 `newvalue()`，且接受單一引數 `row` 的函式。 `row` 引數是一種 dic (`key`：資料行名稱，`val`：目前的值)，而且將會針對資料集中的每一個資料列傳遞至此函式。 此函式必須傳回要用於新資料行的值。 Python 指令碼匯入的任何程式庫都必須存在於執行資料流程所在的環境。

```python
dflow = dflow.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|阿拉巴馬州|Hale County|Hale County, Alabama|1.017100e+10|0.0|
|1|阿拉巴馬州|Hale County|Hale County, Alabama|1.017100e+10|0.0|

### <a name="new-script-filter"></a>新的指令碼篩選條件

建置使用 Python 運算式[ `new_script_filter()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-filter-script--str-----azureml-dataprep-api-dataflow-dataflow)來篩選資料集只有 'Hale' 不在新的資料列`county_state`資料行。 如果我們想要保留資料列，運算式會傳回 `True`，並傳回 `False` 以置放資料列。

```python
dflow = dflow.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|阿拉巴馬州|Jefferson County|Jefferson County, Alabama|1.019200e+10|1.0|
|1|阿拉巴馬州|Jefferson County|Jefferson County, Alabama|1.019200e+10|0.0|

## <a name="next-steps"></a>後續步驟

* 請參閱 Azure Machine Learning 資料準備 SDK[教學課程](tutorial-data-prep.md)如需解決的特定案例的範例
