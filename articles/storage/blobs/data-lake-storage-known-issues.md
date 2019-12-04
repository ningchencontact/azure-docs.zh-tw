---
title: Azure Data Lake Storage Gen2 的已知問題 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 的限制和已知問題
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 7fac09ff236e4bb2c63691f9dc1ad41bb49edae4
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793356"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知問題

本文列出尚未支援的功能和工具，或是只有具有階層命名空間（Azure Data Lake Storage Gen2）之儲存體帳戶的部分支援。

<a id="blob-apis-disabled" />

## <a name="issues-and-limitations-with-using-blob-apis"></a>使用 Blob Api 的問題和限制

Blob Api 和 Data Lake Storage Gen2 Api 可以在相同的資料上運作。

本節說明使用 blob Api 和 Data Lake Storage Gen2 Api 來操作相同資料的問題和限制。

* 您無法同時使用 Blob Api 和 Data Lake Storage Api 來寫入相同的檔案實例。 如果您使用 Data Lake Storage Gen2 Api 來寫入檔案，則對[Get 區塊清單](https://docs.microsoft.com/rest/api/storageservices/get-block-list)blob API 的呼叫將不會顯示該檔案的區塊。 您可以使用 Data Lake Storage Gen2 Api 或 Blob Api 來覆寫檔案。 這不會影響檔案屬性。

* 當您使用[列出 blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs)作業但未指定分隔符號時，結果會包含目錄和 blob。 如果您選擇使用分隔符號，請只使用正斜線（`/`）。 這是唯一支援的分隔符號。

* 如果您使用「[刪除 Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API」來刪除目錄，則只有當該目錄是空的時，才會將它刪除。 這表示您無法以遞迴方式使用 Blob API 刪除目錄。

不支援這些 Blob REST Api：

* [Put Blob （頁面）](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [放置頁面](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [取得頁面範圍](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [增量複製 Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [從 URL 放置頁面](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Put Blob （Append）](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [附加區塊](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [從 URL 附加區塊](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

具有階層式命名空間的帳戶不支援非受控 VM 磁片。 如果您想要在儲存體帳戶上啟用階層式命名空間，請將非受控 VM 磁片放入未啟用階層命名空間功能的儲存體帳戶。

<a id="api-scope-data-lake-client-library" />

## <a name="filesystem-support-in-sdks"></a>Sdk 中的檔案系統支援

- .NET、JAVA 和 Python 處於公開預覽狀態。 目前不支援其他 Sdk。
- 取得和設定 Acl 作業目前不是遞迴的。

## <a name="filesystem-support-in-powershell-and-azure-cli"></a>PowerShell 和 Azure CLI 中的檔案系統支援

取得和設定 Acl 作業目前不是遞迴的。

## <a name="support-for-other-blob-storage-features"></a>其他 Blob 儲存體功能的支援

下表列出所有其他尚未支援的功能和工具，或是只有具有階層命名空間（Azure Data Lake Storage Gen2）之儲存體帳戶的部分支援。

| 功能/工具    | 詳細資訊    |
|--------|-----------|
| **AzCopy** | 版本特定支援 <br><br>只使用最新版本的 AzCopy （[AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)）。 不支援舊版的 AzCopy （例如 AzCopy 8.1）。|
| **Azure Blob 儲存體生命週期管理原則** | 支援生命週期管理原則（預覽）。  支援所有存取層。 封存存取層目前為預覽狀態。 尚不支援刪除 blob 快照集。 <br><br> 目前有一些 bug 會影響生命週期管理原則和封存存取層。  在[這裡](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u)註冊生命週期管理原則和封存存取層的預覽。   |
| **Azure 內容傳遞網路（CDN）** | 尚不支援|
| **Azure 搜尋服務** |支援（預覽）|
| **Azure 儲存體總管** | 版本特定的支援。 <br><br>僅使用 `1.6.0` 或更高版本。 <br> 目前有一個會影響版本 `1.11.0` 的儲存體 bug，在某些情況下可能會導致驗證錯誤。 即將推出儲存體錯誤的修正程式，但作為因應措施，建議您使用[免費下載](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes)的 `1.10.x` 版本。 `1.10.x` 不會受到儲存體錯誤的影響。|
| **Blob 容器 Acl** |尚不支援|
| **Blobfuse** |尚不支援|
| **自訂網域** |尚不支援|
| **Azure 入口網站中的儲存體總管** | 有限的支援。 尚不支援 Acl。 |
| **診斷記錄** |支援診斷記錄（預覽）。<br><br>目前不支援在 Azure 入口網站中啟用記錄。 以下範例說明如何使用 PowerShell 來啟用記錄。 <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`答案中所述步驟，工作帳戶即會啟用。 <br><br>請務必將 `Blob` 指定為 `-ServiceType` 參數的值，如下列範例所示。 <br><br>目前，Azure 儲存體總管無法用於查看診斷記錄。 若要查看記錄，請使用 AzCopy 或 Sdk。
| **不可變的儲存體** |尚不支援 <br><br>不可變的儲存體可讓您將資料儲存在[WORM （一次寫入，多次讀取）](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage)狀態。|
| **物件層級層** |支援非經常性存取和封存層。 封存層處於預覽狀態。 尚不支援其他所有存取層。 <br><br> 目前有一些 bug 會影響封存存取層。  在[這裡](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u)註冊封存存取層的預覽。|
| **靜態網站** |尚不支援 <br><br>具體來說，就是將檔案提供給[靜態網站](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)的能力。|
| **協力廠商應用程式** | 有限支援 <br><br>使用 REST Api 來執行的協力廠商應用程式，如果您搭配 Data Lake Storage Gen2 使用，將會繼續工作。 <br>呼叫 Blob Api 的應用程式可能會有作用。|
|**虛刪除** |尚不支援|
| **版本設定功能** |尚不支援 <br><br>這包括虛[刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)和其他版本控制功能，例如[快照](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)集。|


