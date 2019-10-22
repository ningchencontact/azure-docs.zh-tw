---
title: 匯入資料：模組參考
titleSuffix: Azure Machine Learning service
description: 瞭解如何使用 Azure Machine Learning 服務中的匯入資料模組，將資料從現有的雲端資料服務載入機器學習管線。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fef7d686479b24b0402ab6f1e6990df74231b8d6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693143"
---
# <a name="import-data-module"></a>匯入資料模組

本文描述適用于 Azure Machine Learning 服務的視覺化介面（預覽）模組。

使用此模組，將資料從現有的雲端資料服務載入機器學習管線。  

一開始，請選擇您要讀取的雲端式儲存體類型，然後完成其他設定。 定義您想要的資料並連接到來源之後，匯[入資料](./import-data.md)會根據其包含的值來推斷每個資料行的資料類型，並將資料載入您的 Azure Machine Learning 工作區。 匯[入資料](./import-data.md)的輸出是可以搭配任何管線使用的資料集。

  
如果您的來源資料變更，您可以重新執行匯[入資料](./import-data.md)來重新整理資料集，並加入新的資料。 不過，如果您不想要在每次執行管線時從來源重新讀取，請選取 [使用快取的**結果**] 選項為 [TRUE]。 選取此選項時，模組會檢查管線先前是否已使用相同的來源和相同的輸入選項來執行。 如果找到先前的執行，則會使用快取中的資料，而不是從來源重載資料。
 

## <a name="data-sources"></a>資料來源

匯入資料模組支援下列資料來源。 如需使用每個資料來源的詳細指示和範例，請按一下連結。 
 
如果您不確定應該儲存資料的方式或位置，請參閱資料科學程式中常見資料案例的指南： [Azure Machine Learning 中的先進分析案例](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios)。 


|資料來源| 搭配使用|
|-----------|-----------|  
|[透過 HTTP 的 Web URL](./import-from-web-url-via-http.md)|取得在使用 HTTP 且已提供 CSV、TSV、ARFF 或 SvmLight 格式的 web URL 上主控的資料|  
|[從 Azure Blob 儲存體匯入](./import-from-azure-blob-storage.md) |取得儲存在 Azure blob 服務中的資料|  
|[從 Azure SQL Database 匯入](./import-from-azure-sql-database.md) |從 Azure SQL Database 取得資料|

## <a name="how-to-configure-import-data"></a>如何設定匯入資料
 
1. 將匯**入資料**模組新增至您的管線。 您可以在介面的 [**資料輸入和輸出**] 分類中找到此模組。

1. 按一下 [**資料來源**]，然後選擇您要讀取的雲端式儲存體類型。 

    其他設定取決於您選擇的儲存體類型，以及儲存體是否受到保護。 您可能需要提供帳戶名稱、檔案類型或認證。 有些來源不需要驗證;對於其他人，您可能需要知道帳戶名稱、金鑰或容器名稱。

1. 如果您想要快取資料集以在後續執行時重複使用，請選取 [使用快取的**結果**] 選項。

    假設模組參數沒有其他變更，則管線只會在第一次執行模組時載入資料，之後再使用資料集的快取版本。

    如果您需要在每次執行管線時重載資料，請取消選取此選項。

1. 執行管道。

    當匯入資料將資料載入介面時，它會根據其包含的值（數值或類別）來推斷每個資料行的資料類型。

    - 如果有標頭，則會使用標頭來命名輸出資料集的資料行。

    - 如果資料中沒有現有的資料行標頭，則會使用 col1 格式來產生新的資料行名稱，col2,。 , coln*.

## <a name="results"></a>結果

當匯入完成時，按一下輸出資料集，然後選取 [**視覺化**] 以查看資料是否已成功匯入。

如果您想要儲存資料以供重複使用，而不是在每次執行管線時匯入新的資料集，請以滑鼠右鍵按一下輸出，然後選取 [**另存為資料集**]。 選擇資料集的名稱。 儲存的資料集會在儲存時保留資料，而當重新執行管線時，即使管線中的資料集變更，資料也不會更新。 這可以方便製作資料的快照集。

匯入資料之後，可能需要一些額外的模型化和分析準備：


- 使用 [[編輯中繼資料](./edit-metadata.md)] 來變更資料行名稱、將資料行處理為不同的資料類型，或表示某些資料行是標籤或特徵。

- 使用 [[選取資料集中的資料行](./select-columns-in-dataset.md)]，即可選取要在模型化中轉換或使用的資料行子集。 您可以使用 [[加入資料行](./add-columns.md)] 模組，輕鬆地將已轉換或已移除的資料行重新加入至原始資料集。  

- 使用[Partition 和 Sample](./partition-and-sample.md)來分割資料集、執行取樣，或取得前 n 個數據列。

## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 服務的[模組集合](module-reference.md)。 