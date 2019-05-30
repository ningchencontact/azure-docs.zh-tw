---
title: 將資料從 Azure 檔案傳輸使用 AzCopy v10 |Microsoft Docs
description: 傳輸使用 AzCopy 和檔案儲存體的資料。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 5d11d5c0da350b9abf2e2b06a11a1690cf3f6922
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247105"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>使用 AzCopy 和檔案儲存體傳輸資料 

AzCopy 是命令列公用程式可供您儲存體帳戶來回複製 blob 或檔案。 這篇文章包含使用 Azure 檔案的範例命令。

在開始之前，請參閱[開始使用 AzCopy](storage-use-azcopy-v10.md)文章，以下載 AzCopy 和熟悉的工具。

## <a name="create-file-shares"></a>建立檔案共用

您可以使用 AzCopy`make`命令來建立檔案共用。 在本節中的範例會建立名為的檔案共用`myfileshare`。

|    |     |
|--------|-----------|
| **語法** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **範例** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>上傳檔案

您可以使用 AzCopy`copy`命令來上傳檔案和資料夾從本機電腦。

本區段包含下列範例：

> [!div class="checklist"]
> * 上傳檔案
> * 上傳資料夾
> * 將檔案上傳使用萬用字元

> [!NOTE]
> AzCopy 不會自動計算，並儲存檔案的 md5 雜湊程式碼。 如果您想要這樣做，AzCopy，然後附加`--put-md5`旗標，以每個複製命令。 這樣一來，當下載檔案時，AzCopy 會計算 MD5 雜湊的下載資料，並驗證 MD5 雜湊儲存在檔案的`Content-md5`屬性會比對導出的雜湊。

### <a name="upload-a-file"></a>上傳檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **範例** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-folder"></a>上傳資料夾

此範例會將資料夾 （及其所有該資料夾中的檔案） 複製到檔案共用。 結果是相同名稱的檔案共用的資料夾中。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **範例** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

若要複製至檔案共用中的資料夾，只要命令字串中指定該資料夾的名稱。

|    |     |
|--------|-----------|
| **範例** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

如果您指定不存在的資料夾名稱中的檔案共用時，AzCopy 會建立新的資料夾，該名稱。

### <a name="upload-the-contents-of-a-folder"></a>上傳資料夾的內容

您可以上傳資料夾的內容，而不使用萬用字元符號 （*） 來複製包含資料夾本身。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "<local-folder-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>` |
| **範例** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> 附加`--recursive`旗標，以將所有的子資料夾中的檔案上傳。

## <a name="download-files"></a>下載檔案

您可以使用 AzCopy`copy`到本機電腦的命令，以下載檔案、 資料夾和檔案共用。

本區段包含下列範例：

> [!div class="checklist"]
> * 下載檔案
> * 將資料夾下載
> * 使用萬用字元來下載檔案

> [!NOTE]
> 如果`Content-md5`檔案的屬性值包含雜湊，AzCopy 會計算下載的資料 MD5 雜湊值，並驗證 MD5 雜湊儲存在檔案的`Content-md5`屬性會比對導出的雜湊。 如果這些值不相符，下載會失敗，除非您覆寫這個行為附加`--check-md5=NoCheck`或`--check-md5=LogOnly`複製命令。

### <a name="download-a-file"></a>下載檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **範例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>將資料夾下載

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>" "<local-folder-path>" --recursive` |
| **範例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"  --recursive` |

此範例會產生名為的資料夾`C:\myFolder\myFileShareFolder`，其中包含所有下載的檔案。

### <a name="download-the-contents-of-a-folder"></a>下載資料夾的內容

您可以下載資料夾的內容，而不使用萬用字元符號 （*） 來複製包含資料夾本身。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-folder-path>/"` |
| **範例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"` |

> [!NOTE]
> 附加`--recursive`旗標，以下載所有的子資料夾中的檔案。

## <a name="next-steps"></a>後續步驟

在這些文件中找到更多範例：

- [開始使用 AzCopy](storage-use-azcopy-v10.md)

- [使用 AzCopy 和 blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和 Amazon S3 貯體傳輸資料](storage-use-azcopy-s3.md)

- [設定、 最佳化和疑難排解 AzCopy](storage-use-azcopy-configure.md)