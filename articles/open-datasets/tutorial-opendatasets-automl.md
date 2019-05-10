---
title: 教學課程：擴充自動化機器學習模型
titleSuffix: Azure Open Datasets
description: 了解如何利用 Azure 開放資料集的便利性以及 Azure Machine Learning 服務的強大功能，來建立迴歸模型以預測 NYC 計程車車資價格。
services: open-datasets
ms.service: open-datasets
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 05/02/2019
ms.openlocfilehash: e753793b5da59d09a21991831046a43899c62ef2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027514"
---
# <a name="tutorial-build-a-regression-model-with-automated-machine-learning-and-open-datasets"></a>教學課程：使用自動化機器學習和開放資料集來建置迴歸模型

在此教學課程中，您將利用 Azure 開放資料集的便利性以及 Azure Machine Learning 服務的強大功能，來建立迴歸模型以預測 NYC 計程車車資價格。 輕鬆下載可公開取得的計程車、假日及氣象資料，並使用 Azure Machine Learning 服務來設定自動化機器學習實驗。 此程序接受定型資料和組態設定，並自動逐一查看不同功能正規化/標準化方法、模型及超參數設定的組合，以獲得最佳模型。

在此教學課程中，您將了解下列工作：

- 設定 Azure Machine Learning 服務工作區
- 設定本機 Python 環境
- 使用 Azure 開放資料集存取、轉換及聯結資料
- 為自動化機器學習迴歸模型定型
- 計算模型精確度

## <a name="prerequisites"></a>先決條件

此教學課程需要下列先決條件。

* Azure Machine Learning 服務工作區
* Python 3.6 環境

### <a name="create-a-workspace"></a>建立工作區

