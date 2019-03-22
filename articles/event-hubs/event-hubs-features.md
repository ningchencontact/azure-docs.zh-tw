---
title: 功能概觀 - Azure 事件中樞 | Microsoft Docs
description: 本文將詳細說明 Azure 事件中樞的相關功能與術語。
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: e7f292db06d4da9206aabd14a68e6acde867f92d
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336995"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Azure 事件中樞的功能與術語

事件中樞是可調整的事件處理服務，它會擷取和處理大量的事件和資料，具有低延遲和高可靠性。 如需高階概觀，請參閱[何謂事件中樞？](event-hubs-what-is-event-hubs.md)

這篇文章是根據[概觀](event-hubs-what-is-event-hubs.md)中的資訊建置，並且提供有關事件中樞元件和功能的技術和實作詳細資料。

## <a name="namespace"></a>命名空間
事件中樞命名空間提供唯一的範圍容器 (依其[完整網域名稱](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)來參考)，您可以在其中建立一或多個事件中樞或 Kafka 主題。 

## <a name="event-hubs-for-apache-kafka"></a>適用於 Apache Kafka 的事件中樞

[這項功能](event-hubs-for-kafka-ecosystem-overview.md)提供可讓客戶使用 Kafka 通訊協定來與事件中樞通訊的端點。 此整合可為客戶提供 Kafka 端點。 這讓客戶能夠設定其現有的 Kafka 應用程式來與事件中樞通訊，提供替代方案來執行他們自己的 Kafka 叢集。 適用於 Apache Kafka 的事件中樞支援 Kafka 通訊協定 1.0 和更新版本。 

透過這項整合，您不需要執行 Kafka 叢集，或使用 Zookeeper 加以管理。 這也可讓您搭配事件中樞內需求最高的部分功能來運作，例如擷取、自動擴充和異地災害復原。

此整合也可讓應用程式 (例如 Mirror Maker) 或架構 (例如 Kafka Connect) 只搭配設定變更以無叢集的方式運作。 

## <a name="event-publishers"></a>事件發佈者

任何將資料傳送至事件中樞的實體就稱為「事件產生者」或「事件發佈者」。 事件發佈者可以使用 HTTPS、AMQP 1.0 或 Kafka 1.0 版和更新版本來發佈事件。 事件發佈者使用共用存取簽章 (SAS) 權杖向事件中樞辨識自己的身分，而且可以擁有唯一的身分識別，或使用一般 SAS 權杖。

### <a name="publishing-an-event"></a>發佈事件

