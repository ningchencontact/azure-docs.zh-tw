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
ms.date: 03/19/2019
ms.openlocfilehash: 32f96a28e027bfd0e65d934bb47bb98400af459d
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360716"
---
# <a name="auto-train-a-time-series-forecast-model"></a>自動定型時間序列預測的模型

在本文中，您會學習如何使用自動化的機器學習在 Azure Machine Learning 服務中將時間序列預測迴歸模型定型。 設定預測的模型類似於設定標準迴歸模型使用自動化的機器學習服務，但使用時間序列資料的存在的某些設定選項和前置處理步驟。 下列範例將示範如何以：

* 準備資料進行時間序列模型化
* 設定在特定時間序列參數[ `AutoMLConfig` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py)物件
* 以時間序列資料執行預測

## <a name="prerequisites"></a>必要條件

* Azure Machine Learning 服務工作區。 若要建立工作區，請參閱[建立 Azure 機器學習服務工作區](setup-create-workspace.md)。
* 本文假設基本的熟悉設定自動化的機器學習服務實驗。 請遵循[教學課程](tutorial-auto-train-models.md)或是[how-to](how-to-configure-auto-train.md)查看基本自動化的機器學習服務實驗設計模式。

## <a name="preparing-data"></a>準備資料

最重要的差異之間預測的迴歸工作類型和迴歸工作自動化的 machine learning 中的型別代表有效的時間序列資料中包含一項功能。 規則的時間序列具有定義完善且一致的頻率，在每個樣本點，持續時間範圍中的值。 請考慮下列快照集檔案的`sample.csv`。

    week_starting,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/10/2018,A,2300,37
    9/10/2018,B,550,37
    9/17/2018,A,2100,38
    9/17/2018,B,650,38
    9/24/2018,A,2400,39
    9/24/2018,B,700,39
    10/1/2018,A,2450,40
    10/1/2018,B,650,40

此資料集有兩個不同的存放區，A 和 b。 此外，公司的每週銷售資料的簡單範例，就是功能`week_of_year`，這可讓偵測每週的季節性模型。 欄位`week_starting`表示初始狀態的時間序列、 每週頻率與欄位`sales_quantity`是執行預測的目標資料行。 資料讀取至 Pandas 資料框架，然後使用`to_datetime`函式，以確保時間序列是`datetime`型別。

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["week_starting"] = pd.to_datetime(data["week_starting"])
```

在此情況下已排序資料的時間欄位遞增`week_starting`。 不過，當設定實驗，確定所需的時間資料行以遞增順序來建置有效的時間序列進行排序。 假設資料包含 1,000 筆記錄，並具有決定性的分割中建立定型和測試資料集的資料。 然後個別目標欄位`sales_quantity`建立預測訓練和測試集。

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> 在訓練模型，以預測未來值時，請確定所有執行您預期的作業範圍的預測時，就可以使用定型中所使用的功能。 比方說，在建立時的需求預測，包括目前股價的功能可能可大幅增加訓練精確度。 不過，如果您想要預測使用長的時間範圍，您可能無法精確地預測未來的內建值，對應到未來的時間序列點，以及模型的精確度可能會受到影響。

## <a name="configure-experiment"></a>設定實驗

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
|`max_horizon`|所需的時間序列的頻率單位的預測時間範圍的上限。|✓|

時間序列設定建立成一個字典物件。 設定`time_column_name`至`week_starting`資料集中的欄位。 定義`grain_column_names`參數，確保**兩個不同的時間序列群組**是針對我們的資料; 一個用於存放區 A 和 b。 最後，將所建立`max_horizon`設為 50，若要預測的整個測試設定。

```python
time_series_settings = {
    "time_column_name": "week_starting",
    "grain_column_names": ["store"],
    "max_horizon": 50
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

## <a name="forecasting-with-best-model"></a>使用最佳的模型預測

您可以使用最佳的模型反覆項目來預測的測試資料集的值。

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

計算 RMSE （根平均平方差） 之間`y_test`實際的值，並在預測的值`y_pred`。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

現在，整體尚未決定模型的精確度、 最實際的下一個步驟是使用模型來預測未知的未來值。 只需要提供資料集當做測試集相同格式`X_test`但產生的預測未來的日期時間，與集是每個時間序列步驟的預測的值。 假設資料集內的最後一個時間序列記錄已針對一週開始 2018 年 12 月 31 日。 若要預測下一週的隨選 (或多個句號，您要預測，< = `max_horizon`)，建立一個時間序列記錄每個存放區的一週開始 01/07/2019年。

    week_starting,store,week_of_year
    01/07/2019,A,2
    01/07/2019,A,2

重複這個未來將資料載入至資料框架，然後執行必要的步驟`best_run.predict(X_test)`來預測未來值。

> [!NOTE]
> 值無法預測的數目大於週期`max_horizon`。 必須使用較大的水平分割，來預測未來值超過目前的水平分割重新定型模型。

## <a name="next-steps"></a>後續步驟

* 請遵循[教學課程](tutorial-auto-train-models.md)以了解如何建立與實驗自動化機器學習服務。
* 檢視[適用於 Python 的 Azure Machine Learning SDK](https://aka.ms/aml-sdk)參考文件。
