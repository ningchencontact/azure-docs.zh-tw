---
title: 匯出資料：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務中的 「 匯出資料 」 模組，將結果、 中繼資料，以及工作資料從實驗儲存至 Azure Machine Learning 之外的雲端儲存體目的地。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c3744803f172edf9fbf2556a12677e8faef370c2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028316"
---
# <a name="export-data-module"></a>匯出資料 」 模組

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

使用此模組，將結果、 中繼資料，以及工作資料從實驗儲存至 Azure Machine Learning 之外的雲端儲存體目的地。

這個模組支援匯出，或將您的資料儲存至下列雲端資料服務：


- **匯出至 Azure Blob 儲存體**:將資料儲存至 Azure 中的 Blob 服務中。 Blob 服務中的資料可以公開共用或儲存在安全的應用程式資料存放區。

  
## <a name="how-to-configure-export-data"></a>如何設定匯出資料

1. 新增**匯出資料**模組至您在介面中的實驗。 您可以找到此模組中的**輸入和輸出**類別目錄。

2. 連接**匯出資料**想要匯出包含資料的模組。

3. 按兩下**匯出資料**來開啟**屬性**窗格。

4. 針對**資料目的地**，選取的雲端儲存體，您將在其中儲存您的資料類型。 如果您進行任何變更此選項時，會重設所有其他屬性。 因此請務必選擇這個選項，第一次 ！

5. 提供存取指定的儲存體帳戶所需的帳戶名稱和驗證方法。

    **匯出至 Azure Blob 儲存體**私人預覽中是唯一的選項。 以下示範如何設定模組。
    1. Azure blob 服務是用於儲存大量的資料，包括二進位資料。 有兩種類型的 blob 儲存體： 公用 blob 和 blob 需要登入認證。

    2. 針對**驗證類型**，選擇**公用 (SAS)** 如果您知道儲存體支援透過 SAS URL 的存取。

          SAS URL 是一種特殊的 URL，可以使用 Azure 儲存體公用程式所產生，並且可只有有限的時間。  它包含所需的驗證和下載的所有資訊。

        針對**URI**中，輸入或貼上定義的帳戶和公用 blob 的完整 URI。

        檔案格式，則支援 CSV 和 TSV。

    3. 對於私用的帳戶，請選擇**帳戶**，並提供帳戶名稱和帳戶金鑰，以便實驗可以寫入儲存體帳戶。

         - **帳戶名稱**：輸入或貼上帳戶的名稱要儲存的資料。 例如，如果儲存體帳戶的完整 URL 是`http://myshared.blob.core.windows.net`，您會輸入`myshared`。

        - **帳戶金鑰**：貼上與帳戶相關聯的儲存體存取金鑰。

        -  **容器、 目錄或 blob 路徑**:輸入儲存匯出的資料的 blob 名稱。 例如，若要將實驗的結果儲存到新的 blob 命名為**results01.csv** kontejneru**預測**帳戶中名為**mymldata**的完整 URLblob 會`http://mymldata.blob.core.windows.net/predictions/results01.csv`。

            因此，在欄位**到容器、 目錄或 blob 路徑**，您可以指定容器和 blob 名稱會遵循： `predictions/results01.csv`

        - 如果您指定名稱的 blob 已不存在，Azure 會為您建立 blob。

       -  寫入至現有的 blob 時，您可以指定 blob 的目前內容，藉由設定屬性，覆寫**Azure blob 儲存體寫入模式**。 根據預設，這個屬性設定為**錯誤**，這表示只要找到具有相同名稱的現有的 blob 檔案，就會引發錯誤。


    4. 針對**blob 檔案的檔案格式**，選取應該在儲存資料的格式。

        - **CSV**:以逗號分隔值 (CSV) 是預設儲存體格式。 若要匯出資料行標題，以及資料，請選取選項，**寫入 blob 標頭資料列**。  如需以逗號分隔格式在 Azure Machine Learning 中使用的詳細資訊，請參閱[轉換為 CSV](./convert-to-csv.md)。

        - **TSV**:定位字元分隔值 (TSV) 格式適用於許多機器學習工具。 若要匯出資料行標題，以及資料，請選取選項，**寫入 blob 標頭資料列**。  

 
    5. **使用快取的結果**:如果您想要避免重寫到 blob 檔案結果每次執行實驗，請選取此選項。 如果不有任何其他模組參數的變更，實驗將結果寫入只有第一次執行模組時，或當資料變更時。

    6. 執行實驗。

## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 