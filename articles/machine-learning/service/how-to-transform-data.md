---
title: 轉換：資料準備 Python SDK
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 資料準備 SDK 來轉換和清理資料。 用轉換方法可用來加入資料行、篩選不必要的資料列或資料行，以及插補遺漏的值。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d32244cd49ebd42192b2388215f79a64cacb3caa
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186135"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>使用 Azure Machine Learning 資料準備 SDK 來轉換資料

在此文章中，您將了解使用 [Azure Machine Learning 資料準備 SDK](https://aka.ms/data-prep-sdk) 載入資料的不同方法。 SDK 提供的函式使其很容易就能加入資料行、篩選不必要的資料列或資料行，並插補遺漏值。

目前有可執行下列工作的函式：

- [加入使用運算式的資料行](#column)
- [插補遺漏值](#impute-missing-values)
- [衍生資料行實例化](#derive-column-by-example)
- [篩選](#filtering)
- [自訂 Python 轉換](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>加入使用運算式的資料行

Azure Machine Learning 資料準備 SDK 包含 `substring` 運算式，可以使用這些運算式以現有的資料行來計算值，然後在新的資料行中輸入該值。 在此範例中，您要載入資料，然後嘗試將資料行新增到該輸入資料。

```python
import azureml.dataprep as dprep

# loading data
dataflow = dprep.read_csv(path=r'data\crime0-10.csv')
dataflow.head(3)
```

||ID|案例編號|日期|區塊|IUCR|主要類型|說明|位置描述|阻止|國內|...|Ward|社群區域|FBI 程式碼|X 座標|Y 座標|Year|更新日期|緯度|經度|位置|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|遭竊|$500 以下|街|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|電池|簡單|街|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017、-87.65955018)|
|2|10140270|HY329253|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|電池|簡單的國產電池|街|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|


使用 `substring(start, length)` 運算式擷取 [案例編號] 資料行中的前置詞，並將該字串放在新的資料行 `Case Category` 中。 將 `substring_expression` 變數傳遞至 `expression` 參數可建立新的導出資料行，此資料行會針對每一筆記錄執行運算式。

```python
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dataflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID|案例編號|案例類別|日期|區塊|IUCR|主要類型|說明|位置描述|阻止|...|Ward|社群區域|FBI 程式碼|X 座標|Y 座標|Year|更新日期|緯度|經度|位置|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|HY|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|遭竊|$500 以下|街|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|HY|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|電池|簡單|街|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017、-87.65955018)|
|2|10140270|HY329253|HY|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|電池|簡單的國產電池|街|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|



使用 `substring(start)` 運算式僅擷取 [案例編號] 資料行中的數字，然後建立新的資料行。 使用 `to_number()` 函式將其轉換為數值資料類型，並將字串資料行名稱當作參數傳遞。

```python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dataflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
case_id.head(3)
```

||ID|案例編號|案例識別碼|日期|區塊|IUCR|主要類型|說明|位置描述|阻止|...|Ward|社群區域|FBI 程式碼|X 座標|Y 座標|Year|更新日期|緯度|經度|位置|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|329907.0|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|遭竊|$500 以下|街|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|329265.0|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|電池|簡單|街|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017、-87.65955018)|
|2|10140270|HY329253|329253.0|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|電池|簡單的國產電池|街|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|

## <a name="impute-missing-values"></a>插補遺漏值

SDK 可以在指定的資料行中插補遺漏值。 在此範例中，您要載入緯度和經度值，然後嘗試在輸入資料中插補遺漏值。

```python
import azureml.dataprep as dprep

# loading input data
df = dprep.read_csv(r'data\crime0-10.csv')
df = df.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
df = df.to_number(['Latitude', 'Longitude'])
df.head(5)
```

||ID|阻止|緯度|經度|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|NaN|NaN|
|3|10139885|false|41.902152|-87.754883|
|4|10140379|false|41.885610|-87.657009|

第三個記錄遺漏了緯度和經度值。 若要插補這些遺漏值，您可以使用 `ImputeMissingValuesBuilder` 來了解已修正的運算式。 其可使用計算出的 `MIN`、`MAX` 或 `MEAN` 值或 `CUSTOM` 值來插補資料行。 未指定 `group_by_columns` 時，會使用各群組計算出的 `MIN`、`MAX` 和 `MEAN` 按群組插補遺漏值。

請使用 `summarize()` 函式檢查緯度資料行的 `MEAN` 值。 此函式接受 `group_by_columns` 參數中的資料行陣列來指定彙總層級。 `summary_columns` 參數可接受 `SummaryColumnsValue` 呼叫。 此函式呼叫會指定目前的資料行名稱、新的導出欄位名稱，以及要執行的 `SummaryFunction`。

```python
df_mean = df.summarize(group_by_columns=['Arrest'],
                       summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                 summary_column_name='Latitude_MEAN',
                                                                 summary_function=dprep.SummaryFunction.MEAN)])
df_mean = df_mean.filter(dprep.col('Arrest') == 'false')
df_mean.head(1)
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
impute_builder = df.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
# call learn() to learn a fixed program to impute missing values
impute_builder.learn()
# call to_dataflow() to get a data flow with impute step added
df_imputed = impute_builder.to_dataflow()

# check impute result
df_imputed.head(5)
```

||ID|阻止|緯度|經度|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|41.878961|42.000000|
|3|10139885|false|41.902152|-87.754883|
|4|10140379|false|41.885610|-87.657009|

如上述結果所示，已使用 `Arrest=='false'` 群組的 `MEAN` 值插補遺漏緯度。 已對遺漏經度插補 42。

```python
imputed_longitude = df_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>依範例衍生資料行

Azure Machine Learning 資料準備 SDK 的其中一個更為進階的工具，即是使用所需結果的範例衍生資料行的能力。 這可讓您提供一個範例給 SDK，因此 SDK 可以產生程式碼以達到所需的轉換。

```python
import azureml.dataprep as dprep
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dataflow.head(10)
```

||日期|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00|FM-12|22|50|10|
|2|1/1/2015 1:54|FM-15|22|50|10|
|3|1/1/2015 2:54|FM-15|22|50|11|
|4|1/1/2015 3:54|FM-15|24|46|13|
|5|1/1/2015 4:00|FM-12|24|46|13|
|6|1/1/2015 4:54|FM-15|22|52|15|
|7|1/1/2015 5:54|FM-15|23|48|17|
|8|1/1/2015 6:54|FM-15|23|50|14|
|9|1/1/2015 7:00|FM-12|23|50|14|

假設您需要將這個檔案加入到日期和時間格式為「Mar 10, 2018 | 2AM-4AM」的資料集。

```python
builder = dataflow.builders.derive_column_by_example(source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(source_data=df.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview() 
```

||日期|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|Jan 1, 2015 12AM-2AM|
|1|1/1/2015 1:00|Jan 1, 2015 12AM-2AM|
|2|1/1/2015 1:54|Jan 1, 2015 12AM-2AM|
|3|1/1/2015 2:54|Jan 1, 2015 2AM-4AM|
|4|1/1/2015 3:54|Jan 1, 2015 2AM-4AM|
|5|1/1/2015 4:00|Jan 1, 2015 4AM-6AM|
|6|1/1/2015 4:54|Jan 1, 2015 4AM-6AM|
|7|1/1/2015 5:54|Jan 1, 2015 4AM-6AM|
|8|1/1/2015 6:54|Jan 1, 2015 6AM-8AM|
|9|1/1/2015 7:00|Jan 1, 2015 6AM-8AM|

上述程式碼會先建立衍生的資料行的建立器。 您可以提供要納入考慮的來源資料行陣列 (`DATE`)，以及要新增的新資料行名稱。 如同第一個範例，您會傳入第二個資料列 (索引 1)，並提供衍生的資料行的預期值。

最後要呼叫 `builder.preview()`，您就可以看到來源資料行旁邊出現衍生的資料行。 格式看起來正確，但是您只看到相同日期「Jan 1, 2015」的值。

現在，從頂端將所需的資料列數目傳入到 `skip` 查看更往下的資料列。

```
builder.preview(skip=30)
```

||日期|date_timerange|
|-----|-----|-----|
|30|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|31|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|32|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|33|1/2/2015 0:54|Feb 1, 2015 12AM-2AM|
|34|1/2/2015 1:00|Feb 1, 2015 12AM-2AM|
|35|1/2/2015 1:54|Feb 1, 2015 12AM-2AM|
|36|1/2/2015 2:54|Feb 1, 2015 2AM-4AM|
|37|1/2/2015 3:54|Feb 1, 2015 2AM-4AM|
|38|1/2/2015 4:00|Feb 1, 2015 4AM-6AM|
|39|1/2/2015 4:54|Feb 1, 2015 4AM-6AM|

在這裡可以發現產生的程式發生了問題。 衍生程式完全根據您之前提供的範例，選擇將日期剖析為「日/月/年」，但這不是您在這裡所要的結果。 若要修正此問題，請針對 `builder` 變數使用 `add_example()` 函式，提供另一個範例。

```python
builder.add_example(source_data=preview_df.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=10)
```

||日期|date_timerange|
|-----|-----|-----|
|30|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|31|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|32|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|33|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|
|34|1/2/2015 1:00|Jan 2, 2015 12AM-2AM|
|35|1/2/2015 1:54|Jan 2, 2015 12AM-2AM|
|36|1/2/2015 2:54|Jan 2, 2015 2AM-4AM|
|37|1/2/2015 3:54|Jan 2, 2015 2AM-4AM|
|38|1/2/2015 4:00|Jan 2, 2015 4AM-6AM|
|39|1/2/2015 4:54|Jan 2, 2015 4AM-6AM|

現在，資料列正確地將「1/2/2015」處理為「Jan 2, 2015」，但是，如果您進一步查看衍生的資料行，會看到結尾的值並未出現在衍生的資料行中。 若要修正該問題，則需提供另一個範例給資料列 66。

```python
builder.add_example(source_data=preview_df.iloc[66], example_value='Jan 29, 2015 8PM-10PM')
builder.preview(count=10)
```

||日期|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|1|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|2|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|3|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|
|4|1/2/2015 1:00|Jan 2, 2015 12AM-2AM|
|5|1/2/2015 1:54|Jan 2, 2015 12AM-2AM|
|6|1/2/2015 2:54|Jan 2, 2015 2AM-4AM|
|7|1/2/2015 3:54|Jan 2, 2015 2AM-4AM|
|8|1/2/2015 4:00|Jan 2, 2015 4AM-6AM|
|9|1/2/2015 4:54|Jan 2, 2015 4AM-6AM|

若要使用 '|' 分隔日期和時間，您要新增另一個範例。 這次不需要從預覽傳入資料列，而是幫 `source_data` 參數的值建構資料行名稱的字典。

```python
builder.add_example(source_data={'DATE': '11/11/2015 0:54'}, example_value='Nov 11, 2015 | 12AM-2AM')
builder.preview(count=10)
```

||日期|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|None|
|1|1/1/2015 23:54|None|
|2|1/1/2015 23:59|None|
|3|1/2/2015 0:54|None|
|4|1/2/2015 1:00|None|
|5|1/2/2015 1:54|None|
|6|1/2/2015 2:54|None|
|7|1/2/2015 3:54|None|
|8|1/2/2015 4:00|None|
|9|1/2/2015 4:54|None|

這顯然有負面的影響，因為現在只有在衍生的資料行內出現值的資料列完全與我們提供的範例相符。 在產生器物件上呼叫 `list_examples()` 可查看目前範例衍生的清單。

```python
examples = builder.list_examples()
```

| |日期|範例|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|Jan 1, 2015 12AM-2AM|-1|
|1|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|-2|
|2|1/29/2015 20:54|Jan 29, 2015 8PM-10PM|-3|
|3|11/11/2015 0:54|Nov 11, 2015 \| 12AM-2AM|-4|

在此情況下，已經提供不一致的範例。 若要修正該問題，請以正確的範例取代前三個範例 (包括日期和時間之間的 '|')。

刪除不正確的範例來修正不一致的範例 (可藉由從 pandas 資料框架傳入 `example_row`，也可藉由傳入 `example_id` 值)，然後重新加入新修改的範例。

```python
builder.delete_example(example_id=-1)
builder.delete_example(example_row=examples.iloc[1])
builder.delete_example(example_row=examples.iloc[2])
builder.add_example(examples.iloc[0], 'Jan 1, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[1], 'Jan 2, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[2], 'Jan 29, 2015 | 8PM-10PM')
builder.preview()
```

| | 日期 | date_timerange |
| -------- | -------- | -------- |
| 0 | 1/1/2015 0:54 | Jan 1, 2015 \| 12AM-2AM |
| 1 | 1/1/2015 1:00 | Jan 1, 2015 \| 12AM-2AM |
| 2 | 1/1/2015 1:54 | Jan 1, 2015 \| 12AM-2AM |
| 3 | 1/1/2015 2:54 | Jan 1, 2015 \| 2AM-4AM |
| 4 | 1/1/2015 3:54 | Jan 1, 2015 \| 2AM-4AM |
| 5 | 1/1/2015 4:00 | Jan 1, 2015 \| 4AM-6AM|
| 6 | 1/1/2015 4:54 | Jan 1, 2015 \| 4AM-6AM|
| 7 | 1/1/2015 5:54 | Jan 1, 2015 \| 4AM-6AM|
| 8 | 1/1/2015 6:54 | Jan 1, 2015 \| 6AM-8AM|
| 9 | 1/1/2015 7:00 | Jan 1, 2015 \| 6AM-8AM|

現在，資料看起來正確無誤，然後您可以在產生器上呼叫 `to_dataflow()`，這會傳回已新增所需衍生資料行的資料流程。

```python
dataflow = builder.to_dataflow()
df = dataflow.to_pandas_dataframe()
```

## <a name="filtering"></a>篩選

SDK 內含方法 `Dataflow.drop_columns` 和 `Dataflow.filter`，可用來篩選出資料行或資料列。

### <a name="initial-setup"></a>初始設定

```python
import azureml.dataprep as dprep
from datetime import datetime
dataflow = dprep.read_csv(path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|None|None|None|None|None|None|None|None|None|None|None|None|None|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|N|1|0|0|0|0|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|N|1|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|N|5|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|N|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>篩選資料行

若要篩選資料行，請使用 `Dataflow.drop_columns`。 這個方法會使用將要置放的資料行的清單，或使用稱為 `ColumnSelector` 的較複雜引數。

#### <a name="filtering-columns-with-list-of-strings"></a>使用字串的清單篩選資料行

在此範例中，`drop_columns` 使用的是字串清單。 每個字串應該完全符合要置放的所需資料行。

```python
dataflow = dataflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|None|None|None|None|None|None|None|None|None|None|None|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|0|0|0|0|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|0|0|0|0|1|.00|0|0|3.25|

#### <a name="filtering-columns-with-regex"></a>使用 regex 篩選資料行

或者，使用 `ColumnSelector` 運算式來置放符合 regex 運算式的資料行。 在此範例中，您會置放所有符合運算式 `Column*|.*longitude|.*latitude` 的資料行。

```python
dataflow = dataflow.drop_columns(dprep.ColumnSelector('Column*|.*longitud|.*latitude', True, True))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|None|None|None|None|None|None|None|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|1|.00|0|0|3.25|

## <a name="filtering-rows"></a>篩選資料列

若要篩選資料列，請使用 `DataFlow.filter`。 這個方法會將 Azure Machine Learning 資料準備 SDK 運算式做為引數，並傳回新的資料流，內有資料運算式得出為 True 的資料行。 運算式是使用使用運算式建立器 (`col`、`f_not`、`f_and`、`f_or`) 和規則運算子 (>、<、>=、<=、==、!=) 建置而成。

### <a name="filtering-rows-with-simple-expressions"></a>使用簡單運算式篩選資料列

使用運算式產生器 `col`，將資料行名稱指定為字串引數 `col('column_name')`。 搭配下列其中一個標準運算子的組合使用此運算式 >、<、>=、<=、==、!= 來建置運算式，例如 `col('Tip_amount') > 0`。 最後，將內建的運算式傳遞至 `Dataflow.filter` 函式。

在此範例中，`dataflow.filter(col('Tip_amount') > 0)` 傳回資料列的值 `Tip_amount` 大於 0 的新資料流程。

> [!NOTE] 
> `Tip_amount` 會先轉換為數值，以便我們建立比較運算式，針對其他的數值比較該數值。

```python
dataflow = dataflow.to_number(['Tip_amount'])
dataflow = dataflow.filter(dprep.col('Tip_amount') > 0)
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01 19:33:28|2013-08-01 19:35:21|5|.00|0.08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|.00|0.30|0|3.8|
|2|2013-08-05 14:11:42|2013-08-05 14:12:47|1|.00|1.05|0|4.55|
|3|2013-08-05 14:15:56|2013-08-05 14:18:04|5|.00|2.22|0|5.72|
|4|2013-08-05 14:42:14|2013-08-05 14:42:38|1|.00|0.88|0|4.38|

### <a name="filtering-rows-with-complex-expressions"></a>使用複雜運算式篩選資料列

若要使用複雜運算式篩選，請結合一個或多個簡單運算式與運算式建立器 `f_not`、`f_and` 或 `f_or`。

在此範例中，`Dataflow.filter` 傳回的新資料流中，資料列的 `'Passenger_count'` 小於 5 而 `'Tolls_amount'` 大於 0。

```python
dataflow = dataflow.to_number(['Passenger_count', 'Tolls_amount'])
dataflow = dataflow.filter(dprep.f_and(dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:16:00|2013-08-08 12:16:00|1.0|.00|2.25|5.00|19.75|
|1|2013-08-12 14:43:53|2013-08-12 15:04:50|1.0|5.28|6.46|5.33|32.29|
|2|2013-08-12 19:48:12|2013-08-12 20:03:42|1.0|5.50|1.00|10.66|30.66|
|3|2013-08-13 06:11:06|2013-08-13 06:30:28|1.0|9.57|7.47|5.33|44.8|
|4|2013-08-16 20:33:50|2013-08-16 20:48:50|1.0|5.63|3.00|5.33|27.83|

也可結合多個運算式建立器篩選資料列，以建立巢狀運算式。

> [!NOTE]
> `lpep_pickup_datetime` 和 `Lpep_dropoff_datetime` 會先轉換為日期時間，以便建立比較運算式，將該日期時間值與其他的日期時間值做比較。

```python
dataflow = dataflow.to_datetime(['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dataflow = dataflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013,7,1)
dataflow = dataflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06+00:00|2013-08-13 06:30:28+00:00|1.0|9.57|7.47|5.33|44.80|
|1|2013-08-23 12:28:20+00:00|2013-08-23 12:50:28+00:00|2.0|8.22|8.08|5.33|40.41|
|2|2013-08-25 09:12:52+00:00|2013-08-25 09:34:34+00:00|1.0|8.80|8.33|5.33|41.66|
|3|2013-08-25 16:46:51+00:00|2013-08-25 17:13:55+00:00|2.0|9.66|7.37|5.33|44.20|
|4|2013-08-25 17:42:11+00:00|2013-08-25 18:02:57+00:00|1.0|9.60|6.87|5.33|41.20|

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

df = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
df.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale County|10171002158| |
|1|ALABAMA|1|101710|Hale County|10171002162| |
|2|ALABAMA|1|101710|Hale County|10171002156| |
|3|ALABAMA|1|101710|Hale County|10171000588|2|
|4|ALABAMA|1|101710|Hale County|10171000589| |

精簡資料集，並執行一些基本的轉換。

```python
df = df.keep_columns(['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
df = df.replace_na(columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
df = df.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
df.head(5)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e+10|None|
|1|ALABAMA|Hale County|1.017100e+10|None|
|2|ALABAMA|Hale County|1.017100e+10|None|
|3|ALABAMA|Hale County|1.017100e+10|2|
|4|ALABAMA|Hale County|1.017100e+10|None|

使用下列篩選條件尋找 null 值。

```python
df.filter(col('MAM_MTH00numvalid_1011').is_null()).head(5)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e+10|None|
|1|ALABAMA|Hale County|1.017100e+10|None|
|2|ALABAMA|Hale County|1.017100e+10|None|
|3|ALABAMA|Hale County|1.017100e+10|None|
|4|ALABAMA|Hale County|1.017100e+10|None|

### <a name="transform-partition"></a>轉換分割區

使用 pandas 函式，將所有 null 值取代為 0。 此程式碼會分區執行，而不是一次在整個資料集上執行。 也就是說，在大型資料集上，此程式碼會在執行階段處理資料時，同時逐區平行執行。

Python 指令碼必須定義稱為 `transform()`，且接受兩個引數 `df` 和 `index` 的函式。 `df` 引數將是包含資料分割資料的 pandas 資料框架，而 `index` 引數則是資料分割的唯一識別碼。 轉換函式可以完整編輯傳入的資料框架，但必須傳回資料框架。 Python 指令碼匯入的任何程式庫都必須存在於執行資料流程所在的環境。

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(5)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e+10|0.0|
|1|ALABAMA|Hale County|1.017100e+10|0.0|
|2|ALABAMA|Hale County|1.017100e+10|0.0|
|3|ALABAMA|Hale County|1.017100e+10|2.0|
|4|ALABAMA|Hale County|1.017100e+10|0.0|

### <a name="new-script-column"></a>新的指令碼資料行

您可以使用 Python 程式碼建立含有國家/地區的名稱和狀態名稱的新資料行，並將狀態名稱改成大寫。 若要這樣做，請在資料流上使用 `new_script_column()` 方法。

Python 指令碼必須定義稱為 `newvalue()`，且接受單一引數 `row` 的函式。 `row` 引數是一種 dic (`key`：資料行名稱，`val`：目前的值)，而且將會針對資料集中的每一個資料列傳遞至此函式。 此函式必須傳回要用於新資料行的值。 Python 指令碼匯入的任何程式庫都必須存在於執行資料流程所在的環境。

```python
df = df.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
df.head(5)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|0.0|
|1|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|0.0|
|2|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|0.0|
|3|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|2.0|
|4|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|0.0|

### <a name="new-script-filter"></a>新的指令碼篩選條件

建置 Python 運算式，從資料集中篩選出新的 `county_state` 資料行並未含有 'Hale' 的資料列。 如果我們想要保留資料列，運算式會傳回 `True`，並傳回 `False` 以置放資料列。

```python
df = df.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
df.head(5)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|1.0|
|1|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0.0|
|2|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0.0|
|3|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0.0|
|4|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0.0|
