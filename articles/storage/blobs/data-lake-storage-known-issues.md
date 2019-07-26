---
title: Azure Data Lake Storage Gen2 的已知問題 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 的限制和已知問題
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: normesta
ms.openlocfilehash: fc2d7e4f611e1eee9c369ef26aa7bf66feb7c888
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385691"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知問題

本文列出尚未支援的功能和工具, 或是只有具有階層命名空間 (Azure Data Lake Storage Gen2) 之儲存體帳戶的部分支援。

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>Blob 儲存體 API

Blob 儲存體 Api 已停用, 以防止可能發生的功能操作問題, 因為 Blob 儲存體 Api 尚未與 Azure Data Lake Gen2 Api 互通。

> [!NOTE]
> 如果您在 Data Lake Storage 上註冊多重通訊協定存取的公開預覽, 那麼 blob Api 和 Data Lake Storage Gen2 Api 就可以在相同的資料上運作。 若要深入瞭解, 請參閱[Data Lake Storage 上的多重通訊協定存取](data-lake-storage-multi-protocol-access.md)。

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>如何使用現有的工具、應用程式和服務

如果其中有任何一個使用 Blob Api, 而您想要使用它們來處理您上傳至帳戶的所有內容, 則您有兩個選項。

* **選項 1**：請不要在 Blob 儲存體帳戶上啟用階層式命名空間, 直到 Blob Api 可與 Azure Data Lake Gen2 Api 互通為止。 使用沒有階層式命名空間的儲存體帳戶, 表示您無法存取 Data Lake Storage Gen2 特定功能, 例如目錄和檔系統存取控制清單。

* **選項 2**：在[Data Lake Storage 上註冊多重通訊協定存取](data-lake-storage-multi-protocol-access.md)的公開預覽。 呼叫 Blob Api 的工具和應用程式以及 Blob 儲存體功能, 例如診斷記錄, 都可以使用具有階層式命名空間的帳戶。

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>如果您在停用 Blob Api 之前, 使用 Blob Api 來載入資料, 該怎麼辦

如果您在 API 停用之前已使用這些 API 載入資料，而且您有存取該資料的實際需求，請透過下列資訊連絡 Microsoft 支援服務：

> [!div class="checklist"]
> * 訂用帳戶識別碼 (GUID, 而不是名稱)。
> * 儲存體帳戶名稱。
> * 您是否會在生產環境中主動受到影響, 如果是, 則會對哪些儲存體帳戶？。
> * 即使您不會在生產環境中受到影響，也請告訴我們是否基於某些原因而需要這些資料複製到另一個儲存體帳戶，如果是，為什麼？

在這些情況下，我們可以在一段有限的時間內還原 Blob API 的存取，以便您可以將這些資料複製到未啟用階層命名空間功能的儲存體帳戶。

### <a name="issues-and-limitations-with-using-blob-apis-on-accounts-that-have-a-hierarchical-namespace"></a>在具有階層式命名空間的帳戶上使用 Blob Api 的問題和限制

如果您在 Data Lake Storage 上註冊多重通訊協定存取的公開預覽, 那麼 blob Api 和 Data Lake Storage Gen2 Api 就可以在相同的資料上運作。

本節說明使用 blob Api 和 Data Lake Storage Gen2 Api 來操作相同資料的問題和限制。

* 您無法同時使用 Blob Api 和 Data Lake Storage Api 來寫入相同的檔案實例。