如果您還沒有工作區，請遵循[指示](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace#portal)以透過 Azure 入口網站建立工作區。 建立之後，記下工作區名稱、資源群組名稱和訂用帳戶識別碼。

### <a name="create-a-python-environment"></a>建立 Python 環境

此範例搭配 Jupyter 筆記本使用 Anaconda 環境，但您可以在任何 3.6.x 環境中，使用任何文字編輯器或 IDE 來執行此程式碼。 使用下列步驟來建立新的開發環境。

1. 如果您還沒有開發環境，請[下載](https://www.anaconda.com/distribution/)並安裝 Anaconda，然後選擇 [Python 3.7 版]。
1. 開啟 Anaconda 提示，然後建立新環境。 建立環境可能需要幾分鐘的時間，因為需要下載元件和套件。
    ```
    conda create -n tutorialenv python=3.6.5
    ```
1. 啟用環境。
    ```
    conda activate tutorialenv
    ```
1. 啟用環境特定的 IPython 核心。
    ```
    conda install notebook ipykernel
    ```
1. 建立核心。
    ```
    ipython kernel install --user
    ```
1. 安裝此教學課程所需的套件。 這些套件很大，將需要 5-10 分鐘的時間來安裝。
    ```
    pip install azureml-sdk[automl] azureml-contrib-opendatasets
    ```
1. 從您的環境啟動筆記本核心。
    ```
    jupyter notebook
    ```

完成這些步驟之後，複製[開放資料集筆記本存放庫](https://github.com/Azure/OpenDatasetsNotebooks) \(英文\)，然後開啟 **tutorials/taxi-automl/01-tutorial-opendatasets-automl.ipynb** 筆記本來執行它。

## <a name="download-and-prepare-data"></a>下載並準備資料

匯入必要的套件。 開放資料集套件包含一個代表每個資料來源 (例如 `NycTlcGreen`) 的類別，以在下載之前輕鬆篩選日期參數。


```python
from azureml.contrib.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

從建立用來保存計程車資料的資料架構開始。 在非 Spark 環境中運作時，開放資料集僅允許一次下載一個月含特定類別的資料，以避免使用大型資料集時發生發生 `MemoryError`。 若要下載一年份的計程車資料，請反覆地一次擷取一個月的資料，並且在將該資料附加到 `green_taxi_df` 之前，從每個月中隨機取樣 2000 筆記錄，以避免使資料架構膨脹。 接著預覽資料。

>[!NOTE]
> 開放資料集具有可在 Spark 環境中運作的鏡像類別，在這類環境中，不需考量資料大小和記憶體。

```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016","%m/%d/%Y")
end = datetime.strptime("1/31/2016","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
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
      <th>vendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>paymentType</th>
      <th>fareAmount</th>
      <th>extra</th>
      <th>mtaTax</th>
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0.98</td>
      <td>None</td>
      <td>None</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>...</td>
      <td>2.0</td>
      <td>7.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>8.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01 21:53:28</td>
      <td>2016-01-02 00:00:00</td>
      <td>1</td>
      <td>3.08</td>
      <td>None</td>
      <td>None</td>
      <td>-73.979973</td>
      <td>40.677071</td>
      <td>-73.934349</td>
      <td>...</td>
      <td>2.0</td>
      <td>11.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01 00:50:23</td>
      <td>2016-01-01 01:05:37</td>
      <td>1</td>
      <td>2.44</td>
      <td>None</td>
      <td>None</td>
      <td>-73.863045</td>
      <td>40.882923</td>
      <td>-73.839836</td>
      <td>...</td>
      <td>2.0</td>
      <td>12.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2.87</td>
      <td>None</td>
      <td>None</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>...</td>
      <td>1.0</td>
      <td>12.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0.50</td>
      <td>None</td>
      <td>None</td>
      <td>-73.942589</td>
      <td>40.841423</td>
      <td>-73.943672</td>
      <td>...</td>
      <td>2.0</td>
      <td>4.5</td>
      <td>0.0</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>5.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1049431</th>
      <td>2</td>
      <td>2016-01-29 17:16:18</td>
      <td>2016-01-29 17:27:52</td>
      <td>1</td>
      <td>2.25</td>
      <td>None</td>
      <td>None</td>
      <td>-73.830894</td>
      <td>40.759434</td>
      <td>-73.842422</td>
      <td>...</td>
      <td>2.0</td>
      <td>10.5</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1.93</td>
      <td>None</td>
      <td>None</td>
      <td>-73.927109</td>
      <td>40.762848</td>
      <td>-73.909302</td>
      <td>...</td>
      <td>1.0</td>
      <td>8.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0.80</td>
      <td>None</td>
      <td>None</td>
      <td>-73.881195</td>
      <td>40.741779</td>
      <td>-73.872086</td>
      <td>...</td>
      <td>2.0</td>
      <td>6.5</td>
      <td>0.0</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>7.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>303784</th>
      <td>2</td>
      <td>2016-01-25 18:13:47</td>
      <td>2016-01-25 18:23:50</td>
      <td>1</td>
      <td>1.04</td>
      <td>None</td>
      <td>None</td>
      <td>-73.954376</td>
      <td>40.805729</td>
      <td>-73.939117</td>
      <td>...</td>
      <td>1.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>1.5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2.82</td>
      <td>None</td>
      <td>None</td>
      <td>-73.845200</td>
      <td>40.722134</td>
      <td>-73.810638</td>
      <td>...</td>
      <td>1.0</td>
      <td>13.0</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16.3</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>10 個資料列 × 23 個資料行</p>
</div>



既然已載入初始資料，請定義一個函式，從搭車日期時間欄位建立各種以時間為基礎的功能。 這將會建立適用於月份數字、當月某日、週中的日及當天特定時間的新欄位，並且可讓模型將以時間為基礎的季節性當作考量因素。 此函式也會新增適用於國碼/地區碼的靜態功能來聯結假日資料。 在資料架構上使用 `apply()` 函式，以將 `build_time_features()` 函式反覆地套用至計程車資料中的每個資料列。


```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour
    country_code = "US"

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day, country_code))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day", "country_code"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
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
      <th>vendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0.98</td>
      <td>None</td>
      <td>None</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>8.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>US</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01 21:53:28</td>
      <td>2016-01-02 00:00:00</td>
      <td>1</td>
      <td>3.08</td>
      <td>None</td>
      <td>None</td>
      <td>-73.979973</td>
      <td>40.677071</td>
      <td>-73.934349</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>US</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01 00:50:23</td>
      <td>2016-01-01 01:05:37</td>
      <td>1</td>
      <td>2.44</td>
      <td>None</td>
      <td>None</td>
      <td>-73.863045</td>
      <td>40.882923</td>
      <td>-73.839836</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>US</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2.87</td>
      <td>None</td>
      <td>None</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>US</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0.50</td>
      <td>None</td>
      <td>None</td>
      <td>-73.942589</td>
      <td>40.841423</td>
      <td>-73.943672</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>5.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>US</td>
    </tr>
    <tr>
      <th>1049431</th>
      <td>2</td>
      <td>2016-01-29 17:16:18</td>
      <td>2016-01-29 17:27:52</td>
      <td>1</td>
      <td>2.25</td>
      <td>None</td>
      <td>None</td>
      <td>-73.830894</td>
      <td>40.759434</td>
      <td>-73.842422</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>29</td>
      <td>4</td>
      <td>17</td>
      <td>US</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1.93</td>
      <td>None</td>
      <td>None</td>
      <td>-73.927109</td>
      <td>40.762848</td>
      <td>-73.909302</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>14</td>
      <td>3</td>
      <td>0</td>
      <td>US</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0.80</td>
      <td>None</td>
      <td>None</td>
      <td>-73.881195</td>
      <td>40.741779</td>
      <td>-73.872086</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>7.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>9</td>
      <td>5</td>
      <td>14</td>
      <td>US</td>
    </tr>
    <tr>
      <th>303784</th>
      <td>2</td>
      <td>2016-01-25 18:13:47</td>
      <td>2016-01-25 18:23:50</td>
      <td>1</td>
      <td>1.04</td>
      <td>None</td>
      <td>None</td>
      <td>-73.954376</td>
      <td>40.805729</td>
      <td>-73.939117</td>
      <td>...</td>
      <td>1.5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>25</td>
      <td>0</td>
      <td>18</td>
      <td>US</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2.82</td>
      <td>None</td>
      <td>None</td>
      <td>-73.845200</td>
      <td>40.722134</td>
      <td>-73.810638</td>
      <td>...</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>24</td>
      <td>6</td>
      <td>20</td>
      <td>US</td>
    </tr>
  </tbody>
</table>
<p>10 個資料列 × 28 個資料行</p>
</div>

移除一些您在進行模型建構或建置其他功能時不需用到的資料行。 將搭車時間的時間欄位重新命名，另外使用 `pandas.Series.dt.normalize` 來將該時間轉換為午夜。 您要針對所有時間功能執行此動作，以便之後在每日資料粒度層級上將資料集聯結在一起時，可使用日期時間元件作為索引鍵。

```python
columns_to_remove = ["lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df = green_taxi_df.rename(columns={"lpepPickupDatetime": "datetime"})
green_taxi_df["datetime"] = green_taxi_df["datetime"].dt.normalize()
green_taxi_df.head(5)
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
      <th>vendorID</th>
      <th>Datetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20</td>
      <td>1</td>
      <td>0.98</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>40.761257</td>
      <td>8.8</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>US</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>3.08</td>
      <td>-73.979973</td>
      <td>40.677071</td>
      <td>-73.934349</td>
      <td>40.671654</td>
      <td>12.8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>US</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73.863045</td>
      <td>40.882923</td>
      <td>-73.839836</td>
      <td>40.868336</td>
      <td>13.8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>US</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2.87</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>40.694248</td>
      <td>13.8</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>US</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13</td>
      <td>1</td>
      <td>0.50</td>
      <td>-73.942589</td>
      <td>40.841423</td>
      <td>-73.943672</td>
      <td>40.834396</td>
      <td>5.3</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>US</td>
    </tr>
  </tbody>
</table>
</div>

### <a name="enrich-with-holiday-data"></a>使用假日資料進行擴充

既然您已下載並大致備妥計程車資料，請納入假日資料作為額外功能。 由於重大節日將是計程車需求急遽增加而使得供應變得有限的時間，因此，假日特有的功能將有助於模型精確度。 假日資料集相對較小，因此，使用 `PublicHolidays` 類別建構函式且不含任何參數進行篩選，即可擷取完整的資料集。 預覽資料以檢查格式。

```python
from azureml.contrib.opendatasets import PublicHolidays
# call default constructor to download full dataset
holidays_df = PublicHolidays().to_pandas_dataframe()
holidays_df.head(5)
```

    ActivityStarted, to_pandas_dataframe
    Looking for parquet files...
    Reading them into Pandas dataframe...
    Reading Processed/part-00000-tid-1353805596865908763-9ee4e95b-0d55-4292-addd-a0e19d7c32cb-3559-c000.snappy.parquet under container holidaydatacontainer
    Done.
    ActivityCompleted: Activity=to_pandas_dataframe, HowEnded=Success, Duration=1799.89 [ms]

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
      <th>countryOrRegion</th>
      <th>holidayName</th>
      <th>isPaidTimeOff</th>
      <th>countryRegionCode</th>
      <th>normalizeHolidayName</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>40688</th>
      <td>阿爾巴尼亞</td>
      <td>New Year's Day</td>
      <td>None</td>
      <td>AL</td>
      <td>New Year's Day</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40689</th>
      <td>阿爾及利亞</td>
      <td>New Year's Day</td>
      <td>None</td>
      <td>DZ</td>
      <td>New Year's Day</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40690</th>
      <td>安道爾</td>
      <td>New Year's Day</td>
      <td>None</td>
      <td>AD</td>
      <td>New Year's Day</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40691</th>
      <td>安哥拉</td>
      <td>New Year's Day</td>
      <td>None</td>
      <td>AO</td>
      <td>New Year's Day</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40692</th>
      <td>阿根廷</td>
      <td>New Year's Day</td>
      <td>None</td>
      <td>AR</td>
      <td>New Year's Day</td>
      <td>2008-01-01</td>
    </tr>
  </tbody>
</table>
</div>



將 `countryRegionCode` 和 `date` 資料行重新命名以符合計程車資料的個別欄位名稱，同時也將時間正規化，使它可用來作為索引鍵。 接下來，使用 Pandas `merge()` 函式執行左聯結，來聯結假日資料與計程車資料。 這將保留來自 `green_taxi_df` 的所有記錄，但會基於對應的 `datetime` 和 `country_code` 納入現有的假日資料，在此案例中一律為 `"US"`。 預覽資料以確認它們已正確地合併。

```python
holidays_df = holidays_df.rename(columns={"countryRegionCode": "country_code", "date": "datetime"})
holidays_df["datetime"] = holidays_df["datetime"].dt.normalize()
holidays_df.pop("countryOrRegion")
holidays_df.pop("holidayName")

taxi_holidays_df = pd.merge(green_taxi_df, holidays_df, how="left", on=["datetime", "country_code"])
taxi_holidays_df.head(5)
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
      <th>vendorID</th>
      <th>Datetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
      <th>isPaidTimeOff</th>
      <th>normalizeHolidayName</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2016-01-20</td>
      <td>1</td>
      <td>0.98</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>40.761257</td>
      <td>8.8</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>US</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>3.08</td>
      <td>-73.979973</td>
      <td>40.677071</td>
      <td>-73.934349</td>
      <td>40.671654</td>
      <td>12.8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>US</td>
      <td>True</td>
      <td>New Year's Day</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73.863045</td>
      <td>40.882923</td>
      <td>-73.839836</td>
      <td>40.868336</td>
      <td>13.8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>US</td>
      <td>True</td>
      <td>New Year's Day</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2.87</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>40.694248</td>
      <td>13.8</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>US</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>2016-01-13</td>
      <td>1</td>
      <td>0.50</td>
      <td>-73.942589</td>
      <td>40.841423</td>
      <td>-73.943672</td>
      <td>40.834396</td>
      <td>5.3</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>US</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

### <a name="enrich-with-weather-data"></a>使用氣象資料進行擴充

現在您可以將 NOAA 地面氣象資料附加至計程車和假日資料。 使用類似的方法，反覆地一次下載一個月的資料來擷取氣象資料。 此外，搭配字串陣列指定 `cols` 參數來篩選您想要下載的資料行。 這是一個非常大型的資料集，其中包含來自全世界的地面氣象資料，因此，在附加每個月資料之前，請在資料架構上使用 `query()` 函式來篩選接近 NYC 的經/緯度欄位。 這將確保 `weather_df` 不會變得太大。

```python
from azureml.contrib.opendatasets import NoaaIsdWeather

weather_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016","%m/%d/%Y")
end = datetime.strptime("1/31/2016","%m/%d/%Y")

for sample_month in range(12):
    tmp_df = NoaaIsdWeather(cols=["temperature", "precipTime", "precipDepth", "snowDepth"], start_date=start + relativedelta(months=sample_month), end_date=end + relativedelta(months=sample_month))\
        .to_pandas_dataframe()
    print("--weather downloaded--")

    # filter out coordinates not in NYC to conserve memory
    tmp_df = tmp_df.query("latitude>=40.53 and latitude<=40.88")
    tmp_df = tmp_df.query("longitude>=-74.09 and longitude<=-73.72")
    print("--filtered coordinates--")
    weather_df = weather_df.append(tmp_df)

weather_df.head(10)
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
      <th>wban</th>
      <th>precipTime</th>
      <th>snowDepth</th>
      <th>溫度</th>
      <th>緯度</th>
      <th>precipDepth</th>
      <th>經度</th>
      <th>Datetime</th>
      <th>usaf</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1754979</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>7.2</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 00:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754980</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 01:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754981</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 02:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754982</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.1</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 03:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754983</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 04:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754984</th>
      <td>94741</td>
      <td>24.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40.85</td>
      <td>5.0</td>
      <td>-74.061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754985</th>
      <td>94741</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40.85</td>
      <td>NaN</td>
      <td>-74.061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754986</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 05:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754987</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 06:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754988</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 07:51:00</td>
      <td>725025</td>
    </tr>
  </tbody>
</table>
</div>

在氣象資料的 `datetime` 欄位上再次呼叫 `pandas.Series.dt.normalize`，使它符合 `taxi_holidays_df` 中的時間索引鍵。 刪除不必要的資料行，並篩選出氣溫為 `NaN` 的記錄。

接下來，將氣象資料分組，讓您能夠擁有每日彙總的氣象值。 定義一個字典 `aggregations`，以定義在每日層級上彙總每個欄位的方式。 針對 `snowDepth` 和 `temperature` 採用平均值，並針對 `precipTime` 和 `precipDepth` 採用每日上限。 使用 `groupby()` 函式及彙總來將資料分組。 預覽資料以確保每日均有一筆記錄。

```python
weather_df["datetime"] = weather_df["datetime"].dt.normalize()
weather_df.pop("usaf")
weather_df.pop("wban")
weather_df.pop("longitude")
weather_df.pop("latitude")

# filter out NaN
weather_df = weather_df.query("temperature==temperature")

# group by datetime
aggregations = {"snowDepth": "mean", "precipTime": "max", "temperature": "mean", "precipDepth": "max"}
weather_df_grouped = weather_df.groupby("datetime").agg(aggregations)
weather_df_grouped.head(10)
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
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>溫度</th>
      <th>precipDepth</th>
    </tr>
    <tr>
      <th>Datetime</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2016-01-01</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>5.197345</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-02</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>2.567857</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-03</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>3.846429</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-04</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>0.123894</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-05</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>-7.206250</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-06</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>-0.896396</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-07</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>3.180645</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-08</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>4.384091</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-09</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>6.710274</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>2016-01-10</th>
      <td>NaN</td>
      <td>24.0</td>
      <td>10.943655</td>
      <td>254.0</td>
    </tr>
  </tbody>
</table>
</div>

> [!NOTE]
> 此教學課程的範例會使用 Pandas 函式和自訂彙總來合併資料，但開放資料集 SDK 具有設計來輕鬆合併及擴充資料集的類別。 如需這些設計模式的程式碼範例，請參閱[筆記本](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) \(英文\)。

### <a name="cleanse-data"></a>清理資料

將您已備妥的計程車和假日資料與新的氣象資料合併。 此時，您只需要 `datetime` 索引鍵，並再次執行資料的左聯結。 在新的資料架構上執行 `describe()` 函式，來查看每個欄位的摘要統計資料。

```python
taxi_holidays_weather_df = pd.merge(taxi_holidays_df, weather_df_grouped, how="left", on=["datetime"])
taxi_holidays_weather_df.describe()
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
      <th>vendorID</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>溫度</th>
      <th>precipDepth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>1671.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
    </tr>
    <tr>
      <th>平均值</th>
      <td>1.786583</td>
      <td>6.576208</td>
      <td>1.582588</td>
      <td>20.505491</td>
      <td>84.936413</td>
      <td>-36.232825</td>
      <td>21.723144</td>
      <td>7.863018</td>
      <td>6.500000</td>
      <td>15.113708</td>
      <td>3.240250</td>
      <td>13.664125</td>
      <td>11.764141</td>
      <td>13.258875</td>
      <td>13.903524</td>
      <td>1056.644458</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.409728</td>
      <td>9.086857</td>
      <td>2.418177</td>
      <td>108.847821</td>
      <td>70.678506</td>
      <td>37.650276</td>
      <td>19.104384</td>
      <td>10.648766</td>
      <td>3.452124</td>
      <td>8.485155</td>
      <td>1.956895</td>
      <td>6.650676</td>
      <td>15.651884</td>
      <td>10.339720</td>
      <td>9.474396</td>
      <td>2815.592754</td>
    </tr>
    <tr>
      <th>Min</th>
      <td>1.000000</td>
      <td>-60.000000</td>
      <td>-1.000000</td>
      <td>-74.179482</td>
      <td>0.000000</td>
      <td>-74.190704</td>
      <td>0.000000</td>
      <td>-52.800000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
      <td>-13.379464</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>0.330000</td>
      <td>-73.946680</td>
      <td>40.717712</td>
      <td>-73.945429</td>
      <td>1.770000</td>
      <td>1.000000</td>
      <td>3.750000</td>
      <td>8.000000</td>
      <td>2.000000</td>
      <td>9.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
      <td>6.620773</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2.000000</td>
      <td>4.000000</td>
      <td>0.830000</td>
      <td>1.500000</td>
      <td>40.814129</td>
      <td>0.500000</td>
      <td>21.495000</td>
      <td>2.000000</td>
      <td>6.500000</td>
      <td>15.000000</td>
      <td>3.000000</td>
      <td>15.000000</td>
      <td>4.428571</td>
      <td>6.000000</td>
      <td>13.090753</td>
      <td>10.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.000000</td>
      <td>9.000000</td>
      <td>1.870000</td>
      <td>89.000000</td>
      <td>129.000000</td>
      <td>1.000000</td>
      <td>40.746146</td>
      <td>11.300000</td>
      <td>9.250000</td>
      <td>22.000000</td>
      <td>5.000000</td>
      <td>19.000000</td>
      <td>12.722222</td>
      <td>24.000000</td>
      <td>22.944737</td>
      <td>132.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2.000000</td>
      <td>460.000000</td>
      <td>51.950000</td>
      <td>265.000000</td>
      <td>265.000000</td>
      <td>6.000000</td>
      <td>58.600000</td>
      <td>498.000000</td>
      <td>12.000000</td>
      <td>30.000000</td>
      <td>6.000000</td>
      <td>23.000000</td>
      <td>67.090909</td>
      <td>24.000000</td>
      <td>31.303665</td>
      <td>9999.000000</td>
    </tr>
  </tbody>
</table>
</div>

從摘要統計資料中，您會看到有數個欄位包含將降低模型精確度的極端值或值。 先篩選位於您用於篩選氣象資料之相同範圍內的經/緯度欄位。 `tripDistance` 欄位含有一些不正確的資料，因為最小值為負數。 `passengerCount` 欄位也含有不正確的資料，其最大值為 210 個乘客。 最後，`totalAmount` 欄位含有負值，這在我們的模型內容中不具任何意義。

使用查詢函式篩選出這些異常狀況，然後移除前幾個在定型時不需用到的資料行。

```python
final_df = taxi_holidays_weather_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>0 and tripDistance<75")
final_df = final_df.query("passengerCount>0 and passengerCount<100")
final_df = final_df.query("totalAmount>0")

columns_to_remove_for_training = ["datetime", "pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude", "country_code"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

再次於資料上呼叫 `describe()`，以確保清理會如預期般運作。 您現在具有一組已備妥且已清理的計程車、假日及氣象資料，可用於機器學習服務模型定型。

```python
final_df.describe()
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
      <th>vendorID</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>溫度</th>
      <th>precipDepth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>1490.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
    </tr>
    <tr>
      <th>平均值</th>
      <td>1.786910</td>
      <td>1.343476</td>
      <td>2.848488</td>
      <td>14.689039</td>
      <td>3.499788</td>
      <td>14.948916</td>
      <td>3.234254</td>
      <td>13.647344</td>
      <td>12.508581</td>
      <td>11.855929</td>
      <td>10.301433</td>
      <td>208.432384</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.409508</td>
      <td>1.001232</td>
      <td>2.895960</td>
      <td>10.289832</td>
      <td>1.707865</td>
      <td>8.442438</td>
      <td>1.958477</td>
      <td>6.640280</td>
      <td>16.203195</td>
      <td>10.125701</td>
      <td>8.553512</td>
      <td>1284.892832</td>
    </tr>
    <tr>
      <th>Min</th>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.010000</td>
      <td>3.300000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
      <td>-13.379464</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>1.070000</td>
      <td>8.160000</td>
      <td>2.000000</td>
      <td>8.000000</td>
      <td>2.000000</td>
      <td>9.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
      <td>3.504580</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>1.900000</td>
      <td>11.300000</td>
      <td>3.000000</td>
      <td>15.000000</td>
      <td>3.000000</td>
      <td>15.000000</td>
      <td>4.250000</td>
      <td>6.000000</td>
      <td>10.168182</td>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>3.550000</td>
      <td>17.800000</td>
      <td>5.000000</td>
      <td>22.000000</td>
      <td>5.000000</td>
      <td>19.000000</td>
      <td>15.647059</td>
      <td>24.000000</td>
      <td>16.966923</td>
      <td>41.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2.000000</td>
      <td>6.000000</td>
      <td>51.950000</td>
      <td>150.300000</td>
      <td>6.000000</td>
      <td>30.000000</td>
      <td>6.000000</td>
      <td>23.000000</td>
      <td>67.090909</td>
      <td>24.000000</td>
      <td>26.524107</td>
      <td>9999.000000</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="train-a-model"></a>將模型定型

現在您可以使用已備妥的資料來將自動化機器學習模型定型。 一開始先將 `final_df` 分割為功能 (X 值) 和標籤 (y 值)，對此模型而言，此為計程車車資成本。

```python
y_df = final_df.pop("totalAmount")
x_df = final_df
```

現在您可以使用 `scikit-learn` 程式庫中的 `train_test_split()` 函式，將資料分割成定型集和測試集。 `test_size` 參數會決定要配置給測試的資料百分比。 `random_state` 參數會設定隨機數字產生器的種子，讓您的「定型-測試」分割具有確定性。


```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=222)
```

### <a name="load-workspace-and-configure-experiment"></a>載入工作區並設定實驗

搭配您的訂用帳戶和工作區資訊使用 `get()` 函式，來載入您的 Azure Machine Learning 服務工作區。 在工作區中建立實驗來儲存及監視模型執行。


```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment

workspace = Workspace.get(subscription_id="<your-subscription-id>", name="<your-workspace-name>", resource_group="<your-resource-group>")
experiment = Experiment(workspace, "opendatasets-ml")
```

使用 `AutoMLConfig` 類別來建立用於實驗的設定物件。 您會附加定型資料，並另外指定可控制定型流程的設定和參數。 參數具有下列用途：

* `task`：要執行的實驗類型。
* `X`：定型功能。
* `y`：定型標籤。
* `iterations`：反覆執行次數。 每次反覆執行都會使用多個超參數設定，來嘗試不同功能正規化/標準化方法及不同模型的組合。
* `primary_metric`：可在模型定型過程中最佳化的主要計量。 最適化模型將根據此計量來選擇。
* `preprocess`：控制實驗是否可以預先處理輸入資料 (處理遺漏的資料、將文字轉換成數值等等)。
* `n_cross_validations`：未指定驗證資料時所要執行的交叉驗證分割數目。


```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task="regression",
                             X=X_train.values,
                             y=y_train.values.flatten(),
                             iterations=20,
                             primary_metric="spearman_correlation",
                             preprocess=True,
                             n_cross_validations=5
                            )
```

### <a name="submit-experiment"></a>提交實驗

提交用於定型的實驗。 提交實驗之後，程序會根據您定義的條件約束，反覆運算不同的機器學習演算法和超參數設定。 它會將已定義的精確度計量最佳化，以選擇最適化模型。 將 `automl_config` 物件傳遞給實驗。 將輸出設定為 `True` 以在實驗期間檢視進度。

提交實驗之後，您就會看到定型流程的即時輸出。 對於每次反覆執行，您都會看見模型類型和功能正規化/標準化方法、執行持續期間，以及定型精確度。 欄位 `BEST` 會根據您的計量類型追蹤最佳訓練分數。

```python
training_run = experiment.submit(automl_config, show_output=True)
```

    Running on local machine
    Parent Run ID: AutoML_5c35f2a7-e479-4e7f-a131-ed4cb51e29d1
    Current status: DatasetEvaluation. Gathering dataset statistics.
    Current status: FeaturesGeneration. Generating features for the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: ModelSelection. Beginning model selection.

    ****************************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ****************************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler RandomForest                      0:00:07       0.9081    0.9081
             1   StandardScalerWrapper DecisionTree             0:00:05       0.9121    0.9121
             2   StandardScalerWrapper LightGBM                 0:00:04       0.9318    0.9318
             3   StandardScalerWrapper LightGBM                 0:00:07       0.9286    0.9318
             4   MaxAbsScaler LightGBM                          0:00:05       0.9246    0.9318
             5   MaxAbsScaler LightGBM                          0:00:05       0.9199    0.9318
             6   MaxAbsScaler RandomForest                      0:00:07       0.9327    0.9327
             7   StandardScalerWrapper ElasticNet               0:00:04       0.9371    0.9371
             8   MaxAbsScaler LightGBM                          0:00:05       0.9327    0.9371
             9   MaxAbsScaler SGD                               0:00:04       0.9077    0.9371
            10   MaxAbsScaler LightGBM                          0:00:04       0.9340    0.9371
            11   StandardScalerWrapper LightGBM                 0:00:04       0.8301    0.9371
            12   MaxAbsScaler DecisionTree                      0:00:05       0.9214    0.9371
            13   StandardScalerWrapper DecisionTree             0:00:04       0.9201    0.9371
            14   MaxAbsScaler DecisionTree                      0:00:05       0.9179    0.9371
            15   MaxAbsScaler ExtremeRandomTrees                0:00:05       0.9052    0.9371
            16   StandardScalerWrapper DecisionTree             0:00:04       0.9282    0.9371
            17   StandardScalerWrapper ElasticNet               0:00:04       0.9319    0.9371
            18   VotingEnsemble                                 0:00:16       0.9380    0.9380
            19   StackEnsemble                                  0:00:17       0.9376    0.9380

### <a name="retrieve-the-fitted-model"></a>擷取適合的模型

在反覆執行所有定型結束時，不論是透過封包或堆疊，自動化機器學習流程都會從所有個別執行中建立整體演算法。 將適合的整體模型擷取到 `fitted_model` 變數，並將最適合的個別執行擷取到 `best_run` 變數。

```python
best_run, fitted_model = training_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-model-accuracy"></a>測試模型精確度

使用適合的整體模型，在測試資料集上執行預測以預測計程車車資。 `predict()` 函式會使用適合的模型，針對 `X_test` 資料集預測 y 值 (計程車車資成本)。


```python
y_predict = fitted_model.predict(X_test.values)
```

計算結果的均方根誤差。 使用 `y_test` 資料架構，並將它轉換為 `y_actual` 清單，以便與預測值進行比較。 `mean_squared_error` 函式會採用兩個值陣列，並計算這兩個陣列之間的均方誤差。 採用結果的平方根會產生與 y 變數 (成本) 相同單位的誤差。 這大致上表示計程車車資預測與實際車資的差距，同時會對大量誤差進行大量加權。


```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```




    4.178568987067901



請執行下列程式碼，以使用完整的 `y_actual` 和 `y_predict` 資料集來計算平均絕對百分比誤差 (MAPE)。 此計量會計算每個預測值與實際值之間的絕對差異，並加總所有差異。 然後它會以實際值總計的百分比來表示該總和。


```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.14923619644924357

    Model Accuracy:
    0.8507638035507564

假設我們使用相當小的資料樣本 (相對於完整資料集範例 (n = 11748))，模型精確度相當高 (為 85%)，而 RMSE 在預測計程車車資價格方面大約會有 +- $4.00 美元的誤差。 下一個提高精確度的可能步驟是，返回此筆記本的第二個資料格，然後從每月 2,000 筆記錄提高樣本大小，接著再次執行整個實驗，以使用更多資料來將模型重新定型。

## <a name="clean-up-resources"></a>清除資源

如果您不打算使用您建立的資源，請刪除它們，以免產生任何費用。

1. 在 Azure 入口網站中，選取最左邊的 [資源群組]。
1. 在清單中，選取您所建立的資源群組。
1. 選取 [刪除資源群組]。
1. 輸入資源群組名稱。 然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

* 如需更多程式碼範例，請參閱 Azure 開放資料集[筆記本](https://github.com/Azure/OpenDatasetsNotebooks) \(英文\)。
* 如需 Azure Machine Learning 服務中自動化機器學習的詳細資訊，請遵循[操作說明](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train) \(機器翻譯\)。
