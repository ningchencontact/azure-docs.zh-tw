---
title: Azure Data Lake Storage Gen2 的已知問題 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 的限制和已知問題
services: storage
author: normesta
manager: twooley
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: normesta
ms.openlocfilehash: fa37c23f936173c19f32b76dffab8908176ebd75
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621685"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知問題

本文包含 Azure Data Lake Storage Gen2 的已知問題和暫時性限制。

## <a name="api-interoperability"></a>API 互通性

Blob 儲存體 API 和 Azure Data Lake Gen2 API 無法彼此互通。

如果您需要使用相同工具來處理上傳至帳戶的所有內容，則請勿在 Blob 儲存體帳戶上啟用階層命名空間，除非這些 API 已變為可彼此互通。 使用不具有階層命名空間的儲存體帳戶，表示您無法存取 Data Lake Storage Gen2 的特定功能，例如目錄和檔案系統存取控制清單。

## <a name="blob-storage-apis"></a>Blob 儲存體 API

Blob 儲存體 API 尚未提供給 Azure Data Lake Storage Gen 2 帳戶使用。

這些 API 已停用以防止可能引發的意外資料存取問題，因為 Blob 儲存體 API 還不能與 Azure Data Lake Gen2 API 互通。

非受控虛擬機器 (VM) 磁碟仰賴這些 API，因此如果您要在儲存體帳戶上啟用階層命名空間，請考慮將非受控 VM 磁碟放入沒有啟用階層命名空間的儲存體帳戶。

## <a name="azure-storage-explorer"></a>Azure 儲存體總管

儲存體總管中的某些功能還無法在 Azure Data Lake Storage Gen2 檔案系統中使用。 這些限制適用於 Azure 儲存體總管[的獨立版本](https://azure.microsoft.com/features/storage-explorer/)與 Azure 入口網站中顯示的版本。

## <a name="blob-viewing-tool"></a>Blob 檢視工具

Azure 入口網站上的 Blob 檢視工具對於 Azure Data Lake Storage Gen2 僅提供有限支援。

## <a name="third-party-applications"></a>協力廠商應用程式

協力廠商應用程式可能不支援 Azure Data Lake Storage Gen2。

支援是由每個協力廠商應用程式提供者自行決定是否提供。 目前，Blob 儲存體 API 和 Azure Data Lake Storage Gen2 API 無法用來管理相同內容。 我們致力於提供該互通性，因此可能許多協力廠商工具將會自動支援 Azure Data Lake Storage Gen2。

## <a name="azcopy-support"></a>AzCopy 支援

AzCopy 第 8 版不支援 Azure Data Lake Storage Gen2。

請改為使用 AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)) 最新的預覽版本，其支援 Azure Data Lake Storage Gen2 端點。

## <a name="oauth-authentication"></a>OAuth 驗證

諸如 Azure Databricks、HDInsight 和 Azure Data Factory 等服務都尚未與 Azure Active Directory (Azure AD) OAuth 持有人權杖驗證整合。

## <a name="access-control-lists-acl"></a>存取控制清單 (ACL)

目錄和檔案層級存取控制清單 (ACL) 難以進行管理。 沒有任何可用來取得和設定那些存取控制清單的 UI 型工具。

## <a name="azure-event-grid"></a>Azure Event Grid

[Azure 事件格線](https://azure.microsoft.com/services/event-grid/)不會接收來自 Azure Data Lake Gen2 帳戶的事件，因為那些帳戶還不會產生事件。  

## <a name="role-based-access-control"></a>角色型存取控制

您無法將角色型存取控制套用至 Azure Data Lake Storage Gen2 帳戶中的檔案系統物件。

## <a name="sql-data-warehouse-polybase"></a>SQL 資料倉儲 PolyBase

當 Azure 儲存體帳戶上啟用儲存體防火牆時，SQL 資料倉儲 [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017) 無法存取那些帳戶。

## <a name="soft-delete-and-snapshots"></a>虛刪除和快照集

虛刪除和快照集不適用於Azure Data Lake Storage Gen2 帳戶。

所有的版本控制功能 (包括[快照集](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)和[虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)) 都還無法提供給啟用階層命名空間的儲存體帳戶使用。

## <a name="object-level-storage-tiers"></a>物件層級儲存層

物件層級儲存層 (經常性、非經常性和封存) 都還無法提供給 Azure Data Lake Storage Gen 2 帳戶使用，但它們可提供給未啟用階層空間的儲存體帳戶使用。

## <a name="azure-blob-storage-lifecycle-management-preview-policies"></a>Azure Blob 儲存體生命週期管理 (預覽) 原則

Azure Blob 儲存體生命週期管理 (預覽) 原則還無法提供給 Azure Data Lake Storage Gen2 帳戶使用。

這些原則可提供給未啟用階層空間的儲存體帳戶使用。

## <a name="diagnostic-logs"></a>診斷記錄檔

診斷記錄不適用於 Azure Data Lake Storage Gen2 帳戶。

若要要求診斷記錄，請連絡 Azure 支援。 提供您的帳戶名稱，以及所需之記錄的期間。
