---
title: 使用 AzCopy v10, 將資料傳入或傳出 Azure 檔案儲存體Microsoft Docs
description: 使用 AzCopy 和檔案儲存體來傳輸資料。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 0c350776323c1b2949285a7ebe6a7c2778ae4dc4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648765"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>使用 AzCopy 和檔案儲存體傳輸資料 

AzCopy 是命令列公用程式, 可讓您在儲存體帳戶之間複製 blob 或檔案。 本文包含可搭配 Azure 檔案儲存體使用的範例命令。

開始之前, 請參閱開始[使用 AzCopy](storage-use-azcopy-v10.md)一文, 下載 AzCopy 並熟悉此工具。

## <a name="create-file-shares"></a>建立檔案共用

您可以使用 AzCopy `make`命令來建立檔案共用。 本節中的範例會建立名為`myfileshare`的檔案共用。

|    |     |
|--------|-----------|
| **語法** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **範例** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>上傳檔案

您可以使用 AzCopy `copy`命令, 從本機電腦上傳檔案和目錄。

本區段包含下列範例：

> [!div class="checklist"]
> * 上傳檔案
> * 上傳目錄
> * 使用萬用字元來上傳檔案

> [!NOTE]
> AzCopy 不會自動計算並儲存檔案的 md5 雜湊碼。 如果您想要讓 AzCopy 執行此動作, 請`--put-md5`將旗標附加至每個複製命令。 如此一來, 當下載檔案時, AzCopy 會計算已下載資料的 md5 雜湊, 並驗證儲存在檔案`Content-md5`屬性中的 md5 雜湊是否符合計算的雜湊。

### <a name="upload-a-file"></a>上傳檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **範例** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>上傳目錄

這個範例會將目錄 (以及該目錄中的所有檔案) 複製到檔案共用。 結果是檔案共用中具有相同名稱的目錄。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **範例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

若要複製到檔案共用中的目錄, 只要在命令字串中指定該目錄的名稱即可。

|    |     |
|--------|-----------|
| **範例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

如果您指定的目錄名稱不存在於檔案共用中, AzCopy 會依該名稱建立新的目錄。

### <a name="upload-the-contents-of-a-directory"></a>上傳目錄的內容

您可以上傳目錄的內容, 而不需使用萬用字元符號 (*) 來複製包含目錄本身。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **範例** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> `--recursive`附加旗標, 以上傳所有子目錄中的檔案。

## <a name="download-files"></a>下載檔案

您可以使用 AzCopy `copy`命令, 將檔案、目錄和檔案共用下載到本機電腦。

本區段包含下列範例：

> [!div class="checklist"]
> * 下載檔案
> * 下載目錄
> * 使用萬用字元來下載檔案

> [!NOTE]
> 如果檔案`Content-md5`的屬性值包含雜湊, 則 AzCopy 會計算已下載資料的 md5 雜湊, 並驗證儲存在檔案`Content-md5`屬性中的 md5 雜湊是否符合計算的雜湊。 如果這些值不相符, 除非您將或`--check-md5=NoCheck` `--check-md5=LogOnly`附加至 copy 命令來覆寫此行為, 否則下載將會失敗。

### <a name="download-a-file"></a>下載檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **範例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>下載目錄

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **範例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

這個範例會產生名為`C:\myDirectory\myFileShareDirectory`的目錄, 其中包含所有下載的檔案。

### <a name="download-the-contents-of-a-directory"></a>下載目錄的內容

您可以下載目錄的內容, 而不需使用萬用字元符號 (*) 複製包含的目錄本身。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **範例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> 附加旗`--recursive`標以下載所有子目錄中的檔案。

## <a name="next-steps"></a>後續步驟

在這些文章中尋找更多範例:

- [開始使用 AzCopy](storage-use-azcopy-v10.md)

- [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)