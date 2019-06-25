---
title: 在定型執行的記錄度量
titleSuffix: Azure Machine Learning service
description: 了解如何將記錄新增至您的訓練指令碼、如何提交實驗、如何檢查執行中作業的進度，以及如何檢視執行結果。 您可以追蹤您的實驗，並監視度量，以提高模型建立程序。
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d3cbc2d5be1f7addf833162b23c5db0786e9d361
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66297484"
---
# <a name="log-metrics-during-training-runs-in-azure-machine-learning"></a>記錄度量在定型期間執行 Azure Machine Learning 中

在這篇文章，了解如何將記錄新增至您的訓練指令碼、 提交實驗執行、 監視執行，並在 Azure Machine Learning 服務中檢視測試回合的結果。 提升模型建立程序，追蹤您的實驗，並監視度量。 

## <a name="list-of-training-metrics"></a>訓練計量的清單 

訓練實驗時，可以將下列計量新增到執行中。 若要檢視可在回合中追蹤之內容的更詳細清單，請參閱 [Run 類別參考文件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) \(英文\)。

|類型| Python 函式 | 注意|
|----|:----|:----|
|純量值 |函式：<br>`run.log(name, value, description='')`<br><br>範例：<br>run.log("accuracy", 0.95) |使用指定名稱將數字或字串值記錄到執行中。 將計量記錄到執行中，會導致該計量儲存在實驗的執行記錄中。  您可以在執行中多次記錄相同的計量，結果會視為該計量的向量。|
|清單|函式：<br>`run.log_list(name, value, description='')`<br><br>範例：<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | 使用指定名稱將值清單記錄到執行中。|
|資料列|函式：<br>`run.log_row(name, description=None, **kwargs)`<br>範例：<br>run.log_row("Y over X", x=1, y=0.4) | 使用 log_row  建立計量，並於其中包含 kwargs 中描述的多個資料行。 每個具名的參數都會產生一個具有指定值的資料行。  可以呼叫一次 *log_row* 以記錄任意 Tuple，或者在迴圈中多次呼叫以產生完整的資料表。|
|資料表|函式：<br>`run.log_table(name, value, description='')`<br><br>範例：<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | 使用指定名稱將字典物件記錄到執行中。 |
|映像|函式：<br>`run.log_image(name, path=None, plot=None)`<br><br>範例：<br>`run.log_image("ROC", plt)` | 將映像記錄到執行記錄中。 使用 log_image 將映像檔案或 matplotlib 繪圖記錄到執行中。  這些映像會顯示在執行記錄中，並可供比較。|
|標記執行|函式：<br>`run.tag(key, value=None)`<br><br>範例：<br>run.tag("selected", "yes") | 使用字串索引鍵和可選字串值標記執行。|
|上傳檔案或目錄|函式：<br>`run.upload_file(name, path_or_stream)`<br> <br> 範例：<br>run.upload_file("best_model.pkl", "./model.pkl") | 將檔案上傳到執行記錄。 執行會自動擷取特定輸出目錄中的檔案，對於大多數執行類型，預設為「./outputs」。  只有在需要上傳其他檔案或未指定輸出目錄時，才使用 upload_file。 我們建議在名稱中加上 `outputs`，以便將其上傳到輸出目錄。 您可以透過呼叫 `run.get_file_names()`，列出與該執行記錄相關聯的所有檔案|

> [!NOTE]
> 純量、清單、資料列和資料表的計量可具有以下類型：浮點數、整數或字串。

## <a name="start-logging-metrics"></a>開始記錄計量

如果您想要追蹤或監視您的實驗，必須新增程式碼，以在提交執行時開始記錄。 以下是觸發執行提交的方法：
* __Run.start_logging__ - 將記錄函式加入您的定型指令碼中，並在指定的實驗中，啟動互動式記錄工作階段。 **start_logging** 可建立互動式執行，以便用於 Notebook 等環境中。 工作階段期間記錄的所有計量都會加入實驗的執行記錄中。
* __ScriptRunConfig__ - 將記錄函式加入定型指令碼中，並在執行時載入整個指令碼資料夾。  **ScriptRunConfig** 是一種用於設定指令碼執行設定的類別。 使用此選項，您可以新增監視程式碼以通知完成，或取得視覺小工具以進行監視。

