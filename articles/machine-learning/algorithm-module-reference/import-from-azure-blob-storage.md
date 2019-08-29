---
title: 從 Azure Blob 儲存體匯入:模組參考
titleSuffix: Azure Machine Learning service
description: 瞭解此主題描述如何使用 Azure Machine Learning 服務中的 [從 Azure Blob 儲存體匯入], 從 Azure Blob 儲存體讀取資料, 讓您可以在機器學習實驗中使用資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fea64070c496379351bb75f2a38aba9b4db70dcd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128710"
---
# <a name="import-from-azure-blob-storage-module"></a>從 Azure Blob 儲存體模組匯入

本文描述適用于 Azure Machine Learning 服務的視覺化介面 (預覽) 模組。

使用此模組從 Azure blob 儲存體讀取資料, 以便您可以在機器學習實驗中使用資料。  

Azure Blob 服務是用來儲存大量資料, 包括二進位資料。 您可以使用 HTTP 或 HTTPS 從任何位置存取 Azure blob。 視 blob 儲存體的類型而定, 可能需要進行驗證。 

- 公用 blob 可由任何人或擁有 SAS URL 的使用者存取。
- 私用 blob 需要登入和認證。

從 blob 儲存體匯入需要將資料儲存在使用**區塊 blob**格式的 blob 中。 儲存在 blob 中的檔案必須使用逗點分隔 (CSV) 或 tab 鍵分隔 (TSV) 格式。 當您讀取檔案時, 記錄和任何適用的屬性標題會以資料列形式載入到記憶體中做為資料集。


我們強烈建議您在匯入之前先分析資料, 以確保架構如預期般運作。 匯入程式會掃描一些前端資料列來判斷架構, 但較新的資料列可能會包含額外的資料行或造成錯誤的資料。



## <a name="manually-set-properties-in-the-import-data-module"></a>手動設定匯入資料模組中的屬性

下列步驟說明如何手動設定匯入來源。

1. 將匯**入資料**模組新增至您的實驗。 您可以在介面的**資料輸入和輸出**中找到此模組

2. 針對 [**資料來源**], 選取 [ **Azure Blob 儲存體**]。

3. 針對 [**驗證類型**], 如果您知道已將資訊提供為公用資料源, 請選擇 [**公用 (SAS URL)** ]。 SAS URL 是公用存取的時間界限 URL, 您可以使用 Azure 儲存體公用程式來產生。

    否則, 請選擇 [**帳戶**]。

4. 如果您的資料位於可以使用 SAS URL 存取的**公用**blob 中, 您就不需要額外的認證, 因為 URL 字串包含下載和驗證所需的所有資訊。

    在 [ **URI** ] 欄位中, 輸入或貼上定義帳戶和公用 blob 的完整 URI。



5. 如果您的資料位於**私人**帳戶, 您必須提供包含帳戶名稱和金鑰的認證。

    - 在 [**帳戶名稱**] 中, 輸入或貼上包含您要存取之 blob 的帳戶名稱。

        例如, 如果儲存體帳戶的完整 URL 是`http://myshared.blob.core.windows.net`, 您會輸入。 `myshared`

    - 針對 [**帳戶金鑰**], 貼上與帳戶相關聯的儲存體存取金鑰。

        如果您不知道的存取金鑰，請參閱 區段中，這篇文章中的 「 管理您的 Azure 儲存體帳戶 」:[關於 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/storage-create-storage-account)。

6. 針對 [**容器、目錄或 blob 的路徑**], 輸入您想要取得的特定 blob 名稱。

    例如, 如果您將名為**data01**的檔案上傳至名為**mymldata**的帳戶中的容器`http://mymldata.blob.core.windows.net/trainingdata/data01.txt` **trainingdata** , 檔案的完整 URL 會是:。

    因此, 在**容器、目錄或 blob**的欄位路徑中, 您會輸入:`trainingdata/data01.csv`

    若要匯入多個檔案, 您可以使用`*`萬用字元 (星號) `?`或 (問號)。

    例如, 假設容器`trainingdata`包含多個相容格式的檔案, 您可以使用下列規格來讀取開頭為`data`的所有檔案, 並將它們串連成單一資料集:

    `trainingdata/data*.csv`

    您不能在容器名稱中使用萬用字元。 如果您需要從多個容器匯入檔案, 請針對每個容器使用個別的匯**入資料**模組實例, 然後使用 [加入資料[列](./add-rows.md)] 模組合併資料集。

    > [!NOTE]
    > 如果您已選取 [使用快取的**結果**] 選項, 對容器中的檔案所做的任何變更都不會觸發實驗中的資料重新整理。

7. 針對 [ **blob 檔案格式**], 選取一個選項, 指出儲存在 blob 中的資料格式, 讓 Azure Machine Learning 可以適當地處理資料。 支援下列格式:

    - **CSV**:逗號分隔值 (CSV) 是在 Azure Machine Learning 中匯出和匯入檔案的預設儲存體格式。 如果資料已經包含標題列, 請務必選取 [檔案**具有標頭資料列**] 選項, 否則會將此標頭視為資料列。

       

    - **TSV**:Tab 鍵分隔值 (TSV) 是許多機器學習工具所使用的格式。 如果資料已經包含標題列, 請務必選取 [檔案**具有標頭資料列**] 選項, 否則會將此標頭視為資料列。

       

    - **ARFF**:此格式支援以 Weka 工具組所使用的格式匯入檔案。 

   

8. 執行實驗。


## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 服務的[模組集合](module-reference.md)。 