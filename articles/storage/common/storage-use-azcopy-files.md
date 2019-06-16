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
ms.openlocfilehash: 69d7136396c3d989e63b8956d3e703cc7f9666c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66687943"
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

您可以使用 AzCopy`copy`命令來上傳檔案和目錄從本機電腦。

本區段包含下列範例：

> [!div class="checklist"]
> * 上傳檔案
> * 上傳目錄
> * 將檔案上傳使用萬用字元

> [!NOTE]
> AzCopy 不會自動計算，並儲存檔案的 md5 雜湊程式碼。 如果您想要這樣做，AzCopy，然後附加`--put-md5`旗標，以每個複製命令。 這樣一來，當下載檔案時，AzCopy 會計算 MD5 雜湊的下載資料，並驗證 MD5 雜湊儲存在檔案的`Content-md5`屬性會比對導出的雜湊。

### <a name="upload-a-file"></a>上傳檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **範例** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>上傳目錄

此範例會將目錄 （以及所有在該目錄中的檔案） 複製到檔案共用。 結果是相同名稱的檔案共用中的目錄。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **範例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

若要複製到檔案共用內的目錄，只要命令字串中指定該目錄的名稱。

|    |     |
|--------|-----------|
| **範例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

如果您指定的目錄不存在名稱中的檔案共用時，AzCopy 會建立新目錄，該名稱。

### <a name="upload-the-contents-of-a-directory"></a>上傳目錄的內容

您可以上傳目錄的內容，而不使用萬用字元符號 （*） 複製本身的上層目錄。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **範例** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> 附加`--recursive`旗標，以將所有的子目錄中的檔案上傳。

## <a name="download-files"></a>下載檔案

您可以使用 AzCopy`copy`到本機電腦的命令，以下載檔案、 目錄和檔案共用。

本區段包含下列範例：

> [!div class="checklist"]
> * 下載檔案
> * 下載目錄
> * 使用萬用字元來下載檔案

> [!NOTE]
> 如果`Content-md5`檔案的屬性值包含雜湊，AzCopy 會計算下載的資料 MD5 雜湊值，並驗證 MD5 雜湊儲存在檔案的`Content-md5`屬性會比對導出的雜湊。 如果這些值不相符，下載會失敗，除非您覆寫這個行為附加`--check-md5=NoCheck`或`--check-md5=LogOnly`複製命令。

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

此範例會產生名為`C:\myDirectory\myFileShareDirectory`，其中包含所有下載的檔案。

### <a name="download-the-contents-of-a-directory"></a>下載目錄的內容

您可以下載目錄的內容，而不使用萬用字元符號 （*） 複製本身的上層目錄。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **範例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> 附加`--recursive`旗標，以下載所有子目錄中的檔案。

## <a name="next-steps"></a>後續步驟

在這些文件中找到更多範例：

- [開始使用 AzCopy](storage-use-azcopy-v10.md)

- [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)