* 如果您使用 Data Lake Storage Gen2 Api 來寫入檔案, 則對[Get 區塊清單](https://docs.microsoft.com/rest/api/storageservices/get-block-list)blob API 的呼叫將不會顯示該檔案的區塊。

* 您可以使用 Data Lake Storage Gen2 Api 或 Blob Api 來覆寫檔案。 這不會影響檔案屬性。

* 當您使用[列出 blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs)作業但未指定分隔符號時, 結果會包含目錄和 blob。

  如果您選擇使用分隔符號, 請只使用正斜線 (`/`)。 這是唯一支援的分隔符號。

* 如果您使用「[刪除 Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API」來刪除目錄, 則只有當該目錄是空的時, 才會將它刪除。

  這表示您無法以遞迴方式使用 Blob API 刪除目錄。

不支援這些 Blob REST Api:

* [Put Blob (頁面)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [放置頁面](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [取得頁面範圍](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [增量複製 Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [從 URL 放置頁面](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Put Blob (Append)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [附加區塊](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [從 URL 附加區塊](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

## <a name="issues-with-unmanaged-virtual-machine-vm-disks"></a>非受控虛擬機器 (VM) 磁片的問題

具有階層式命名空間的帳戶不支援非受控 VM 磁片。 如果您想要在儲存體帳戶上啟用階層式命名空間, 請將非受控 VM 磁片放入未啟用階層命名空間功能的儲存體帳戶。


## <a name="support-for-other-blob-storage-features"></a>其他 Blob 儲存體功能的支援

下表列出所有其他尚未支援的功能和工具, 或是只有具有階層命名空間 (Azure Data Lake Storage Gen2) 之儲存體帳戶的部分支援。

| 功能/工具    | 詳細資訊    |
|--------|-----------|
| **Data Lake Storage Gen2 儲存體帳戶的 Api** | 部分支援 <br><br>Data Lake Storage 上的多重通訊協定存取目前為公開預覽狀態。 此預覽可讓您使用 .NET、JAVA、Python Sdk 中的 Blob Api, 搭配具有階層式命名空間的帳戶。  Sdk 尚未包含 Api, 可讓您與目錄互動或設定存取控制清單 (Acl)。 若要執行這些功能, 您可以使用 Data Lake Storage Gen2 **REST** api。 |
| **AzCopy** | 版本特定支援 <br><br>只使用最新版本的 AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json))。 不支援舊版的 AzCopy (例如 AzCopy 8.1)。|
| **Azure Blob 儲存體生命週期管理原則** | 只有當您在[Data Lake Storage preview 上註冊多重通訊協定存取權](data-lake-storage-multi-protocol-access.md)時才支援。 只有預覽支援非經常性存取和封存存取層。 尚不支援刪除 blob 快照集。 |
| **Azure 內容傳遞網路 (CDN)** | 尚不支援|
| **Azure 搜尋服務** |只有當您在[Data Lake Storage preview 上註冊多重通訊協定存取權](data-lake-storage-multi-protocol-access.md)時才支援。|
| **Azure 儲存體總管** | 版本特定支援 <br><br>僅使用版本`1.6.0`或更高版本。 <br>版本`1.6.0`可[免費下載](https://azure.microsoft.com/features/storage-explorer/)。|
| **Blob 容器 Acl** |尚不支援|
| **Blobfuse** |尚不支援|
| **自訂網域** |尚不支援|
| **檔案系統 Explorer** | 有限支援 |
| **診斷記錄** |只有當您在[Data Lake Storage preview 上註冊多重通訊協定存取權](data-lake-storage-multi-protocol-access.md)時才支援。|
| **不可變的儲存體** |尚不支援 <br><br>不可變的儲存體可讓您將資料儲存在[WORM (一次寫入, 多次讀取)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage)狀態。|
| **物件層級層** |只有當您在[Data Lake Storage preview 上註冊多重通訊協定存取權](data-lake-storage-multi-protocol-access.md)時, 才支援非經常性和封存層。 <br><br> 尚不支援其他所有存取層。|
| **Powershell 和 CLI 支援** | 有限的功能 <br><br>支援建立帳戶之類的管理作業。 資料平面作業 (例如上傳和下載檔案) 在[Data Lake Storage 的多重通訊協定存取](data-lake-storage-multi-protocol-access.md)過程中是公開預覽。 尚不支援使用目錄及設定存取控制清單 (Acl)。 |
| **靜態網站** |尚不支援 <br><br>具體來說, 就是將檔案提供給[靜態網站](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)的能力。|
| **協力廠商應用程式** | 有限支援 <br><br>使用 REST Api 來執行的協力廠商應用程式, 如果您搭配 Data Lake Storage Gen2 使用, 將會繼續工作。 <br>如果您在[Data Lake Storage 上註冊多重通訊協定存取](data-lake-storage-multi-protocol-access.md)的公開預覽, 則呼叫 Blob api 的應用程式可能會有作用。 
| **版本設定功能** |尚不支援 <br><br>這包括[快照](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)集和虛[刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)。|


