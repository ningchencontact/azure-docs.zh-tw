---
title: 從 Azure Blob 儲存體匯入：模組參考
titleSuffix: Azure Machine Learning service
description: 了解本主題描述如何使用從 Azure Blob 儲存體模組匯入 Azure Machine Learning 服務中，可以從 Azure blob 儲存體，讀取資料，讓您可用於機器學習實驗中的資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4ac98516c1a326e1ede09bbb9660113ffd0642a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029681"
---
# <a name="import-from-azure-blob-storage-module"></a>從 Azure Blob 儲存體模組匯入

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

使用此模組從 Azure blob 儲存體，讀取資料，好讓您可以在機器學習服務實驗中使用的資料。  

Azure Blob 服務是用於儲存大量的資料，包括二進位資料。 可以使用 HTTP 或 HTTPS，從任何地方存取 azure blob。 驗證可能需要根據 blob 儲存體的類型。 

- 任何人，或擁有 SAS URL 的使用者，可以存取公用的 blob。
- 私用 blob 需要登入和認證。

從 blob 儲存體匯入需要您將資料儲存在使用的 blob**區塊 blob**格式。 以逗號分隔 (CSV) 或 tab 鍵分隔 (TSV) 格式，必須使用儲存在 blob 中的檔案。 當讀取檔案時，記錄和任何適用的屬性標題都會載入為資料列的記憶體做為資料集。


我們強烈建議匯入之前，分析您的資料，並確認結構描述如預期般運作。 匯入程序會掃描標頭的資料列，以判斷結構描述中，一些，但較新的資料列可能包含額外的資料行或造成錯誤的資料。



## <a name="manually-set-properties-in-the-import-data-module"></a>手動匯入資料模組中設定屬性

下列步驟說明如何手動設定，匯入來源。

1. 新增**匯入資料**模組至您的實驗。 您可以尋找在介面中，此模組在**資料輸入和輸出**

2. 針對**資料來源**，選取**Azure Blob 儲存體**。

3. 針對**驗證類型**，選擇**Public (SAS URL)** 如果您知道已做為公用的資料來源提供的資訊。 SAS URL 是您可以使用 Azure 儲存體公用程式產生的公用存取的時間繫結 URL。

    否則，選擇**帳戶**。

4. 如果您的資料處於**公開**使用 SAS URL，可以存取的 blob URL 字串包含下載和驗證所需的所有資訊，因為不需要額外的認證。

    在  **URI**欄位中，輸入或貼上定義的帳戶和公用 blob 的完整 URI。



5. 如果您的資料處於**私人**帳戶，您必須提供認證，包括帳戶名稱和金鑰。

    - 針對**帳戶名稱**中，輸入或貼上帳戶包含您想要存取之 blob 的名稱。

        例如，如果儲存體帳戶的完整 URL 是`http://myshared.blob.core.windows.net`，您會輸入`myshared`。

    - 針對**帳戶金鑰**，貼上與帳戶相關聯的儲存體存取金鑰。

        如果您不知道的存取金鑰，請參閱 區段中，這篇文章中的 「 管理您的 Azure 儲存體帳戶 」:[關於 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/storage-create-storage-account)。

6. 針對**到容器、 目錄或 blob 路徑**，輸入您想要擷取的特定 blob 的名稱。

    例如，如果您上傳檔案，名為**data01.csv**容器**trainingdata**帳戶中名為**mymldata**，檔案的完整 URL 會是： `http://mymldata.blob.core.windows.net/trainingdata/data01.txt`.

    因此，在欄位**到容器、 目錄或 blob 路徑**，您會輸入： `trainingdata/data01.csv`

    若要匯入多個檔案，您可以使用萬用字元`*`（星號） 或`?`（問號）。

    例如，假設容器`trainingdata`包含多個檔案是相容的格式，您可以使用下列規格，來讀取開頭的所有檔案`data`，並將它們串連成單一資料集：

    `trainingdata/data*.csv`

    容器名稱中，您不能使用萬用字元。 如果您需要從多個容器匯入檔案，使用個別的執行個體**匯入資料**適用於每個容器和資料集使用然後合併模組[加入資料列](./add-rows.md)模組。

    > [!NOTE]
    > 如果您已選取選項**使用快取的結果**，您對容器中的檔案進行任何變更不會觸發在實驗中的資料重新整理。

7. 針對**Blob 檔案格式**，選取選項，表示儲存在 blob 中的資料格式，以便 Azure Machine Learning 可以適當地處理資料。 支援下列格式：

    - **CSV**:以逗號分隔值 (CSV) 是匯出和匯入 Azure Machine Learning 中的檔案的預設儲存體格式。 如果資料已經包含標頭資料列，請務必選取選項，**檔案具有標頭資料列**，或標頭將會被視為資料列。

       

    - **TSV**:定位字元分隔值 (TSV) 是許多機器學習工具所使用的格式。 如果資料已經包含標頭資料列，請務必選取選項，**檔案具有標頭資料列**，或標頭將會被視為資料列。

       

    - **ARFF**:此格式支援匯入 Weka 工具組所使用的格式中的檔案。 

   

8. 執行實驗。


## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 