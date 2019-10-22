---
title: 從 Azure SQL Database 匯入
titleSuffix: Azure Machine Learning service
description: 瞭解如何使用 Azure Machine Learning 視覺化介面中的匯入資料模組，從 Azure SQL Database 取得資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 374776088d93813f39122f2018b00466c55d2b6e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694606"
---
# <a name="import-from-azure-sql-database"></a>從 Azure SQL Database 匯入

本文說明如何使用 Azure Machine Learning 視覺化介面中的「匯[入資料](import-data.md)」模組，從 Azure SQL Database 取得資料。  

若要從資料庫匯入資料，您必須同時指定伺服器名稱和資料庫名稱，以及定義資料表、view 或 query 的 SQL 語句。  

一般而言，將資料儲存在 Azure 資料庫中比使用 Azure 中的資料表或 blob 更耗費資源。 根據您的訂用帳戶類型而定，您可以儲存在資料庫中的資料量也可能有所限制。 不過，對於 SQL Azure 資料庫不會有任何交易費用，因此這個選項適合用來快速存取較少量的常用資訊，例如資料查閱資料表或資料字典。

如果您需要能夠在讀取資料前先進行篩選，或想要將預測或計量儲存回資料庫以進行報告，則最好也將資料儲存在 Azure 資料庫中。

## <a name="how-to-import-data-from-azure-sql-database"></a>如何從 Azure SQL Database 匯入資料

1. 將匯[入資料](import-data.md)模組新增至您的管線。 您可以在視覺化介面的 [資料輸入和輸出] 分類中找到此模組。

1. 針對 [**資料來源**]，選取 [ **Azure SQL Database**]。

1. 將下列選項設定為 [Azure SQL Database]。

    **資料庫伺服器名稱**：輸入 Azure 所產生的伺服器名稱。 其格式通常為 `<generated_identifier>.database.windows.net`。

    **資料庫名稱**：輸入您指定的伺服器上現有的資料庫名稱。

    **伺服器使用者帳戶名稱**：輸入具有資料庫存取權限之帳戶的使用者名稱。

    **伺服器使用者帳戶密碼**：提供指定之使用者帳戶的密碼。

    **資料庫查詢**：輸入或貼上描述您要讀取之資料的 SQL 語句。 請一律驗證 SQL 語句，並使用 Visual Studio 伺服器總管或 SQL Server Data Tools 之類的工具，預先確認查詢結果。

1. 如果您讀入 Azure Machine Learning 中的資料集不應在管線的執行之間變更，請選取 [使用快取的**結果**] 選項。

    選取此選項時，如果模組參數沒有其他變更，管線會在第一次執行模組時載入資料，之後再使用資料集的快取版本。

    如果您想要在管線的每個反復專案上重載資料集，請取消選取此選項。 每當匯[入資料](import-data.md)中有任何參數變更時，就會從來源重載資料集。

1. 執行管道。

    當 [匯[入資料](import-data.md)] 將資料載入視覺化介面時，可能也會執行某些隱含類型轉換，視源資料庫中使用的資料類型而定。

## <a name="results"></a>結果

匯入完成時，按一下輸出資料集，然後選取 [**視覺化**] 以查看資料是否已成功匯入。

（選擇性）您可以使用視覺化介面中的工具來變更資料集和其中繼資料：

- 使用 [[編輯中繼資料](edit-metadata.md)] 來變更資料行名稱、將資料行轉換成不同的資料類型，或指示哪些資料行是標籤或特徵。

- 使用 [[選取資料集中的資料行](select-columns-in-dataset.md)] 來選取資料行的子集。

- 使用 [分割區][和 [範例](partition-and-sample.md)]，依據準則來分隔資料集，或取得前 n 個數據列。

## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 服務的[模組集合](module-reference.md)。 
