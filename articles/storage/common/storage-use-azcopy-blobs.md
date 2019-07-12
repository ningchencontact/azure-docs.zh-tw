---
title: 將資料從 Azure Blob 儲存體來回傳輸使用 AzCopy v10 |Microsoft Docs
description: 這篇文章包含的 AzCopy 集合範例指令，可協助您建立容器、 檔案、 複製和同步處理本機檔案系統和容器之間的目錄。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 83e32a1e8f77604330a9f3aba0e011a0a0851e2f
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625614"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>使用 AzCopy 和 Blob 儲存體傳輸資料

AzCopy 是命令列公用程式可供您至、 或儲存體帳戶之間複製資料。 這篇文章包含搭配 Blob 儲存體的範例命令。

## <a name="get-started"></a>開始使用

請參閱[開始使用 AzCopy](storage-use-azcopy-v10.md)文章，以下載 AzCopy，並了解您可以提供儲存體服務的授權認證的方式。

> [!NOTE]
> 這篇文章中的範例假設您已經使用驗證您的身分識別`AzCopy login`命令。 然後，AzCopy 會使用您的 Azure AD 帳戶來授權存取 Blob 儲存體中的資料。
>
> 如果您想使用 SAS 權杖來授權存取 blob 資料，您可以將該權杖附加至每個 AzCopy 命令中的資源 URL。
>
> 例如： `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"` 。

## <a name="create-a-container"></a>建立容器

您可以使用 AzCopy`make`命令來建立容器。 在本節中的範例會建立一個名為容器`mycontainer`。

|    |     |
|--------|-----------|
| **語法** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **範例** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **範例**（階層式命名空間） | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>上傳檔案

您可以使用 AzCopy`copy`命令來上傳檔案和目錄從本機電腦。

本區段包含下列範例：

> [!div class="checklist"]
> * 上傳檔案
> * 上傳目錄
> * 將檔案上傳使用萬用字元

> [!NOTE]
> AzCopy 不會自動計算，並儲存檔案的 md5 雜湊程式碼。 如果您想要這樣做，AzCopy，然後附加`--put-md5`旗標，以每個複製命令。 如此一來，下載 blob 之後，AzCopy 會計算 MD5 雜湊的下載資料，並驗證 MD5 雜湊儲存在 blob 的`Content-md5`屬性會比對導出的雜湊。

### <a name="upload-a-file"></a>上傳檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **範例** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **範例**（階層式命名空間） | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> AzCopy 依預設會將資料上傳至區塊 blob。 若要上傳檔案，因為附加 Blob 或分頁 Blob 使用旗標`--blob-type=[BlockBlob|PageBlob|AppendBlob]`。

### <a name="upload-a-directory"></a>上傳目錄

此範例會將目錄 （以及所有在該目錄中的檔案） 複製到 blob 容器。 結果是相同名稱的容器中的目錄。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **範例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **範例**（階層式命名空間） | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

若要複製到容器內的目錄，只要命令字串中指定該目錄的名稱。

|    |     |
|--------|-----------|
| **範例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **範例**（階層式命名空間） | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

如果您指定的容器中不存在的目錄名稱時，AzCopy 會建立新目錄，該名稱。

### <a name="upload-the-contents-of-a-directory"></a>上傳目錄的內容

您可以上傳目錄的內容，而不使用萬用字元符號 （*） 複製本身的上層目錄。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **範例** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **範例**（階層式命名空間） | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> 附加`--recursive`旗標，以將所有的子目錄中的檔案上傳。

## <a name="download-files"></a>下載檔案

您可以使用 AzCopy`copy`命令會下載到本機電腦的 blob、 目錄和容器。

本區段包含下列範例：

> [!div class="checklist"]
> * 下載檔案
> * 下載目錄
> * 使用萬用字元來下載檔案

