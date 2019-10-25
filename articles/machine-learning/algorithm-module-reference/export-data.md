---
title: 匯出資料：模組參考
titleSuffix: Azure Machine Learning service
description: 瞭解如何使用 Azure Machine Learning 服務中的「匯出資料」模組，將您管線中的結果、中繼資料和工作資料儲存到 Azure Machine Learning 以外的雲端儲存體目的地。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b7b4b9de1e91279243e35f1b71f1ef6d2244e9e0
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693724"
---
# <a name="export-data-module"></a>匯出資料模組

本文描述適用于 Azure Machine Learning 服務的視覺化介面（預覽）模組。

使用此模組可將您管線中的結果、中繼資料和工作資料儲存到 Azure Machine Learning 外部的雲端儲存體目的地。

此模組支援將您的資料匯出或儲存至下列雲端資料服務：


- **匯出至 Azure Blob 儲存體**：將資料儲存至 Azure 中的 Blob 服務。 Blob 服務中的資料可以公開共用，或儲存在安全的應用程式資料存放區中。

  
## <a name="how-to-configure-export-data"></a>如何設定匯出資料

1. 將**匯出資料**模組新增至您在介面中的管線。 您可以在 [**輸入和輸出**] 分類中找到此模組。

2. 將**匯出資料**連接到包含您要匯出之資料的模組。

3. 按兩下 [**匯出資料**] 以開啟 [**屬性**] 窗格。

4. 針對 [**資料目的地**]，選取您要儲存資料的雲端儲存體類型。 如果您對此選項進行任何變更，則會重設所有其他屬性。 因此，請務必先選擇此選項！

5. 提供存取指定儲存體帳戶所需的帳戶名稱和驗證方法。

    [**匯出至 Azure Blob 儲存體**] 是私人預覽中的唯一選項。 以下顯示如何設定模組。
    1. Azure blob 服務是用來儲存大量資料，包括二進位資料。 Blob 儲存體有兩種類型：公用 blob 和需要登入認證的 blob。

    2. 如果您知道儲存體支援透過 SAS URL 存取，請在 [**驗證類型**] 中選擇 [**公用（SAS）** ]。

          SAS URL 是一種特殊類型的 URL，可以使用 Azure 儲存體公用程式來產生，而且僅限時間內提供。  其中包含驗證和下載所需的所有資訊。

        針對 [ **URI**]，輸入或貼上定義帳戶和公用 blob 的完整 URI。

        針對檔案格式，支援 CSV 和 TSV。

    3. 針對 [私人帳戶]，選擇 [**帳戶**]，然後提供帳戶名稱和帳戶金鑰，讓管線可以寫入儲存體帳戶。

         - **帳戶名稱**：輸入或貼上您要儲存資料的帳戶名稱。 例如，如果儲存體帳戶的完整 URL 是 `http://myshared.blob.core.windows.net`，您會輸入 `myshared`。

        - **帳戶金鑰**：貼上與帳戶相關聯的儲存體存取金鑰。

        -  **容器、目錄或 blob 的路徑**：輸入將儲存匯出資料的 blob 名稱。 例如，若要將管線的結果儲存到名為**mymldata**之帳戶的容器**預測**中名為**results01**的新 blob，則會 `http://mymldata.blob.core.windows.net/predictions/results01.csv`blob 的完整 URL。

            因此，在**容器、目錄或 blob**的欄位路徑中，您會指定容器和 blob 名稱，如下所示： `predictions/results01.csv`

        - 如果您指定不存在的 blob 名稱，Azure 就會為您建立 blob。

       -  寫入現有的 blob 時，您可以藉由設定屬性（ **Azure blob 儲存體寫入模式**）來指定要覆寫 blob 的目前內容。 根據預設，此屬性設定為 [**錯誤**]，表示每當找到相同名稱的現有 blob 檔案時，就會引發錯誤。


    4. 針對 [blob 檔案的**檔案格式**]，選取應該儲存資料的格式。

        - **Csv**：逗號分隔值（csv）是預設的儲存體格式。 若要將資料行標題與資料一起匯出，請選取 [**寫入 blob 標頭資料**列] 選項。  如需 Azure Machine Learning 中使用之逗號分隔格式的詳細資訊，請參閱[轉換成 CSV](./convert-to-csv.md)。

        - **TSV**： Tab 鍵分隔值（TSV）格式與許多機器學習工具相容。 若要將資料行標題與資料一起匯出，請選取 [**寫入 blob 標頭資料**列] 選項。  

 
    5. **使用**快取的結果：如果您想要在每次執行管線時避免將結果重寫至 blob 檔案，請選取此選項。 如果模組參數沒有其他變更，管線就只會在第一次執行模組時或資料變更時，才寫入結果。

    6. 執行管道。

## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 服務的[模組集合](module-reference.md)。 