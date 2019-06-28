---
title: 自動定型時間序列預測的模型
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務來定型時間序列預測迴歸模型使用自動化機器學習服務。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: 4a3ab9094080ab257a885bb7a745fc83948327c2
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331690"
---
# <a name="auto-train-a-time-series-forecast-model"></a>自動定型時間序列預測的模型

在本文中，您會學習如何使用自動化的機器學習在 Azure Machine Learning 服務中將時間序列預測迴歸模型定型。 設定預測的模型類似於設定標準迴歸模型使用自動化的機器學習服務，但使用時間序列資料的存在的某些設定選項和前置處理步驟。 下列範例將示範如何以：

* 準備資料進行時間序列模型化
* 設定在特定時間序列參數[ `AutoMLConfig` ](/python/api/azureml-train-automl/azureml.train.automl.automlconfig)物件
* 以時間序列資料執行預測

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

您可以使用自動化的 ML 結合技術和方法，並取得建議的高品質時間序列預測。 自動化的時間序列實驗會被視為多變量迴歸問題。 過去的時間序列值 「 樞紐 」 以及其他預測值的迴歸輸入變數成為更多的維度。 

這個方法時，不同於傳統的時間序列方法，好處是自然併入多個內容變數和其在訓練期間之間的關聯性。 在真實世界預測應用程式中，多個因素會影響預測。 例如，當預測銷售，互動的歷史趨勢、 貨幣匯率和價格所有共同磁碟機的銷售結果。 進一步的優點是迴歸模型中的所有最新創新立即套用至預測。

