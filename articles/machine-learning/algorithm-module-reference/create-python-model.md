---
title: 建立 Python 模型：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務中建立 Python 模型的模型，來建立自訂的模型或資料處理模組。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ea9b50cede3e2d264ca0476b6a987ca6896c3c79
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029741"
---
# <a name="create-python-model"></a>建立 Python 模型

這篇文章說明如何使用**建立 Python 模型**模組建立未定型的模型，從 Python 指令碼。 

您可以根據在 Azure Machine Learning 環境中的 Python 套件所包含的任何學習模組的模型。 

在建立模型之後，您可以使用[訓練模型](train-model.md)來定型模型的資料集，例如在 Azure Machine Learning 中的任何其他學習模組。 定型的模型可以傳遞至[評分模型](score-model.md)使用模型來進行預測。 接著可以儲存已定型的模型，並將計分工作流程可以發行為 web 服務。

> [!WARNING]
> 目前不可以傳遞至 Python 模型的計分的結果[評估模型](evaluate-model.md)。 如果您需要評估模型，您可以撰寫自訂的 Python 指令碼，並執行使用加以[執行 Python 指令碼](execute-python-script.md)模組。  


## <a name="how-to-configure-create-python-model"></a>如何設定建立 Python 模型

使用此模組需要中繼或專業知識的 Python。 此模組支援使用包含在已安裝 Azure Machine Learning 中的 Python 套件的任何學習模組。 請參閱中的預先安裝的 Python 套件清單[執行 Python 指令碼](execute-python-script.md)。
  

本文將說明如何使用**建立 Python 模型**的簡單實驗。 以下是實驗圖形。

![create-python-model](./media/module/aml-create-python-model.png)

1.  按一下 **建立 Python 模型**、 編輯指令碼來實作您的模型或資料管理程序。 您可以根據在 Azure Machine Learning 環境中的 Python 套件所包含的任何學習模組的模型。


    以下是雙類別貝氏機率分類分類器的範例程式碼使用熱門*sklearn*封裝。

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. 連接**建立 Python 模型**您剛才建立的模組**定型模型**和**評分模型**

3. 如果您需要評估模型，新增[執行 Python 指令碼](execute-python-script.md)和編輯的 Python 指令碼，以實作評估。

以下是範例評估程式碼。

```Python


# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to 
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):
    
    from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
    import pandas as pd
    import numpy as np
    
    scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
    ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
    ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
    probabilities = scores["probabilities"]
    
    accuracy, precision, recall, auc = \
    accuracy_score(ytrue, ypred),\
    precision_score(ytrue, ypred),\
    recall_score(ytrue, ypred),\
    roc_auc_score(ytrue, probabilities)
    
    metrics = pd.DataFrame();
    metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
    metrics["Value"] = [accuracy, precision, recall, auc]
    
    return metrics,

```
