---
title: 使用 .NET 的 Azure 儲存體範例 | Microsoft Docs
description: 檢視、下載及執行 Azure 儲存體的範例程式碼和應用程式。 探索使用 .NET 儲存體用戶端程式庫之 Blob、佇列、資料表和檔案的入門範例。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 9d6e441adeb7025e09b8d73764d2defab439dd90
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350912"
---
# <a name="azure-storage-samples-using-net"></a>使用 .NET 的 Azure 儲存體範例

下表提供我們的範例儲存機制和每個範例所涵蓋案例的概觀。 按一下連結即可檢視 GitHub 中對應的範例程式碼。

## <a name="blob-samples"></a>Blob 範例

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 附加 Blob | [開始使用 Blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144) |
| 區塊 Blob | [Azure Blob Storage Photo Gallery Web Application (Azure Blob 儲存體影像中心 Web 應用程式)](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| 用戶端加密 | [Blob 加密範例](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs) |
| 複製 Blob | [開始使用 Blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| 建立容器 | [Azure Blob Storage Photo Gallery Web Application (Azure Blob 儲存體影像中心 Web 應用程式)](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| 刪除 Blob | [Azure Blob Storage Photo Gallery Web Application (Azure Blob 儲存體影像中心 Web 應用程式)](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| 刪除容器 | [開始使用 Blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Blob 中繼資料/屬性/統計資料 | [開始使用 Blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| 容器 ACL/中繼資料/屬性 | [Azure Blob Storage Photo Gallery Web Application (Azure Blob 儲存體影像中心 Web 應用程式)](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| 取得頁面範圍 | [開始使用 Blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| 租用 Blob/容器 | [開始使用 Blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| 列出 Blob/容器 | [開始使用 Blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| 分頁 Blob | [開始使用 Blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| SAS | [開始使用 Blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| 服務屬性 | [開始使用 Blob](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| 快照 Blob | [使用增量快照備份 Azure 虛擬機器磁碟](https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs) |

## <a name="file-samples"></a>檔案範例

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 建立共用/目錄/檔案 | [Azure 儲存體 .NET 檔案儲存體範例](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| 刪除共用/目錄/檔案 | [Getting Started with Azure File Service in .NET (開始使用 .NET 中的 Azure 檔案服務)](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs) |
| 目錄屬性/中繼資料 | [Azure 儲存體 .NET 檔案儲存體範例](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| 下載檔案 | [Azure 儲存體 .NET 檔案儲存體範例](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| 檔案屬性/中繼資料/計量 | [Azure 儲存體 .NET 檔案儲存體範例](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| 檔案服務屬性 | [Azure 儲存體 .NET 檔案儲存體範例](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| 列出目錄和檔案 | [Azure 儲存體 .NET 檔案儲存體範例](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| 列出共用 | [Azure 儲存體 .NET 檔案儲存體範例](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| 共用屬性/中繼資料/統計資料 | [Azure 儲存體 .NET 檔案儲存體範例](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |

## <a name="queue-samples"></a>佇列範例

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 新增訊息 | [Getting Started with Azure Queue Service in .NET (開始使用 .NET 中的 Azure 佇列服務)](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| 用戶端加密 | [Azure 儲存體 .NET 佇列用戶端加密](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs) |
| 建立佇列 | [Getting Started with Azure Queue Service in .NET (開始使用 .NET 中的 Azure 佇列服務)](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| 刪除訊息/佇列 | [Getting Started with Azure Queue Service in .NET (開始使用 .NET 中的 Azure 佇列服務)](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| 查看訊息 | [Getting Started with Azure Queue Service in .NET (開始使用 .NET 中的 Azure 佇列服務)](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| 佇列 ACL/中繼資料/統計資料 | [Getting Started with Azure Queue Service in .NET (開始使用 .NET 中的 Azure 佇列服務)](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| 佇列服務屬性 | [Getting Started with Azure Queue Service in .NET (開始使用 .NET 中的 Azure 佇列服務)](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| 更新訊息 | [Getting Started with Azure Queue Service in .NET (開始使用 .NET 中的 Azure 佇列服務)](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |

## <a name="table-samples"></a>資料表範例

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 建立資料表 | [使用 Azure 儲存體管理並行存取 - 範例應用程式](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| 刪除實體/資料表 | [在 .NET 中開始使用 Azure 表格儲存體](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| 插入/合併/取代實體 | [使用 Azure 儲存體管理並行存取 - 範例應用程式](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| 查詢實體 | [在 .NET 中開始使用 Azure 表格儲存體](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| 查詢資料表 | [在 .NET 中開始使用 Azure 表格儲存體](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| 資料表 ACL/屬性 | [在 .NET 中開始使用 Azure 表格儲存體](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs) |
| 更新實體 | [使用 Azure 儲存體管理並行存取 - 範例應用程式](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |

## <a name="azure-code-samples-library"></a>Azure 程式碼範例程式庫

若要檢視完整的範例程式庫，請移至 [Azure 程式碼範例](https://azure.microsoft.com/resources/samples/?service=storage)程式庫，其中包含您可以下載並在本機執行的「Azure 儲存體」範例。 程式碼範例程式庫會提供 .zip 格式的範例程式碼。 或者，您可以瀏覽並複製每個範例的 GitHub 儲存機制。

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>入門指南

如果您要尋找有關如何安裝和開始使用「Azure 儲存體用戶端程式庫」的指示，請查看下列指南。

* [Getting Started with Azure Blob Service in .NET (開始使用 .NET 中的 Azure Blob 服務)](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Getting Started with Azure Queue Service in .NET (開始使用 .NET 中的 Azure 佇列服務)](../storage-dotnet-how-to-use-queues.md)
* [Getting Started with Azure Table Service in .NET (開始使用 .NET 中的 Azure 表格服務)](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Getting Started with Azure File Service in .NET (開始使用 .NET 中的 Azure 檔案服務)](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>後續步驟

如需其他語言的範例相關資訊︰

* Java：[使用 Java 的 Azure 儲存體範例](storage-samples-java.md)
* JavaScript/Node.js：[使用 JavaScript 的 Azure 儲存體範例](storage-samples-javascript.md)
* Python：[使用 Python 的 Azure 儲存體範例](storage-samples-python.md)
* 所有其他語言：[Azure 儲存體範例](../storage-samples.md)
