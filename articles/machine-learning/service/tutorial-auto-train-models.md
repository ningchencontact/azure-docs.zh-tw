---
title: 迴歸模型教學課程：自動化 ML
titleSuffix: Azure Machine Learning service
description: 了解如何使用自動化機器學習來產生機器學習模型。 Azure Machine Learning 可以用自動化方式，為您執行資料前處理、演算法選擇及超參數選擇。 然後使用 Azure Machine Learning 服務來部署最終模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: trbye
ms.date: 04/11/2019
ms.custom: seodec18
ms.openlocfilehash: 8cedf7abf71a772a0b770dd2f82d9a5508f5dd75
ms.sourcegitcommit: dda9fc615db84e6849963b20e1dce74c9fe51821
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622367"
---
# <a name="tutorial-use-automated-machine-learning-to-build-your-regression-model"></a>教學課程：使用自動機器學習建置迴歸模型

本教學課程是**兩部分教學課程系列的第二部分**。 在上一個教學課程中，您[已備妥 NYC 計程車資料來建立迴歸模型](tutorial-data-prep.md)。

現在，您已準備好使用 Azure Machine Learning 服務來開始建置模型。 在教學課程的這個部分，您將使用已備妥的資料並自動產生迴歸模型，以預測計程車的車資。 您可以藉由使用此服務的自動化機器學習功能，定義機器學習目標和條件約束。 您需啟動自動化機器學習程序。 然後允許為您進行演算法選擇和超參數調整。 自動化機器學習技術會逐一嘗試演算法和超參數的多種組合，直到根據您的準則找到最佳模型為止。

![流程圖](./media/tutorial-auto-train-models/flow2.png)

在本教學課程中，您會了解下列工作：

> [!div class="checklist"]
> * 設定 Python 環境並匯入 SDK 套件。
> * 設定 Azure Machine Learning 服務工作區。
> * 自動將迴歸模型定型。
> * 使用自訂參數在本機執行模型。
> * 探索結果。

如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

>[!NOTE]
> 本文中的程式碼已進行過 Azure Machine Learning SDK 1.0.39 版的測試。

## <a name="prerequisites"></a>必要條件

