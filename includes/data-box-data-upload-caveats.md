---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: b159ec8620fa8c93e4917f73be9b9898e1b4fbcc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244645"
---
- 不會將檔案複製到任何預先建立的共用直接。 您需要建立在共用資料夾，然後再將檔案複製到該資料夾。
- StorageAccount_BlockBlob  和 StorageAccount_PageBlob  底下的資料夾是容器。 例如，容器會建立為 StorageAccount_BlockBlob/container  和 StorageAccount_PageBlob/container  。
- 直接在 StorageAccount_AzureFiles  底下建立的每個資料夾會轉譯成 Azure 檔案共用。
- 如果雲端中現有的 Azure 物件 (例如 Blob 或檔案) 與要複製的物件同名，資料箱將會覆寫雲端中的檔案。
- 寫入 StorageAccount_BlockBlob  和 StorageAccount_PageBlob  共用中的每個檔案，分別會以區塊 Blob 和分頁 Blob 的形式上傳。
- Azure blob 儲存體不支援目錄。 如果您在 StorageAccount_BlockBlob  資料夾底下建立資料夾，則會以 Blob 名稱建立虛擬資料夾。 對於 Azure 檔案服務，則會維持實際的目錄結構。
- 任何空白下所建立的目錄階層 （不含任何檔案） *StorageAccount_BlockBlob*並*StorageAccount_PageBlob*不會上傳資料夾。
- 如果將資料上傳至 Azure 時發生任何錯誤，則會在目標儲存體帳戶中建立錯誤記錄。 在上傳完成後，系統會提供此錯誤記錄的路徑，而您可以檢閱記錄以執行更正動作。 請勿刪除資料來源的資料不確認上傳的資料。
