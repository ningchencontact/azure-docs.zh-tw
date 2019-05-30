---
title: 包含檔案
description: 包含檔案
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 1/10/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 6d06ac6efd08c14f77fd963ddf2c67de54260959
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238526"
---
Azure Data Factory 是多租用戶的服務，以確保客戶訂用帳戶受到保護免於彼此的工作負載中有下列的預設限制。 引發最多可以為您的訂用帳戶的限制，請連絡支援人員。

### <a name="version-2"></a>第 2 版

| Resource | 預設限制 | 上限 |
| -------- | ------------- | ------------- |
| Azure 訂用帳戶中的資料處理站 | 50 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 實體，例如管線、 資料集、 觸發程序、 連結的服務和整合執行階段，為 data factory 中的總次數 | 5,000 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 總 CPU 核心 Azure SSIS 整合執行階段的一個訂用帳戶 | 256 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個處理站中的所有管線之間共用的 data factory 的並行管線執行回合 | 10,000  | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個管線，其中包含適用於容器的內部活動的最大活動 | 40 | 40 |
| 您可以針對單一的自我裝載的整合執行階段建立的連結的整合執行階段的最大數目 | 100 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個管線的最大值參數 | 50 | 50 |
| ForEach 項目 | 100,000 | 100,000 |
| ForEach 平行處理原則 | 20 | 50 |
| 每個運算式的字元數 | 8,192 | 8,192 |
| 最小的輪轉視窗觸發程序間隔 | 15 分鐘 | 15 分鐘 |
| 執行管線活動的逾時上限 | 7 天 | 7 天 |
| 管線物件的每個物件位元組<sup>1</sup> | 200 KB | 200 KB |
| 每個資料集的物件和連結的服務物件的位元組<sup>1</sup> | 100 KB | 2,000 KB |
| 每個複製活動執行的資料整合單位<sup>3</sup> | 256 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 寫入 API 呼叫 | 2,500/小時<br/><br/> 這是由 Azure Resource Manager 所加諸的限制，而不是 Azure Data Factory。 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 讀取 API 呼叫 | 12,500/小時<br/><br/> 這是由 Azure Resource Manager 所加諸的限制，而不是 Azure Data Factory。 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 監視每分鐘查詢次數 | 1,000 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每分鐘的實體 CRUD 作業 | 50 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |


### <a name="version-1"></a>第 1 版

| **Resource** | **預設限制** | **上限** |
| --- | --- | --- |
| Azure 訂用帳戶中的資料處理站 |50 |[連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 資料處理站內的管線 |2,500 |[連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 為 data factory 中的資料集 |5,000 |[連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個資料集的並行配量 |10 |10 |
| 管線物件的每個物件位元組<sup>1</sup> |200 KB |200 KB |
| 位元組資料的每個物件集和連結服務物件<sup>1</sup> |100 KB |2,000 KB |
| 在訂用帳戶的 azure HDInsight 隨選叢集核心<sup>2</sup> |60 |[連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 雲端資料移動單位，每個複製活動執行<sup>3</sup> |32 |[連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 管線活動執行的重試計數 |1,000 |MaxInt (32 位元) |

<sup>1</sup>管線、 資料集和連結的服務物件代表您的工作負載的邏輯群組。 使用 Azure Data Factory 的資料來移動或處理量不與這些物件的限制。 Data Factory 可調整以處理數 pb 的資料。

<sup>2</sup>隨選 HDInsight 核心並非配置在包含資料處理站的訂用帳戶之中。 如此一來，先前的限制是在隨選 HDInsight 核心的 Data Factory 強制執行的核心限制。 它與不同 Azure 訂用帳戶相關聯的核心限制。

<sup>3</sup>雲端到雲端複製作業中使用的資料整合單位 (DIU)，針對第 2 版或第 1 版的雲端資料移動單位 (DMU)。 它是量值，代表在 Data Factory 中單一單位的能力。 它結合了 CPU、 記憶體和網路資源配置。 在某些情況下，使用更多 DMU 可以達到更高的複製輸送量。 如需詳細資訊，請參閱 < [（第 2 版） 的資料整合單位](../articles/data-factory/copy-activity-performance.md#data-integration-units)並[雲端資料移動單位 （第 1 版）](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units)。 如需計費的詳細資訊，請參閱[Azure Data Factory 定價](https://azure.microsoft.com/pricing/details/data-factory/)。

<sup>4</sup>整合執行階段 (IR) 是 Azure Data Factory 用來提供跨不同網路環境中，資料移動，將活動分派給計算服務，例如資料整合功能的計算基礎結構和執行 SSIS 套件。 如需詳細資訊，請參閱 <<c0> [ 整合執行階段概觀](../articles/data-factory/concepts-integration-runtime.md)。

| **Resource** | **預設下限** | **下限** |
| --- | --- | --- |
| 排程間隔 |15 分鐘 |15 分鐘 |
| 重試嘗試間的間隔 |1 秒 |1 秒 |
| 重試逾時值 |1 秒 |1 秒 |

#### <a name="web-service-call-limits"></a>Web 服務呼叫限制
Azure Resource Manager 有 API 呼叫限制。 您可使用 [Azure 資源管理員 API 限制](../articles/azure-subscription-service-limits.md#resource-group-limits)內的速率進行 API 呼叫。
