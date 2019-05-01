---
title: Azure Data Lake Storage Gen2 的已知問題 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 的限制和已知問題
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: normesta
ms.openlocfilehash: d56fb411eb032e5e6227d68cd8abe02c0e30850b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708459"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知問題

本文包含 Data Lake Storage Gen2 的已知问题和临时限制。

## <a name="sdk-support-for-data-lake-storage-gen2-accounts"></a>针对 Data Lake Storage Gen2 帐户的 SDK 支持

没有可用于 Data Lake Storage Gen2 帐户的 SDK。

## <a name="blob-storage-apis"></a>Blob 儲存體 API

Data Lake Storage Gen2 帐户尚不能使用 Blob 存储 API。

這些 API 已停用以防止可能引發的意外資料存取問題，因為 Blob 儲存體 API 還不能與 Azure Data Lake Gen2 API 互通。

如果您在 API 停用之前已使用這些 API 載入資料，而且您有存取該資料的實際需求，請透過下列資訊連絡 Microsoft 支援服務：

* 訂用帳戶識別碼 (GUID，而不是名稱)

* 儲存體帳戶名稱

* 您是否在生產環境中受到影響，如果受影響，是哪些儲存體帳戶？

* 即使您不會在生產環境中受到影響，也請告訴我們是否基於某些原因而需要這些資料複製到另一個儲存體帳戶，如果是，為什麼？

在這些情況下，我們可以在一段有限的時間內還原 Blob API 的存取，以便您可以將這些資料複製到未啟用階層命名空間功能的儲存體帳戶。

非受控虛擬機器 (VM) 磁碟仰賴停用的 Blob 儲存體 API，因此如果您要在儲存體帳戶上啟用階層命名空間，請考慮將非受控 VM 磁碟放入沒有啟用階層命名空間功能的儲存體帳戶。

## <a name="api-interoperability"></a>API 互通性

Blob 儲存體 API 和 Azure Data Lake Gen2 API 無法彼此互通。

如果您具有使用 Blob API 的工具、應用程式、服務或指令碼，而想要使用它們來處理上傳至帳戶的所有內容，則在 Blob API 與 Azure Data Lake Gen2 API 已可交互作用之前，請勿在 Blob 儲存體帳戶上啟用階層命名空間。 使用儲存體帳戶不具有階層式命名空間表示則不需要存取 Data Lake 儲存體 Gen2 特定功能，例如目錄和檔案系統存取控制清單。

## <a name="azure-storage-explorer"></a>Azure 儲存體總管

若要檢視或使用 Azure 儲存體總管來管理 Data Lake Storage Gen2 帳戶，您至少必須有版本 `1.6.0` 的工具，這個版本可供[免費下載](https://azure.microsoft.com/features/storage-explorer/)。

請注意，內嵌至 Azure 入口網站的儲存體總管的版本不在目前不支援檢視或管理 Data Lake 儲存體 Gen2 帳戶已啟用階層式命名空間功能。

## <a name="blob-viewing-tool"></a>Blob 檢視工具

Azure 门户中的 Blob 查看工具仅为 Data Lake Storage Gen2 提供有限的支持。

## <a name="third-party-applications"></a>協力廠商應用程式

第三方应用程序可能不支持 Data Lake Storage Gen2。

支援是由每個協力廠商應用程式提供者自行決定是否提供。 目前，Blob 存储 API 和 Data Lake Storage Gen2 API 不可用于管理相同的内容。 由于我们正在努力实现这种互操作性，有可能许多第三方工具将自动支持 Data Lake Storage Gen2。

## <a name="azcopy-support"></a>AzCopy 支援

AzCopy 版本 8 不支持 Data Lake Storage Gen2。

请改用 AzCopy 的最新预览版 ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json))，因为它支持 Data Lake Storage Gen2 终结点。

## <a name="azure-event-grid"></a>Azure Event Grid

[Azure 事件格線](https://azure.microsoft.com/services/event-grid/)不會接收來自 Azure Data Lake Gen2 帳戶的事件，因為那些帳戶還不會產生事件。  

## <a name="soft-delete-and-snapshots"></a>虛刪除和快照集

软删除和快照不适用于 Data Lake Storage Gen2 帐户。

所有的版本控制功能 (包括[快照集](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)和[虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)) 都還無法提供給啟用階層命名空間功能的儲存體帳戶使用。

## <a name="object-level-storage-tiers"></a>物件層級儲存層

物件層級儲存層 (經常性、非經常性和封存) 都還無法提供給 Azure Data Lake Storage Gen 2 帳戶使用，但它們可提供給未啟用階層命名空間功能的儲存體帳戶使用。

## <a name="azure-blob-storage-lifecycle-management-policies"></a>Azure Blob 儲存體生命週期管理原則

Azure Blob 儲存體生命週期管理原則尚未適用於 Data Lake 儲存體 Gen2 帳戶。

這些原則可提供給未啟用階層命名空間功能的儲存體帳戶使用。

## <a name="diagnostic-logs"></a>診斷記錄

诊断日志不适用于 Data Lake Storage Gen2 帐户。
