---
title: Blob 儲存體簡介 - Azure 中的物件儲存體
description: Azure Blob 儲存體可儲存大量的非結構化物件資料，例如文字或二進位資料。 Azure Blob 儲存體具有高度擴充性與可用性。 用戶端可從 PowerShell 或 Azure CLI、以程式設計方式透過 Azure 儲存體用戶端程式庫，或使用 REST 來存取 Blob 儲存體中的資料物件。
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 01/03/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 761d5fe52222331a26f85cdb2787a50a89504021
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240942"
---
# <a name="introduction-to-azure-blob-storage"></a>Azure Blob 儲存體簡介

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Blob 儲存體資源

Blob 儲存體提供三種類型資源：

- **儲存體帳戶**。 
- 儲存體帳戶中的**容器**
- 容器中的 **Blob** 

下圖顯示資源之間的關係。

![Blob 儲存體架構圖](./media/storage-blob-introduction/blob1.png)

### <a name="storage-accounts"></a>儲存體帳戶

儲存體帳戶會在 Azure 中為您的資料提供唯一命名空間。 每個儲存在 Azure 儲存體中的物件都有一個位址，其中包含您的唯一帳戶名稱。 帳戶名稱與 Azure 儲存體 Blob 端點的組合會形成儲存體帳戶中物件的基底位址。

例如，如果您的儲存體帳戶名為 *mystorageaccount*，則 Blob 儲存體的預設端點將是：

```
http://mystorageaccount.blob.core.windows.net 
```

若要建立儲存體帳戶，請參閱[儲存體帳戶](../common/storage-quickstart-create-account.md)。 若要深入了解儲存體帳戶，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

### <a name="containers"></a>容器

容器會組織一組 Blob，類似於檔案系統中的目錄。 儲存體帳戶可以包含無限數量的容器，而一個容器則可儲存無限數量的 Blob。 

  > [!NOTE]
  > 容器名稱必須是小寫。 如需為容器命名的詳細資訊，請參閱[命名和參考容器、Blob 及中繼資料](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)。

### <a name="blobs"></a>Blob
 
Azure 儲存體支援三種 blob 類型：

* **區塊 Blob** 可儲存文字和二進位資料 (最多約 4.7 TB)。 區塊 Blob 是由可個別管理的資料區塊所組成。
* **附加 Blob** 和區塊 Blob 相似，由區塊所組成，但已針對附加作業最佳化。 附加 Blob 很適合某些案例，例如記錄虛擬機器中的資料。
* **分頁 Blob** 可儲存隨機存取檔案 (大小上限為 8 TB)。 分頁 Blob 存放區可儲存虛擬硬碟 (VHD) 檔案，以做為 Azure 虛擬機器的磁碟。 如需有關分頁 Blob 的詳細資訊，請參閱 [Azure 分頁 Blob 的概觀](storage-blob-pageblob-overview.md)

如需不同 Blob 類型的相關詳細資訊，請參閱[了解區塊 Blob、附加 Blob 及分頁 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)。

## <a name="move-data-to-blob-storage"></a>移動資料至 Blob 儲存體

目前已有幾個解決方案，可將現有資料移轉至 Blob 儲存體：

- **AzCopy** 是一個簡單易用的命令列工具，可供 Windows 和 Linux 跨容器或跨儲存體帳戶，將資料複製到 Blob 儲存體或從該處複製資料。 如需 AzCopy 的相關詳細資訊，請參閱[使用 AzCopy v10 (預覽) 傳輸資料](../common/storage-use-azcopy-v10.md)。 
- **Azure 儲存體資料移動程式庫**是用於在 Azure 儲存體服務之間移動資料的 .NET 程式庫。 AzCopy 公用程式已內建資料移動程式庫。 如需詳細資訊，請參閱資料移動程式庫的[參考文件](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.datamovement)。 
- **Azure Data Factory**使用「帳戶金鑰」、「共用存取簽章」、「服務主體」或「Azure 資源的受控識別」驗證來複製資料至 Blob 儲存體，或從該處複製資料。 如需詳細資訊，請參閱[使用 Azure Data Factory 將資料複製到 Azure Blob 或從該處複製資料](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。 
- **Blobfuse** 是 Azure Blob 儲存體的虛擬檔案系統驅動程式。 您可以使用 Blobfuse 透過 Linux 檔案系統存取您儲存體帳戶中現有的區塊 Blob 資料。 如何詳細資訊，請參閱[如何使用 Blobfuse 將 Blob 儲存體掛接為檔案系統](storage-how-to-mount-container-linux.md)。
- **Azure 資料箱磁碟**是一項服務，可在因為要上傳大型資料集，或因網路限制而難以透過網路上傳資料時，將內部部署資料傳輸至 Blob 儲存體。 您可以使用 [Azure 資料箱磁碟](../../databox/data-box-disk-overview.md)要求 Microsoft 提供固態硬碟 (SSD)。 接著，您可以將資料複製到這些磁碟，並將其寄回給 Microsoft，以上傳至 Blob 儲存體。
- **Azure 匯入/匯出服務**提供一種方便的方式，可讓您從儲存體帳戶將大量資料匯出至您自行提供的硬碟，並由 Microsoft 在完成資料移轉作業後，將硬碟和資料寄回去給您。 如需詳細資訊，請參閱[使用 Microsoft Azure 匯入/匯出服務將資料移轉至 Blob 儲存體](../common/storage-import-export-service.md)。

## <a name="next-steps"></a>後續步驟

* [建立儲存體帳戶](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure 儲存體的延展性與效能目標](../common/storage-scalability-targets.md)
