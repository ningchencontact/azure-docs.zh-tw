---
title: 寫入：資料準備 Python SDK
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 資料準備 SDK 來載入資料。 您可以在資料流程的任何時間點寫出資料，也可以將資料寫入我們支援的任何位置 (本機檔案系統、Azure Blob 儲存體和 Azure Data Lake Storage)。
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
ms.openlocfilehash: 6206ad1a7356221bf94134e5d293c27d778cc187
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752865"
---
# <a name="write-and-configure-data--with-the-azure-machine-learning-data-prep-sdk"></a>撰寫並使用 Azure Machine Learning 資料準備 SDK 設定資料

在本文中，您將了解不同的方法，將使用的資料寫入[Azure Machine Learning 資料準備 Python SDK](https://aka.ms/data-prep-sdk) ，以及如何設定該資料以實驗[Azure 機器學習服務 SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  在資料流程中的任何時間點，就能寫入輸出資料。 寫入步驟產生的資料流，以及這些步驟在每次執行的資料流執行時加入。 資料會寫入多個分割區檔案，以允許平行寫入。

> [!Important]
> 如果您要建立新的方案，請嘗試[Azure Machine Learning 資料集](how-to-explore-prepare-data.md)（預覽） 來轉換您的資料、 快照集資料和儲存已建立版本的資料集定義。 資料集是資料準備供應項目來管理 AI 解決方案中的資料集的擴充的功能 SDK 的下一個版本。
> 如果您使用`azureml-dataprep`套件來建立資料流程，與您的轉換，而不是使用`azureml-datasets`封裝來建立資料集，您將無法供日後使用快照集或已建立版本的資料集。

由於管線中的寫入步驟數目沒有任何限制，因此您能輕鬆地新增其他寫入步驟，取得要進行疑難排解或針對其他管線使用的中繼結果。

每次執行寫入步驟時，就會完整提取資料流程中的資料。 例如，具有三個寫入步驟的資料流程會讀取及處理資料集中的每一筆記錄三次。

## <a name="supported-data-types-and-location"></a>支援的資料類型與位置

支援下列檔案格式
-   符號分隔檔案 (CSV、TSV 等)
-   Parquet 檔案

您可以使用 Azure Machine Learning 資料準備 Python SDK，來撰寫資料：
+ 本機檔案系統
+ Azure Blob 儲存體
+ Azure Data Lake 儲存體

## <a name="spark-considerations"></a>Spark 考量

在 Spark 中執行資料流程時，您必須寫入空白的資料夾。 嘗試寫入現有資料夾的動作導致失敗。 請確定您的目標資料夾是空的，或針對每次執行使用不同的目標位置，否則寫入將會失敗。

## <a name="monitoring-write-operations"></a>監視寫入作業

為了方便起見，在完成寫入之後，會產生名為 SUCCESS 的標記檔案。 它的存在可協助您識別中繼寫入何時完成，而不需要等待整個管線完成。

## <a name="example-write-code"></a>範例寫入程式碼

對於此範例中，開始先將資料載入至資料的流程使用`auto_read_file()`。 您可以不同的格式重複使用此資料。

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(5)
```

範例輸出︰

| | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | None | 否 | 否 | ENRS | NaN | NaN | NaN |   
|1| 10003.0 | 99999.0 | None | 否 | 否 | ENSO | NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | None | 否 | JN | ENJA | 70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | None | 否 | 否 |      | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | None | 否 | 否 | ENSO | 59783.0 | 5350.0 |  500.0|

### <a name="delimited-file-example"></a>以符號分隔的檔案範例

下列程式碼會使用[ `write_to_csv()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-csv-directory-path--datadestination--separator--str--------na--str----na---error--str----error------azureml-dataprep-api-dataflow-dataflow)函式來將資料寫入至分隔檔案中。

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

範例輸出︰

| | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | ERROR | 否 | 否 | ENRS | NaN    | NaN | NaN |   
|1| 10003.0 | 99999.0 | ERROR | 否 | 否 | ENSO |    NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | ERROR | 否 | JN | ENJA |    70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | ERROR | 否 | 否 |     | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | ERROR | 否 | 否 | ENSO |    59783.0 | 5350.0 |  500.0|

在上面的輸出中，由於未正確剖析數字，因此數值欄中出現數個錯誤。 寫入 CSV 時，Null 值預設會取代為字串 "ERROR"。

請新增參數作為寫入呼叫的一部分，並指定要用來代表 Null 值的字串。

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

上述程式碼會產生以下輸出：

| | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | BadData | 否 | 否 | ENRS | NaN  | NaN | NaN |   
|1| 10003.0 | 99999.0 | BadData | 否 | 否 | ENSO |  NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | BadData | 否 | JN | ENJA |  70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | BadData | 否 | 否 |   | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | BadData | 否 | 否 | ENSO |  59783.0 | 5350.0 |  500.0|

### <a name="parquet-file-example"></a>Parquet 檔案範例

類似於`write_to_csv()`，則[ `write_to_parquet()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-parquet-file-path--typing-union--datadestination--nonetype----none--directory-path--typing-union--datadestination--nonetype----none--single-file--bool---false--error--str----error---row-groups--int---0-----azureml-dataprep-api-dataflow-dataflow)函式會傳回新的資料流，寫入 Parquet 步驟的資料流執行時所執行。

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

執行資料流程，以開始寫入作業。

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(5)
```

上述程式碼會產生以下輸出：

|   | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |-------- |
|0| 10000.0 | 99999.0 | MiscreantData | 否 | 否 | ENRS | MiscreantData | MiscreantData | MiscreantData |
|1| 10003.0 | 99999.0 | MiscreantData | 否 | 否 | ENSO | MiscreantData | MiscreantData | MiscreantData |   
|2| 10010.0 | 99999.0 | MiscreantData | 否| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0 | 99999.0 | MiscreantData | 否| 否| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0 | 99999.0 | MiscreantData | 否| 否| ENSO|   59783.0|    5350.0| 500.0|

## <a name="configure-data-for-automated-machine-learning-training"></a>設定自動化的機器學習服務訓練資料

傳遞至您新寫入的資料檔案[ `AutoMLConfig` ](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#automlconfig)以準備進行自動化的機器學習服務訓練的物件。 

下列程式碼範例說明如何將您的資料流程轉換的 Pandas 資料框架和接下來，將它分割為訓練和測試自動化的機器學習服務訓練資料集。

```Python
from azureml.train.automl import AutoMLConfig
from sklearn.model_selection import train_test_split

dflow = dprep.auto_read_file(path="")
X_dflow = dflow.keep_columns([feature_1,feature_2, feature_3])
y_dflow = dflow.keep_columns("target")

X_df = X_dflow.to_pandas_dataframe()
y_df = y_dflow.to_pandas_dataframe()

X_train, X_test, y_train, y_test = train_test_split(X_df, y_df, test_size=0.2, random_state=223)

# flatten y_train to 1d array
y_train.values.flatten()

#configure 
automated_ml_config = AutoMLConfig(task = 'regression',
                               X = X_train.values,  
                   y = y_train.values.flatten(),
                   iterations = 30,
                       Primary_metric = "AUC_weighted",
                       n_cross_validation = 5
                       )

```

如果您不需要任何中繼資料準備步驟，如上述範例所示，您可以傳遞至資料流程，直接將`AutoMLConfig`。

```Python
automated_ml_config = AutoMLConfig(task = 'regression', 
                   X = X_dflow,   
                   y = y_dflow, 
                   iterations = 30, 
                   Primary_metric = "AUC_weighted",
                   n_cross_validation = 5
                   )
```

## <a name="next-steps"></a>後續步驟
* 請參閱 SDK[概觀](https://aka.ms/data-prep-sdk)設計模式和使用方式範例 
* 將自動化的機器學習[教學課程](tutorial-auto-train-models.md)迴歸模型範例
