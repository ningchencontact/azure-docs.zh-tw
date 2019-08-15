---
title: 對 Azure Blob 儲存體中的容器與 Blob 啟用公用讀取權限 | Microsoft Docs
description: 了解如何讓容器與 Blob 可供匿名存取，以及如何以程式設計方式存取。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 6293fc84969c4e246c05da4482f76142263db230
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985552"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>管理對容器與 Blob 的匿名讀取權限。

您可以對 Azure Blob 儲存體中的容器及其 Blob 啟用匿名與公用讀取權限。 如此您就可以將這些資源的唯讀存取權限授與他人，而無須共用您的帳戶金鑰，也無須要求共用存取簽章 (SAS)。

公用讀取權限適用於您想要某些 Blob 永遠可供匿名讀取存取的狀況。 對於更深入的控管需求，您可以建立共用存取簽章。 共用存取簽章可讓您針對一段特定時間提供不同權限的限制存取。 如需建立共用存取簽章的詳細資訊，請參閱[在 Azure 儲存體中使用共用存取簽章 (SAS)](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>授與容器和 Blob 的匿名使用者權限

根據預設, 容器和其中的任何 blob 只能由已獲得適當許可權的使用者進行存取。 若要為匿名使用者授與容器及其 blob 的讀取權限, 您可以設定容器公用存取層級。 當您授與容器的公用存取權時, 匿名使用者可以讀取可公開存取之容器內的 blob, 而不需要授權要求。

您可以為容器設定下列權限︰

* **沒有公用讀取權限:** 只有儲存體帳戶擁有者可以存取容器和其 blob。 這是所有新建容器的預設值。
* **僅適用于 blob 的公用讀取權限:** 匿名要求可以讀取容器內的 blob, 但無法使用容器資料。 匿名用戶端無法列舉容器內的 Blob。
* **容器和其 blob 的公用讀取權限:** 匿名要求可以讀取所有容器和 blob 資料。 用戶端可以透過匿名要求列舉容器內的 Blob，但無法列舉儲存體帳戶內的容器。

您可以使用下列方式設定容器權限：

* [Azure 入口網站](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure CLI](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* 使用其中一個儲存體用戶端程式庫或 REST API 以程式設計方式設定

### <a name="set-container-public-access-level-in-the-azure-portal"></a>在 Azure 入口網站中設定容器公用存取層級

從[Azure 入口網站](https://portal.azure.com)中, 您可以更新一或多個容器的公用存取層級:

1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. 在功能表分頁的 [ **Blob 服務**] 底下, 選取 [ **blob**]。
1. 選取您要設定其公用存取層級的容器。
1. 使用 [**變更存取層級**] 按鈕來顯示公用存取設定。
1. 從 [**公用存取層級**] 下拉式清單中選取想要的公用存取層級, 然後按一下 [確定] 按鈕, 將變更套用至選取的容器。

下列螢幕擷取畫面顯示如何變更所選容器的公用存取層級。

![顯示如何在入口網站中設定公用存取層級的螢幕擷取畫面](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> 您無法變更個別 blob 的公用存取層級。 公用存取層級只會在容器層級設定。

### <a name="set-container-public-access-level-with-net"></a>使用 .NET 設定容器公用存取層級

若要使用 .NET 的 C# 和儲存體用戶端程式庫來設定容器的權限，請先呼叫 **GetPermissions** 方法來擷取容器的現有權限。 接著為由 **GetPermissions** 方法傳回的 **BlobContainerPermissions** 物件設定 **PublicAccess** 屬性。 最後，使用更新的權限呼叫 **SetPermissions** 方法。

下列範例將容器的權限設為完整公用讀取權限。 若只要將 Blob 的權限設為公用讀取權限，請將 **PublicAccess** 屬性設為 **BlobContainerPublicAccessType.Blob**。 若要移除匿名使用者的所有權限，請將屬性設為 **BlobContainerPublicAccessType.Off**。

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>匿名存取容器與 Blob

匿名存取容器與 Blob 的用戶端可使用不需要認證的建構函式。 下列範例示範以匿名方式參考容器和 blob 的幾種不同方法。

### <a name="create-an-anonymous-client-object"></a>建立匿名用戶端物件

您可以藉由提供帳戶的 Blob 儲存體端點, 為匿名存取建立新的服務用戶端物件。 不同，您也必須知道可供匿名存取的帳戶中的容器名稱。

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>匿名參考容器

如果您有可供匿名使用之容器的 URL，您可以使用該 URL 直接參考容器。

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>匿名參考 Blob

如果您有可供匿名存取之 Blob 的 URL，您可以使用該 URL 直接參考 Blob：

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>匿名使用者可使用的功能

下表顯示當容器設定為公用存取時, 可以匿名方式呼叫哪些作業。

| REST 作業 | 容器的公用讀取權限 | 僅限 Blob 的公用讀取存取 |
| --- | --- | --- |
| 列出容器 | 僅限授權的要求 | 僅限授權的要求 |
| 建立容器 | 僅限授權的要求 | 僅限授權的要求 |
| 取得容器屬性 | 允許匿名要求 | 僅限授權的要求 |
| 取得容器中繼資料 | 允許匿名要求 | 僅限授權的要求 |
| 設定容器中繼資料 | 僅限授權的要求 | 僅限授權的要求 |
| 取得容器 ACL | 僅限授權的要求 | 僅限授權的要求 |
| 設定容器 ACL | 僅限授權的要求 | 僅限授權的要求 |
| 刪除容器 | 僅限授權的要求 | 僅限授權的要求 |
| 列出 Blob | 允許匿名要求 | 僅限授權的要求 |
| 放置 Blob | 僅限授權的要求 | 僅限授權的要求 |
| 取得 Blob | 允許匿名要求 | 允許匿名要求 |
| 取得 Blob 屬性 | 允許匿名要求 | 允許匿名要求 |
| 設定 Blob 屬性 | 僅限授權的要求 | 僅限授權的要求 |
| 取得 Blob 中繼資料 | 允許匿名要求 | 允許匿名要求 |
| 設定 Blob 中繼資料 | 僅限授權的要求 | 僅限授權的要求 |
| 放置區塊 | 僅限授權的要求 | 僅限授權的要求 |
| 取得區塊清單 (僅限認可區塊) | 允許匿名要求 | 允許匿名要求 |
| 取得區塊清單 (僅限未認可的區塊或所有區塊) | 僅限授權的要求 | 僅限授權的要求 |
| 放置區塊清單 | 僅限授權的要求 | 僅限授權的要求 |
| 刪除 Blob | 僅限授權的要求 | 僅限授權的要求 |
| 複製 Blob | 僅限授權的要求 | 僅限授權的要求 |
| 快照 Blob | 僅限授權的要求 | 僅限授權的要求 |
| 租用 Blob | 僅限授權的要求 | 僅限授權的要求 |
| 放置頁面 | 僅限授權的要求 | 僅限授權的要求 |
| 取得頁面範圍 | 允許匿名要求 | 允許匿名要求 |
| 附加 Blob | 僅限授權的要求 | 僅限授權的要求 |

## <a name="next-steps"></a>後續步驟

* [Azure 儲存體服務的授權](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-azure-storage-services)
* [使用共用的存取簽章 (SAS)](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)