---
title: Azure Data Lake Storage Gen2 的已知問題 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 的限制和已知問題
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: normesta
ms.openlocfilehash: ff158b726c57f4aa5b7822dc0273ab42c350522c
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895528"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知問題

本文包含 Azure Data Lake Storage Gen2 的已知問題和暫時性限制。

## <a name="api-interoperability"></a>API 互通性

Blob 儲存體 API 和 Azure Data Lake Gen2 API 無法彼此互通。

如果您具有使用 Blob API 的工具、應用程式、服務或指令碼，而想要使用它們來處理上傳至帳戶的所有內容，則在 Blob API 與 Azure Data Lake Gen2 API 已可交互作用之前，請勿在 Blob 儲存體帳戶上啟用階層命名空間。 使用不具有階層命名空間的儲存體帳戶，表示您無法存取 Data Lake Storage Gen2 的特定功能，例如目錄和檔案系統存取控制清單。

## <a name="blob-storage-apis"></a>Blob 儲存體 API

Blob 儲存體 API 尚未提供給 Azure Data Lake Storage Gen2 帳戶使用。

這些 API 已停用以防止可能引發的意外資料存取問題，因為 Blob 儲存體 API 還不能與 Azure Data Lake Gen2 API 互通。

如果您在 API 停用之前已使用這些 API 載入資料，而且您有存取該資料的實際需求，請透過下列資訊連絡 Microsoft 支援服務：

* 訂用帳戶識別碼 (GUID，而不是名稱)

* 儲存體帳戶名稱

* 您是否在生產環境中受到影響，如果受影響，是哪些儲存體帳戶？

* 即使您不會在生產環境中受到影響，也請告訴我們是否基於某些原因而需要這些資料複製到另一個儲存體帳戶，如果是，為什麼？

在這些情況下，我們可以在一段有限的時間內還原 Blob API 的存取，以便您可以將這些資料複製到未啟用階層命名空間功能的儲存體帳戶。

非受控虛擬機器 (VM) 磁碟仰賴停用的 Blob 儲存體 API，因此如果您要在儲存體帳戶上啟用階層命名空間，請考慮將非受控 VM 磁碟放入沒有啟用階層命名空間功能的儲存體帳戶。

## <a name="azure-storage-explorer"></a>Azure 儲存體總管

若要檢視或使用 Azure 儲存體總管來管理 Data Lake Storage Gen2 帳戶，您至少必須有版本 `1.6.0` 的工具，這個版本可供[免費下載](https://azure.microsoft.com/features/storage-explorer/)。

請注意，內嵌至 Azure 入口網站的儲存體總管版本目前不支援檢視或管理已啟用階層命名空間功能的 Data Lake Storage Gen2 帳戶。

## <a name="blob-viewing-tool"></a>Blob 檢視工具

Azure 入口網站上的 Blob 檢視工具對於 Azure Data Lake Storage Gen2 僅提供有限支援。

## <a name="third-party-applications"></a>協力廠商應用程式

協力廠商應用程式可能不支援 Azure Data Lake Storage Gen2。

支援是由每個協力廠商應用程式提供者自行決定是否提供。 目前，Blob 儲存體 API 和 Azure Data Lake Storage Gen2 API 無法用來管理相同內容。 我們致力於提供該互通性，因此可能許多協力廠商工具將會自動支援 Azure Data Lake Storage Gen2。

## <a name="azcopy-support"></a>AzCopy 支援

AzCopy 第 8 版不支援 Azure Data Lake Storage Gen2。

請改為使用 AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)) 最新的預覽版本，其支援 Azure Data Lake Storage Gen2 端點。

## <a name="azure-event-grid"></a>Azure Event Grid

[Azure 事件格線](https://azure.microsoft.com/services/event-grid/)不會接收來自 Azure Data Lake Gen2 帳戶的事件，因為那些帳戶還不會產生事件。  

## <a name="soft-delete-and-snapshots"></a>虛刪除和快照集

虛刪除和快照集不適用於Azure Data Lake Storage Gen2 帳戶。

所有的版本控制功能 (包括[快照集](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)和[虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)) 都還無法提供給啟用階層命名空間功能的儲存體帳戶使用。

## <a name="object-level-storage-tiers"></a>物件層級儲存層

物件層級儲存層 (經常性、非經常性和封存) 都還無法提供給 Azure Data Lake Storage Gen 2 帳戶使用，但它們可提供給未啟用階層命名空間功能的儲存體帳戶使用。

## <a name="azure-blob-storage-lifecycle-management-policies"></a>Azure Blob 儲存體生命週期管理原則

Azure Blob 儲存體生命週期管理原則還無法提供給 Azure Data Lake Storage Gen2 帳戶使用。

這些原則可提供給未啟用階層命名空間功能的儲存體帳戶使用。

## <a name="diagnostic-logs"></a>診斷記錄檔

診斷記錄不適用於 Azure Data Lake Storage Gen2 帳戶。