## <a name="set-up-the-workspace"></a>設定工作區
新增記錄並提交實驗之前，您必須先設定工作區。

1. 使用工作區。 若要深入了解設定工作區設定，請依照中的步驟[建立 Azure 機器學習服務工作區](setup-create-workspace.md#sdk)。

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```
  
## <a name="option-1-use-startlogging"></a>選項 1：使用 start_logging

**start_logging** 可建立互動式執行，以便用於 Notebook 等環境中。 工作階段期間記錄的所有計量都會加入實驗的執行記錄中。

以下範例在本機 Jupyter Notebook 中，將簡單的 sklearn Ridge 模型定型。 若要深入了解將實驗提交至不同的環境，請參閱[使用 Azure Machine Learning 服務設定模型定型的計算目標](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets)。

1. 在本機 Jupyter Notebook 中建立定型指令碼。 

   ```python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. 使用 Azure Machine Learning 服務 SDK 新增實驗定型，並將保存的模型上傳至實驗執行記錄。 以下程式碼新增標記、記錄，並將模型檔案上傳到實驗執行中。

   ```python
   # Get an experiment object from Azure Machine Learning
   experiment = Experiment(workspace = ws, name = "train-within-notebook")
  
   # Create a run object in the experiment
   run = experiment.start_logging()# Log the algorithm parameter alpha to the run
   run.log('alpha', 0.03)

   # Create, fit, and test the scikit-learn Ridge regression model
   regression_model = Ridge(alpha=0.03)
   regression_model.fit(data['train']['X'], data['train']['y'])
   preds = regression_model.predict(data['test']['X'])

   # Output the Mean Squared Error to the notebook and to the run
   print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
   run.log('mse', mean_squared_error(data['test']['y'], preds))

   # Save the model to the outputs directory for capture
   joblib.dump(value=regression_model, filename='outputs/model.pkl')

   # Take a snapshot of the directory containing this notebook
   run.take_snapshot('./')

   # Complete the run
   run.complete()
  
   ```

指令碼以 ```run.complete()``` 結尾，完成時會標記執行。  此函式通常用於互動式 Notebook 案例中。

## <a name="option-2-use-scriptrunconfig"></a>選項 2：使用 ScriptRunConfig

[**ScriptRunConfig** ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py)是一種類別，針對執行指令碼的組態設定。 使用此選項，您可以新增監視程式碼以通知完成，或取得視覺小工具以進行監視。

此範例擴展上述的基本 sklearn Ridge 模型。 它執行簡單的參數掃掠以掃掠模型的 Alpha 值，以在實驗下的執行中擷取計量和定型的模型。 此範例會針對使用者管理的環境在本機上執行。 

1. 建立定型指令碼 `train.py`。

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. `train.py` 指令碼參考 `mylib.py`，可讓您取得要在 Ridge 模型中使用的 Alpha 值清單。

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. 設定使用者管理的本機環境。

   ```python
   from azureml.core.runconfig import RunConfiguration

   # Editing a run configuration property on-fly.
   run_config_user_managed = RunConfiguration()

   run_config_user_managed.environment.python.user_managed_dependencies = True

   # You can choose a specific Python environment by pointing to a Python path 
   #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
   ```

4. 提交要在使用者管理環境中執行的 ```train.py``` 指令碼。 提交整個指令碼資料夾進行定型，包括 ```mylib.py``` 檔案。

   ```python
   from azureml.core import ScriptRunConfig
  
   experiment = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory = './', script = 'train.py', run_config = run_config_user_managed)
   run = experiment.submit(src)
   ```

## <a name="manage-a-run"></a>管理測試回合

[開始]、 [監視器] 和 [取消定型執行](how-to-manage-runs.md)篇文章強調特定的 Azure Machine Learning 工作流程，如何管理您的實驗。

## <a name="view-run-details"></a>檢視執行詳細資料

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>使用 Jupyter Notebook 小工具執行監視
當您使用 **ScriptRunConfig** 方法以提交執行時，您可以查看使用 Jupyter Notebook 小工具執行的進度。 就像執行提交一樣，小工具為非同步工作，並每隔 10 至 15 秒提供即時更新，直到工作完成為止。

1. 等待執行完成時，檢視 Jupyter 小工具。

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Jupyter Notebook 小工具的螢幕擷取畫面](./media/how-to-track-experiments/widgets.PNG)