請跳至[設定您的開發環境](#start)閱讀完整的 Notebook 步驟，或依照下列指示取得 Notebook，並在 Azure Notebooks 或您自己的 Notebook 伺服器上加以執行。 若要執行 Notebook，您將需要：

* [執行資料準備教學課程](tutorial-data-prep.md)。
* 已安裝下列項目的 Python 3.6 Notebook 伺服器：
    * 適用於 Python 的 Azure Machine Learning SDK，含 `automl` 和 `notebooks` 額外項目
    * `matplotlib`
* 教學課程筆記本
* 機器學習工作區
* 與 Notebook 位於相同目錄中的工作區組態檔

請從以下各節取得前述所有必要項目。

* 使用[您工作區中的雲端 Notebook 伺服器](#azure) 
* 使用[您自己的 Notebook 伺服器](#server)

### <a name="azure"></a>使用您工作區中的雲端 Notebook 伺服器

您可以輕鬆地開始使用自己的雲端式 Notebook 伺服器。 我們已在您建立此雲端資源後，為您安裝及設定[適用於 Python 的 Azure Machine Learning SDK](https://aka.ms/aml-sdk)。

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* 啟動 Notebook 網頁之後，請執行 **tutorials/regression-part2-automated-ml.ipynb** Notebook。

### <a name="server"></a>使用您自己的 Jupyter Notebook 伺服器

使用下列步驟在您的電腦上建立本機 Jupyter Notebook 伺服器。  務必在您的環境中安裝 `matplotlib``automl` 和 `notebooks` 額外項目。

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

完成所有步驟後，請執行 **tutorials/regression-part2-automated-ml.ipynb** 筆記本。

## <a name="start"></a>設定您的開發環境

針對您開發工作的所有設定都可以在 Python Notebook 中完成。 設定包含下列動作：

* 安裝 SDK
* 匯入 Python 套件
* 設定您的工作區

### <a name="install-and-import-packages"></a>安裝並匯入套件

如果您要在自己的 Python 環境中進行本教學課程，請使用下列程序安裝必要套件。

```shell
pip install azureml-sdk[automl,notebooks] matplotlib
```

匯入本教學課程中所需的 Python 套件：

```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
import logging
import os
```

### <a name="configure-workspace"></a>設定工作區

從現有的工作區建立工作區物件。 [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) \(英文\) 是會接受您 Azure 訂用帳戶和資源資訊的類別。 它也會建立雲端資源來監視及追蹤您的模型執行。

`Workspace.from_config()` 會讀取檔案 **config.json**，並將詳細資料載入到名為 `ws` 的物件。  `ws` 用於本教學課程的其餘程式碼。

在您有工作區物件之後，請為實驗指定一個名稱。 請建立一個本機目錄並向工作區註冊該目錄。 所有執行的歷程記錄都會記錄在指定的實驗下及 [Azure 入口網站](https://portal.azure.com)中。


```python
ws = Workspace.from_config()
# choose a name for the run history container in the workspace
experiment_name = 'automated-ml-regression'
# project folder
project_folder = './automated-ml-regression'

output = {}
output['SDK version'] = azureml.core.VERSION
output['Subscription ID'] = ws.subscription_id
output['Workspace'] = ws.name
output['Resource Group'] = ws.resource_group
output['Location'] = ws.location
output['Project Directory'] = project_folder
pd.set_option('display.max_colwidth', -1)
pd.DataFrame(data=output, index=['']).T
```

## <a name="explore-data"></a>探索資料

請使用在上一個教學課程中建立的資料流程物件。 總結來說，本教學課程的第 1 部分會清除 NYC 計程車資料，使其可在機器學習模型中使用。 現在，您可以使用資料集中的各種特性，並且讓自動化模型建置這些特性與計程車車程價格之間的關聯性。 開啟並執行資料流程，然後檢閱結果：


```python
import azureml.dataprep as dprep

file_path = os.path.join(os.getcwd(), "dflows.dprep")

dflow_prepared = dprep.Dataflow.open(file_path)
dflow_prepared.get_profile()
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
      <th>平均值</th>
      <th>標準差</th>
      <th>Variance</th>
      <th>偏度</th>
      <th>峰度</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>vendor</th>
      <td>FieldType.STRING</td>
      <td>1</td>
      <td>VTS</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
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
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_weekday</th>
      <td>FieldType.STRING</td>
      <td>星期五</td>
      <td>星期三</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
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
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2.90047</td>
      <td>2.69355</td>
      <td>9.72889</td>
      <td>16</td>
      <td>19.3713</td>
      <td>22.6974</td>
      <td>23</td>
      <td>23</td>
      <td>14.2731</td>
      <td>6.59242</td>
      <td>43.46</td>
      <td>-0.693723</td>
      <td>-0.570403</td>
    </tr>
    <tr>
      <th>pickup_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>4.99701</td>
      <td>4.95833</td>
      <td>14.1528</td>
      <td>29.3832</td>
      <td>44.6825</td>
      <td>56.4444</td>
      <td>58.9909</td>
      <td>59</td>
      <td>29.427</td>
      <td>17.4333</td>
      <td>303.921</td>
      <td>0.0120999</td>
      <td>-1.20981</td>
    </tr>
    <tr>
      <th>pickup_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.28131</td>
      <td>5</td>
      <td>14.7832</td>
      <td>29.9293</td>
      <td>44.725</td>
      <td>56.7573</td>
      <td>59</td>
      <td>59</td>
      <td>29.7443</td>
      <td>17.3595</td>
      <td>301.351</td>
      <td>-0.0252399</td>
      <td>-1.19616</td>
    </tr>
    <tr>
      <th>dropoff_weekday</th>
      <td>FieldType.STRING</td>
      <td>星期五</td>
      <td>星期三</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
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
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>dropoff_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2.57153</td>
      <td>2</td>
      <td>9.58795</td>
      <td>15.9994</td>
      <td>19.6184</td>
      <td>22.8317</td>
      <td>23</td>
      <td>23</td>
      <td>14.2105</td>
      <td>6.71093</td>
      <td>45.0365</td>
      <td>-0.687292</td>
      <td>-0.61951</td>
    </tr>
    <tr>
      <th>dropoff_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.44383</td>
      <td>4.84694</td>
      <td>14.1036</td>
      <td>28.8365</td>
      <td>44.3102</td>
      <td>56.6892</td>
      <td>59</td>
      <td>59</td>
      <td>29.2907</td>
      <td>17.4108</td>
      <td>303.136</td>
      <td>0.0222514</td>
      <td>-1.2181</td>
    </tr>
    <tr>
      <th>dropoff_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.07801</td>
      <td>5</td>
      <td>14.5751</td>
      <td>29.5972</td>
      <td>45.4649</td>
      <td>56.2729</td>
      <td>59</td>
      <td>59</td>
      <td>29.772</td>
      <td>17.5337</td>
      <td>307.429</td>
      <td>-0.0212575</td>
      <td>-1.226</td>
    </tr>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>Y</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
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
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0781</td>
      <td>-73.7459</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0578</td>
      <td>-73.9639</td>
      <td>-73.9656</td>
      <td>-73.9508</td>
      <td>-73.9255</td>
      <td>-73.8529</td>
      <td>-73.8302</td>
      <td>-73.8238</td>
      <td>-73.7697</td>
      <td>-73.9123</td>
      <td>0.0503757</td>
      <td>0.00253771</td>
      <td>0.352172</td>
      <td>-0.923743</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5755</td>
      <td>40.8799</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.632</td>
      <td>40.7117</td>
      <td>40.7115</td>
      <td>40.7213</td>
      <td>40.7565</td>
      <td>40.8058</td>
      <td>40.8478</td>
      <td>40.8676</td>
      <td>40.8778</td>
      <td>40.7649</td>
      <td>0.0494674</td>
      <td>0.00244702</td>
      <td>0.205972</td>
      <td>-0.777945</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0857</td>
      <td>-73.7209</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0775</td>
      <td>-73.9875</td>
      <td>-73.9882</td>
      <td>-73.9638</td>
      <td>-73.935</td>
      <td>-73.8755</td>
      <td>-73.8125</td>
      <td>-73.7759</td>
      <td>-73.7327</td>
      <td>-73.9202</td>
      <td>0.0584627</td>
      <td>0.00341789</td>
      <td>0.623622</td>
      <td>-0.262603</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5835</td>
      <td>40.8797</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.5973</td>
      <td>40.6928</td>
      <td>40.6911</td>
      <td>40.7226</td>
      <td>40.7567</td>
      <td>40.7918</td>
      <td>40.8495</td>
      <td>40.868</td>
      <td>40.8787</td>
      <td>40.7583</td>
      <td>0.0517399</td>
      <td>0.00267701</td>
      <td>0.0390404</td>
      <td>-0.203525</td>
    </tr>
    <tr>
      <th>passengers</th>
      <td>FieldType.DECIMAL</td>
      <td>1</td>
      <td>6</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>5</td>
      <td>5</td>
      <td>6</td>
      <td>6</td>
      <td>2.39249</td>
      <td>1.83197</td>
      <td>3.3561</td>
      <td>0.763144</td>
      <td>-1.23467</td>
    </tr>
    <tr>
      <th>distance</th>
      <td>FieldType.DECIMAL</td>
      <td>0.01</td>
      <td>32.34</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0108744</td>
      <td>0.743898</td>
      <td>0.738194</td>
      <td>1.243</td>
      <td>2.40168</td>
      <td>4.74478</td>
      <td>10.5136</td>
      <td>14.9011</td>
      <td>21.8035</td>
      <td>3.5447</td>
      <td>3.2943</td>
      <td>10.8524</td>
      <td>1.91556</td>
      <td>4.99898</td>
    </tr>
    <tr>
      <th>cost</th>
      <td>FieldType.DECIMAL</td>
      <td>0.1</td>
      <td>88</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2.33837</td>
      <td>5.00491</td>
      <td>5</td>
      <td>6.93129</td>
      <td>10.524</td>
      <td>17.4811</td>
      <td>33.2343</td>
      <td>50.0093</td>
      <td>63.1753</td>
      <td>13.6843</td>
      <td>9.66571</td>
      <td>93.426</td>
      <td>1.78518</td>
      <td>4.13972</td>
    </tr>
  </tbody>
</table>

您可以藉由將資料行新增至 `dflow_x` 來準備實驗用的資料，以作為建立模型的特徵。 您可以將 `dflow_y` 定義為我們的預測值 **cost**：

```python
dflow_X = dflow_prepared.keep_columns(['pickup_weekday','pickup_hour', 'distance','passengers', 'vendor'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-the-data-into-train-and-test-sets"></a>將資料分成定型集和測試集

現在您可以使用 `sklearn` 程式庫中的 `train_test_split` 函式，將資料分割成定型集和測試集。 此函式會將資料分為用於模型定型的資料集 x (**功能**)，以及用於測試的資料集 y (**要預測的值**)。 `test_size` 參數會決定要配置給測試的資料百分比。 `random_state` 參數會設定隨機產生器的種子，讓您的「訓練-測試」分割一律具有確定性：

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
# flatten y_train to 1d array
y_train.values.flatten()
```

此步驟的目的是要以資料點測試已完成、但尚未用來訓練模型的模型，以評估實際的精確度。 換句話說，訓練完善的模型應該能夠準確地從資料預測它尚未觀察到的部分。 您現在已備妥進行模型自動定型所需的套件和資料。

## <a name="automatically-train-a-model"></a>自動為模型定型

若要自動將模型定型，請執行下列步驟：
1. 定義用於實驗執行的設定。 將訓練資料附加至組態，並修改用來控制訓練程序的設定。
1. 提交實驗來調整模型。 提交實驗之後，程序會根據您定義的條件約束，反覆運算不同的機器學習演算法和超參數設定。 它會將精確度計量最佳化，以選擇最適化模型。

### <a name="define-settings-for-autogeneration-and-tuning"></a>定義自動產生和微調的設定

定義用於自動產生和調整的實驗參數與模型設定。 檢視[設定](how-to-configure-auto-train.md)的完整清單。 提交使用這些預設設定的實驗大約需要 10-15 分鐘的時間，但如果您想要縮短執行時間，請降低 `iterations` 或 `iteration_timeout_minutes`。


|屬性| 本教學課程中的值 |說明|
|----|----|---|
|**iteration_timeout_minutes**|10|每次反覆運算的時間限制 (分鐘)。 降低此值以減少總執行時間。|
|**反覆運算次數**|30|反覆運算次數。 在每次的反覆運算中，都會以您的資料訓練新的機器學習模型。 總執行時間主要會受此值影響。|
|**primary_metric**| spearman_correlation | 您想要最佳化的度量。 最適化模型將根據此計量來選擇。|
|**preprocess**| True | 使用 **True** 時，實驗可以預先處理輸入資料 (處理遺漏的資料、將文字轉換成數值等等)。|
|**verbosity**| logging.INFO | 控制記錄層級。|
|**n_cross_validations**|5|未指定驗證資料時所要執行的交叉驗證分割數目。|



```python
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 30,
    "primary_metric" : 'spearman_correlation',
    "preprocess" : True,
    "verbosity" : logging.INFO,
    "n_cross_validations": 5
}
```

請使用您定義的訓練設定作為 `AutoMLConfig` 物件的參數。 此外，請指定您的訓練資料和模型類型 (在此案例中為 `regression`)。

```python
from azureml.train.automl import AutoMLConfig

# local compute
automated_ml_config = AutoMLConfig(task = 'regression',
                             debug_log = 'automated_ml_errors.log',
                             path = project_folder,
                             X = x_train.values,
                             y = y_train.values.flatten(),
                             **automl_settings)
```

### <a name="train-the-automatic-regression-model"></a>定型自動迴歸模型

開始在本機執行實驗。 將已定義的 `automated_ml_config` 物件傳遞給實驗。 將輸出設定為 `True` 以在實驗期間檢視進度：


```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

顯示的輸出會隨著實驗的執行即時更新。 對於每次反覆運算，您都可以檢視模型類型、執行的持續時間，以及訓練精確度。 欄位 `BEST` 會根據您的計量類型追蹤最佳訓練分數。

    Parent Run ID: AutoML_02778de3-3696-46e9-a71b-521c8fca0651
    *******************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    *******************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler ExtremeRandomTrees                0:00:08       0.9447    0.9447
             1   StandardScalerWrapper GradientBoosting         0:00:09       0.9536    0.9536
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.8580    0.9536
             3   StandardScalerWrapper RandomForest             0:00:08       0.9147    0.9536
             4   StandardScalerWrapper ExtremeRandomTrees       0:00:45       0.9398    0.9536
             5   MaxAbsScaler LightGBM                          0:00:08       0.9562    0.9562
             6   StandardScalerWrapper ExtremeRandomTrees       0:00:27       0.8282    0.9562
             7   StandardScalerWrapper LightGBM                 0:00:07       0.9421    0.9562
             8   MaxAbsScaler DecisionTree                      0:00:08       0.9526    0.9562
             9   MaxAbsScaler RandomForest                      0:00:09       0.9355    0.9562
            10   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            11   MaxAbsScaler LightGBM                          0:00:09       0.9553    0.9602
            12   MaxAbsScaler DecisionTree                      0:00:07       0.9484    0.9602
            13   MaxAbsScaler LightGBM                          0:00:08       0.9540    0.9602
            14   MaxAbsScaler RandomForest                      0:00:10       0.9365    0.9602
            15   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:49       0.9171    0.9602
            17   SparseNormalizer LightGBM                      0:00:08       0.9191    0.9602
            18   MaxAbsScaler DecisionTree                      0:00:08       0.9402    0.9602
            19   StandardScalerWrapper ElasticNet               0:00:08       0.9603    0.9603
            20   MaxAbsScaler DecisionTree                      0:00:08       0.9513    0.9603
            21   MaxAbsScaler SGD                               0:00:08       0.9603    0.9603
            22   MaxAbsScaler SGD                               0:00:10       0.9602    0.9603
            23   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            24   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            25   MaxAbsScaler SGD                               0:00:09       0.9603    0.9603
            26   TruncatedSVDWrapper ElasticNet                 0:00:09       0.9602    0.9603
            27   MaxAbsScaler SGD                               0:00:12       0.9413    0.9603
            28   StandardScalerWrapper ElasticNet               0:00:07       0.9603    0.9603
            29    Ensemble                                      0:00:38       0.9622    0.9622

## <a name="explore-the-results"></a>探索結果

使用 Jupyter 小工具，或藉由檢查實驗歷程記錄，探索自動定型的結果。

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>選項 1：新增 Jupyter 小工具以查看結果

如果您使用 Jupyter Notebook，請使用此 Jupyter Notebook 小工具來查看所有結果的圖表和資料表：


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupyter 小工具執行詳細資料](./media/tutorial-auto-train-models/automl-dash-output.png)
![Jupyter 小工具繪圖](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>選項 2：在 Python 中取得並檢查所有執行的反覆項目

您也可以擷取每個實驗的歷程記錄，並瀏覽每次反覆運算執行的個別計量。 藉由檢查每次個別模型執行的 RMSE (root_mean_squared_error)，您會看到大部分的反覆運算都預測計程車車資費用落在合理的差距內 ($3-4)。

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items() if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

rundata = pd.DataFrame(metricslist).sort_index(1)
rundata
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>20</th>
      <th>21</th>
      <th>22</th>
      <th>23</th>
      <th>24</th>
      <th>25</th>
      <th>26</th>
      <th>27</th>
      <th>28</th>
      <th>29</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>explained_variance</th>
      <td>0.811037</td>
      <td>0.880553</td>
      <td>0.398582</td>
      <td>0.776040</td>
      <td>0.663869</td>
      <td>0.875911</td>
      <td>0.115632</td>
      <td>0.586905</td>
      <td>0.851911</td>
      <td>0.793964</td>
      <td>...</td>
      <td>0.850023</td>
      <td>0.883603</td>
      <td>0.883704</td>
      <td>0.880797</td>
      <td>0.881564</td>
      <td>0.883708</td>
      <td>0.881826</td>
      <td>0.585377</td>
      <td>0.883123</td>
      <td>0.886817</td>
    </tr>
    <tr>
      <th>mean_absolute_error</th>
      <td>2.189444</td>
      <td>1.500412</td>
      <td>5.480531</td>
      <td>2.626316</td>
      <td>2.973026</td>
      <td>1.550199</td>
      <td>6.383868</td>
      <td>4.414241</td>
      <td>1.743328</td>
      <td>2.294601</td>
      <td>...</td>
      <td>1.797402</td>
      <td>1.415815</td>
      <td>1.418167</td>
      <td>1.578617</td>
      <td>1.559427</td>
      <td>1.413042</td>
      <td>1.551698</td>
      <td>4.069196</td>
      <td>1.505795</td>
      <td>1.430957</td>
    </tr>
    <tr>
      <th>median_absolute_error</th>
      <td>1.438417</td>
      <td>0.850899</td>
      <td>4.579662</td>
      <td>1.765210</td>
      <td>1.594600</td>
      <td>0.869883</td>
      <td>4.266450</td>
      <td>3.627355</td>
      <td>0.954992</td>
      <td>1.361014</td>
      <td>...</td>
      <td>0.973634</td>
      <td>0.774814</td>
      <td>0.797269</td>
      <td>1.147234</td>
      <td>1.116424</td>
      <td>0.783958</td>
      <td>1.098464</td>
      <td>2.709027</td>
      <td>1.003728</td>
      <td>0.851724</td>
    </tr>
    <tr>
      <th>normalized_mean_absolute_error</th>
      <td>0.024908</td>
      <td>0.017070</td>
      <td>0.062350</td>
      <td>0.029878</td>
      <td>0.033823</td>
      <td>0.017636</td>
      <td>0.072626</td>
      <td>0.050219</td>
      <td>0.019833</td>
      <td>0.026105</td>
      <td>...</td>
      <td>0.020448</td>
      <td>0.016107</td>
      <td>0.016134</td>
      <td>0.017959</td>
      <td>0.017741</td>
      <td>0.016076</td>
      <td>0.017653</td>
      <td>0.046293</td>
      <td>0.017131</td>
      <td>0.016279</td>
    </tr>
    <tr>
      <th>normalized_median_absolute_error</th>
      <td>0.016364</td>
      <td>0.009680</td>
      <td>0.052101</td>
      <td>0.020082</td>
      <td>0.018141</td>
      <td>0.009896</td>
      <td>0.048538</td>
      <td>0.041267</td>
      <td>0.010865</td>
      <td>0.015484</td>
      <td>...</td>
      <td>0.011077</td>
      <td>0.008815</td>
      <td>0.009070</td>
      <td>0.013052</td>
      <td>0.012701</td>
      <td>0.008919</td>
      <td>0.012497</td>
      <td>0.030819</td>
      <td>0.011419</td>
      <td>0.009690</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_error</th>
      <td>0.047968</td>
      <td>0.037882</td>
      <td>0.085572</td>
      <td>0.052282</td>
      <td>0.065809</td>
      <td>0.038664</td>
      <td>0.109401</td>
      <td>0.071104</td>
      <td>0.042294</td>
      <td>0.049967</td>
      <td>...</td>
      <td>0.042565</td>
      <td>0.037685</td>
      <td>0.037557</td>
      <td>0.037643</td>
      <td>0.037513</td>
      <td>0.037560</td>
      <td>0.037465</td>
      <td>0.072077</td>
      <td>0.037249</td>
      <td>0.036716</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_log_error</th>
      <td>0.055353</td>
      <td>0.045000</td>
      <td>0.110219</td>
      <td>0.065633</td>
      <td>0.063589</td>
      <td>0.044412</td>
      <td>0.123433</td>
      <td>0.092312</td>
      <td>0.046130</td>
      <td>0.055243</td>
      <td>...</td>
      <td>0.046540</td>
      <td>0.041804</td>
      <td>0.041771</td>
      <td>0.045175</td>
      <td>0.044628</td>
      <td>0.041617</td>
      <td>0.044405</td>
      <td>0.079651</td>
      <td>0.042799</td>
      <td>0.041530</td>
    </tr>
    <tr>
      <th>r2_score</th>
      <td>0.810900</td>
      <td>0.880328</td>
      <td>0.398076</td>
      <td>0.775957</td>
      <td>0.642812</td>
      <td>0.875719</td>
      <td>0.021603</td>
      <td>0.586514</td>
      <td>0.851767</td>
      <td>0.793671</td>
      <td>...</td>
      <td>0.849809</td>
      <td>0.880142</td>
      <td>0.880952</td>
      <td>0.880586</td>
      <td>0.881347</td>
      <td>0.880887</td>
      <td>0.881613</td>
      <td>0.548121</td>
      <td>0.882883</td>
      <td>0.886321</td>
    </tr>
    <tr>
      <th>root_mean_squared_error</th>
      <td>4.216362</td>
      <td>3.329810</td>
      <td>7.521765</td>
      <td>4.595604</td>
      <td>5.784601</td>
      <td>3.398540</td>
      <td>9.616354</td>
      <td>6.250011</td>
      <td>3.717661</td>
      <td>4.392072</td>
      <td>...</td>
      <td>3.741447</td>
      <td>3.312533</td>
      <td>3.301242</td>
      <td>3.308795</td>
      <td>3.297389</td>
      <td>3.301485</td>
      <td>3.293182</td>
      <td>6.335581</td>
      <td>3.274209</td>
      <td>3.227365</td>
    </tr>
    <tr>
      <th>root_mean_squared_log_error</th>
      <td>0.243184</td>
      <td>0.197702</td>
      <td>0.484227</td>
      <td>0.288349</td>
      <td>0.279367</td>
      <td>0.195116</td>
      <td>0.542281</td>
      <td>0.405559</td>
      <td>0.202666</td>
      <td>0.242702</td>
      <td>...</td>
      <td>0.204464</td>
      <td>0.183658</td>
      <td>0.183514</td>
      <td>0.198468</td>
      <td>0.196067</td>
      <td>0.182836</td>
      <td>0.195087</td>
      <td>0.349935</td>
      <td>0.188031</td>
      <td>0.182455</td>
    </tr>
    <tr>
      <th>spearman_correlation</th>
      <td>0.944743</td>
      <td>0.953618</td>
      <td>0.857965</td>
      <td>0.914703</td>
      <td>0.939846</td>
      <td>0.956159</td>
      <td>0.828187</td>
      <td>0.942069</td>
      <td>0.952581</td>
      <td>0.935477</td>
      <td>...</td>
      <td>0.951287</td>
      <td>0.960335</td>
      <td>0.960195</td>
      <td>0.960279</td>
      <td>0.960288</td>
      <td>0.960323</td>
      <td>0.960161</td>
      <td>0.941254</td>
      <td>0.960293</td>
      <td>0.962158</td>
    </tr>
    <tr>
      <th>spearman_correlation_max</th>
      <td>0.944743</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>...</td>
      <td>0.960303</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.962158</td>
    </tr>
  </tbody>
</table>
<p>12 個資料列 × 30 個資料行</p>
</div>

## <a name="retrieve-the-best-model"></a>擷取最佳模型

從我們的反覆項目中選取最佳管線。 `automl_classifier` 上的 `get_output` 方法會傳回最佳執行和上一個配適引動過程的已配適模型。 藉由在 `get_output` 上使用多載，您便可以針對任何已記錄的計量或特定的反覆項目，擷取最佳執行和配適模型：

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-the-best-model-accuracy"></a>測試最佳模型的精確度

使用最佳模型在測試資料集上執行預測，以預測計程車車資。 `predict` 函式會使用最佳模型，並從 `x_test` 資料集預測 y 值 (**行程成本**)。 從 `y_predict` 列印前 10 個預測成本值：

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

建立散佈圖，以視覺化方式呈現預測成本值與實際成本值的比較。 下列程式碼使用 `distance` 特徵作為 x 軸，以及行程 `cost` 作為 y 軸。 為了比較每個行程距離值之預測成本的變化，會將前 100 個預測成本值和實際成本值建立成個別的序列。 檢查繪圖會顯示距離/成本近似線性關係，而且預測的成本值在大部分情況下都非常接近相同車程距離的實際成本值。

```python
%matplotlib inline

import matplotlib.pyplot as plt

fig = plt.figure(figsize=(14, 10))
ax1 = fig.add_subplot(111)

distance_vals = [x[4] for x in x_test.values]
y_actual = y_test.values.flatten().tolist()

ax1.scatter(distance_vals[:100], y_predict[:100], s=18, c='b', marker="s", label='Predicted')
ax1.scatter(distance_vals[:100], y_actual[:100], s=18, c='r', marker="o", label='Actual')

ax1.set_xlabel('distance (mi)')
ax1.set_title('Predicted and Actual Cost/Distance')
ax1.set_ylabel('Cost ($)')

plt.legend(loc='upper left', prop={'size': 12})
plt.rcParams.update({'font.size': 14})
plt.show()
```

![預測散佈圖](./media/tutorial-auto-train-models/automl-scatter-plot.png)

計算結果的 `root mean squared error`。 使用 `y_test` 資料框架。 將其轉換為要與預測值比較的清單。 `mean_squared_error` 函式會採用兩個值陣列，並計算這兩個陣列之間的均方誤差。 取結果的平方根會產生與 y 變數 (**成本**) 相同單位的誤差。 這大致上可表示計程車車資預測與實際車資的差距：

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

    3.2204936862688798

請執行下列程式碼，以使用完整的 `y_actual` 和 `y_predict` 資料集來計算平均絕對百分比誤差 (MAPE)。 此計量會計算每個預測值與實際值之間的絕對差異，並加總所有差異。 然後再以實際值總計的百分比來表示該總和：

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
    0.10545153869569586

    Model Accuracy:
    0.8945484613043041

從最終的預測精確度計量中，您會看到模型從資料集的特性預測計程車車資的表現相當不錯，大多在 +- 3.00 美元以內。 傳統的機器學習模型開發程序會耗費大量資源，而且需要投入大量的網域知識和時間來執行並比較數十個模型的結果。 使用自動化機器學習，是您針對個人案例快速測試許多不同模型的絕佳方式。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在此自動化機器學習教學課程中，您已執行下列工作：

> [!div class="checklist"]
> * 設定工作區和備妥用於實驗的資料。
> * 搭配自訂參數在本機使用自動化迴歸模型來進行定型。
> * 瀏覽及檢閱定型結果。

使用 Azure Machine Learning [部署模型](tutorial-deploy-models-with-aml.md)。
