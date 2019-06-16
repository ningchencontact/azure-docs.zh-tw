---
title: Azure IoT 中樞訊息類的概觀
description: Azure IoT 中樞訊息的訊息類的概觀
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 13e35ab93fc37541548785c6355489eaf3a3efc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754559"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>當裝置到雲端的 IoT 中樞訊息 （預覽） 的訊息類

*訊息類*是 IoT 中樞的能力*戳記*訊息之前訊息傳送至指定端點的其他資訊。 若要使用訊息類的其中一個原因是包含可用來簡化下游處理的資料。 比方說，豐富的裝置對應項標記的裝置遙測訊息可以減少客戶進行 API 呼叫這項資訊的裝置對應項上的負載。

![類的訊息流程](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

訊息豐富 」 有三個索引鍵的項目：

* 擴充名稱或索引鍵

* 值，

* 一或多個[端點](iot-hub-devguide-endpoints.md)應套用的擴充。

金鑰可以是任何字串。

值可以是任何下列的範例：

* 任何靜態字串。 不允許動態的值，例如條件、 邏輯、 作業和函式。 比方說，如果您開發可由數個客戶的 SaaS 應用程式時，您可以將識別項指派給每位客戶，並應用程式中提供該識別項。 當應用程式執行時，IoT 中樞將戳記裝置遙測訊息與客戶的識別碼，因此能夠以不同方式處理訊息，每個客戶。

* 裝置對應項，例如其路徑中的資訊。 範例包括 *$twin.tags.field*並 *$twin.tags.latitude*。

* IoT 中樞傳送訊息的名稱。 這個值是 *$iothubname*。

## <a name="applying-enrichments"></a>套用類

訊息可能來自任何支援的資料來源[IoT 中樞訊息路由](iot-hub-devguide-messages-d2c.md)，包括下列的範例：

* 裝置遙測，例如溫度或壓力
* 裝置對應項變更通知-裝置對應項中的變更
* 裝置生命週期事件，例如當建立或刪除裝置

您可以新增類要內建的 IoT 中樞，端點的訊息或訊息會路由傳送至自訂端點，例如 Azure Blob 儲存體、 服務匯流排佇列或服務匯流排主題。

您也可以新增類訊息會由 Event Grid 為選取的端點發佈至 Event Grid。 如需詳細資訊，請參閱 < [Iot 中樞與 Event Grid](iot-hub-event-grid.md)。

類會套用每個端點。 如果您指定特定端點加上戳記的五個類，移至該端點的所有訊息都會都加上相同的五個類。

若要了解如何試用類訊息，請參閱[訊息類教學課程](tutorial-message-enrichments.md)

## <a name="limitations"></a>限制

* 您可以新增這些中樞的每一個 IoT 中樞的最多 10 個類標準或基本層中。 在免費層 IoT 中樞，您可以新增最多 2 個類。

* 在某些情況下，如果您要將擴充套用值，設為標籤或在裝置對應項的屬性值會加上戳記的字串值。 比方說，如果擴充值設定為 $twin.tags.field，訊息會標示 「 $twin.tags.field"的字串而不是該欄位的值從對應項。 此動作會在下列情況：

   * 您的 IoT 中樞會處於 「 基本 」 層。 基本層 IoT 中樞不支援裝置對應項。

   * 您的 IoT 中樞標準層中，但傳送訊息之裝置已沒有裝置對應項。

   * 您的 IoT 中樞標準層中，但提供的值所使用的裝置對應項路徑不存在。 比方說，如果擴充值設為 $twin.tags.location，裝置對應項並沒有標記下的 [位置] 屬性，訊息是加上戳記 」 $twin.tags.location"的字串。 

* 裝置對應項更新可能需要五分鐘的時間才會反映在相對應的 「 擴充 」 值。

* 總訊息大小，包括類，不能超過 256 KB。 如果訊息大小超過 256 KB，IoT 中樞會卸除訊息。 您可以使用[IoT 中樞度量](iot-hub-metrics.md)找出並捨棄的訊息時，將錯誤進行偵錯。 例如，您可以監視 d2c.telemetry.egress.invalid。

## <a name="pricing"></a>價格

訊息類可供不需額外付費。 目前，則要收費時您將訊息傳送至 IoT 中樞。 您只需支付一次該訊息，即使訊息會進入多個端點。

## <a name="availability"></a>可用性

這項功能為預覽狀態，並會在美國東部、 美國西部、 西歐、 以外的所有區域內上市[Azure Government](/azure/azure-government/documentation-government-welcome)， [Azure 中國 21Vianet](/azure/china)，和[Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="next-steps"></a>後續步驟

請參閱這些文章中的訊息路由傳送至 IoT 中樞的詳細資訊：

* [使用 IoT 中樞訊息路由來傳送裝置到雲端訊息至不同的端點](iot-hub-devguide-messages-d2c.md)

* [教學課程：路由的 IoT 中樞](tutorial-routing.md)