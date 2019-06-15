---
title: 防止儲存體限制和實驗的輸入和輸出目錄的延遲
description: 在本文中，您了解以儲存您實驗的輸入的檔案，以及要寫入輸出檔，以防止儲存體限制錯誤，並實驗延遲位置。
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 05/28/2019
ms.openlocfilehash: 28d8c47db8ea9c8a51bc8e9deb0a689eb0b20177
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66392912"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Azure Machine Learning 實驗的位置來儲存和寫入檔案

在本文中，您了解如何儲存輸入的檔案，以及寫入輸出檔案，從您的實驗，以避免儲存體限制錯誤，以及實驗延遲的位置。

當啟動訓練上執行[計算目標](how-to-set-up-training-targets.md)，它們會與外部環境隔離。 此設計的目的是實驗的確保重現性和可攜性。 如果您執行兩次相同的指令碼，在相同或另一個計算目標，您會收到相同的結果。 與這個設計中，您可以將計算目標視為無狀態的計算資源，每個都完成之後執行的作業沒有任何同質性。

## <a name="where-to-save-input-files"></a>儲存輸入的檔的位置

您可以起始實驗的計算目標或本機電腦上之前，您必須確定必要的檔案可用於計算目標，例如相依性檔案和資料檔案需要執行您的程式碼。

Azure Machine Learning 將整個指令碼資料夾複製到目標計算內容執行訓練指令碼，然後會建立快照集。 實驗快照集儲存體限制為 300 MB 和/或 2000年的檔案。

基於這個理由，我們建議：

* **將您的檔案儲存在 Azure Machine Learning[資料存放區](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)。** 這可防止實驗延遲問題，並具有存取資料，從遠端計算目標，這表示驗證和掛接由 Azure Machine Learning 服務的優點。 深入了解為來源目錄中，指定資料存放區和檔案上傳至您的資料存放區中[存取您的資料存放區中的資料](how-to-access-data.md)文章。

* **如果您只需要幾個資料檔案和相依性指令碼，而且不能使用資料存放區，** 將檔案放在同一個資料夾目錄中為您的訓練指令碼。 指定此資料夾為您`source_directory`直接在您的訓練指令碼，或呼叫您的訓練指令碼的程式碼中。

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>儲存體限制的實驗快照集

用於實驗，Azure Machine Learning 會自動將您的程式碼取決於您建議當您設定執行目錄的實驗快照集。 這有總數上限為 300 MB 和/或 2000年的檔案。 如果您超過此限制，您會看到下列錯誤：

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

若要解決這個錯誤，儲存您的實驗檔案的資料存放區上。 如果您無法使用資料存放區中，下表提供可能的替代方案。

實驗&nbsp;描述|儲存體限制的解決方案
---|---
少於 2000年個檔案，與不能使用資料存放區| 覆寫快照集大小限制，而且具有 <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> 這可能需要幾分鐘的時間視檔案的大小與數量而定。
必須使用特定的指令碼目錄| 請`.amlignore`檔案，以排除您不屬於原始碼的實驗快照中的檔案。 新增至檔案名稱`.amlignore`檔案，並將它放在與您的訓練指令碼相同的目錄。 `.amlignore`檔案會使用相同[語法和模式](https://git-scm.com/docs/gitignore)做為`.gitignore`檔案。
管線|每個步驟中使用不同的子目錄
Jupyter Notebook| 建立`.amlignore`檔案或將您的 notebook 移到新的空白，子目錄，然後再次執行程式碼。

## <a name="where-to-write-files"></a>要寫入檔案

訓練實驗的隔離，因為在執行期間發生的檔案的變更不會一定保存您的環境之外。 如果您的指令碼會修改計算的本機檔案，變更不會保存下一個實驗執行，並將它們傳播回用戶端電腦自動。 因此，所做的第一個實驗期間執行的變更不，而且應該不會影響在第二個。

在撰寫時變更，我們建議 Azure Machine Learning 資料存放區中寫入檔案。 請參閱[存取您的資料存放區中的資料](how-to-access-data.md)。

如果您不需要資料存放區，將檔案寫入`./outputs`及/或`./logs`資料夾。

>[!Important]
> 兩個資料夾中，*輸出*並*記錄*，會受到 Azure Machine learning 的特殊處理。 在訓練期間，當您將檔案寫入`./outputs`和`./logs`資料夾，檔案會自動上傳至您的執行歷程記錄，以便在測試回合完成之後，才能存取它們。

* **狀態訊息或評分結果，例如輸出**將檔案寫入`./outputs`資料夾，以便將它們保存為執行歷程記錄中的成品。 請留意的數目和大小的檔案寫入此資料夾中，因為內容上傳至執行歷程記錄時，可能會發生延遲。 如果延遲是一項考量，建議您使用檔案寫入資料存放區。

* **若要將寫入的檔案儲存為執行的歷程記錄中的記錄檔**將檔案寫入`./logs`資料夾。 記錄檔上傳，所以這個方法適用於串流處理即時的更新，從遠端執行。

## <a name="next-steps"></a>後續步驟

* 深入了解[從您的資料存放區中存取資料](how-to-access-data.md)。

* 深入了解[如何將訓練目標](how-to-set-up-training-targets.md)。
