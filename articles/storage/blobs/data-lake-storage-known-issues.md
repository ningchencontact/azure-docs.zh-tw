---
title: Azure Data Lake Storage Gen2 的已知問題 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 的限制和已知問題
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: normesta
ms.openlocfilehash: 61d168a5f501923812db5945fa6df439ae7e70f9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145097"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知問題

本文列出的功能和工具尚不支援或僅部分支援且具有階層式命名空間 (Azure Data Lake 儲存體 Gen2) 的儲存體帳戶。

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>Blob 儲存體 API

Blob 儲存體 Api 會停用，以避免不小心的資料存取問題，因為 Blob 儲存體 Api 尚無法使用 Azure Data Lake Gen2 Api 互通，就會發生。

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>該如何處理現有的工具、 應用程式和服務

如果任一這些使用 Blob 的 Api，以及您想要使用它們來處理所有的內容，您將上傳至您的帳戶，然後不在您的 Blob 儲存體帳戶上的階層式命名空間之前啟用 Blob Api 即可與 Azure Data Lake Gen2 Api 互通。

使用儲存體帳戶不具有階層式命名空間表示則不需要存取 Data Lake 儲存體 Gen2 特定功能，例如目錄和檔案系統存取控制清單。

### <a name="what-to-do-with-unmanaged-virtual-machine-vm-disks"></a>該如何處理虛擬機器 (VM) 的非受控磁碟

這些取決於已停用的 Blob 儲存體 Api，因此如果您想要啟用階層式的命名空間，儲存體帳戶，請考慮將它們放入儲存體帳戶不具有已啟用 「 階層式命名空間功能。

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>如果您使用 Blob Api 來載入資料，Blob Api 已停用之前，該怎麼辦

如果您在 API 停用之前已使用這些 API 載入資料，而且您有存取該資料的實際需求，請透過下列資訊連絡 Microsoft 支援服務：

> [!div class="checklist"]
> * 訂用帳戶識別碼 （GUID，而不是名稱）。
> * 儲存體帳戶名稱。
> * 是否生產環境中，會主動受到影響，以及如果是這樣，哪些儲存體帳戶嗎？
> * 即使您不會在生產環境中受到影響，也請告訴我們是否基於某些原因而需要這些資料複製到另一個儲存體帳戶，如果是，為什麼？

在這些情況下，我們可以在一段有限的時間內還原 Blob API 的存取，以便您可以將這些資料複製到未啟用階層命名空間功能的儲存體帳戶。

## <a name="all-other-features-and-tools"></a>所有其他功能和工具

下表列出所有其他功能與工具，尚不支援或僅部分支援且具有階層式命名空間 (Azure Data Lake 儲存體 Gen2) 的儲存體帳戶。

| 功能 / 工具    | 詳細資訊    |
|--------|-----------|
| **Data Lake 儲存體 Gen2 儲存體帳戶的 Api** | 部分支援 <br><br>您可以使用 Data Lake 儲存體 Gen2 **REST** Api，但 Api，例如.NET、 Java、 Python Sdk 的其他 Blob Sdk 中尚無法使用。|
| **AzCopy** | 特定版本的支援 <br><br>使用最新版的 AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json))。 不支援例如 AzCopy v8.1，舊版的 AzCopy。|
| **Azure Blob 儲存體生命週期管理原則** | 尚不支援 |
| **Azure 內容傳遞網路 (CDN)** | 尚不支援|
| **Azure 搜尋服務** |尚不支援|
| **Azure 儲存體總管** | 特定版本的支援 <br><br>使用唯一版本`1.6.0`或更高版本。 <br>版本`1.6.0`可從[免費下載](https://azure.microsoft.com/features/storage-explorer/)。|
| **Blob 容器的 Acl** |尚不支援|
| **Blobfuse** |尚不支援|
| **自訂網域** |尚不支援|
| **診斷記錄** |尚不支援|
| **檔案系統總管** | 有限的支援 |
| **不可變的儲存體** |尚不支援 <br><br>不可變的儲存體可讓資料儲存在[蠕蟲 （寫入一次，多次讀取）](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage)狀態。|
| **物件層級層** |尚不支援 <br><br>例如︰Premium、 經常性存取、 冷的、 和封存層。|
| **Powershell 和 CLI 的支援** | 有限的功能 <br><br>您可以使用 Powershell 或 CLI 來建立帳戶。 您無法執行作業，或在檔案系統、 目錄和檔案上設定存取控制清單。|
| **靜態網站** |尚不支援 <br><br>具體來說，能夠提供檔案[靜態網站](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)。|
| **協力廠商應用程式** | 有限的支援 <br><br>使用 REST Api 來運作的協力廠商應用程式會繼續運作，如果您使用 Data Lake 儲存體 Gen2。 <br>如果您有使用 Blob Api 的應用程式時，該應用程式很可能會有問題如果您使用該應用程式與 Data Lake 儲存體 Gen2。 若要進一步了解，請參閱[Blob 儲存體的 Data Lake 儲存體 Gen2 儲存體帳戶已停用 Api](#blob-apis-disabled)一節。|
| **版本控制功能** |尚不支援 <br><br>這包括[快照集](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)並[虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)。|
|

