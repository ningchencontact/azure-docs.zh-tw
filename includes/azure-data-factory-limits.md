---
title: 包含檔案
description: 包含檔案
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 5/30/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: ac0747dcff4e74363a58dc9aaf6da4dbd4c6a1c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427603"
---
Azure Data Factory 是多租用戶的服務，以確保客戶訂用帳戶受到保護免於彼此的工作負載中有下列的預設限制。 引發最多可以為您的訂用帳戶的限制，請連絡支援人員。

### <a name="version-2"></a>第 2 版

| Resource | 預設限制 | 上限 |
| -------- | ------------- | ------------- |
| Azure 訂用帳戶中的資料處理站 | 50 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 實體，例如管線、 資料集、 觸發程序、 連結的服務和整合執行階段，為 data factory 中的總次數 | 5,000 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 總 CPU 核心 Azure SSIS 整合執行階段的一個訂用帳戶 | 256 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個處理站中的所有管線之間共用的 data factory 的並行管線執行回合 | 10,000  | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 外部的並行活動執行每個訂用帳戶每[Azure 整合執行階段區域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>外部活動會在整合執行階段管理，但在連結的服務，包括 Databricks、 預存程序、 HDInsights，和其他項目上執行。</small> | 3000 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 並行的管線活動執行每個訂用帳戶每[Azure 整合執行階段區域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>管線活動執行整合執行階段，包括 Lookup、 GetMetadata，並刪除。 </small>| 1000 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 撰寫每訂用帳戶每個作業的並行[Azure 整合執行階段區域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>測試連接、 瀏覽資料夾清單和資料表清單，包括預覽資料。 | 200 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 並行資料整合單位<sup>1</sup>每個訂用帳戶每個耗用量[Azure 整合執行階段區域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| 區域群組 1<sup>2</sup>:6000<br>區域群組 2<sup>2</sup>:3000<br>區域群組 3<sup>2</sup>:1500 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個管線，其中包含適用於容器的內部活動的最大活動 | 40 | 40 |
| 您可以針對單一的自我裝載的整合執行階段建立的連結的整合執行階段的最大數目 | 100 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每個管線的最大值參數 | 50 | 50 |
| ForEach 項目 | 100,000 | 100,000 |
| ForEach 平行處理原則 | 20 | 50 |
| 每個運算式的字元數 | 8,192 | 8,192 |
| 最小的輪轉視窗觸發程序間隔 | 15 分鐘 | 15 分鐘 |
| 執行管線活動的逾時上限 | 7 天 | 7 天 |
| 管線物件的每個物件位元組<sup>3</sup> | 200 KB | 200 KB |
| 每個資料集的物件和連結的服務物件的位元組<sup>3</sup> | 100 KB | 2,000 KB |
| 資料整合單位<sup>1</sup>每次複製活動執行 | 256 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 寫入 API 呼叫 | 2,500/小時<br/><br/> 這是由 Azure Resource Manager 所加諸的限制，而不是 Azure Data Factory。 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 讀取 API 呼叫 | 12,500/小時<br/><br/> 這是由 Azure Resource Manager 所加諸的限制，而不是 Azure Data Factory。 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 監視每分鐘查詢次數 | 1,000 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每分鐘的實體 CRUD 作業 | 50 | [連絡客戶支援](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |

<sup>1</sup>雲端到雲端複製作業中使用的資料整合單位 (DIU)，進一步了解[（第 2 版） 的資料整合單位](../articles/data-factory/copy-activity-performance.md#data-integration-units)。 如需計費的詳細資訊，請參閱[Azure Data Factory 定價](https://azure.microsoft.com/pricing/details/data-factory/)。

<sup>2</sup> [azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime)是[全球](https://azure.microsoft.com/global-infrastructure/services/)以確保資料合規性、 效率和降低的網路輸出成本。 

| 區域群組 | Regions | 
| -------- | ------ |
| 區域群組 1 | 美國中部、 美國東部、 美國東部 2、 北歐、 西歐、 美國西部、 美國西部 2 |
| 區域群組 2 | 澳洲東部、 澳洲東南部、 巴西南部、 印度中部、 日本東部、 美國 Northcentral、 美國中南部、 東南亞、 美國西部 |
| 區域群組 3 | 加拿大中部、 東亞、 法國中部、 韓國中部、 英國南部 |

<sup>3</sup>管線、 資料集和連結的服務物件代表您的工作負載的邏輯群組。 使用 Azure Data Factory 的資料來移動或處理量不與這些物件的限制。 Data Factory 可調整以處理數 pb 的資料。

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

<sup>2</sup> 隨選 HDInsight 核心並非配置在包含資料處理站的訂用帳戶之中。 如此一來，先前的限制是在隨選 HDInsight 核心的 Data Factory 強制執行的核心限制。 它與不同 Azure 訂用帳戶相關聯的核心限制。

<sup>3</sup>雲端資料移動單位 (DMU) 第 1 版會使用在雲端到雲端複製作業中，進一步了解[雲端資料移動單位 （第 1 版）](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units)。 如需計費的詳細資訊，請參閱[Azure Data Factory 定價](https://azure.microsoft.com/pricing/details/data-factory/)。

| **Resource** | **預設下限** | **下限** |
| --- | --- | --- |
| 排程間隔 |15 分鐘 |15 分鐘 |
| 重試嘗試間的間隔 |1 秒 |1 秒 |
| 重試逾時值 |1 秒 |1 秒 |

#### <a name="web-service-call-limits"></a>Web 服務呼叫限制
Azure Resource Manager 有 API 呼叫限制。 您可使用 [Azure 資源管理員 API 限制](../articles/azure-subscription-service-limits.md#resource-group-limits)內的速率進行 API 呼叫。
