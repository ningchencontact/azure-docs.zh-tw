---
title: 使用 Java 的 Azure 儲存體範例 | Microsoft Docs
description: 檢視、下載及執行 Azure 儲存體的範例程式碼和應用程式。 探索使用 Java 儲存體用戶端程式庫之 Blob、佇列、資料表和檔案的入門範例。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: b295c5a945f41059ca98e96d742bc5e1f6b4bf9f
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744796"
---
# <a name="azure-storage-samples-using-java"></a>使用 Java 的 Azure 儲存體範例

下表提供我們的範例儲存機制和每個範例所涵蓋案例的概觀。 按一下連結即可檢視 GitHub 中對應的範例程式碼。

## <a name="blob-samples"></a>Blob 範例

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 附加 Blob | [開始使用 Java 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 區塊 Blob | [開始使用 Java 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 用戶端加密 | [開始使用 Java 中的 Azure 用戶端加密](https://github.com/Azure-Samples/storage-java-client-side-encryption) |
| 複製 Blob | [開始使用 Java 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 建立容器 | [開始使用 Java 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 刪除 Blob | [開始使用 Java 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 刪除容器 | [開始使用 Java 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Blob 中繼資料/屬性/統計資料 | [開始使用 Java 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| 容器 ACL/中繼資料/屬性 | [開始使用 Java 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| 取得頁面範圍 | [開始使用 Java 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399) |
| 租用 Blob/容器 | [開始使用 Java 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 列出 Blob/容器 | [開始使用 Java 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 分頁 Blob | [開始使用 Java 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| SAS | [SAS 測試範例](https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513) |
| 服務屬性 | [開始使用 Java 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| 快照 Blob | [開始使用 Java 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |

## <a name="file-samples"></a>檔案範例

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 建立共用/目錄/檔案 | [開始使用 Java 中的 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 刪除共用/目錄/檔案 | [開始使用 Java 中的 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 目錄屬性/中繼資料 | [開始使用 Java 中的 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| 下載檔案 | [開始使用 Java 中的 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 檔案屬性/中繼資料/計量 | [開始使用 Java 中的 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| 檔案服務屬性 | [開始使用 Java 中的 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| 列出目錄和檔案 | [開始使用 Java 中的 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 列出共用 | [開始使用 Java 中的 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 共用屬性/中繼資料/統計資料 | [開始使用 Java 中的 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |

## <a name="queue-samples"></a>佇列範例

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 新增訊息 | [開始使用 Java 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63) |
| 用戶端加密 | [開始使用 Java 中的 Azure 用戶端加密](https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java) |
| 建立佇列 | [開始使用 Java 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| 刪除訊息/佇列 | [開始使用 Java 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| 查看訊息 | [開始使用 Java 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| 佇列 ACL/中繼資料/統計資料 | [開始使用 Java 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| 佇列服務屬性 | [開始使用 Java 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| 更新訊息 | [開始使用 Java 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java)
|
## <a name="table-samples"></a>資料表範例

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 建立資料表 | [開始使用 Java 中的 Azure 表格服務](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| 刪除實體/資料表 | [開始使用 Java 中的 Azure 表格服務](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| 插入/合併/取代實體 | [開始使用 Java 中的 Azure 表格服務](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| 查詢實體 | [開始使用 Java 中的 Azure 表格服務](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| 查詢資料表 | [開始使用 Java 中的 Azure 表格服務](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| 資料表 ACL/屬性 | [開始使用 Java 中的 Azure 表格服務](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java) |
| 更新實體 | [開始使用 Java 中的 Azure 表格服務](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
## <a name="azure-code-samples-library"></a>Azure 程式碼範例程式庫

若要檢視完整的範例程式庫，請移至 [Azure 程式碼範例](https://azure.microsoft.com/resources/samples/?service=storage)程式庫，其中包含您可以下載並在本機執行的「Azure 儲存體」範例。 程式碼範例程式庫會提供 .zip 格式的範例程式碼。 或者，您可以瀏覽並複製每個範例的 GitHub 儲存機制。

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>入門指南

如果您要尋找有關如何安裝和開始使用 Azure 儲存體用戶端程式庫的指示，請查看下列指南。

* [開始使用 Java 中的 Azure Blob 服務](../blobs/storage-quickstart-blobs-java.md)
* [開始使用 Java 中的 Azure 佇列服務](../queues/storage-java-how-to-use-queue-storage.md)
* [開始使用 Java 中的 Azure 表格服務](../../cosmos-db/table-storage-how-to-use-java.md)
* [開始使用 Java 中的 Azure 檔案服務](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>後續步驟

如需其他語言的範例相關資訊︰

* .NET：[使用 .NET 的 Azure 儲存體範例](storage-samples-dotnet.md)
* Python：[使用 Python 的 Azure 儲存體範例](storage-samples-python.md)
* 所有其他語言：[Azure 儲存體範例](storage-samples.md)
