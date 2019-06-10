---
title: 如何將 IoT 中樞的事件來源新增至 Azure 時間序列深入解析 | Microsoft Docs
description: 本文說明如何將與 IoT 中樞連線的事件來源新增至時間序列深入解析環境。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: 955b0e36c63b181e2fe6d2f87e7b015196fceff9
ms.sourcegitcommit: ec7b0bf593645c0d1ef401a3350f162e02c7e9b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2019
ms.locfileid: "66455595"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>新增 IoT 中樞事件來源到您的時間序列深入解析環境

此文章說明如何使用 Azure 入口網站，將從 Azure IoT 中樞讀取資料的事件來源新增至您的 Azure 時間序列深入解析環境。

> [!NOTE]
> 此文章中的這些指示同時適用於 Azure 時間序列深入解析公開推出版 (GA) 與時間序列深入解析 (預覽) 環境。

## <a name="prerequisites"></a>必要條件

* 建立 [Azure 時間序列深入解析環境](time-series-insights-update-create-environment.md)。
* [使用 Azure 入口網站建立 IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* IoT 中樞必須要有傳入的作用中訊息事件。
* 在 IoT 中樞建立專用取用者群組，讓時間序列深入解析環境從中取用。 每個時間序列深入解析事件來源都必須有自身專屬的取用者群組，且不可與任何其他取用者共用。 如果多個讀取器從同一個取用者群組取用事件，則所有讀取器都可能會看到錯誤。 如需詳細資料，請參閱 [Azure IoT 中樞開發人員指南](../iot-hub/iot-hub-devguide.md)。

### <a name="add-a-consumer-group-to-your-iot-hub"></a>將取用者群組新增至 IoT 中樞

應用程式會使用取用者群組從 Azure IoT 中樞提取資料。 若要可靠地從 IoT 中樞讀取資料，提供專用取用者群組，僅供此時間序列深入解析環境。

新增取用者群組至 IoT 中樞：

1. 在 Azure 入口網站中，尋找並開啟您的 IoT 中樞。

1. 底下**設定**，選取**內建端點**，然後選取**事件**端點。

   [![在 [內建端點] 頁面中，選取 [事件] 按鈕](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png#lightbox)

1. 在 [取用者群組]  下，輸入取用者群組的唯一名稱。 當您建立新的事件來源時，請在時間序列深入解析環境中使用此同一名稱。

1. 選取 [ **儲存**]。

## <a name="add-a-new-event-source"></a>新增事件來源

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在左側功能表中，選取 [所有資源]  。 選取 Time Series Insights 環境。

1. 在 [環境拓撲]  下，選取 [事件來源]  ,，然後選取 [新增]  。

   [![選取事件來源，然後選取 [新增] 按鈕](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png#lightbox)

1. 在 [新增事件來源]  窗格中，為 [事件來源名稱]  輸入對此時間序列深入解析環境而言為唯一的值。 例如，輸入 **event-stream**。

1. 對於 [來源]  ，請選取 [Iot 中樞]  。

1. 為 [輸入選項]  選取一個值：

   * 如果您的其中一個訂用帳戶已經有 IoT 中樞，請選取 [從可用的訂閱帳戶使用 IoT 中樞]  。 此選項是最簡單的方法。
   
     [![在 [新的事件來源] 窗格中選取選項](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png#lightbox)

    * 下表說明 [從可用的訂用帳戶使用 Iot 中樞]  選項的必要屬性：

       [![新的事件來源窗格-設定中使用的 IoT 中樞，從可用的訂用帳戶選項的屬性](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png#lightbox)

       | 屬性 | 描述 |
       | --- | --- |
       | 订阅 ID | 選取建立 IoT 中樞的訂用帳戶。
       | IoT 中樞名稱 | 選取 IoT 中樞的名稱。
       | IoT 中樞原則名稱 | 選取共用存取原則。 您可以在 [IoT 中樞設定] 索引標籤中找到共用存取原則。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 事件來源的共用存取原則必須  有**服務連線**權限。
       | IoT 中樞原則金鑰 | 索引鍵已預先填入。
       | IoT 中樞取用者群組 | 從 IoT 中樞讀取事件的取用者群組。 強烈建議您使用專屬於您事件來源的取用者群組。
       | 事件序列化格式 | 目前，JSON 是目前唯一可用的序列化格式。 事件訊息必須是這種格式，否則無法讀取任何資料。 |
       | 時間戳記屬性名稱 | 若要判斷此值，您需要了解傳送至 IoT 中樞的訊息資料採用那一種訊息格式。 這個值是您想要使用作為事件時間戳記之訊息資料中特定事件屬性的**名稱**。 此值區分大小寫。 若保留空白，會使用事件來源中的**事件加入佇列時間**作為事件時間戳記。 |

    * 如果 IoT 中樞對於您的訂用帳戶是外部，或如果想要選擇進階選項，請選取 [手動提供 IoT 中樞設定]  。

      下表說明 [手動提供 Iot 中樞設定]  的必要屬性：

       | 屬性 | 描述 |
       | --- | --- |
       | 订阅 ID | 建立 IoT 中樞的訂用帳戶。
       | 資源群組 | 建立 IoT 中樞所在的資源群組名稱。
       | IoT 中樞名稱 | 您 IoT 中樞的名稱。 當您建立 IoT 中樞時，也會輸入 IoT 中樞的名稱。
       | IoT 中樞原則名稱 | 共用存取原則。 您可以在 [IoT 中樞設定] 索引標籤中建立共用存取原則。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 事件來源的共用存取原則必須  有**服務連線**權限。
       | IoT 中樞原則金鑰 | 用來驗證 Azure 服務匯流排命名空間存取權的共用存取金鑰。 在這裡輸入主要金鑰或次要金鑰。
       | IoT 中樞取用者群組 | 從 IoT 中樞讀取事件的取用者群組。 強烈建議您使用專屬於您事件來源的取用者群組。
       | 事件序列化格式 | 目前，JSON 是目前唯一可用的序列化格式。 事件訊息必須是這種格式，否則無法讀取任何資料。 |
       | 時間戳記屬性名稱 | 若要判斷此值，您需要了解傳送至 IoT 中樞的訊息資料採用那一種訊息格式。 這個值是您想要使用作為事件時間戳記之訊息資料中特定事件屬性的**名稱**。 此值區分大小寫。 若保留空白，會使用事件來源中的**事件加入佇列時間**作為事件時間戳記。 |

1. 新增您新增至 IoT 中樞的專用時間序列深入解析取用者群組名稱。

1. 選取 [建立]  。

   [![[建立] 按鈕](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png#lightbox)

1. 建立事件來源之後，時間序列深入解析會自動開始將資料串流處理至您的環境。

## <a name="next-steps"></a>後續步驟

* [定義資料存取原則](time-series-insights-data-access.md)來保護資料。

* [將事件傳送](time-series-insights-send-events.md)到事件來源。

* 在[時間序列深入解析總管](https://insights.timeseries.azure.com)中存取您的環境。
