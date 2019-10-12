---
title: 將事件中樞的事件來源新增至 Azure 時間序列深入解析 | Microsoft Docs
description: 此文章說明如何將與 Azure 事件中樞連線的事件來源新增至時間序列深入解析環境。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/09/2019
ms.custom: seodec18
ms.openlocfilehash: aaddfb19889e31bb8e0d52d1df2d6b034b6e7f6b
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274301"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>新增事件中樞事件來源到您的時間序列深入解析環境

此文章說明如何使用 Azure 入口網站，將從 Azure 事件中樞讀取資料的事件來源新增至您的 Azure 時間序列深入解析環境。

> [!NOTE]
> 本文中所述的步驟適用于時間序列深入解析 GA 和時間序列深入解析預覽環境。

## <a name="prerequisites"></a>必要條件

- 建立時間序列深入解析環境，如[建立 Azure 時間序列深入解析環境](./time-series-insights-update-create-environment.md)中所述。
- 建立事件中樞。 請參閱[使用 Azure 入口網站建立事件中樞命名空間和事件中樞](../event-hubs/event-hubs-create.md)。
- 事件中樞必須要有傳入的作用中訊息事件。 瞭解如何[使用 .NET Framework 將事件傳送至 Azure 事件中樞](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md)。
- 在事件中樞建立專用取用者群組，讓時間序列深入解析環境從中取用。 每個時間序列深入解析事件來源都必須有自身專屬的取用者群組，且不可與任何其他取用者共用。 如果多個讀取器從同一個取用者群組取用事件，則所有讀取器都可能會看到錯誤。 另外也限制每一個事件中樞最多有 20 個取用者群組。 如需詳細資訊，請參閱 [事件中樞程式設計指南](../event-hubs/event-hubs-programming-guide.md)。

### <a name="add-a-consumer-group-to-your-event-hub"></a>將取用者群組新增至事件中樞

應用程式會使用取用者群組從 Azure 事件中樞提取資料。 若要可靠地從事件中樞讀取資料，請提供僅供此時間序列深入解析環境使用的專用取用者群組。

將新的取用者群組新增至事件中樞：

1. 在[Azure 入口網站](https://portal.azure.com)中，從事件中樞命名空間找出並開啟您的事件中樞。

    [![Open 您的事件中樞命名空間](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png#lightbox)

1. 在 [實體] 下，選取 [取用者群組]，然後選取 [取用者群組]。

   [![Event 中樞-新增取用者群組](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png#lightbox)

1. 在 [取用者群組] 頁面上，在 [名稱] 中輸入新的唯一值。  在時間序列深入解析環境中建立新的事件來源時，使用此相同名稱。

1. 選取 [建立]。

## <a name="add-a-new-event-source"></a>新增事件來源

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 找出您的現有時間序列深入解析環境。 在左側功能表中，選取 [所有資源]，然後選取您的時間序列深入解析環境。

1. 在 [環境拓撲] 下，選取 [事件來源],，然後選取 [新增]。

   [![Under 事件來源中，選取 [新增] 按鈕](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

1. 在 [事件來源名稱] 中輸入對此時間序列深入解析環境而言為唯一的值，例如 **event-stream**。

1. 在 [來源] 中，選取 [事件中樞]。

1. 為 [匯入選項] 選取適當的值：

   * 若您的其中一個訂用帳戶中有現有的事件中樞，請選取 [來自可用訂閱的使用事件中樞]。 此選項是最簡單的方法。

     [![Select 事件來源匯入選項](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png#lightbox)

    *  下表說明 [來自可用訂閱的使用事件中樞] 選項的必要屬性：

       [![Subscription 和事件中樞詳細資料](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png#lightbox)

       | 屬性 | 描述 |
       | --- | --- |
       | 訂閱 | 所需事件中樞實例和命名空間所屬的訂用帳戶。 |
       | 事件中樞命名空間 | 所需事件中樞實例所屬的事件中樞命名空間。 |
       | 事件中樞名稱 | 所需事件中樞實例的名稱。 |
       | 事件中樞原則值 | 選取所需的共用存取原則。 您可以在事件中樞的 [設定] 索引標籤上建立共用存取原則。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 事件來源的共用存取原則必須有**讀取**權限。 |
       | 事件中樞原則金鑰 | 從選取的事件中樞原則值預先填入。 |

    * 若事件中樞對於您的訂用帳戶而言是外部，或您想要選取進階選項，請選取 [手動提供事件中樞設定]。

       下表說明 [手動提供事件中樞設定] 選項的必要屬性：
 
       | 屬性 | 描述 |
       | --- | --- |
       | 訂用帳戶識別碼 | 所需事件中樞實例和命名空間所屬的訂用帳戶。 |
       | 資源群組 | 所需事件中樞實例和命名空間所屬的資源群組。 |
       | 事件中樞命名空間 | 所需事件中樞實例所屬的事件中樞命名空間。 |
       | 事件中樞名稱 | 所需事件中樞實例的名稱。 |
       | 事件中樞原則值 | 選取所需的共用存取原則。 您可以在事件中樞的 [設定] 索引標籤上建立共用存取原則。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 事件來源的共用存取原則必須有**讀取**權限。 |
       | 事件中樞原則金鑰 | 用來驗證服務匯流排命名空間之存取權的共用存取金鑰。 在這裡輸入主要金鑰或次要金鑰。 |

    * 這兩個選項都共用下列設定選項：

       | 屬性 | 描述 |
       | --- | --- |
       | 事件中樞取用者群組 | 從事件中樞讀取事件的取用者群組。 強烈建議您使用專屬於您事件來源的取用者群組。 |
       | 事件序列化格式 | 目前，JSON 是目前唯一可用的序列化格式。 事件訊息必須是這種格式，否則無法讀取資料。 |
       | 時間戳記屬性名稱 | 若要判斷此值，您需要了解傳送至事件中樞之訊息資料的訊息格式。 這個值是您想要使用作為事件時間戳記之訊息資料中特定事件屬性的**名稱**。 此值區分大小寫。 若保留空白，會使用事件來源內的**事件加入佇列時間**作為事件時間戳記。 |

1. 新增您新增至事件中樞的專用時間序列深入解析取用者群組名稱。

1. 選取 [建立]。

   建立事件來源之後，時間序列深入解析會自動開始將資料串流處理至您的環境。

## <a name="next-steps"></a>後續步驟

* [定義資料存取原則](time-series-insights-data-access.md)來保護資料。

* [將事件傳送](time-series-insights-send-events.md)到事件來源。

* 在[時間序列深入解析總管](https://insights.timeseries.azure.com)中存取您的環境。
