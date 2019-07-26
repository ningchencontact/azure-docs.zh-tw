---
title: 自動定型時間序列預測模型
titleSuffix: Azure Machine Learning service
description: 瞭解如何使用 Azure Machine Learning 服務, 使用自動化機器學習來定型時間序列預測回歸模型。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: 34902aa23339b62920f918ae19b410a99e226a0e
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358806"
---
# <a name="auto-train-a-time-series-forecast-model"></a>自動定型時間序列預測模型

在本文中, 您將瞭解如何使用 Azure Machine Learning 服務中的自動化機器學習來定型時間序列預測回歸模型。 設定預測模型類似于使用自動化機器學習來設定標準回歸模型, 但有一些特定的設定選項和前置處理步驟可用於處理時間序列資料。 下列範例示範如何:

* 準備資料以進行時間序列模型化
* 在[`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig)物件中設定特定的時間序列參數
* 使用時間序列資料執行預測

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

您可以使用自動化 ML 結合技術和方法, 並取得建議的高品質時間序列預測。 自動化的時間序列實驗會被視為多變數回歸問題。 過去的時間序列值會「切換」為回歸輸入變數的其他維度與其他預測指標。 

與傳統時間序列方法不同的是, 這種方法的優點是在定型期間自然結合多個內容變數及其關聯性。 在真實世界的預測應用程式中, 有多個因素會影響預測。 例如, 當預測銷售時, 歷程記錄趨勢的互動、匯率和價格全都會共同推動銷售結果。 另一個優點是回歸模型中所有最近的創新都適用于預測。

您可以[設定](#config)預測未來應延伸的時間 (預測範圍), 以及延遲和更多。 自動化 ML 會針對資料集和預測視野中的所有專案學習單一但通常會在內部分支的模型。 因此, 有更多的資料可用於估計模型參數, 而一般化的數列則會變成可行的。 

從定型資料中解壓縮的功能扮演重要的角色。 而且, 自動化 ML 會執行標準的前置處理步驟, 並產生額外的時間序列功能來捕捉季節性效果並最大化預測準確度。 

## <a name="prerequisites"></a>必要條件

* Azure Machine Learning 服務工作區。 若要建立工作區, 請參閱[建立 Azure Machine Learning 服務工作區](setup-create-workspace.md)。
* 本文假設您對設定自動化機器學習實驗的基本概念十分熟悉。 依照[教學](tutorial-auto-train-models.md)課程或[如何](how-to-configure-auto-train.md)操作來查看基本的自動化機器學習實驗設計模式。

## <a name="preparing-data"></a>準備資料

自動化機器學習服務中的預測回歸工作類型和回歸工作類型之間最重要的差異, 包括資料中代表有效時間序列的功能。 一般時間序列具有定義完善且一致的頻率, 而且在連續時間範圍內的每個取樣點都有一個值。 請考慮下列檔案的快照`sample.csv`集。

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

此資料集是公司每日銷售資料的簡單範例, 其中有兩個不同的商店 a 和 B。此外, 還有一項功能`week_of_year`可讓模型偵測每週季節性。 欄位`day_datetime`代表具有每日頻率的清除時間序列, 而欄位`sales_quantity`則是執行預測的目標資料行。 將資料讀取至 Pandas 資料框架, 然後使用`to_datetime`函數來確保時間序列是一`datetime`種類型。

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

在此情況下, 資料已依時間欄位`day_datetime`遞增排序。 不過, 在設定實驗時, 請確定所需的時間資料行是以遞增順序排序, 以建立有效的時間序列。 假設資料包含1000記錄, 並在資料中進行具決定性的分割, 以建立定型和測試資料集。 然後分隔 [目標] `sales_quantity`欄位, 以建立預測訓練和測試集。

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> 定型模型以預測未來的值時, 請確定在針對您想要的水準執行預測時, 可以使用定型中使用的所有功能。 例如, 建立需求預測時, 包括目前股價的功能可能會大幅增加定型準確度。 不過, 如果您想要長期預測, 可能無法精確地預測未來時間序列點對應的未來股價, 而且模型精確度可能會受到影響。

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>設定及執行實驗

針對預測工作, 自動化機器學習會使用時間序列資料特有的前置處理和估計步驟。 將會執行下列前置處理步驟:

* 偵測時間序列取樣頻率 (例如每小時、每天、每週), 並建立不存在時間點的新記錄, 讓數列持續進行。
* 插補目標中的遺漏值 (透過向前填滿) 和特徵資料行 (使用中位數的資料行值)
* 建立以細微性為基礎的功能, 以啟用跨不同數列的固定效果
* 建立以時間為基礎的功能, 以協助學習季節性模式
* 將類別變數編碼為數值數量

`AutoMLConfig`物件會定義自動化機器學習工作所需的設定和資料。 類似于回歸問題, 您可以定義標準訓練參數, 例如工作類型、反復專案數目、定型資料, 以及交叉驗證的數目。 針對預測工作, 還有一些必須設定的參數會影響實驗。 下表說明每個參數和其使用方式。

| 參數 | 描述 | 必要項 |
|-------|-------|-------|
|`time_column_name`|用來指定輸入資料中用來建立時間序列並推斷其頻率的日期時間資料行。|✓|
|`grain_column_names`|在輸入資料中定義個別數列群組的名稱。 如果未定義細微性, 則會假設資料集為一個時間序列。||
|`max_horizon`|以時間序列頻率的單位, 定義所需的預測範圍上限。 單位是以定型資料的時間間隔為基礎, 例如每個月、每週 forecaster 應預測。|✓|
|`target_lags`|在模型定型之前, 要向前延後的目標值為*n*個期間。||
|`target_rolling_window_size`|*n*要用來產生預測值的歷程記錄期間, < = 定型集大小。 如果省略, 則*n*是完整的定型集大小。||

將時間序列設定建立為 dictionary 物件。 將設定`day_datetime`為資料集內的欄位。 `time_column_name` 定義參數以確保針對資料建立**兩個不同的時間序列群組**, 一個用於儲存 A 和 B。 `max_horizon`最後, 將設定為 50, 以便預測整個測試集。 `grain_column_names` 使用`target_rolling_window_size`將 [預測] 視窗設定為10個週期, 並使用`target_lags`參數來延後2個週期的目標值。

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10
}
```

現在, 請建立`AutoMLConfig`標準物件, 並`forecasting`指定工作類型並提交實驗。 在模型完成之後, 請抓取最佳的執行反復專案。

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
> 針對交叉驗證 (CV) 程式, 時間序列資料可能違反標準 K 折迭交叉驗證策略的基本統計假設, 因此自動化機器學習會執行輪流來源驗證程式來建立時間序列資料的交叉驗證折迭。 若要使用這個程式, 請`n_cross_validations` `AutoMLConfig`在物件中指定參數。 您可以略過驗證, 並搭配`X_valid`和`y_valid`參數使用您自己的驗證集。

### <a name="view-feature-engineering-summary"></a>視圖功能工程摘要

如需自動化機器學習服務中的時間序列工作類型, 您可以從功能工程處理常式中查看詳細資料。 下列程式碼會顯示每個原始功能以及下列屬性:

* 原始功能名稱
* 從這個原始功能形成的工程功能數目
* 偵測到的類型
* 是否已卸載功能
* 原始功能的功能轉換清單

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>使用最佳模型進行預測

使用最佳模型反復專案來預測測試資料集的值。

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

或者, 您可以使用`forecast()`函式, `predict()`而不是, 這將會允許預測應開始時的規格。 在下列範例中, 您會先將中`y_pred`的所有值取代為。 `NaN` 在此情況下, 預測來源會在定型資料的結尾, 如同在使用`predict()`時一般的情況。 不過, 如果您只`y_pred`使用`NaN`來取代的後半個, 則函式會保留前一半未`NaN`修改的數值, 但會在後半個中預測值。 函式會傳回預測的值和對齊的功能。

您也可以在函`forecast_destination`式`forecast()`中使用參數來預測值, 直到指定的日期為止。

```python
y_query = y_test.copy().astype(np.float)
y_query.fill(np.nan)
y_fcst, X_trans = fitted_pipeline.forecast(
    X_test, y_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

計算`y_test`實際值與中`y_pred`預測值之間的 RMSE (方根誤差)。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

現在已決定整體模型精確度, 最實際的下一個步驟是使用模型來預測未知的未來值。 只要以與測試集`X_test`相同的格式提供資料集, 但使用未來的日期時間, 所產生的預測集就是每個時間序列步驟的預測值。 假設資料集內的最後一個時間序列記錄是12/31/2018。 若要預測下一天的需求 (或需要預測的期間數, 請 < = `max_horizon`), 為01/01/2019 的每個商店建立單一時間序列記錄。

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

重複必要的步驟, 將未來的資料載入至資料框架, 然後執行`best_run.predict(X_test)`以預測未來的值。

> [!NOTE]
> 無法預測大於的`max_horizon`期間數的值。 模型必須以較大的範圍重新定型, 以預測目前範圍以外的未來值。

## <a name="next-steps"></a>後續步驟

* 請遵循[本教學](tutorial-auto-train-models.md)課程, 以瞭解如何使用自動化機器學習來建立實驗。
* 查看[適用于 Python 的 AZURE MACHINE LEARNING SDK](https://aka.ms/aml-sdk)參考檔。
