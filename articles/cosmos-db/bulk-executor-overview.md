---
title: Azure Cosmos DB BulkExecutor 程式庫概觀 | Microsoft Docs
description: 了解 Azure Cosmos DB BulkExecutor 程式庫、使用程式庫的優點和其架構。
keywords: Java 大量執行程式
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: d395376ad6cf191f8f355f6308f27e525da2911f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cosmos-db-bulkexecutor-library-overview"></a>Azure Cosmos DB BulkExecutor 程式庫概觀
 
Azure Cosmos DB 是一種快速且具有彈性的全域分散式資料庫服務，其彈性相應放大的設計可支援： 

* 大型讀取和寫入輸送量 (每秒百萬個作業)。  
* 儲存大量 (數百 TB 或更多) 且可預測延遲毫秒數的交易和作業資料。  

BulkExecutor 程式庫會協助您運用此大量輸送量與儲存體，BulkExecutor 程式庫可讓您透過大量匯入和大量更新 API，在 Azure Cosmos DB 中執行大量作業。 您可以在下列各節中深入了解 BulkExecutor 程式庫的功能。 

> [!NOTE] 
> 目前，Bulkxecutor 程式庫支援匯和更新作業，而且僅有 Azure Cosmos DB SQL API 帳戶可支援此程式庫。 如需有關程式庫的任何更新，請參閱 [.NET](sql-api-sdk-bulk-executor-dot-net.md) 和 [Java](sql-api-sdk-bulk-executor-java.md) 的版本資訊。
 
## <a name="key-features-of-the-bulkexecutor-library"></a>BulkExecutor 程式庫的主要功能  
 
* 它會大幅減少佔滿輸送量 (配置到容器) 的必要用戶端運算資源。 比起平行寫入資料而佔滿用戶端機器 CPU 的多執行緒應用程式，使用大量匯入 API 寫入資料的單一執行緒應用程式，可達到 10 倍多的寫入輸送量。  

* 它以概念來簡單化撰寫應用程式邏輯的沉悶工作，有效率地在程式庫中處理要求節流、要求逾時和其他暫時性例外狀況。  

* 它為執行大量作業的應用程式提供可相應放大的簡化機制。在 Azure VM 上執行的單一 BulkExecutor 執行個體可取用大於 500K RU/s 的輸送量，而且您可以藉由在個別用戶端 VM 上新增其他執行個體，來達到更高的輸送量。  
 
* 它可以使用相應放大架構，在一小時內大量匯入 1 TB 以上的資料。  

* 它能夠以修補程式的形式，大量更新 Azure Cosmos DB 容器中的現有資料。 
 
## <a name="how-does-the-bulk-executor-operate"></a>Bulk Executor 如何運作？ 

當以實體批次觸發匯入或更新文件的大量作業時，這些資料一開始會隨機打散到與 Azure Cosmos DB 分割區索引鍵範圍對應的貯體。 在與分割區索引鍵範圍對應的每個貯體中，資料會細分為小型批次，而每個小型批次都會作為可在伺服器端上認可的承載。 BulkExecutor 程式庫已內建平行執行這些小型批次的最佳化作業，在分割區索引鍵範圍內或或跨分割區索引鍵範圍都適用。 下圖將說明 BulkExecutory 如何將資料批次處理到不同分割區索引鍵：  

![大量執行程式架構](./media/bulk-executor-overview/bulk-executor-architecture.png)

Bulk Executor 程式庫可確保充分利用配置到集合的輸送量。 它會為每個 Azure Cosmos DB 分割區索引鍵範圍使用  [AIMD 樣式的壅塞控制機制](https://tools.ietf.org/html/rfc5681)，以有效率地處理節流和逾時。 

## <a name="next-steps"></a>後續步驟 
  
* 您可以嘗試透過 [.NET](bulk-executor-dot-net.md) 和 [Java](bulk-executor-java.md) 使用應用程式範例來取用 Bulk Executor 程式庫，以對其有進一步了解。  
* 請在 [.NET](sql-api-sdk-bulk-executor-dot-net.md) 和 [Java](sql-api-sdk-bulk-executor-java.md) 中查看 BulkExecutor SDK 資訊和版本資訊。
* Bulk Executor 程式庫已整合至 Cosmos DB Spark 連接器，若要進一步了解，請參閱 [Azure Cosmos DB Spark 連接器](spark-connector.md)一文。  
* BulkExecutor 程式庫也整合至新版 [Azure Cosmos DB 連接器](https://aka.ms/bulkexecutor-adf-v2)，以便 Azure Data Factory 複製資料。
