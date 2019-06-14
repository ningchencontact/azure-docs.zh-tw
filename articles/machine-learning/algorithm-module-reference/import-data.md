---
title: 匯入資料：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure Machine Learning 服務中使用 「 匯入資料 」 模組，將資料載入到 machine learning 實驗中，從現有的雲端資料服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ed51c4e7b6c7d226c7827d1ba00bc96a7be1e6b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028301"
---
# <a name="import-data-module"></a>匯入資料模組

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

使用此模組將資料載入到 machine learning 實驗中，從現有的雲端資料服務。  
模組現在會提供精靈，可協助您選擇儲存體選項，並快速地設定所有的選項中選取 從現有的訂用帳戶和帳戶。 若要編輯現有的資料連接的需要嗎？ 沒問題，精靈會載入所有先前的組態詳細資料，讓您不必從頭重新啟動。 
  
定義您想要的資料連接到來源之後,[匯入資料](./import-data.md)推斷每個資料行的值，它包含，並將資料載入您的 Azure Machine Learning 工作區為基礎的資料類型。 輸出[匯入資料](./import-data.md)是可以搭配任何實驗的資料集。

  
如果您的來源資料變更時，您可以重新整理資料集，並加入新的資料重新執行[匯入資料](./import-data.md)。 不過，如果您不想重新讀取來源的每次執行實驗，選取**使用快取的結果**選項設為 TRUE。 選取此選項時，會檢查模組的實驗是否已執行先前使用相同的來源和相同的輸入的選項。 如果上一次執行找到，則使用快取中的資料，而不是重新載入從來源資料。
 

## <a name="data-sources"></a>資料來源

匯入資料模組支援下列資料來源。 按一下所需的詳細的指示和範例使用每個資料來源的連結。 
 
如果您不確定的方式或位置，您就應該儲存您的資料，請參閱本指南來 data science process 中的常見資料案例：[適用於 Azure Machine Learning 中進階分析案例](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios)。 


|資料來源| 搭配使用|
|-----------|-----------|  
|[透過 HTTP 的 web URL](./import-from-web-url-via-http.md)|取得裝載於 web URL，會使用 HTTP，並已提供，以 CSV、 TSV、 ARFF 或 SvmLight 格式的資料|  
|[從 Azure Blob 儲存體匯入](./import-from-azure-blob-storage.md) |取得儲存在 Azure blob 服務中的資料|  

## <a name="how-to-use-import-data"></a>如何使用匯入資料
 
1. 新增**匯入資料**模組至您的實驗。 您可以找到此模組中的**資料輸入和輸出**介面中的類別。

2. 按一下 **啟動資料匯入精靈**設定資料來源，使用精靈。

    精靈會取得帳戶名稱和認證，以及設定其他選項的說明。 如果您要編輯現有的組態，它會先載入目前的值。

3. 如果您不想要使用此精靈，按一下**資料來源**，然後選擇您從讀取的雲端儲存體類型。 

    額外的設定取決於您選擇，儲存體類型和與否，是否保護儲存體。 您可能需要提供帳戶名稱、 檔案類型或認證。 某些來源不需要驗證;對於其他人，您可能需要知道帳戶名稱、 金鑰或容器名稱。

4. 選取 **使用快取的結果**選項，如果您想要快取在後續的執行中的重複使用的資料集。

    假設已有模組參數沒有其他變更，則實驗會載入資料的第一個有更多的時間執行時，此模組，並之後使用 資料集的快取的版本。

    如果您要重新載入資料每次執行實驗，請取消選取此選項。

5. 執行實驗。

    當匯入資料會將資料載入的介面時，它會推斷值、 包含每個資料行的資料型別數值或類別。

    - 如果標頭存在，標頭用來命名輸出資料集的資料行。

    - 如果資料中不有任何現有的資料行標頭，使用格式 col1，col2，產生新的資料行名稱... coln *。

## <a name="results"></a>結果

匯入完成時，按一下 輸出資料集，然後選取**視覺化**以查看資料已成功匯入。

如果您想要儲存的資料重複使用，而不是匯入新的資料集每次執行實驗時，請以滑鼠右鍵按一下輸出並選取**做為資料集儲存**。 選擇資料集的名稱。 儲存的資料集時儲存，保留的資料和資料時未更新重新執行實驗時，即使在實驗中的資料集的變更。 這可以方便建立快照集的資料。

在匯入資料之後, 它可能需要一些額外的準備工作，進行模型化和分析：


- 使用[編輯中繼資料](./edit-metadata.md)變更資料行名稱，以處理資料行做為不同的資料類型，或是指出某些資料行是標籤或功能。

- 使用 [選取資料集中的資料行](./select-columns-in-dataset.md)選取來轉換，或在模型中使用的資料行的子集。 已轉換或移除資料行可以輕鬆地重新加入至原始資料集使用[加入資料行](./add-columns.md)模組。  

- 使用[資料分割和取樣](./partition-and-sample.md)來分割資料集、 執行取樣，或取得前 n 個資料列。

## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 