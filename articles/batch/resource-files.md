---
title: 建立和使用資源檔-Azure Batch |Microsoft Docs
description: 了解如何從各種輸入來源建立 Azure Batch 資源檔。
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: 113faffb0ebac50a67c96ce21e0ee2c1564bb4fc
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405646"
---
# <a name="creating-and-using-resource-files"></a>建立和使用資源檔

Azure Batch 工作通常需要某種形式的要處理的資料。 資源檔會提供這項資料到批次的虛擬機器 (VM) 透過工作的方法。 所有類型的工作都支援的資源檔： 工作，啟動工作、 作業準備工作、 作業發行工作等等。本文涵蓋如何建立資源檔，並將它們放在 VM 上的幾個常見方法。  

資源檔是一種機制來將資料放入批次中的 VM，但資料和使用方式的型別是有彈性。 有，不過，某些常見的使用案例：

1. 常見的檔案，使用資源檔，啟動工作在每個 VM 上佈建
1. 佈建工作所處理的輸入的資料

常見的檔案可能是，比方說，用來安裝您的工作執行的應用程式的啟動工作上的檔案。 輸入的資料可以是未經處理的影像或視訊資料或可由 Batch 所處理的任何資訊。

## <a name="types-of-resource-files"></a>資源檔類型

有幾個不同的選項可用來產生資源檔。 資源檔的建立程序會根據原始資料的儲存位置而有所不同。

建立資源檔的選項：

- [儲存體容器 URL](#storage-container-url):從在 Azure 中的任何儲存體容器產生的資源檔
- [儲存體容器名稱](#storage-container-name):從 Azure 儲存體帳戶連結至批次中的容器名稱產生資源檔
- [Web 端點](#web-endpoint):從任何有效的 HTTP URL 產生資源檔

### <a name="storage-container-url"></a>儲存體容器 URL

使用儲存體容器 URL，表示您可以存取具有正確的權限在 Azure 中的任何儲存體容器中的檔案。

在這個C#範例中，檔案已上傳至 Azure 儲存體容器與 blob 儲存體。 若要存取 建立資源檔所需的資料，我們需要以取得存取權的儲存體容器。

建立共用的存取簽章 (SAS) URI 來存取儲存體容器的正確權限。 設定到期時間和權限的 sas。 在此情況下，指定沒有開始時間，讓 SAS 立即生效，並產生後的兩個小時到期。

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> 對於容器的存取權，您必須同時`Read`並`List`權限，而使用 blob 的存取權，您只需要`Read`權限。

一旦設定權限，來建立 SAS 權杖，並設定儲存體容器的存取權之 SAS URL 的格式。 使用儲存體容器中，格式化的 SAS URL 產生資源檔[ `FromStorageContainerUrl` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)。

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

產生 SAS URL 的替代方式是，若要啟用對容器及其在 Azure Blob 儲存體 blob 的匿名與公用讀取權限。 如此一來，您可以授與這些資源的唯讀存取而不需要共用您的帳戶金鑰，且不需要 SAS。 公用讀取權限通常是適合您想要某些 blob 永遠可供匿名讀取權限。 如果此案例中適合您的解決方案，請參閱[匿名存取 blob](../storage/blobs/storage-manage-access-to-resources.md)文章以深入了解管理對 blob 資料的存取權。

### <a name="storage-container-name"></a>儲存體容器名稱

而不是設定和建立 SAS URL，您可以使用 Azure 儲存體容器的名稱來存取 blob 資料。 儲存體容器會使用必須連結到您的 Batch 帳戶，稱為 autostorage 帳戶的 Azure 儲存體帳戶中。 使用 autostorage 帳戶的儲存體容器名稱，可讓您略過設定，並建立用來存取儲存體容器的 SAS URL。

在此範例中，我們假設要用於建立資源檔的資料已在連結至您的 Batch 帳戶的 Azure 儲存體帳戶。 如果您還沒有 autostorage 帳戶，請參閱中的步驟[建立 Batch 帳戶](batch-account-create-portal.md)如需有關如何建立並將帳戶連結。

藉由使用連結的儲存體帳戶，您不需要建立及設定儲存體容器的 SAS URL。 相反地，提供您連結的儲存體帳戶中的儲存體容器名稱。

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Web 端點

未上傳至 Azure 儲存體的資料仍可用來建立資源檔。 您可以指定任何有效的 HTTP URL，其中包含您的輸入的資料。 Batch api，提供的 URL，然後使用資料建立的資源檔。

在下列C#範例中，輸入資料虛構的 GitHub 端點上裝載。 API 會擷取有效的 web 端點的檔案，並會產生資源檔，供您的工作。 在此案例不需要任何認證。

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>祕訣與建議

每個 Azure Batch 工作會使用檔案有不同，這也是為什麼批次有選項可用來管理工作上的檔案。 下列案例不是用來在完整，但改為涵蓋幾個常見的情況，並提供建議。

### <a name="many-resource-files"></a>許多資源檔

您的 Batch 作業可能包含數個工作都使用相同且常見的檔案。 如果在許多工作之間共用通用的工作檔案，則使用應用程式封裝包含的檔案，而不是使用資源檔可能是更好的選項。 應用程式套件提供下載速度的最佳化。 此外，在應用程式封裝中的資料會快取之間的工作，因此如果您的工作檔案不常變更，應用程式封裝可能適合您的解決方案。 使用應用程式套件，您不需要手動管理數個資源檔，或是產生 SAS Url 以存取 Azure 儲存體中的檔案。 批次會在背景工作使用 Azure 儲存體來儲存及部署至計算節點的應用程式套件。

如果每項工作有許多檔案，該工作唯一，資源檔最有可能是最佳選項。 經常使用唯一的檔案的工作需要更新或取代，不是與應用程式封裝內容一樣簡單。 資源檔會提供額外的彈性，針對更新、 新增或編輯個別檔案。

### <a name="number-of-resource-files-per-task"></a>每個工作的資源檔的數目

如果有一項工作中指定的數個數百個資源檔，批次可能會拒絕工作，過大。 最好的方式是保留您的工作的小型工作本身上的資源檔的數目降到最低。

如果沒有任何方法以減少檔案數目將您的工作需要您可以建立參考資源檔的儲存體容器的單一資源檔，以最佳化工作。 若要這樣做，請將資源檔放入 Azure 儲存體容器並使用不同的資源檔的 「 容器 」 模式。 您可以使用 blob 前置詞的選項來指定要下載您的工作檔案的集合。

## <a name="next-steps"></a>後續步驟

- 深入了解[應用程式封裝](batch-application-packages.md)作為資源檔的替代方案。
- 如需使用容器的資源檔的詳細資訊，請參閱[容器工作負載](batch-docker-container-workloads.md)。
- 若要了解如何收集並儲存您的工作輸出資料，請參閱[將作業和工作輸出保存](batch-task-output.md)。
- 了解可用來建置 Batch 解決方案的 [Batch API 和工具](batch-apis-tools.md)。