> [!NOTE]
> 如果`Content-md5`blob 的屬性值包含雜湊，AzCopy 會計算下載的資料 MD5 雜湊值，並驗證 MD5 雜湊儲存在 blob 的`Content-md5`屬性會比對導出的雜湊。 如果這些值不相符，下載會失敗，除非您覆寫這個行為附加`--check-md5=NoCheck`或`--check-md5=LogOnly`複製命令。

### <a name="download-a-file"></a>下載檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **範例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **範例**（階層式命名空間） | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>下載目錄

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **範例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **範例**（階層式命名空間） | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

此範例會產生名為`C:\myDirectory\myBlobDirectory`，其中包含所有下載的檔案。

### <a name="download-the-contents-of-a-directory"></a>下載目錄的內容

您可以下載目錄的內容，而不使用萬用字元符號 （*） 複製本身的上層目錄。

> [!NOTE]
> 目前，只會針對沒有階層式命名空間的帳戶支援此案例。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **範例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> 附加`--recursive`旗標，以下載所有子目錄中的檔案。

## <a name="copy-blobs-between-storage-accounts"></a>儲存體帳戶之間複製 blob

您可以使用 AzCopy 將 blob 複製到其他儲存體帳戶。 複製作業是同步的因此當此命令會傳回，表示已複製所有檔案。

> [!NOTE]
> 目前，只會針對沒有階層式命名空間的帳戶支援此案例。 

AzCopy 會使用[從 URL 將區塊](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url)API，以便直接在存放區伺服器之間複製資料。 這些複製作業不會使用您電腦的網路頻寬。

本區段包含下列範例：

> [!div class="checklist"]
> * 將 blob 複製到另一個儲存體帳戶
> * 將目錄複製到另一個儲存體帳戶
> * 將容器複製到另一個儲存體帳戶
> * 將所有容器、 目錄和檔案都複製到另一個儲存體帳戶

> [!NOTE]
> 在目前的版本中，您必須將 SAS 權杖附加至每個來源的 URL。 如果您可以使用 Azure Active Directory (AD)，以提供授權認證，您可以省略只能從目的地 URL 的 SAS 權杖。 

### <a name="copy-a-blob-to-another-storage-account"></a>將 blob 複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **範例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>將目錄複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" --recursive` |
| **範例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>將容器複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **範例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-files-to-another-storage-account"></a>將所有容器、 目錄和檔案都複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **範例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>同步處理檔案

您可以與 blob 容器，以同步處理本機檔案系統的內容。 同步處理是單向的。 換句話說，您會選擇這兩個端點的來源且其中一個是目的地。

> [!NOTE]
> 目前，只會針對沒有階層式命名空間的帳戶支援此案例。 目前版本的 AzCopy 不會同步處理之間的其他來源和目的地 (例如：檔案儲存體或 Amazon Web Services (AWS) S3 貯體）。

`sync`命令比較檔案名稱和上次修改時間戳記。 設定`--delete-destination`的值的選擇性旗標`true`或`prompt`刪除目的地目錄中的檔案，如果這些檔案不再存在於來源目錄。

如果您設定`--delete-destination`旗標設為`true`AzCopy 會刪除檔案，而不需提供提示。 如果您想要出現之前 AzCopy 刪除檔案的提示，請設定`--delete-destination`旗標設為`prompt`。

> [!NOTE]
> 若要防止意外刪除，請務必啟用[虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)功能才能使用`--delete-destination=prompt|true`旗標。

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>使用本機檔案系統的變更來更新容器

在此情況下，容器是目的地，而且本機檔案系統來源。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **範例** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>使用容器的變更來更新本機檔案系統

在此情況下，本機檔案系統是目的地，以及容器的來源。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **範例** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>後續步驟

在這些文件中找到更多範例：

- [開始使用 AzCopy](storage-use-azcopy-v10.md)

- [教學課程：移轉至雲端儲存體使用 AzCopy 的內部部署資料](storage-use-azcopy-migrate-on-premises-data.md)

- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)

- [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)
