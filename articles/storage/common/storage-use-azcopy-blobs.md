---
title: 將資料從 Azure Blob 儲存體來回傳輸使用 AzCopy v10 |Microsoft Docs
description: 這篇文章包含的 AzCopy 集合範例指令，可協助您建立容器、 檔案、 複製和同步處理本機檔案系統和容器之間的資料夾。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 98e33f838ee9b6f506bf1dc01e1dd61ad587aa05
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299399"
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

您可以使用 AzCopy`copy`命令來上傳檔案和資料夾從本機電腦。

本區段包含下列範例：

> [!div class="checklist"]
> * 上傳檔案
> * 上傳資料夾
> * 將檔案上傳使用萬用字元

> [!NOTE]
> AzCopy 不會自動計算，並儲存檔案的 md5 雜湊程式碼。 如果您想要這樣做，AzCopy，然後附加`--put-md5`旗標，以每個複製命令。 如此一來，下載 blob 之後，AzCopy 會計算 MD5 雜湊的下載資料，並驗證 MD5 雜湊儲存在 blob 的`Content-md5`屬性會比對導出的雜湊。

### <a name="upload-a-file"></a>上傳檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **範例** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **範例**（階層式命名空間） | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> AzCopy 依預設會將資料上傳至區塊 blob。 若要上傳檔案，因為附加 Blob 或分頁 Blob 使用旗標`--blob-type=[BlockBlob|PageBlob|AppendBlob]`。

### <a name="upload-a-folder"></a>上傳資料夾

此範例會將資料夾 （及其所有該資料夾中的檔案） 複製到 blob 容器。 結果是相同名稱的容器中的資料夾。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **範例** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **範例**（階層式命名空間） | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

若要複製到容器內的資料夾，只要命令字串中指定該資料夾的名稱。

|    |     |
|--------|-----------|
| **範例** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |
| **範例**（階層式命名空間） | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder" --recursive` |

如果您指定的容器中不存在的資料夾名稱時，AzCopy 會建立新的資料夾，該名稱。

### <a name="upload-the-contents-of-a-folder"></a>上傳資料夾的內容

您可以上傳資料夾的內容，而不使用萬用字元符號 （*） 來複製包含資料夾本身。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "<local-folder-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>` |
| **範例** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder"` |
| **範例**（階層式命名空間） | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder"` |

> [!NOTE]
> 附加`--recursive`旗標，以將所有的子資料夾中的檔案上傳。

## <a name="download-files"></a>下載檔案

您可以使用 AzCopy`copy`命令會下載到本機電腦的 blob、 資料夾和容器。

本區段包含下列範例：

> [!div class="checklist"]
> * 下載檔案
> * 將資料夾下載
> * 使用萬用字元來下載檔案

> [!NOTE]
> 如果`Content-md5`blob 的屬性值包含雜湊，AzCopy 會計算下載的資料 MD5 雜湊值，並驗證 MD5 雜湊儲存在 blob 的`Content-md5`屬性會比對導出的雜湊。 如果這些值不相符，下載會失敗，除非您覆寫這個行為附加`--check-md5=NoCheck`或`--check-md5=LogOnly`複製命令。

### <a name="download-a-file"></a>下載檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **範例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |
| **範例**（階層式命名空間） | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>將資料夾下載

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>" "<local-folder-path>" --recursive` |
| **範例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |
| **範例**（階層式命名空間） | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |

此範例會產生名為的資料夾`C:\myFolder\myBlobFolder`，其中包含所有下載的檔案。

### <a name="download-the-contents-of-a-folder"></a>下載資料夾的內容

您可以下載資料夾的內容，而不使用萬用字元符號 （*） 來複製包含資料夾本身。

> [!NOTE]
> 目前，只會針對沒有階層式命名空間的帳戶支援此案例。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-folder-path>/"` |
| **範例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder/*" "C:\myFolder"` |

> [!NOTE]
> 附加`--recursive`旗標，以下載所有的子資料夾中的檔案。

## <a name="copy-blobs-between-storage-accounts"></a>儲存體帳戶之間複製 blob

您可以使用 AzCopy 將 blob 複製到其他儲存體帳戶。 複製作業是同步的因此當此命令會傳回，表示已複製所有檔案。

> [!NOTE]
> 目前，只會針對沒有階層式命名空間的帳戶支援此案例。

AzCopy 會使用[從 URL 將區塊](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url)API，以便直接在存放區伺服器之間複製資料。 這些複製作業不會使用您電腦的網路頻寬。

本區段包含下列範例：

> [!div class="checklist"]
> * 將 blob 複製到另一個儲存體帳戶
> * 將資料夾複製到另一個儲存體帳戶
> * 將容器複製到另一個儲存體帳戶
> * 將所有容器、 資料夾和檔案都複製到另一個儲存體帳戶

### <a name="copy-a-blob-to-another-storage-account"></a>將 blob 複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **範例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-folder-to-another-storage-account"></a>將資料夾複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" --recursive` |
| **範例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobFolder" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>將容器複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **範例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-folders-and-files-to-another-storage-account"></a>將所有容器、 資料夾和檔案都複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **範例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>同步處理檔案

您可以同步處理本機檔案系統的 blob 容器的內容。 您也可以在您的電腦上同步到本機檔案系統的 blob 容器。 同步處理是單向的。 換句話說，您會選擇這兩個端點的來源且其中一個是目的地。

> [!NOTE]
> 目前版本的 AzCopy 不會同步處理之間的其他來源和目的地 (例如：檔案儲存體或 Amazon Web Services (AWS) S3 貯體）。

`sync`命令比較檔案名稱和上次修改時間戳記。 設定`--delete-destination`的值的選擇性旗標`true`或`prompt`刪除目的地資料夾中的檔案，如果這些檔案不再存在於來源資料夾。

如果您設定`--delete-destination`旗標設為`true`AzCopy 會刪除檔案，而不需提供提示。 如果您想要出現之前 AzCopy 刪除檔案的提示，請設定`--delete-destination`旗標設為`prompt`。

> [!NOTE]
> 若要防止意外刪除，請務必啟用[虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)功能才能使用`--delete-destination=prompt|true`旗標。

### <a name="synchronize-a-container-to-a-local-file-system"></a>同步處理到本機檔案系統的容器

在此情況下，本機檔案系統變成來源，而容器為目的地。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **範例** | `azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **範例**（階層式命名空間） | `azcopy sync "C:\myFolder" "https://<storage-account-name>.dfs.core.windows.net/mycontainer" --recursive` |


### <a name="synchronize-a-local-file-system-to-a-container"></a>同步處理至容器的本機檔案系統

在此情況下，容器會變成來源，並在本機檔案系統是目的地。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myFolder" --recursive` |
| **範例** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myFolder" --recursive` |
| **範例**（階層式命名空間） | `azcopy sync "https://mystorageaccount.dfs.core.windows.net/mycontainer" "C:\myFolder" --recursive` |

## <a name="next-steps"></a>後續步驟

在這些文件中找到更多範例：

- [開始使用 AzCopy](storage-use-azcopy-v10.md)

- [教學課程：移轉至雲端儲存體使用 AzCopy 的內部部署資料](storage-use-azcopy-migrate-on-premises-data.md)

- [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md)

- [使用 AzCopy 和 Amazon S3 貯體傳輸資料](storage-use-azcopy-s3.md)

- [設定、 最佳化和疑難排解 AzCopy](storage-use-azcopy-configure.md)