您可以[設定](#config)如何遙遠的未來預測應該以及延遲和更多擴充 （預測時間範圍）。 自動化的 ML 學習單一的但通常在內部分支模型的資料集和預測的視野中的所有項目。 詳細資料可因此估計模型參數，並看不見的系列的一般化可能實現。 

從定型資料中擷取功能扮演重要角色。 而自動化的 ML 執行標準處理前的步驟，並會產生額外的時間序列功能，以擷取季節性效果，並最大化的預測精確度。 

## <a name="prerequisites"></a>必要條件

* Azure Machine Learning 服務工作區。 若要建立工作區，請參閱[建立 Azure 機器學習服務工作區](setup-create-workspace.md)。
* 本文假設基本的熟悉設定自動化的機器學習服務實驗。 請遵循[教學課程](tutorial-auto-train-models.md)或是[how-to](how-to-configure-auto-train.md)查看基本自動化的機器學習服務實驗設計模式。

## <a name="preparing-data"></a>準備資料

最重要的差異之間預測的迴歸工作類型和迴歸工作自動化的 machine learning 中的型別代表有效的時間序列資料中包含一項功能。 規則的時間序列具有定義完善且一致的頻率，在每個樣本點，持續時間範圍中的值。 請考慮下列快照集檔案的`sample.csv`。

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

此資料集有兩個不同的存放區，A 和 b。 此外，公司的每日銷售資料的簡單範例，就是功能`week_of_year`，這可讓偵測每週的季節性模型。 欄位`day_datetime`與每日頻率 和  欄位表示初始狀態的時間序列`sales_quantity`是執行預測的目標資料行。 資料讀取至 Pandas 資料框架，然後使用`to_datetime`函式，以確保時間序列是`datetime`型別。

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

在此情況下已排序資料的時間欄位遞增`day_datetime`。 不過，當設定實驗，確定所需的時間資料行以遞增順序來建置有效的時間序列進行排序。 假設資料包含 1,000 筆記錄，並具有決定性的分割中建立定型和測試資料集的資料。 然後個別目標欄位`sales_quantity`建立預測訓練和測試集。

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> 在訓練模型，以預測未來值時，請確定所有執行您預期的作業範圍的預測時，就可以使用定型中所使用的功能。 比方說，在建立時的需求預測，包括目前股價的功能可能可大幅增加訓練精確度。 不過，如果您想要預測使用長的時間範圍，您可能無法精確地預測未來的內建值，對應到未來的時間序列點，以及模型的精確度可能會受到影響。

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>設定及執行實驗

預測工作自動化的機器學習服務會使用前置處理和估計特有時間序列資料的步驟。 將執行下列處理前的步驟：

* 偵測時間序列範例頻率 （例如每小時、 每天、 每週），並建立新記錄不存在的時間點，以讓連續序列。
* 推算遺漏值 （透過轉寄填滿） 的目標和特徵資料行 （使用中間值的資料行值）
* 建立數個不同的系列提供固定的效果的資料粒度為基礎的功能
* 建立以時間為基礎的功能，以協助學習季節性模式
* 編碼類別變數數字的數量

`AutoMLConfig`物件定義的設定和自動化的機器學習工作所需的資料。 類似於迴歸問題，您定義標準的訓練參數，例如工作類型、 的反覆項目，定型資料，以及交叉驗證數目。 預測工作，有額外的參數必須設定會影響實驗。 下表說明每個參數，以及其使用方式。

| 參數 | 描述 | 必要項 |
|-------|-------|-------|
|`time_column_name`|用來指定用來建立時間序列和推斷其頻率的輸入資料中的日期時間資料行。|✓|
|`grain_column_names`|輸入資料中定義個別的數列群組的名稱。 如果未定義資料粒度，資料集被假設一個時間序列。||
|`max_horizon`|定義最大的所需預測時間範圍的時間序列的頻率單位。 單位根據定型資料，例如每月、 每週的時間間隔 forecaster microsoft frx 應該預測出。|✓|
|`target_lags`|*n*轉寄延隔時間週期的目標在模型定型之前的值。||
|`target_rolling_window_size`|*n*歷程記錄的期間，用來產生預測的值，< = 定型集大小。 如果省略， *n*完整的定型集大小。||

時間序列設定建立成一個字典物件。 設定`time_column_name`至`day_datetime`資料集中的欄位。 定義`grain_column_names`參數，確保**兩個不同的時間序列群組**而建立的資料; 一個用於存放區 A 和 b。 最後，將`max_horizon`設為 50，若要預測的整個測試設定。 將預測的視窗設成 10 個週期`target_rolling_window_size`，以及目標值 2 期向前邁進的延隔`target_lags`參數。

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10
}
```

現在建立標準`AutoMLConfig`物件，指定`forecasting`工作類型，並提交實驗。 模型完成之後，請擷取最適合執行反覆項目。

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

> [!NOTE]
> 交叉驗證 (CV) 程序中，時間序列資料可能會違反的統計的基本假設，在標準的 K 摺疊交叉驗證策略，以便自動化的機器學習服務會實作循環的原始驗證程序，以建立時間序列資料的交叉驗證折數。 若要使用此程序，指定`n_cross_validations`中的參數`AutoMLConfig`物件。 您可以略過驗證並使用您自己的驗證設定具有`X_valid`和`y_valid`參數。

### <a name="view-feature-engineering-summary"></a>檢視功能工程摘要

針對時間序列工作自動化的 machine learning 中的類型，您可以檢視特性工程設計程序的詳細的資料。 下列程式碼會顯示每個未經處理的功能，以及下列屬性：

* 未經處理的功能名稱
* 馬上此原始特性工程設計的特徵數目
* 偵測到的類型
* 功能是否已卸除
* 功能轉換未經處理的功能清單

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>使用最佳的模型預測

您可以使用最佳的模型反覆項目來預測的測試資料集的值。

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

或者，您可以使用`forecast()`函式，而不是`predict()`，讓預測何時應該開始的規格。 在下列範例中，您先取代中的所有值`y_pred`與`NaN`。 預測的來源會在定型資料的結尾在此情況下，因為它通常可以使用時`predict()`。 不過，如果您更換的第二個半部`y_pred`與`NaN`，函式會讓數值上半年未經過修改，但預測`NaN`的第二部分中的值。 函數會傳回預測的值和對齊的功能。

您也可以使用`forecast_destination`中的參數`forecast()`函式來預測值至指定的日期為止。

```python
y_query = y_test.copy().astype(np.float)
y_query.fill(np.nan)
y_fcst, X_trans = fitted_pipeline.forecast(X_test, y_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

計算 RMSE （根平均平方差） 之間`y_test`實際的值，並在預測的值`y_pred`。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

現在，整體尚未決定模型的精確度、 最實際的下一個步驟是使用模型來預測未知的未來值。 只需要提供資料集當做測試集相同格式`X_test`但產生的預測未來的日期時間，與集是每個時間序列步驟的預測的值。 假定 2018 年 12 月 31 日的資料集內的最後一個時間序列記錄。 若要預測隔天的需求 (或多個句號，您要預測，< = `max_horizon`)，建立一個時間序列記錄每個存放區 01/01/2019年。

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

重複這個未來將資料載入至資料框架，然後執行必要的步驟`best_run.predict(X_test)`來預測未來值。

> [!NOTE]
> 值無法預測的數目大於週期`max_horizon`。 必須使用較大的水平分割，來預測未來值超過目前的水平分割重新定型模型。

## <a name="next-steps"></a>後續步驟

* 請遵循[教學課程](tutorial-auto-train-models.md)以了解如何建立與實驗自動化機器學習服務。
* 檢視[適用於 Python 的 Azure Machine Learning SDK](https://aka.ms/aml-sdk)參考文件。
