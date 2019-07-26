---
title: Azure IoT 中樞 message 擴充的總覽
description: Azure IoT 中樞訊息的訊息擴充總覽
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 8e24489ad2909879b035a08316e66788034e99bc
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377206"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>裝置到雲端 IoT 中樞訊息的訊息擴充 (預覽)

*Message 擴充*是 IoT 中樞在訊息傳送至指定的端點之前, 以其他資訊來*戳記*訊息的能力。 使用 message 擴充的其中一個原因是要包含可用於簡化下游處理的資料。 例如, 使用裝置對應項標記來擴充裝置遙測訊息, 可以降低客戶的負載, 讓裝置對應項 API 呼叫此資訊。

![訊息擴充流程](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Message 擴充有三個主要元素:

* 擴充名稱或金鑰

* 值

* 應套用擴充的一或多個[端點](iot-hub-devguide-endpoints.md)。

索引鍵可以是任何字串。

此值可以是下列任何一個範例:

* 任何靜態字串。 不允許動態值, 例如條件、邏輯、作業和函數。 例如, 如果您開發數個客戶所使用的 SaaS 應用程式, 您可以將識別碼指派給每個客戶, 並讓該識別碼可用於應用程式中。 當應用程式執行時, IoT 中樞會使用客戶的識別碼來戳記裝置遙測訊息, 讓每個客戶能夠以不同方式處理訊息。

* 傳送訊息的 IoT 中樞名稱。 這個值是 *$iothubname*。

* 來自裝置對應項的資訊, 例如其路徑。 範例會 *$twin. tags. 欄位*和 *$twin. 標記. 緯度*。

   > [!NOTE]
   > 此時, 只會 $iothubname、$twin 標記、$twin 的屬性和 $twin。報告為 message 擴充所支援的變數。

訊息擴充會當做應用程式屬性新增至傳送至所選端點的訊息。  

## <a name="applying-enrichments"></a>套用擴充

訊息可以來自[IoT 中樞訊息路由](iot-hub-devguide-messages-d2c.md)所支援的任何資料來源, 包括下列範例:

* 裝置遙測資料, 例如溫度或壓力
* 裝置對應項變更通知--裝置對應項中的變更
* 裝置生命週期事件, 例如建立或刪除裝置時

您可以將擴充新增至要傳送至 IoT 中樞內建端點的訊息, 或將訊息路由至自訂端點 (例如 Azure Blob 儲存體、服務匯流排佇列或服務匯流排主題)。

您也可以藉由選取端點作為事件方格, 將擴充新增至要發佈至事件方格的訊息。 如需詳細資訊, 請參閱[Iot 中樞和事件方格](iot-hub-event-grid.md)。

擴充適用于每個端點。 如果您指定要為特定端點戳記的五個擴充, 則所有傳送至該端點的訊息都會以相同的五個擴充戳記。

若要瞭解如何試用 message 擴充, 請參閱[message 擴充教學](tutorial-message-enrichments.md)課程

## <a name="limitations"></a>限制

* 在標準或基本層中, 您可以為這些中樞的每個 IoT 中樞新增最多10個擴充。 針對免費層中的 IoT 中樞, 您最多可以新增2個擴充。

* 在某些情況下, 如果您要套用的擴充值設定為裝置對應項中的標籤或屬性, 則此值會被標記為字串值。 例如, 如果 [擴充] 值設定為 [$twin], 則會以字串 "$twin 標記, 而非對應項中該欄位的值來標記訊息。 這會發生在下列情況:

   * 您的 IoT 中樞是在基本層中。 基本層 IoT 中樞不支援裝置 twins。

   * 您的 IoT 中樞是在標準層中, 但傳送訊息的裝置沒有裝置對應項。

   * 您的 IoT 中樞在標準層中, 但擴充的值所使用的裝置對應項路徑不存在。 例如, 如果 [擴充] 值設定為 [$twin 標籤], 而 [裝置對應項] 在 [標籤] 底下沒有 [位置] 屬性, 則會以字串「$twin 標記」來標記訊息。 

* 裝置對應項的更新最多可能需要五分鐘的時間, 才會反映在對應的擴充值中。

* 訊息大小總計 (包括擴充) 不能超過 256 KB。 如果訊息大小超過 256 KB, IoT 中樞將會捨棄訊息。 當訊息卸載時, 您可以使用[IoT 中樞度量](iot-hub-metrics.md)來識別和偵測錯誤。 例如, 您可以監視 d2c 無效。

## <a name="pricing"></a>定價

Message 擴充可免費使用。 目前, 當您將訊息傳送至 IoT 中樞時, 會向您收取費用。 即使訊息傳送至多個端點, 您只需要針對該訊息收取一次。

## <a name="availability"></a>可用性

這項功能目前可供預覽, 適用于美國東部、美國西部、西歐、 [Azure Government](/azure/azure-government/documentation-government-welcome)、 [azure 中國世紀](/azure/china)和[azure 德國](https://azure.microsoft.com/global-infrastructure/germany/)以外的所有區域。

## <a name="next-steps"></a>後續步驟

如需將訊息路由傳送至 IoT 中樞的詳細資訊, 請參閱這些文章:

* [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至不同的端點](iot-hub-devguide-messages-d2c.md)

* [教學課程：IoT 中樞路由](tutorial-routing.md)
