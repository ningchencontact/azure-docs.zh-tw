---
title: 使用 JavaScript 的 Azure 儲存體範例 | Microsoft Docs
description: 檢視、下載及執行 Azure 儲存體的範例程式碼和應用程式。 探索使用 JavaScript/Node.js 儲存體用戶端程式庫之 Blob、佇列、資料表和檔案的入門範例。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/26/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 363739bbab67b92e78e0cdb56cf98cdb8e1446eb
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350896"
---
# <a name="azure-storage-samples-using-javascript"></a>使用 JavaScript 的 Azure 儲存體範例

下表提供我們的範例儲存機制和每個範例所涵蓋案例的概觀。 按一下連結即可檢視 GitHub 中對應的範例程式碼。

## <a name="blob-samples"></a>Blob 範例

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 區塊 Blob | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L43) |
| 用戶端加密 | [使用 JavaScript 在 Azure Key Vault 中管理儲存體帳戶金鑰](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| 複製 Blob | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L73) |
| 建立容器 | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L54) |
| 刪除 Blob | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L103) |
| 刪除容器 | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L110) |
| Blob 中繼資料 | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L538) |
| Blob 屬性 | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L478) |
| 容器 ACL | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L444) |
| 容器中繼資料 | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L409) |
| 容器屬性 | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L377) |
| 取得頁面範圍 | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L170) |
| 租用 Blob | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L216) |
| 租用容器 | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L185) |
| 列出 Blob/容器 | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L134) |
| 分頁 Blob | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L129) |
| SAS | [在 JavaScript 中共用存取簽章](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L257) |
| 服務屬性 | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L308) |
| 設定 Cors 規則 | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L152) |
| 快照 Blob | [開始使用 JavaScript 中的 Azure Blob 服務](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L79) |

## <a name="file-samples"></a>檔案範例

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 建立共用/目錄/檔案 | [開始使用 JavaScript 中的 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L97) |
| 刪除共用/目錄/檔案 | [開始使用 JavaScript 中的 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L135) |
| 下載檔案 | [開始使用 JavaScript 中的 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L128) |
| 列出目錄和檔案 | [開始使用 JavaScript 中的 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L115) |
| 列出共用 | [開始使用 JavaScript 中的 Azure 檔案服務](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L187) |

## <a name="queue-samples"></a>佇列範例

| **案例** | **範例程式碼** |
|--------------|-----------------|
| 新增訊息 | [開始使用 JavaScript 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L142) |
| 用戶端加密 | [使用 JavaScript 在 Azure Key Vault 中管理儲存體帳戶金鑰](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| 建立佇列 | [開始使用 JavaScript 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L57) |
| 刪除訊息 | [開始使用 JavaScript 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L164) |
| 刪除佇列 | [開始使用 JavaScript 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L203) |
| 列出佇列 | [開始使用 JavaScript 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L111) |
| 查看訊息 | [開始使用 JavaScript 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L170) |
| 佇列 ACL | [開始使用 JavaScript 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L192) |
| 佇列 Cors 規則 | [開始使用 JavaScript 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L55) |
| 關閉中繼資料 | [開始使用 JavaScript 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L161) |
| 佇列服務屬性 | [開始使用 JavaScript 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L94) |
| 佇列統計資料 | [開始使用 JavaScript 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L149) |
| 更新訊息 | [開始使用 JavaScript 中的 Azure 佇列服務](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L176) |

## <a name="table-samples"></a>資料表範例

| **案例** | **範例程式碼** |
|--------------|-----------------|
| Batch 實體 | [開始使用 JavaScript 中的 Azure 資料表服務](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L87) |
| 建立資料表 | [開始使用 JavaScript 中的 Azure 資料表服務](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L41) |
| 刪除實體/資料表 | [開始使用 JavaScript 中的 Azure 資料表服務](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L67) |
| 插入/合併/取代實體 | [開始使用 JavaScript 中的 Azure 資料表服務](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |
| 列出資料表 | [開始使用 JavaScript 中的 Azure 資料表服務](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L63) |
| 查詢實體 | [開始使用 JavaScript 中的 Azure 資料表服務](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L59) |
| 查詢資料表 | [開始使用 JavaScript 中的 Azure 資料表服務](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L140) |
| 範圍查詢 | [開始使用 JavaScript 中的 Azure 資料表服務](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L102) |
| SAS | [在 JavaScript 中共用存取簽章](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L87) |
| 資料表 ACL | [開始使用 JavaScript 中的 Azure 資料表服務](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L255) |
| 資料表 Cors 規則 | [開始使用 JavaScript 中的 Azure 資料表服務](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L149) |
| 資料表屬性 | [開始使用 JavaScript 中的 Azure 資料表服務](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L188) |
| 資料表統計資料 | [開始使用 JavaScript 中的 Azure 資料表服務](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L243) |
| 更新實體 | [開始使用 JavaScript 中的 Azure 資料表服務](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |

## <a name="azure-code-samples-library"></a>Azure 程式碼範例程式庫

若要檢視完整的範例程式庫，請移至 [Azure 程式碼範例](https://azure.microsoft.com/resources/samples/?service=storage)程式庫，其中包含您可以下載並在本機執行的「Azure 儲存體」範例。 程式碼範例程式庫會提供 .zip 格式的範例程式碼。 或者，您可以瀏覽並複製每個範例的 GitHub 儲存機制。

[!INCLUDE [storage-node-samples-include](../../../includes/storage-node-samples-include.md)]

## <a name="getting-started-guides"></a>入門指南

如果您要尋找有關如何安裝和開始使用「Azure 儲存體用戶端程式庫」的指示，請查看下列指南。

* [開始使用 JavaScript 中的 Azure Blob 服務](../blobs/storage-quickstart-blobs-nodejs.md)
* [開始使用 JavaScript 中的 Azure 佇列服務](../queues/storage-nodejs-how-to-use-queues.md)
* [開始使用 JavaScript 中的 Azure 資料表服務](../../cosmos-db/table-storage-how-to-use-nodejs.md)

## <a name="next-steps"></a>後續步驟

如需其他語言的範例相關資訊︰

* .NET：[使用 .NET 的 Azure 儲存體範例](storage-samples-dotnet.md)
* Java：[使用 Java 的 Azure 儲存體範例](storage-samples-java.md)
* Python：[使用 Python 的 Azure 儲存體範例](storage-samples-python.md)
* 所有其他語言：[Azure 儲存體範例](storage-samples.md)