您可以透過 AMQP 1.0、Kafka 1.0 (和更新版本) 或 HTTPS 來發佈事件。 事件中樞提供[用戶端程式庫和類別](event-hubs-dotnet-framework-api-overview.md)，以供您從 .NET 用戶端將事件發佈到事件中樞。 對於其他執行階段和平台，您可以使用任何 AMQP 1.0 用戶端 (如 [Apache Qpid](https://qpid.apache.org/))。 您可以單獨發佈事件，或以批次方式進行。 不論是單一事件或批次，單次發佈 (事件資料執行個體) 的上限為 1 MB。 發佈大於此臨界值的事件會導致錯誤。 發佈者最好別顧慮事件中樞內的資料分割，他們只需要指定 *資料分割索引鍵* (下一節將加以介紹)，或透過其 SAS 權杖指定身分識別即可。

使用 AMQP 或 HTTPS 的選擇因使用案例而異。 AMQP 除了需要使用传输级别安全 (TLS) 或 SSL/TLS 以外，还需要建立持久的双向套接字。 AMQP 初始化工作階段時的網路成本較高，但 HTTPS 需要額外的 SSL 工作負荷來處理每個要求。 對於頻繁的發行者，AMQP 的效能較高。

![事件中樞](./media/event-hubs-features/partition_keys.png)

事件中樞能確保所有共用資料分割索引鍵值的事件依序傳遞至同一個資料分割。 如果資料分割索引鍵與發佈者原則搭配使用，發佈者的身分識別與資料分割索引鍵的值必須相符， 否則便會發生錯誤。

### <a name="publisher-policy"></a>发布者策略

事件中樞可讓您透過發佈者 原則更精確地控制事件發佈者。 发布者策略是运行时功能，旨在为大量的独立事件发布者提供方便。 有了發佈者原則，當每位發佈者使用下列機制將事件發佈到事件中樞時，都能使用自己的唯一識別碼：

```http
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

您不需要事先建立發佈者名稱，不過這些名稱必須符合發佈事件時使用的 SAS 權杖，以確保發佈者身分識別是獨立的。 在使用發佈者原則時，系統會將 **PartitionKey** 值設定為發佈者名稱。 為了正常運作，這些值必須相符。

## <a name="capture"></a>擷取

[事件中樞擷取](event-hubs-capture-overview.md)可讓您自動擷取「事件中樞」中的串流資料，然後將它儲存到您選擇的 Blob 儲存體帳戶，或是 Azure Data Lake 服務帳戶。 您可以從 Azure 入口網站啟用「擷取」，然後指定執行擷取的大小下限和時間範圍。 使用事件中樞擷取，您就可以指定自己的 Azure Blob 儲存體帳戶和容器或是 Azure Data Lake 服務帳戶，使用其中之一來儲存擷取的資料。 擷取的資料會以 Apache Avro 格式撰寫。

## <a name="partitions"></a>分区

事件中樞透過分割取用者模式來提供訊息串流，每位取用者只會讀取訊息串流的特定子集 (即資料分割)。 此模式能水平擴充事件處理規模，並提供佇列和主題缺少的其他串流導向功能。

資料分割是經過排序且保存在事件中樞內的事件序列。 當較新的事件送達時，系統會將它們加入序列的結尾。 您可以將資料分割視為一種「認可記錄」。

![事件中樞](./media/event-hubs-features/partition.png)

事件中樞會將資料保留一段設定的保留時間，這段時間在事件中樞的所有資料分割上都一樣。 事件會隨著時間經過而到期，因此您無法明確地予以刪除。 資料分割各自獨立，只包含其本身的資料序列，通常會以不同的速度成長。

![事件中樞](./media/event-hubs-features/multiple_partitions.png)

分区数在创建时指定，必须介于 2 到 32 之间。 資料分割計數不可變更，您在設定資料分割計數時，應該考慮長期的規模。 分区是一种数据组织机制，与使用方应用程序中所需的下游并行度相关。 事件中樞內的資料分割數目，與您預期有的並行讀取器數目直接相關。 您可以連絡事件中樞小組，將資料分割數目提高到 32 個以上。

雖然可以識別並直接傳送至資料分割，但是不建議直接傳送至資料分割。 相反地，您可以使用較高層級的建構中引進[事件發行者](#event-publishers)和容量的區段。 

資料分割中會填入一連串事件資料，內含事件本文、使用者定義屬性包，以及中繼資料，例如在資料分割中的位移和串流序列中的編號。

如需資料分割和取捨可用性和可靠性的詳細資訊，請參閱[事件中樞程式設計指南](event-hubs-programming-guide.md#partition-key)和[事件中樞的可用性和一致性](event-hubs-availability-and-consistency.md)文章。

### <a name="partition-key"></a>資料分割索引鍵

您可以使用[資料分割索引鍵](event-hubs-programming-guide.md#partition-key)，將連入事件資料對應至特定的資料分割來組織資料。 資料分割索引鍵是由傳送者提供的值。系統會將它傳遞到事件中樞， 然後透過靜態雜湊函式加以處理，而建立資料分割指派。 如果在发布事件时未指定分区键，则会使用循环分配。

事件發佈行者只會知道資料分割索引鍵，不會知道事件發佈的目的地資料分割。 索引鍵和資料分割脫鉤的這種機制，讓傳送者不需要知道太多有關下游處理的細節。 每部裝置或每位使用者的唯一身分識別能成為有效的資料分割索引鍵，不過像地理位置之類的其他屬性也能用來將相關事件歸納成一個資料分割。

## <a name="sas-tokens"></a>SAS 權杖

事件中樞使用命名空間和事件中樞層級可用的「共用存取簽章」。 SAS 權杖可自 SAS 金鑰產生，它是經過特定格式編碼的 URL SHA 雜湊。 事件中樞可以使用金鑰 (原則) 的名稱和權杖來重新產生雜湊，藉此驗證傳送者。 一般而言，建立的事件發佈者 SAS 權杖只具備特定事件中樞的**傳送**權限。 此 SAS 權杖 URL 機制是導入發佈者原則之發佈者識別的基礎。 如需使用 SAS 的詳細資訊，請參閱[使用服務匯流排的共用存取簽章驗證](../service-bus-messaging/service-bus-sas.md)。

## <a name="event-consumers"></a>事件使用者

任何從事件中樞讀取事件資料的實體就稱為「事件取用者」。 所有事件中樞取用者都透過 AMQP 1.0 工作階段連接，而可供取用的事件都透過工作階段傳遞。 用戶端不需要輪詢資料可用性。

### <a name="consumer-groups"></a>用戶群組

事件中樞的發佈/訂閱機制可透過「取用者群組」啟用。 取用者群組是檢視整個事件中樞 (狀態、位置或位移) 的窗口。 取用者群組能讓多個取用應用程式擁有自己的事件串流檢視，以及按照自己的步調及運用自己的位移自行讀取串流。

在串流處理架構中，每個下游應用程式等同於一個取用者群組。 如果您想要將事件資料寫入長期存放區，該存放裝置寫入器應用程式即是取用者群組。 複雜的事件處理可以由另一個獨立的取用者群組來執行。 您只能透過取用者群組來存取資料分割。 每個事件中樞永遠有一個預設的取用者群組，而您可以為標準層事件中樞建立最多 20 個取用者群組。

每一取用者群組的一個分割區上最多可以有 5 個並行讀取器；不過，**建議在每一取用者群組的一個分割區上只有一個作用中的接收器**。 在單一分割區內，每個讀取器都會接收所有訊息。 如果相同分割區上有多個讀取器，則您會需要處理重複的訊息。 您需要在程式碼中處理此狀況，但這可能不容易。 不過，在某些情況下這是有效的方法。


以下是取用者群組 URI 慣例的範例：

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

下圖顯示事件中樞串流處理架構︰

![事件中樞](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>串流位移

「位移」是事件在資料分割內的位置。 您可以將位移視為用戶端指標。 位移是事件的位元組編號。 此位移可讓事件取用者 (讀取器) 在事件串流中指定某個點，以便從該點開始讀取事件。 您可以指定時間戳記或位移值等形式的位移。 使用者负责在事件中心服务的外部存储其自身的偏移量值。 在資料分割內，每個事件都含有一個位移。

![事件中樞](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>檢查點

*設立檢查點* 是讀取器在資料分割事件序列中標記或認可其位置的程序。 检查点操作由使用者负责，并在使用者组中的每个分区上进行。 此責任表示在每個取用者群組內，每個資料分割讀取器必須追蹤自己目前在事件串流中的位置，而當它們認為資料串流完成時，可以通知服務。

如果讀取器與資料分割中斷連線，當它重新連線時，會從該取用者群組中該資料分割最後一個讀取器先前提交的檢查點開始讀取。 當讀取器連線時，它會將此位移傳遞給事件中樞，以指定要開始讀取的位置。 如此一來，檢查點能成為下游應用程式標記事件「完成」的方法，也能針對在不同機器上執行的讀取器提供容錯移轉發生時的恢復功能。 從這個檢查點處理程序中指定較低的位移，即可回到較舊的資料。 透過這項機制，檢查點可提供容錯移轉彈性，並支援事件串流重播。

### <a name="common-consumer-tasks"></a>常見的取用者工作

所有事件中樞取用者都會透過 AMQP 1.0 工作階段 (狀態感知的雙向通訊通道) 來連線。 每個資料分割都有 AMQP 1.0 工作階段，有助於傳輸資料分割所隔離的事件。

#### <a name="connect-to-a-partition"></a>連接資料分割

連線至資料分割時，常見的做法是使用租用機制來協調讀取器至特定資料分割的連線。 这样，便可以做到一个使用者组中每分区只有一个活动的读取者。 使用 .NET 用戶端的 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 類別可以簡化檢查點檢查、租用和管理讀取器。 事件處理器主機是智慧型取用者代理程式。

#### <a name="read-events"></a>讀取事件

在開啟特定資料分割的 AMQP 1.0 工作階段和連結後，事件中樞服務會將事件傳遞至 AMQP 1.0 用戶端。 相較於以提取為基礎的機制 (如 HTTP GET)，這個傳遞機制能產生更高的輸送量和較低的延遲。 將事件傳送到用戶端時，每個事件資料執行個體都包含如位移和序號等有助於在事件序列上設立檢查點的重要中繼資料。

事件資料︰
* Offset
* 序號
* 正文
* 使用者屬性
* 系统属性

您必須負責管理位移。

## <a name="scaling-with-event-hubs"></a>使用事件中樞自動調整規模

有兩個因素影響事件中樞使用自動調整規模。
*   輸送量單位
*   分割數

### <a name="throughput-units"></a>輸送量單位

事件中樞的輸送量容量受「輸送量單位」所控制。 輸送量單位是預先購買的容量單位。 單一輸送量，可讓您：

* 輸入：每秒最多 1 MB 或 1000 個事件 (以先達到者為準)。
* 輸出：最高每秒 2 MB 或每秒 4096 個事件。

超出所購買輸送量單位的容量時，輸入就開始節流，並傳回 [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception)。 輸出不會產生節流例外狀況，但仍受限於所購買輸送量單位的容量。 如果您收到發佈速率例外狀況，或輸出速率低於預期，請務必檢查您為命名空間所購買的輸送量單位數目。 在 [Azure 入口網站](https://portal.azure.com)中，您可以在命名空間的 [調整規模] 刀鋒視窗管理輸送量單位。 您也可以使用[事件中樞 API](event-hubs-api-overview.md) 以程式設計方式管理輸送量單位。

輸送量單位是預先購買制且以每小時計費。 一經購買，您至少必須支付一個小時的輸送量單位費用。 最多可以為一個事件中樞命名空間購買 20 個輸送量單位，讓該命名空間中的所有事件中樞共用。

### <a name="partitions"></a>分割數

資料分割可讓您向下游處理。 由於事件中樞資料分割提供資料分割取用者模型，您可以向外延展時同時處理您的事件。 事件中樞可以有最多 32 個分割區。

我們建議您平衡 1:1 輸送量單位和分割區，以達到最佳規模。 單一分割區具有保證的輸入和輸出的最多一個輸送量單位。 雖然您可以達到更高輸送量的磁碟分割上，不保證效能。 這就是為什麼我們強烈建議在事件中樞的資料分割數目會大於或等於輸送量單位數目。

假設您計劃需要的總輸送量，您知道您所需要的輸送量單位數目和最小數目的資料分割，但您多少個分割區應該有嗎？ 選擇您想要達成的下游平行處理原則為基礎的資料分割數目，以及您未來的輸送量需求。 沒有會收取您擁有事件中樞內的資料分割數目。

如需詳細的事件中樞價格資訊，請參閱[事件中樞價格](https://azure.microsoft.com/pricing/details/event-hubs/)。

## <a name="next-steps"></a>後續步驟

有关事件中心的详细信息，请访问以下链接：

* 開始使用[事件中樞教學課程][Event Hubs tutorial]
* [事件中樞程式設計指南](event-hubs-programming-guide.md)
* [事件中樞的可用性和一致性](event-hubs-availability-and-consistency.md)
* [事件中樞常見問題集](event-hubs-faq.md)
* [事件中樞範例][]

[Event Hubs tutorial]: event-hubs-dotnet-standard-getstarted-send.md
[事件中樞範例]: https://github.com/Azure/azure-event-hubs/tree/master/samples
