---
title: Azure Cosmos DB 中使用 Apache Spark 的內建作業分析簡介
description: 了解如何在 Azure Cosmos DB 中使用 Apache Spark 內建支援執行作業分析與 AI
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/06/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 504f463f55edf1a22f99e1ba02449121e8bf5efc
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472267"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark-preview"></a>Azure Cosmos DB 中使用 Apache Spark (預覽) 的內建作業分析 

Azure Cosmos DB 中的 Apache Spark 內建支援可讓您從 Apache Spark 對 Azure Cosmos 帳戶中儲存的資料執行分析。 它會提供 Apache Spark 作業的原生支援，以直接在散佈全球的 Cosmos 資料庫上執行。 有了這些功能，開發人員、資料工程師和資料科學家可以使用 Azure Cosmos DB 作為有彈性、可擴充和高效能的資料平台，以執行 **OLTP 和 OLAP/HTAP** 工作負載。 

> [!NOTE]
> Azure Cosmos DB 中的 Apache Spark 內建支援目前以有限預覽形式提供。 若要註冊預覽版，請巡覽至[註冊預覽版](https://portal.azure.com/?feature.customportal=false#create/Microsoft.DocumentDB)頁面。 

Azure Cosmos DB 中的 Apache Spark 支援提供下列優點：

* 您可以最快的速度深入了解分散各地的使用者和資料。

* 您可以簡化解決方案架構及降低[擁有權總成本](total-cost-ownership.md) (TCO)。 系統會有最少量的資料處理元件，並且避免其間有任何不必要的資料移動。

* 建立[安全性](secure-access-to-data.md)、[合規性](compliance.md)和稽核界限，其中包含所有受管理的資料。

* 提供由嚴苛 SLA 所支援的「持續上線」或[高可用性](high-availability.md)使用者分析。

![Azure Cosmos DB 中的 Apache Spark 支援視覺效果](./media/spark-api-introduction/spark-api-visualization.png)
 
您可以使用 Azure Cosmos DB 中的 Apache Spark 支援建置及部署解決方案，例如 AI 和深度學習模型、預測性分析、建議、IoT、Customer 360、詐騙偵測、文字情感、點擊流分析。 這些解決方案直接對您的 Azure Cosmos DB 資料運作。

您可以在 Azure Cosmos DB 中設定批次和串流 ETL 作業，而不需離開資料庫服務，或新增其他計算服務。 當您需要執行 ETL 作業時，您可以彈性擴充計算環境，並在作業完成時將計算環境相應縮小。

Azure Cosmos DB 中的 Apache Spark 支援在 Apache Spark 執行階段提供內建 Machine Learning 支援。 執行階段包括 Spark MLLib、適用於 Spark 的 Microsoft Machine Learning、Azure Machine Learning 和認知服務。 透過這些功能，資料科學家、資料工程師和資料分析師可以直接在 Azure Cosmos DB 內建置和實作機器學習模型，而此花費更少時間和更低成本。


## <a name="key-benefits"></a>主要權益

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>散佈全球、低延遲的作業分析和 AI

在散佈全球的 Azure Cosmos 資料庫上使用 Apache Spark，您現在可以快速洞察全世界。 Azure Cosmos DB 能夠利用三個關鍵技巧進行**散佈全球、低延遲的作業分析** (彈性規模)：

* 由於您的 Azure Cosmos 資料庫散佈全球，因此所有資料會都在資料生產者 (例如使用者) 所在位置的當地擷取。 不論資料的生產者和取用者位於世界何處，都會針對最接近他們的本機複本提供查詢服務。 

* 您所有的分析查詢都會直接在資料分割區內儲存的已編制索引資料上執行，而不需要進行任何不必要的資料移動。 

* Spark 與 Azure Cosmos DB 共置，因此發生較少的中繼轉譯和資料移動，以致效能和延展性更佳。

### <a name="unified-serverless-experience-for-apache-spark"></a>統一的 Apache Spark 無伺服器體驗

Azure Cosmos DB 為多模型資料庫，現在透過索引鍵值、文件、圖形、資料行系列資料模型提供**統一的 Apache Spark 無伺服器體驗**，進而擴充其 OSS API 支援。 使用 MongoDB、Cassandra、Gremlin、Etcd 和 SQL API 支援不同的資料模型 - 全都在相同的基礎資料上運作。 

使用 Azure Cosmos DB 中的 Apache Spark 支援時，您可以原生方式支援以 Scala、Python、Java 撰寫的應用程式，以及使用數個適用於 SQL 且緊密整合的程式庫。 這些程式庫包括 ([Spark SQL](https://spark.apache.org/sql/))、機器學習 (Spark [MLlib](https://spark.apache.org/mllib/))、串流處理 ([Spark 結構化串流](https://spark.apache.org/streaming/))，以及圖形處理 (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html))。 這些工具可讓您更輕鬆地將 Apache Spark 運用於各種使用案例。 您不必處理 Spark 或 Spark 叢集的管理。 您可以使用熟悉的 Apache Spark API 和 **Jupyter Notebook** 進行分析，以及使用 SQL API 或任何 OSS NoSQL API (如 Cassandra ) 同時對相同的基礎資料進行交易處理。

### <a name="no-schema-or-index-management"></a>不需要任何結構描述或索引管理

不同於傳統分析資料庫，使用 Azure Cosmos DB 時，資料工程師和資料科學家不再需要處理麻煩的結構描述和索引管理。 Azure Cosmos DB 中的資料庫引擎不需要任何明確的結構描述或索引管理，而且能夠為其擷取的所有資料自動編製索引，以便快速處理 Apache Spark 查詢。 

### <a name="consistency-choices"></a>一致性選擇

由於 Apache Spark 作業是在 Azure Cosmos 資料庫的資料分割區中執行，所以查詢會取得[五個定義完善的一致性選項](consistency-levels.md)。 這些一致性模型讓您有選擇嚴格一致性的彈性，以提供最精確的機器學習演算法結果，而不會危害延遲和高可用性。 

### <a name="slas"></a>SLA

Apache Spark 作業會有 Azure Cosmos DB 優點，例如領先業界的全方位 [SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99.999)，不會有管理個別 Apache Spark 叢集的額外負荷。 這些 SLA 涵蓋輸送量、第 99 百分位的延遲、一致性和高可用性。 

### <a name="mixed-workloads"></a>混合的工作負載

將 Apache Spark 整合到 Azure Cosmos DB 中，可跨越交易與分析區隔，當客戶建置全球規模的雲端原生應用程式時，此種區隔已成為主要的痛苦點之一。 

## <a name="next-steps"></a>後續步驟

* 若要深入了解 Azure Cosmos DB 的優點，請參閱[概觀](introduction.md)一文。
* [開始使用適用於 MongoDB 的 Azure Cosmos DB API](mongodb-introduction.md)
* [開始使用 Azure Cosmos DB Cassandra API](cassandra-introduction.md)
* [開始使用 Azure Cosmos DB Gremlin API](graph-introduction.md)
* [開始使用 Azure Cosmos DB 資料表 API](table-introduction.md)




