---
title: 迴歸模型教學課程：準備資料
titleSuffix: Azure Machine Learning service
description: 在本教學課程的第一個部分中，您將了解如何使用 Azure Machine Learning SDK 來準備建立迴歸模型所需的 Python 資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sihhu
ms.author: MayMSFT
ms.reviewer: trbye
ms.date: 03/29/2019
ms.custom: seodec18
ms.openlocfilehash: d85f1ddcfe264e027a0f9d6c5f291d0005cad67c
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665010"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>教學課程：準備建立迴歸模型所需的資料

在本教學課程中，您將了解如何[適用於 Python 的 Azure Machine Learning 資料準備 SDK](https://aka.ms/data-prep-sdk) 來準備建立迴歸模型所需的資料。 您將執行各種轉換來篩選和結合兩個不同的 NYC 計程車資料集。

本教學課程是**兩部分教學課程系列的第一部分**。 完成教學課程系列之後，您可以訓練模型的資料特性，以預測計程車車程的費用。 這些特性包括上車的日期和時間、乘客數和上車地點。

在本教學課程中，您：

> [!div class="checklist"]
> * 設定 Python 環境並匯入套件。
> * 載入具有不同欄位名稱的兩個資料集。
> * 清理資料以移除異常的部分。
> * 使用智慧型轉換來轉換資料，以建立新特性。
> * 儲存要在迴歸模型中使用的資料流程物件。

## <a name="prerequisites"></a>必要條件

請跳至[設定您的開發環境](#start)閱讀完整的 Notebook 步驟，或依照下列指示取得 Notebook，並在 Azure Notebooks 或您自己的 Notebook 伺服器上加以執行。 若要執行 Notebook，您將需要：

* 已安裝下列項目的 Python 3.6 Notebook 伺服器：
    * 適用於 Python 的 Azure Machine Learning 資料準備 SDK
* 教學課程筆記本

請從以下各節取得前述所有必要項目。

* 使用 [Azure Notebooks](#azure)
* 使用[您自己的 Notebook 伺服器](#server)

### <a name="azure"></a>使用 Azure Notebooks：雲端中的免費 Jupyter Notebook

您可以輕鬆地開始使用 Azure Notebooks！ 我們已為您在 [Azure Notebooks](https://notebooks.azure.com/) 上安裝並設定 Azure Machine Learning 資料準備 SDK。 Azure 服務會自動管理安裝和未來的更新。

完成下列步驟之後，請在您的**開始使用**專案中執行 **tutorials/regression-part1-data-prep.ipynb** Notebook。

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

### <a name="server"></a>使用您自己的 Jupyter Notebook 伺服器

使用下列步驟在您的電腦上建立本機 Jupyter Notebook 伺服器。  完成所有步驟後，請執行 **tutorials/regression-part1-data-prep.ipynb** 筆記本。

1. 完成 [[Azure Machine Learning Python 快速入門](quickstart-run-local-notebook.md)](setup-create-workspace.md#python)中的安裝步驟，以建立 Miniconda 環境。  如果您希望的話，也可以放心地略過**建立工作區**一節，但您在本教學課程系列的[第 2 部分](tutorial-auto-train-models.md)會需要該項目。
1. 使用 `pip install azureml-dataprep` 在環境中安裝資料準備 SDK。
1. 複製 [GitHub 存放庫](https://aka.ms/aml-notebooks)。

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 從複製的目錄中啟動 Notebook 伺服器。

    ```shell
    jupyter notebook
    ```

## <a name="start"></a>設定您的開發環境

針對您開發工作的所有設定都可以在 Python Notebook 中完成。 設定包含下列動作：

* 安裝 SDK
* 匯入 Python 套件

### <a name="install-and-import-packages"></a>安裝並匯入套件

如果您還沒有必要的套件，請使用下列步驟加以安裝。

```shell
pip install azureml-dataprep==1.0.17
```

匯入 SDK。

```python
import azureml.dataprep as dprep
```

> [!IMPORTANT]
> 請確定您安裝 1.0.17 版。 最新版本 1.1.0 不適用於本教學課程

## <a name="load-data"></a>載入資料

將兩個不同的 NYC 計程車資料集下載到資料流程物件中。 這些資料集包含稍有不同的欄位。 方法 `auto_read_file()` 會自動辨識輸入檔類型。

```python
from IPython.display import display
dataset_root = "https://dprepdata.blob.core.windows.net/demo"

green_path = "/".join([dataset_root, "green-small/*"])
yellow_path = "/".join([dataset_root, "yellow-small/*"])

green_df_raw = dprep.read_csv(path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file automatically identifies and parses the file type, which is useful when you don't know the file type.
yellow_df_raw = dprep.auto_read_file(path=yellow_path)

display(green_df_raw.head(5))
display(yellow_df_raw.head(5))
```

`Dataflow` 物件類似於資料框架，代表對資料所做的一系列惰性評估、不可變的作業。 藉由叫用不同的轉換和可用的篩選方法，可以新增作業。 將作業新增至 `Dataflow` 的結果一律是新的 `Dataflow` 物件。

## <a name="cleanse-data"></a>清理資料

現在，您可以在一些變數中填入將套用至所有資料流程的捷徑轉換。 變數 `drop_if_all_null` 會用來刪除所有欄位皆為 Null 的記錄。 變數 `useful_columns` 會保存每個資料流程中保留的資料行描述陣列。

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

您會先處理綠色計程車資料，並使其成為可與黃色計程車資料結合的有效型態。 使用您所建立的捷徑轉換變數呼叫 `replace_na()`、`drop_nulls()` 和 `keep_columns()` 函式。 此外，請將資料框架中的所有資料行重新命名，使其符合 `useful_columns` 變數中的名稱。


```python
green_df = (green_df_raw
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "VendorID": "vendor",
        "lpep_pickup_datetime": "pickup_datetime",
        "Lpep_dropoff_datetime": "dropoff_datetime",
        "lpep_dropoff_datetime": "dropoff_datetime",
        "Store_and_fwd_flag": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Pickup_longitude": "pickup_longitude",
        "Pickup_latitude": "pickup_latitude",
        "Dropoff_longitude": "dropoff_longitude",
        "Dropoff_latitude": "dropoff_latitude",
        "Passenger_count": "passengers",
        "Fare_amount": "cost",
        "Trip_distance": "distance"
     })
    .keep_columns(columns=useful_columns))
green_df.head(5)
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
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>dropoff_datetime</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 08:14:37</td>
      <td>2013-08-01 09:09:06</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>21.25</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 09:13:00</td>
      <td>2013-08-01 11:38:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>.00</td>
      <td>74.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-01 09:48:00</td>
      <td>2013-08-01 09:49:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-01 10:38:35</td>
      <td>2013-08-01 10:38:51</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>3.25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-01 11:51:45</td>
      <td>2013-08-01 12:03:52</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>8.5</td>
    </tr>
  </tbody>
</table>
</div>

對黃色計程車資料執行相同的轉換步驟。 這些函式可確保會從資料集中移除 Null 資料，因此有助於提升機器學習模型的精確度。

```python
yellow_df = (yellow_df_raw
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "vendor_name": "vendor",
        "VendorID": "vendor",
        "vendor_id": "vendor",
        "Trip_Pickup_DateTime": "pickup_datetime",
        "tpep_pickup_datetime": "pickup_datetime",
        "Trip_Dropoff_DateTime": "dropoff_datetime",
        "tpep_dropoff_datetime": "dropoff_datetime",
        "store_and_forward": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Start_Lon": "pickup_longitude",
        "Start_Lat": "pickup_latitude",
        "End_Lon": "dropoff_longitude",
        "End_Lat": "dropoff_latitude",
        "Passenger_Count": "passengers",
        "passenger_count": "passengers",
        "Fare_Amt": "cost",
        "fare_amount": "cost",
        "Trip_Distance": "distance",
        "trip_distance": "distance"
    })
    .keep_columns(columns=useful_columns))
yellow_df.head(5)
```

對綠色計程車資料呼叫 `append_rows()` 函式，以附加黃色計程車資料。 此時會建立新的合併資料框架。

```python
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>轉換類型和篩選

檢查上車和下車座標摘要統計資料，以了解如何資料的分布情形。 首先，請定義將經、緯度欄位變更為十進位類型的 `TypeConverter` 物件。 接著，呼叫 `keep_columns()` 函式將輸出限定於經、緯度欄位，然後呼叫 `get_profile()` 函式。 這些函式呼叫會建立資料流程的精簡檢視而僅顯示經緯度欄位，以方便評估遺漏或超出範圍的座標。


```python
decimal_type = dprep.TypeConverter(data_type=dprep.FieldType.DECIMAL)
combined_df = combined_df.set_column_types(type_conversions={
    "pickup_longitude": decimal_type,
    "pickup_latitude": decimal_type,
    "dropoff_longitude": decimal_type,
    "dropoff_latitude": decimal_type
})
combined_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>類型</th>
      <th>Min</th>
      <th>max</th>
      <th>Count</th>
      <th>遺漏計數</th>
      <th>未遺漏計數</th>
      <th>遺漏百分比</th>
      <th>錯誤計數</th>
      <th>空白計數</th>
      <th>0.1% 分位數</th>
      <th>1% 分位數</th>
      <th>5% 分位數</th>
      <th>25% 分位數</th>
      <th>50% 分位數</th>
      <th>75% 分位數</th>
      <th>95% 分位數</th>
      <th>99% 分位數</th>
      <th>99.9% 分位數</th>
      <th>標準差</th>
      <th>平均值</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-88.114046</td>
      <td>-73.961840</td>
      <td>-73.961964</td>
      <td>-73.947693</td>
      <td>-73.922097</td>
      <td>-73.846670</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.792672</td>
      <td>-68.833579</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>40.919121</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.682889</td>
      <td>40.675541</td>
      <td>40.721075</td>
      <td>40.756159</td>
      <td>40.803909</td>
      <td>40.849406</td>
      <td>40.870681</td>
      <td>40.891244</td>
      <td>10.345967</td>
      <td>37.936742</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-87.699611</td>
      <td>-73.984734</td>
      <td>-73.985777</td>
      <td>-73.956250</td>
      <td>-73.928948</td>
      <td>-73.866208</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.696526</td>
      <td>-68.896978</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>41.008934</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.662763</td>
      <td>40.654851</td>
      <td>40.717821</td>
      <td>40.756534</td>
      <td>40.784688</td>
      <td>40.852437</td>
      <td>40.879289</td>
      <td>40.937291</td>
      <td>10.290780</td>
      <td>37.963774</td>
    </tr>
  </tbody>
</table>



在摘要統計資料輸出中，您會看到有遺漏的座標和不在紐約市內的座標 (這取決於主觀分析)。 請篩選出不在市界內的位置座標。 在 `filter()` 函式內鏈結資料行篩選命令，並定義每個欄位的下限和上限。 然後，再次呼叫 `get_profile()` 函式以驗證轉換。


```python
latlong_filtered_df = (combined_df
    .drop_nulls(
        columns=["pickup_longitude", "pickup_latitude", "dropoff_longitude", "dropoff_latitude"],
        column_relationship=dprep.ColumnRelationship(dprep.ColumnRelationship.ANY)
    )
    .filter(dprep.f_and(
        dprep.col("pickup_longitude") <= -73.72,
        dprep.col("pickup_longitude") >= -74.09,
        dprep.col("pickup_latitude") <= 40.88,
        dprep.col("pickup_latitude") >= 40.53,
        dprep.col("dropoff_longitude") <= -73.72,
        dprep.col("dropoff_longitude") >= -74.09,
        dprep.col("dropoff_latitude") <= 40.88,
        dprep.col("dropoff_latitude") >= 40.53
    )))
latlong_filtered_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>類型</th>
      <th>Min</th>
      <th>max</th>
      <th>Count</th>
      <th>遺漏計數</th>
      <th>未遺漏計數</th>
      <th>遺漏百分比</th>
      <th>錯誤計數</th>
      <th>空白計數</th>
      <th>0.1% 分位數</th>
      <th>1% 分位數</th>
      <th>5% 分位數</th>
      <th>25% 分位數</th>
      <th>50% 分位數</th>
      <th>75% 分位數</th>
      <th>95% 分位數</th>
      <th>99% 分位數</th>
      <th>99.9% 分位數</th>
      <th>標準差</th>
      <th>平均值</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.078156</td>
      <td>-73.736481</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.076314</td>
      <td>-73.962542</td>
      <td>-73.962893</td>
      <td>-73.948975</td>
      <td>-73.927856</td>
      <td>-73.866662</td>
      <td>-73.830438</td>
      <td>-73.823160</td>
      <td>-73.769750</td>
      <td>0.048711</td>
      <td>-73.913865</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.575485</td>
      <td>40.879852</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.632884</td>
      <td>40.713105</td>
      <td>40.711600</td>
      <td>40.721403</td>
      <td>40.758142</td>
      <td>40.805145</td>
      <td>40.848855</td>
      <td>40.867567</td>
      <td>40.877690</td>
      <td>0.048348</td>
      <td>40.765226</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.085747</td>
      <td>-73.720871</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.078828</td>
      <td>-73.985650</td>
      <td>-73.985813</td>
      <td>-73.959041</td>
      <td>-73.936681</td>
      <td>-73.884846</td>
      <td>-73.815507</td>
      <td>-73.776697</td>
      <td>-73.733471</td>
      <td>0.055961</td>
      <td>-73.920718</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.583530</td>
      <td>40.879734</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.597741</td>
      <td>40.695376</td>
      <td>40.695115</td>
      <td>40.727549</td>
      <td>40.758160</td>
      <td>40.788378</td>
      <td>40.850372</td>
      <td>40.867968</td>
      <td>40.878586</td>
      <td>0.050462</td>
      <td>40.759487</td>
    </tr>
  </tbody>
</table>

### <a name="split-and-rename-columns"></a>分割並重新命名資料行

查看 `store_forward` 資料行的資料設定檔。 此欄位是一個布林旗標；當計程車在載客之後無法連線到伺服器，因而必須將車程資料儲存在記憶體中，稍後再將其轉送至連線的伺服器時，此欄位會顯示為 `Y`。


```python
latlong_filtered_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>類型</th>
      <th>Min</th>
      <th>max</th>
      <th>Count</th>
      <th>遺漏計數</th>
      <th>未遺漏計數</th>
      <th>遺漏百分比</th>
      <th>錯誤計數</th>
      <th>空白計數</th>
      <th>0.1% 分位數</th>
      <th>1% 分位數</th>
      <th>5% 分位數</th>
      <th>25% 分位數</th>
      <th>50% 分位數</th>
      <th>75% 分位數</th>
      <th>95% 分位數</th>
      <th>99% 分位數</th>
      <th>99.9% 分位數</th>
      <th>標準差</th>
      <th>平均值</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>Y</td>
      <td>7059.0</td>
      <td>99.0</td>
      <td>6960.0</td>
      <td>0.014025</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>



請注意，`store_forward` 資料行中的資料設定檔輸出會顯示資料不一致，而且有遺漏值或 Null 值。 請使用 `replace()` 和 `fill_nulls()` 函式將這些值取代為字串 "N"：


```python
replaced_stfor_vals_df = latlong_filtered_df.replace(columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

在 `distance` 欄位上執行 `replace` 函式。 此函式會將錯誤標示為 `.00` 的距離值重新格式化，並在所有 Null 中填入零。 將 `distance` 欄位轉換為數值格式。 不正確的資料點很可能是計程車資料收集系統中的異常。


```python
replaced_distance_vals_df = replaced_stfor_vals_df.replace(columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
replaced_distance_vals_df = replaced_distance_vals_df.to_number(["distance"])
```

將上車和下車的日期時間值分割到各自的日期資料行和時間資料行中。 使用 `split_column_by_example()` 函式進行分割。 在此案例中，會省略函式 `split_column_by_example()` 的選擇性 `example` 參數。 因此，函式將根據資料自動決定分割的目的地。


```python
time_split_df = (replaced_distance_vals_df
    .split_column_by_example(source_column="pickup_datetime")
    .split_column_by_example(source_column="dropoff_datetime"))
time_split_df.head(5)
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
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_datetime_1</th>
      <th>pickup_datetime_2</th>
      <th>dropoff_datetime</th>
      <th>dropoff_datetime_1</th>
      <th>dropoff_datetime_2</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>N</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>2013-08-01</td>
      <td>17:24:00</td>
      <td>2013-08-01 17:25:00</td>
      <td>2013-08-01</td>
      <td>17:25:00</td>
      <td>N</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>2013-08-06</td>
      <td>06:51:19</td>
      <td>2013-08-06 06:51:36</td>
      <td>2013-08-06</td>
      <td>06:51:36</td>
      <td>N</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>2013-08-06</td>
      <td>13:26:34</td>
      <td>2013-08-06 13:26:57</td>
      <td>2013-08-06</td>
      <td>13:26:57</td>
      <td>N</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>2013-08-06</td>
      <td>13:27:53</td>
      <td>2013-08-06 13:28:08</td>
      <td>2013-08-06</td>
      <td>13:28:08</td>
      <td>N</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

將 `split_column_by_example()` 函式產生的資料行重新命名，以使用有意義的名稱。

```python
renamed_col_df = (time_split_df
    .rename_columns(column_pairs={
        "pickup_datetime_1": "pickup_date",
        "pickup_datetime_2": "pickup_time",
        "dropoff_datetime_1": "dropoff_date",
        "dropoff_datetime_2": "dropoff_time"
    }))
renamed_col_df.head(5)
```

呼叫 `get_profile()` 函式，以檢視所有清理步驟完成後的完整摘要統計資料。

```python
renamed_col_df.get_profile()
```

## <a name="transform-data"></a>轉換資料

將上車和下車日期進一步分割成週中的日期、月中的日期和月份值。 若要取得「週中的日期」值，請使用 `derive_column_by_example()` 函式。 此函數會採用定義輸入資料和所需輸出的範例物件所組成的陣列參數。 此函式會自動判斷您所需的轉換。 針對上車和下車時間資料行，請使用不含範例參數的 `split_column_by_example()` 函式，將時間分割成小時、分鐘和秒。

在您產生這些新特性後，請使用 `drop_columns()` 函式刪除原始欄位，因為您所需的是新產生的特性。 將其餘欄位重新命名，以使用有意義的描述。

以此方式轉換資料以建立以時間為基礎的新特性，可改善機器學習模型的精確度。 例如，產生工作日的新特性，將有助於建立星期幾與計程車車資價格之間的關聯性；此價格常會因為一週中的某幾天需求較高而較為昂貴。


```python
transformed_features_df = (renamed_col_df
    .derive_column_by_example(
        source_columns="pickup_date",
        new_column_name="pickup_weekday",
        example_data=[("2009-01-04", "Sunday"), ("2013-08-22", "Thursday")]
    )
    .derive_column_by_example(
        source_columns="dropoff_date",
        new_column_name="dropoff_weekday",
        example_data=[("2013-08-22", "Thursday"), ("2013-11-03", "Sunday")]
    )

    .split_column_by_example(source_column="pickup_time")
    .split_column_by_example(source_column="dropoff_time")
    # The following two calls to split_column_by_example reference the column names generated from the previous two calls.
    .split_column_by_example(source_column="pickup_time_1")
    .split_column_by_example(source_column="dropoff_time_1")
    .drop_columns(columns=[
        "pickup_date", "pickup_time", "dropoff_date", "dropoff_time",
        "pickup_date_1", "dropoff_date_1", "pickup_time_1", "dropoff_time_1"
    ])

    .rename_columns(column_pairs={
        "pickup_date_2": "pickup_month",
        "pickup_date_3": "pickup_monthday",
        "pickup_time_1_1": "pickup_hour",
        "pickup_time_1_2": "pickup_minute",
        "pickup_time_2": "pickup_second",
        "dropoff_date_2": "dropoff_month",
        "dropoff_date_3": "dropoff_monthday",
        "dropoff_time_1_1": "dropoff_hour",
        "dropoff_time_1_2": "dropoff_minute",
        "dropoff_time_2": "dropoff_second"
    }))

transformed_features_df.head(5)
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
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_weekday</th>
      <th>pickup_hour</th>
      <th>pickup_minute</th>
      <th>pickup_second</th>
      <th>dropoff_datetime</th>
      <th>dropoff_weekday</th>
      <th>dropoff_hour</th>
      <th>dropoff_minute</th>
      <th>dropoff_second</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>2013-08-01 17:22:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>N</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>24</td>
      <td>00</td>
      <td>2013-08-01 17:25:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>25</td>
      <td>00</td>
      <td>N</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>Tuesday</td>
      <td>06</td>
      <td>51</td>
      <td>19</td>
      <td>2013-08-06 06:51:36</td>
      <td>Tuesday</td>
      <td>06</td>
      <td>51</td>
      <td>36</td>
      <td>N</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>26</td>
      <td>34</td>
      <td>2013-08-06 13:26:57</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>26</td>
      <td>57</td>
      <td>N</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>27</td>
      <td>53</td>
      <td>2013-08-06 13:28:08</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>28</td>
      <td>08</td>
      <td>N</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

請注意，資料會顯示從衍生的轉換產生的上車和下車日期和時間元件正確無誤。 `pickup_datetime` 和 `dropoff_datetime` 資料行已不再需要，請加以捨棄 (小時、分和秒之類的細微時間特性較適合用於模型定型)。


```python
processed_df = transformed_features_df.drop_columns(columns=["pickup_datetime", "dropoff_datetime"])
```

使用型別推斷功能自動檢查每個欄位的資料類型，並顯示推斷結果。


```python
type_infer = processed_df.builders.set_column_types()
type_infer.learn()
type_infer
```

`type_infer` 產生的輸出如下。

    Column types conversion candidates:
    'pickup_weekday': [FieldType.STRING],
    'pickup_hour': [FieldType.DECIMAL],
    'pickup_minute': [FieldType.DECIMAL],
    'pickup_second': [FieldType.DECIMAL],
    'dropoff_hour': [FieldType.DECIMAL],
    'dropoff_minute': [FieldType.DECIMAL],
    'dropoff_second': [FieldType.DECIMAL],
    'store_forward': [FieldType.STRING],
    'pickup_longitude': [FieldType.DECIMAL],
    'dropoff_longitude': [FieldType.DECIMAL],
    'passengers': [FieldType.DECIMAL],
    'distance': [FieldType.DECIMAL],
    'vendor': [FieldType.STRING],
    'dropoff_weekday': [FieldType.STRING],
    'pickup_latitude': [FieldType.DECIMAL],
    'dropoff_latitude': [FieldType.DECIMAL],
    'cost': [FieldType.DECIMAL]

推斷結果依資料來看是正確的。 接著請將類型轉換套用至資料流程。


```python
type_converted_df = type_infer.to_dataflow()
type_converted_df.get_profile()
```

在封裝資料流程之前，請先在資料集上執行兩個最終篩選條件。 若要排除未正確擷取的資料點，請在 `cost` 和 `distance` 變數值皆大於零的記錄上篩選資料流程。 此步驟可大幅改善機器學習模型的精確度，因為成本或距離為零的資料點代表失去預測精確度的主要極端值。

```python
final_df = type_converted_df.filter(dprep.col("distance") > 0)
final_df = final_df.filter(dprep.col("cost") > 0)
```

現在，您已完整轉換並備妥要在機器學習模型中使用的資料流程物件。 SDK 包含物件序列化功能，其使用方式如下列程式碼所示。

```python
import os

file_path = os.path.join(os.getcwd(), "dflows.dprep")
package = dprep.Package([final_df])
package.save(file_path)
```

## <a name="clean-up-resources"></a>清除資源

若要繼續進行本教學課程的第二部分，您必須在目前的目錄中保留 **dflows.dprep** 檔案。

如果您不打算繼續進行第二部分，請在目前的目錄中刪除 **dflows.dprep** 檔案。 無論您是在本機還是 [Azure Notebooks](https://notebooks.azure.com/) 中執行，均請刪除此檔案。

## <a name="next-steps"></a>後續步驟

在本教學課程的第一部分中，您已：

> [!div class="checklist"]
> * 設定您的開發環境。
> * 載入並清理資料集。
> * 使用智慧轉換根據範例預測您的邏輯。
> * 合併並封裝機器學習訓練的資料集。

您已準備就緒，可在教學課程的第二部分中使用訓練資料：

> [!div class="nextstepaction"]
> [教學課程 (第二部分)：訓練迴歸模型](tutorial-auto-train-models.md)
