---
title: 使用適用於 Python 的 Machine Learning 資料準備 SDK 來準備資料 - Azure
description: 了解如何使用適用於 Python 的 Azure Machine Learning 資料準備 SDK 來載入各種格式的資料、將資料轉換為更實用的格式，然後該資料寫入到某個位置供您的模型存取。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 11/27/2018
ms.openlocfilehash: 91d0f3565db484504a67a3b6ae0989b9291cd24f
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446413"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>使用 Azure Machine Learning 準備模型用的資料

在本文中，您會認識到 Azure Machine Learning 資料準備 SDK 的使用案例與獨特功能。 資料準備是機器學習工作流程中最重要的一個部分。 若未經過大幅清理與轉換，真實世界的資料通常破碎、不一致，或是無法當做訓練資料使用。 修正未經處理資料中的錯誤與異常情形，並建立與您想嘗試解決之問題相關的新特徵，會提升模型的精確度。 此 SDK 採用的設計，對其他常見資料準備程式庫的使用者而言應相當熟悉，且能提供重要案例的優勢，並維持與其他程式庫的互通性。

您可以使用 [Azure Machine Learning 資料準備 SDK](https://aka.ms/data-prep-sdk)，以 Python 準備資料。

## <a name="azure-machine-learning-data-prep-sdk"></a>Azure Machine Learning 資料準備 SDK

[Azure Machine Learning 資料準備 SDK](https://aka.ms/data-prep-sdk) 是 Python 程式庫，可提供以下項目：

* 智慧省時轉換功能，例如模糊群組、依範例衍生資料行、自動分割、智慧讀取檔案，以及不齊右結構描述的處理。
* 在本機處理小型資料或在雲端處理大型資料的單一 API，**幾乎沒有變更程式碼**。
* 以串流方式處理資料 (而非載入記憶體)，而能更有效率地在單一電腦上進行調整。

### <a name="install-the-sdk"></a>安裝 SDK

使用下列命令，將 SDK 安裝在您的 Python 環境中。

```shell
pip install azureml-dataprep
```

使用下列程式碼匯入套件。

```python
import azureml.dataprep as dprep
```

### <a name="examples-and-reference"></a>範例和參考

若要深入了解此 SDK 的模組與函式，請參閱[資料準備 SDK 參考文件](https://aka.ms/data-prep-sdk) (英文)。

下列範例重點放在 SDK 的某些獨特功能，包括：

* 自動偵測檔案類型
* 智慧轉換
* 摘要統計資料
* 跨環境功能


#### <a name="automatic-file-type-detection"></a>自動偵測檔案類型

使用 `smart_read_file()` 函式載入資料，無需指定檔案類型。 此函式會自動辨識與剖析檔案類型。

```python
dataflow = dprep.smart_read_file(path="<your-file-path>")
```

#### <a name="intelligent-transforms"></a>智慧轉換

使用 SDK 可依範例與推斷來分割與衍生資料行，將功能的設計過程自動化。 假設在您的資料流程物件中，有名為 `datetime` 且含有 `2018-09-15 14:30:00` 值的欄位。

若要自動分割 `datetime` 欄位，請呼叫下列函式。

```python
new_dataflow = dataflow.split_column_by_example(source_column="datetime")
```

在不定義範例參數的情況下，此函式會自動將 `datetime` 欄位分割成兩個欄位，分別是 `datetime_1` 與 `datetime_2`。 產生的值分別是 `2018-09-15` 與 `14:30:00`。 也可以提供範例模式，SDK 會預測和執行您要的轉換。 下列程式碼使用相同的 `datetime` 物件，會根據提供的範例，為工作日建立新資料行 `datetime_weekday`。

```python
new_dataflow = dataflow.derive_column_by_example(
        source_columns="datetime", 
        new_column_name="datetime_weekday", 
        example_data=[("2009-01-04 10:12:00", "Sunday"), ("2013-08-22 17:00:00", "Thursday")]
    )
```

#### <a name="summary-statistics"></a>摘要統計資料

您可以使用一行程式碼來產生資料流程的快速摘要統計資料。 這個方法有利於了解您的資料及其分布狀況。

```python
dataflow.get_profile()
```

在資料流程物件上呼叫此函式，即會產生如下表的輸出。

![摘要統計資料輸出](./media/concept-data-preparation/output-example.png)

## <a name="multiple-environment-compatibilities"></a>多種環境相容性

SDK 也可讓資料流程物件序列化，並在*任何* Python 環境中皆能開啟。 物件的開啟環境可以不同於儲存環境。 透過這項功能，即可輕鬆地在多個 Python 環境之間進行傳輸，並快速與 Azure Machine Learning 模型整合。

使用下列程式碼可以儲存您的資料流程物件。

```python
package = dprep.Package([dataflow_1, dataflow_2])
package.save("<your-local-path>")
```

使用下列程式碼可在任何環境中重新開啟您的套件，並擷取資料流程物件清單。

```python
package = dprep.Package.open("<your-local-path>")
dataflow_list = package.dataflows
```

## <a name="data-preparation-pipeline"></a>資料準備管道

若要查看每個準備步驟的詳細範例和程式碼，請使用下列使用說明指南：

1. [載入資料](how-to-load-data.md)，它可以是各種格式
2. [轉換](how-to-transform-data.md)成更有用的結構
3. 將資料[寫入](how-to-write-data.md)到您模型可以存取的位置

![資料準備程序](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>後續步驟

檢閱使用「Azure Machine Learning 資料準備 SDK」進行之資料準備的[範例 Notebook](https://github.com/Microsoft/AMLDataPrepDocs/tree/master/tutorials/getting-started/getting-started.ipynb)。

Azure Machine Learning 資料準備 SDK [參考文件](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py) (英文)。
