---
title: 使用 Python 的 Azure 儲存體範例 | Microsoft Docs
description: 檢視、下載及執行 Azure 儲存體的範例程式碼和應用程式。 探索使用 Python 儲存體用戶端程式庫之 Blob、佇列、資料表和檔案的入門範例。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 7f694ee51989023a3e7a72f40700edcbb6a97bae
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747980"
---
# <a name="azure-storage-samples-using-python"></a>使用 Python 的 Azure 儲存體範例

下表提供我們的範例儲存機制和每個範例所涵蓋案例的概觀。 按一下連結即可檢視 GitHub 中對應的範例程式碼。

> [!NOTE]
> 這些範例會使用 Azure 儲存體 Python v2.1 程式庫。 如需 v12 程式碼，請參閱 GitHub 存放庫中的[範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)。

## <a name="blob-samples-v21"></a>Blob 範例 (v2.1)

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 附加 Blob | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L166) \(英文\) |
| 區塊 Blob | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L77) \(英文\) |
| 用戶端加密 | [使用 Python 在 Azure Key Vault 中管理儲存體帳戶金鑰](https://github.com/Azure-Samples/key-vault-python-storage-accounts) \(英文\) |
| 複製 Blob | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L102) \(英文\) |
| 建立容器 | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L91) \(英文\) |
| 刪除 Blob | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L114) \(英文\) |
| 刪除容器 | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L118) \(英文\) |
| Blob 中繼資料/屬性/統計資料 | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L298) \(英文\) |
| 容器 ACL/中繼資料/屬性 | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L268) \(英文\) |
| 取得頁面範圍 | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L151) \(英文\) |
| 租用 Blob/容器 | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L377) \(英文\) |
| 列出 Blob/容器 | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L103) \(英文\) |
| 分頁 Blob | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L124) \(英文\) |
| SAS | [在 Python 中共用存取簽章](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L145) |
| 服務屬性 | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L540) \(英文\) |
| 快照 Blob | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L214) \(英文\) |

## <a name="file-samples-v21"></a>檔案範例 (v2.1)

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 建立共用/目錄/檔案 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L71) \(英文\) |
| 刪除共用/目錄/檔案 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L170) \(英文\) |
| 目錄屬性/中繼資料 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L175) \(英文\) |
| 下載檔案 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L138) \(英文\) |
| 檔案屬性/中繼資料/計量 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L193) \(英文\) |
| 檔案服務屬性 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L125) \(英文\) |
| 列出目錄和檔案 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L153) \(英文\) |
| 列出共用 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L82) \(英文\) |
| 共用屬性/中繼資料/統計資料 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L144) \(英文\) |

## <a name="queue-samples-v21"></a>佇列範例 (v2.1)

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 新增訊息 | [開始在 Python 中使用 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L94) \(英文\) |
| 用戶端加密 | [使用 Python 在 Azure Key Vault 中管理儲存體帳戶金鑰](https://github.com/Azure-Samples/key-vault-python-storage-accounts) \(英文\) |
| 建立佇列 | [開始在 Python 中使用 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L75) \(英文\) |
| 刪除訊息/佇列 | [開始在 Python 中使用 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L144) \(英文\) |
| 查看訊息 | [開始在 Python 中使用 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L110) \(英文\) |
| 佇列 ACL/中繼資料/統計資料 | [開始在 Python 中使用 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L148) \(英文\) |
| 佇列服務屬性 | [開始在 Python 中使用 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L128) \(英文\) |
| 更新訊息 | [開始在 Python 中使用 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L120) \(英文\) |

## <a name="table-samples-v21"></a>資料表範例 (v2.1)

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 建立資料表 | [開始在 Python 中使用 Azure 表格服務](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L46) \(英文\) |
| 刪除實體/資料表 | [開始在 Python 中使用 Azure 表格服務](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L79) \(英文\) |
| 插入/合併/取代實體 | [開始在 Python 中使用 Azure 表格服務](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L57) \(英文\) |
| 查詢實體 | [開始在 Python 中使用 Azure 表格服務](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L62) \(英文\) |
| 查詢資料表 | [開始在 Python 中使用 Azure 表格服務](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py) \(英文\) |
| 資料表 ACL/屬性 | [開始在 Python 中使用 Azure 表格服務](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_advanced_samples.py#L138) \(英文\) |
| 更新實體 | [開始在 Python 中使用 Azure 表格服務](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L68) \(英文\) |

## <a name="azure-code-samples-library"></a>Azure 程式碼範例程式庫

若要檢視完整的範例程式庫，請移至 [Azure 程式碼範例](https://azure.microsoft.com/resources/samples/?service=storage)程式庫，其中包含您可以下載並在本機執行的「Azure 儲存體」範例。 程式碼範例程式庫會提供 .zip 格式的範例程式碼。 或者，您可以瀏覽並複製每個範例的 GitHub 存放庫。

[!INCLUDE [storage-python-samples-include](../../../includes/storage-python-samples-include.md)]

## <a name="getting-started-guides"></a>入門指南

如果您要尋找有關如何安裝和開始使用「Azure 儲存體用戶端程式庫」的指示，請查看下列指南。

* [開始在 Python 中使用 Azure Blob 服務](../blobs/storage-quickstart-blobs-python.md) \(英文\)
* [開始在 Python 中使用 Azure 佇列服務](../queues/storage-python-how-to-use-queue-storage.md) \(英文\)
* [開始在 Python 中使用 Azure 表格服務](../../cosmos-db/table-storage-how-to-use-python.md) \(英文\)
* [開始在 Python 中使用 Azure 檔案服務](../files/storage-python-how-to-use-file-storage.md) \(英文\)

## <a name="next-steps"></a>後續步驟

如需其他語言的範例相關資訊︰

* .NET：[使用 .NET 的 Azure 儲存體範例](storage-samples-dotnet.md)
* Java：[使用 Java 的 Azure 儲存體範例](storage-samples-java.md)
* JavaScript/Node.js：[使用 JavaScript 的 Azure 儲存體範例](storage-samples-javascript.md)
* 所有其他語言：[Azure 儲存體範例](storage-samples.md)
