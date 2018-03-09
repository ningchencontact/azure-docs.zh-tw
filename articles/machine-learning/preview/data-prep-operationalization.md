---
title: "關於如何使用 Azure Machine Learning 資料準備運算化的深度指南 | Microsoft Docs"
description: "本文件提供事先執行設計之資料來源和資料準備套件的詳細資料"
services: machine-learning
author: hughz
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/13/2018
ms.openlocfilehash: 0849747fe6d66d55d11c131b51b07d8f689774e1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2018
---
# <a name="data-preparation-operationalization"></a>資料準備運算化 

## <a name="operationalization-scenario"></a>運算化案例

以下是使用者可能遇到的各種資料準備運算化案例。

### <a name="develop-your-model-based-on-training-data"></a>根據訓練資料開發您的模型

假設您想要建立及部署即時評分 API 服務。 第一個步驟是開發可根據一些訓練資料集進行評分的模型。 「資料準備」會匯入訓練資料的範例，作為新的資料來源。 資料準備好後，DataPrep 套件會包含準備步驟。 使用 AzureML 測試帳戶，使用者可以反覆執行機器學習模型，為訓練資料中的每項輸入產生標籤。

當資料的特性需要更新時，使用者會回過頭使用 DataPrep 套件以新的方式準備資料，並儲存這些步驟。 這個產生新特性並調整其機器學習模型的反覆執行程序會持續進行，直到模型可正確對其測試資料評分為止。 

### <a name="deploy-your-model-to-the-azure-model-management-service"></a>將您的模型部署至 Azure 模型管理服務

現在，您想要對客戶資料進行即時評分。 您可以使用 Azure 模型管理服務，從 AzureML Python CLI 將此模型部署為服務。 已部署的服務會公開 REST 端點以即時接收客戶資料，並從您的定型模型傳回對應的輸出標籤。

為了方便使用，模型端點可接受 JSON 格式的資料。 輸入應為表示二維資料陣列的 JSON 字串，此字串會透過 ReadJSONLiteral 轉換傳遞，並轉換為 DataPrep 資料來源。 在測試階段建立的資料準備套件，隨後可對串流 JSON 資料執行。 產生的資料框架接下來可傳至定型模型進行評分。

## <a name="read-json-literal-transformation"></a>讀取 JSON 常值轉換

### <a name="description"></a>說明

基於運算化用途，「資料準備」會包含 **ReadJsonLiteral** 轉換，以執行活動並產生 Pandas 或 Spark Dataframe。 這項轉換只會以現有的資料準備套件和 JSON 資料來源作為輸入。 這項轉換會透過 DataPrep Python CLI 公開。

### <a name="instructions"></a>範例的指示

#### <a name="pythoncli"></a>PythonCLI

從 Azure Machine Learning Workbench 開啟命令列介面 ([檔案] > [開啟命令提示字元])。

在此範例中，會使用 TrainingPreparationSteps 資料準備套件來準備資料，並將容量特性新增至每個輸入。

```
>>> import azureml.dataprep.package as azdp

>>> azdp.run_on_data.__doc__
"Generate a dataframe based on a selected dataflow in a package using in-memory data sources.
'user_config' is expected as a dictionary that maps the absolute path of a dsource file to an in-memory data source represented as a list of lists."

>>> real_time_customer_data = [[1.0, 1.5, 2.5], [2.5, 1.5, 3]]
>>> azdp.run_on_data({ "C:\\TrainingSampleData.dsource": real_time_customer_data}, "C:\\TrainingPreparationSteps.dprep")
    Height   Width   Depth  Volumne
0   1.0       1.5    2.5    3.75
1   2.5       1.5    3.0    11.25
```

`run_on_data()` 具有下列選擇性參數
 - `dataflow_idx`：指定要在套件中執行之資料流程的索引
 - `secrets`：密碼存放區字典 (索引鍵：secretId，值：儲存的密碼)
 - `spark`：提供執行 Spark 的 Spark 工作階段

#### <a name="input"></a>輸入

一個二維陣列輸入 (「陣列的陣列」)。 在 Python 中，輸入應為清單的清單。 由於 JSON 沒有原生日期類型，因此任何 Python datetime.datetime 物件都將轉換為 ISO 格式的日期字串。 對於 REST 端點，輸入應為表示 2-D JSON 陣列的 JSON 常值字串。

#### <a name="output"></a>輸出

Pandas 或 Spark DataFrame。 DataFrame 的類型取決於執行組態中選取的架構 (`.runconfig`)。 產生的資料框架可作為輸入傳至定型模型進行評分。
