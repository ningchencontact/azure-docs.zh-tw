---
title: 使用 Python 的 Azure 儲存體範例 | Microsoft Docs
description: 檢視、下載及執行 Azure 儲存體的範例程式碼和應用程式。 探索使用 Python 儲存體用戶端程式庫之 Blob、佇列、資料表和檔案的入門範例。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 5d823059842bf539f00ad372127fc8b636c74b65
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70745965"
---
# <a name="azure-storage-samples-using-python"></a>使用 Python 的 Azure 儲存體範例

下表提供我們的範例儲存機制和每個範例所涵蓋案例的概觀。 按一下連結即可檢視 GitHub 中對應的範例程式碼。

## <a name="blob-samples"></a>Blob 範例

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 附加 Blob | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L166) |
| 區塊 Blob | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L77) |
| 用戶端加密 | [使用 Python 在 Azure Key Vault 中管理儲存體帳戶金鑰](https://github.com/Azure-Samples/key-vault-python-storage-accounts) \(英文\) |
| 複製 Blob | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L102) |
| 建立容器 | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L91) |
| 刪除 Blob | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L114) |
| 刪除容器 | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L118) |
| Blob 中繼資料/屬性/統計資料 | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L298) |
| 容器 ACL/中繼資料/屬性 | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L268) |
| 取得頁面範圍 | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L151) |
| 租用 Blob/容器 | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L377) |
| 列出 Blob/容器 | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L103) |
| 分頁 Blob | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L124) |
| SAS | [在 Python 中共用存取簽章](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L145) |
| 服務屬性 | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L540) |
| 快照 Blob | [開始在 Python 中使用 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L214) |

## <a name="file-samples"></a>檔案範例

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 建立共用/目錄/檔案 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L71) |
| 刪除共用/目錄/檔案 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L170) |
| 目錄屬性/中繼資料 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L175) |
| 下載檔案 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L138) |
| 檔案屬性/中繼資料/計量 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L193) |
| 檔案服務屬性 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L125) |
| 列出目錄和檔案 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L153) |
| 列出共用 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L82) |
| 共用屬性/中繼資料/統計資料 | [開始在 Python 中使用 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L144) |

## <a name="queue-samples"></a>佇列範例

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 新增訊息 | [開始在 Python 中使用 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L94) |
| 用戶端加密 | [使用 Python 在 Azure Key Vault 中管理儲存體帳戶金鑰](https://github.com/Azure-Samples/key-vault-python-storage-accounts) \(英文\) |
| 建立佇列 | [開始在 Python 中使用 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L75) |
| 刪除訊息/佇列 | [開始在 Python 中使用 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L144) |
| 查看訊息 | [開始在 Python 中使用 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L110) |
| 佇列 ACL/中繼資料/統計資料 | [開始在 Python 中使用 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L148) |
| 佇列服務屬性 | [開始在 Python 中使用 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L128) |
| 更新訊息 | [開始在 Python 中使用 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L120) |

## <a name="table-samples"></a>資料表範例

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 建立資料表 | [開始在 Python 中使用 Azure 表格服務](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L46) |
| 刪除實體/資料表 | [開始在 Python 中使用 Azure 表格服務](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L79) |
| 插入/合併/取代實體 | [開始在 Python 中使用 Azure 表格服務](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L57) |
| 查詢實體 | [開始在 Python 中使用 Azure 表格服務](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L62) |
| 查詢資料表 | [開始在 Python 中使用 Azure 表格服務](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py) |
| 資料表 ACL/屬性 | [開始在 Python 中使用 Azure 表格服務](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_advanced_samples.py#L138) |
| 更新實體 | [開始在 Python 中使用 Azure 表格服務](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L68) |

## <a name="azure-code-samples-library"></a>Azure 程式碼範例程式庫

若要檢視完整的範例程式庫，請移至 [Azure 程式碼範例](https://azure.microsoft.com/resources/samples/?service=storage)程式庫，其中包含您可以下載並在本機執行的「Azure 儲存體」範例。 程式碼範例程式庫會提供 .zip 格式的範例程式碼。 或者，您可以瀏覽並複製每個範例的 GitHub 儲存機制。

[!INCLUDE [storage-python-samples-include](../../../includes/storage-python-samples-include.md)]

## <a name="getting-started-guides"></a>入門指南

如果您要尋找有關如何安裝和開始使用「Azure 儲存體用戶端程式庫」的指示，請查看下列指南。

* [開始在 Python 中使用 Azure Blob 服務](../blobs/storage-quickstart-blobs-python.md)
* [開始在 Python 中使用 Azure 佇列服務](../queues/storage-python-how-to-use-queue-storage.md)
* [開始在 Python 中使用 Azure 表格服務](../../cosmos-db/table-storage-how-to-use-python.md)
* [開始在 Python 中使用 Azure 檔案服務](../files/storage-python-how-to-use-file-storage.md)

## <a name="next-steps"></a>後續步驟

如需其他語言的範例相關資訊︰

* .NET：[使用 .NET 的 Azure 儲存體範例](storage-samples-dotnet.md)
* Java：[使用 Java 的 Azure 儲存體範例](storage-samples-java.md)
* 所有其他語言：[Azure 儲存體範例](storage-samples.md)