2. **[適用於自動化機器學習回合]** 存取來自上一個回合的圖表。 取代`<<experiment_name>>`具有適當的實驗名稱：

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![適用於自動化機器學習的 Jupyter Notebook 小工具](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


若要進一步檢視管線的詳細資料，在表格中按一下您想要探索的管線，而圖表將呈現在 Azure 入口網站的快顯視窗中。

### <a name="get-log-results-upon-completion"></a>在完成時取得記錄檔結果

模型定型和監視在背景進行，以便您可以在等待時執行其他工作。 您也可以等到模型完成定型，再執行更多程式碼。 當您使用 **ScriptRunConfig** 時，可以使用 ```run.wait_for_completion(show_output = True)``` 以顯示模型定型完成的時間。 ```show_output``` 旗標為您提供詳細資訊輸出。 
  
### <a name="query-run-metrics"></a>查詢執行計量

您可以使用 ```run.get_metrics()``` 檢視定型模型的計量。 您現在可以取得上述範例中記錄的所有計量，以確定最佳模型。

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal"></a>在 Azure 入口網站中檢視實驗

當實驗完成執行時，您可以瀏覽記錄的實驗執行記錄。 您可以使用下列兩種方式來存取記錄：

* 直接取得執行的 URL ```print(run.get_portal_url())```
* 藉由提交執行名稱 (在此情況下為 ```run```)，以檢視執行詳細資料。 此方式可為您指出實驗名稱、識別碼、類型、狀態、詳細資料頁面、Azure 入口網站的連結，以及文件連結。

執行連結將帶您直接前往 Azure 入口網站中的執行詳細資料頁面。 在這裡，您可以看到實驗中記錄的任何屬性、追蹤計量、映像和圖表。 在此情況下，我們會記錄 MSE 和 Alpha 值。

  ![Azure 入口網站中的回合詳細資料](./media/how-to-track-experiments/run-details-page-web.PNG)

您也可以檢視執行的任何輸出或記錄，或下載讓您提交的實驗快照集，以便與其他人共用實驗資料夾。

### <a name="viewing-charts-in-run-details"></a>檢視執行詳細資料中的圖表

有各種方式可以在執行期間使用記錄 API 記錄不同類型的計量，並以 Azure 入口網站中的圖表形式加以檢視。 

|記錄的值|程式碼範例| 在入口網站中檢視|
|----|----|----|
|記錄數值的陣列| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|單一變數的折線圖|
|透過重複使用的相同計量名稱，記錄單一數值 (例如，從 for 迴圈中)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| 單一變數的折線圖|
|使用 2 個數字資料行重複記錄資料列|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|兩個變數的折線圖|
|使用 2 個數字資料行記錄資料表|`run.log_table(name='Sine Wave', value=sines)`|兩個變數的折線圖|

<a name="auto"></a>
## <a name="understanding-automated-ml-charts"></a>了解自動化 ML 圖表

在 Notebook 中提交自動化 ML 作業之後，您可以在機器學習服務工作區中找到這些回合的記錄。 

深入了解：
+ [分類模型的圖表和曲線](#classification)
+ [迴歸模型的圖表和圖形](#regression)
+ [模型說明能力](#model-explain-ability-and-feature-importance)


### <a name="view-the-run-charts"></a>檢視回合圖表

1. 移至工作區。 

1. 在工作區的最左邊面板中，選取 [實驗]  。

   ![實驗功能表的螢幕擷取畫面](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_menu.PNG)

1. 選取您感興趣的實驗。

   ![實驗清單](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_list.PNG)

1. 在表格中，選取回合數。

   ![實驗回合](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_run.PNG)

1. 在表格中，選取您想要進一步探索之模型的重覆項目數。

   ![實驗模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_model.PNG)



### <a name="classification"></a>分類

對於您使用 Azure Machine Learning 自動化機器學習功能建置的每個分類模型，您可以查看下列圖表： 
+ [混淆矩陣](#confusion-matrix)
+ [精確度與召回率圖表](#precision-recall-chart)
+ [接收者操作特徵 (或 ROC)](#roc)
+ [升力曲線](#lift-curve)
+ [增益曲線](#gains-curve)
+ [校正圖](#calibration-plot)

#### <a name="confusion-matrix"></a>混淆矩陣

混淆矩陣可用來說明分類模型的效能。 每一列都會顯示真值類別的執行個體，而每一行均代表預測類別的執行個體。 混淆矩陣會針對指定的模型來顯示分類正確的標籤和分類不正確的標籤。

針對分類問題，Azure Machine Learning 會針對每個已建置的模型自動提供混淆矩陣。 針對每個混淆矩陣，自動化 ML 會將分類正確的標籤顯示為綠色，並將分類不正確的標籤顯示為紅色。 圓形的大小代表該組合中的樣本數。 此外，每個預測標籤和每個真值標籤的頻率計數均會顯示於相鄰的長條圖中。 

範例 1：準確度很差的分類模型 ![準確度很差的分類模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix1.PNG)

範例 2：高準確度的分類模型 (理想) ![高準確度的分類模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix2.PNG)


#### <a name="precision-recall-chart"></a>精確度與召回率圖表

利用此圖表，您可以比較每個模型的精確度與召回率曲線，以針對您的特定商務問題來判斷哪一個模型在精確度與召回率之間具有可接受的關聯性。 此圖表顯示宏平均精確度與召回率、微平均精確度與召回率，以及與模型之所有類別相關聯的精確度與召回率。

精確度一詞代表分類器能夠正確標示所有執行個體。 召回率代表分類器能夠針對特定標籤找到的所有執行個體。 精確度與召回率曲線會顯示這兩個概念之間的關聯性。 在理想情況下，此模型會有 100% 的精確度和 100% 的準確度。

範例 1：具有低精確度與低召回率的分類模型 ![具有低精確度與低召回率的分類模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall1.PNG)

範例 2：具有 ~100% 精確度與 ~100% 召回率的分類模型 (理想) ![具有高精確度與召回率的分類模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall2.PNG)

#### <a name="roc"></a>ROC

接收者操作特徵 (或 ROC) 是對於特定模型之分類正確標籤與分類不正確標籤的繪圖。 在具有高偏差的資料集上將模型定型時，ROC 曲線可提供的資訊較少，因為它將不會顯示誤判標籤。

範例 1：具有低確判標籤與高誤判標籤的分類模型 ![具有低確判標籤與高誤判標籤的分類模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc1.PNG)

範例 2：具有高確判標籤與低誤判標籤的分類模型 ![具有高確判標籤與低誤判標籤的分類模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc2.PNG)

#### <a name="lift-curve"></a>升力曲線

您可以將使用 Azure Machine Learning 自動建置的模型升力與基準進行比較，以檢視該特定模型的值增益。

升力圖可用來評估分類模型的效能。 它會顯示相較於不使用模型，您能夠預期使用模型會做得更好。 

範例 1：模型執行效能比隨機選取模型更糟 ![比隨機選取模型更糟的分類模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve1.PNG)

範例 2：模型執行效能優於隨機選取模型 ![執行得更好的分類模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve2.PNG)

#### <a name="gains-curve"></a>增益曲線

增益圖會依每個部分的資料來評估分類模型的效能。 它會顯示資料集的每個百分位數值，相較於隨機選取模型，您能夠預期它會執行得更好。

使用累計增益圖，可協助您使用對應至模型中所需增益的百分比來選擇分類截止。 此資訊提供另一種方式來查看隨附升力圖中的結果。

範例 1：增益最少的分類模型 ![增益最少的分類模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve1.PNG)

範例 2：增益顯著的分類模型 ![增益顯著的分類模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve2.PNG)

#### <a name="calibration-plot"></a>校正圖

針對所有分類問題，您可以檢閱微平均、宏平均及指定預測模型中每個類別的校正線。 

校正圖可用來顯示預測模型的信賴度。 它會顯示預測機率與實際機率之間的關聯性，其中「機率」代表特定執行個體屬於某個標籤的可能性。 經過準確校正的模型會與 y=x 線對齊，在其預測中具有合理的信賴度。 過度信賴的模型會與 y=0 線對齊，其會顯示預測機率，但沒有任何實際機率。

範例 1：經過更準確校正的模型 ![經過更準確校正的模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve1.PNG)

範例 2：過度信賴的模型 ![過度信賴的模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve2.PNG)

### <a name="regression"></a>迴歸
對於您使用 Azure Machine Learning 自動化機器學習功能建置的每個迴歸模型，您可以查看下列圖表： 
+ [預測與真值](#pvt)
+ [殘差直方圖](#histo)

<a name="pvt"></a>

#### <a name="predicted-vs-true"></a>預測與True

「預測與真值」會針對迴歸問題顯示預測值與其相互關聯真值之間的關聯性。 此圖表可用來測量模型的效能，因為預設值愈接近 y=x 線，預測模型的準確度就愈好。

在每個回合之後，您都能查看每個迴歸模型的預測與真值圖。 為了保護資料隱私權，會將值組合在一起，而每組的大小均會顯示為圖表區域下半部的長條圖。 您可以根據模型所在的理想值，將預測模型與顯示誤差幅度且顏色較淡的陰影區域進行比較。

範例 1：在預測中準確度較低的迴歸模型 ![在預測中準確度較低的迴歸模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression1.PNG)

範例 2：在其預測中準確度較高的迴歸模型 ![在其預測中準確度較高的迴歸模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression2.PNG)

<a name="histo"></a>

#### <a name="histogram-of-residuals"></a>殘差直方圖

殘差代表觀察到的 y - 預測的 y。 若要顯示低偏差的錯誤幅度，殘差直方圖應該會形成以 0 為中心的鐘形曲線。 

範例 1：其錯誤中有偏差的迴歸模型 ![其錯誤中有偏差的 SA 迴歸模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression3.PNG)

範例 2：錯誤分佈更均勻的迴歸模型 ![錯誤分佈更均勻的迴歸模型](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression4.PNG)

### <a name="model-explain-ability-and-feature-importance"></a>模型說明能力與特徵重要性

特徵重要性讓分數能夠指出每個特徵在模型建構中有多麼重要。 您可以檢閱整個模型以及預測模型上每個類別的特徵重要性分數。 您可以查看每個特徵的重要性如何針對每個類別及整體進行比較。

![特徵說明能力](./media/how-to-track-experiments/azure-machine-learning-auto-ml-feature_explain1.PNG)

## <a name="example-notebooks"></a>Notebook 範例
下列 Notebook 示範了此文章中說明的概念：
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/training/logging-api/logging-api.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

請嘗試下列步驟，了解如何使用適用於 Python 的 Azure Machine Learning SDK：

* 關於如何註冊最佳模型，並在教學課程中加以部署的範例，請參閱[使用 Azure Machine Learning 定型映像分類模型](tutorial-train-models-with-aml.md)。

* 了解如何[使用 Azure Machine Learning 定型 PyTorch 模型](how-to-train-pytorch.md)。
