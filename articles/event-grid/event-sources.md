---
title: Azure 事件方格事件來源
description: 描述支援的 Azure 事件方格事件來源
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: tomfitz
ms.openlocfilehash: f9c3bcb6b92b43fe5b5bad72c99e6ce199c17448
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2018
---
# <a name="event-sources-in-azure-event-grid"></a>Azure 事件方格中的事件來源

事件來源是事件發生的地點。 許多 Azure 服務都已自動設定為可傳送事件。 您也可以建立會傳送事件的自訂應用程式。 自訂應用程式不需要裝載於 Azure，即可使用事件方格進行事件配送。

本文會提供每個事件來源的內容連結。

## <a name="azure-subscriptions"></a>Azure 訂用帳戶

訂閱 Azure 訂用帳戶事件，以回應整個 Azure 訂用帳戶中的資源變更。

|標題 |說明  |
|---------|---------|
| [將 Azure 自動化與事件方格和 Microsoft Teams 整合](ensure-tags-exists-on-new-virtual-machines.md) |建立會傳送事件的虛擬機器。 事件會觸發標記虛擬機器的自動化 Runbook，以及觸發傳送給 Microsoft Teams 通道的訊息。 |
| [事件結構描述](event-schema-subscriptions.md) | 顯示 Azure 訂用帳戶事件中的欄位。 |

## <a name="custom-topics"></a>自訂主題

訂閱自訂主題，以回應應用程式事件。

|標題  |說明  |
|---------|---------|
| [使用 Azure CLI 建立和路由自訂事件](custom-event-quickstart.md) | 示範如何使用 Azure CLI 傳送自訂事件。 |
| [使用 Azure PowerShell 建立和路由傳送自訂事件](custom-event-quickstart-powershell.md) | 示範如何使用 Azure PowerShell 傳送自訂事件。 |
| [使用 Azure 入口網站建立和路由自訂事件](custom-event-quickstart-portal.md) | 示範如何使用入口網站傳送自訂事件。 |
| [張貼到自訂主題](post-to-custom-topic.md) | 示範如何將事件張貼到自訂主題。 |
| [將自訂事件路由到 Azure 佇列儲存體](custom-event-to-queue-storage.md) | 描述如何將自訂事件傳送至佇列儲存體。 |
| [事件結構描述](event-schema.md) | 顯示自訂事件中的欄位。 |

## <a name="event-hubs"></a>事件中樞

訂閱事件中樞事件，以回應「擷取」檔案事件。

|標題  |說明  |
|---------|---------|
| [將巨量資料串流處理至資料倉儲](event-grid-event-hubs-integration.md) | 當事件中樞建立「擷取」檔案時，事件方格會傳送一個事件至函式應用程式。 應用程式會對「擷取」檔案進行擷取，並將資料遷移到資料倉儲。 |
| [事件結構描述](event-schema-event-hubs.md) | 顯示事件中樞事件中的欄位。 |

## <a name="iot-hub"></a>IoT 中樞

訂閱 IoT 中樞事件，以回應裝置建立和刪除事件。

|標題  |說明  |
|---------|---------|
| [使用 Logic Apps 來傳送 Azure IoT 中樞事件的相關電子郵件通知](publish-iot-hub-events-to-logic-apps.md) | 每當有裝置新增至您的 IoT 中樞時，邏輯應用程式就會傳送電子郵件通知。 |
| [使用事件方格來觸發動作以回應「IoT 中樞」事件](../iot-hub/iot-hub-event-grid.md) | 整合 IoT 中樞與事件方格的概觀。 |
| [事件結構描述](event-schema-iot-hub.md) | 顯示 IoT 中樞事件中的欄位。 |

## <a name="media-services"></a>媒體服務

訂閱媒體服務事件，以回應作業狀態事件。

|標題  |說明  |
|---------|---------|
| [回應媒體服務事件](../media-services/latest/reacting-to-media-services-events.md) | 整合媒體服務與事件方格的概觀。 |
| [使用 CLI，將 Azure 媒體服務事件路由至自訂 Web 端點](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 示範如何從媒體服務傳送事件。 |
| [事件結構描述](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 顯示媒體服務事件中的欄位。 |

## <a name="resource-groups"></a>資源群組

訂閱資源群組事件，以回應整個資源群組中的資源變。

|標題  |說明  |
|---------|---------|
| [使用 Azure Event Grid 和 Logic Apps 監視虛擬機器變更](monitor-virtual-machine-changes-event-grid-logic-app.md) | 邏輯應用程式會監視虛擬機器的變更，並傳送有關這些變更的電子郵件。 |
| [事件結構描述](event-schema-resource-groups.md) | 顯示資源群組事件中的欄位。 |

## <a name="service-bus"></a>服務匯流排

訂閱服務匯流排事件，以回應訊息沒有作用中接聽程式的事件。

|標題  |說明  |
|---------|---------|
| [Azure 服務匯流排與 Azure 事件方格的整合範例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件方格會從服務匯流排主題傳送訊息至函式應用程式和邏輯應用程式。 |
| [Azure 服務匯流排與事件方格的整合概觀](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | 整合服務匯流排與事件方格的概觀。 |
| [事件結構描述](event-schema-service-bus.md) | 顯示服務匯流排事件中的欄位。 |

## <a name="storage"></a>儲存體

訂閱 Blob 儲存體事件，以回應 Blob 建立和刪除事件。

|標題  |說明  |
|---------|---------|
| [使用 Azure CLI 將 Blob 儲存體事件路由至自訂的 Web 端點](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 示範如何使用 Azure CLI 傳送Blob 儲存體事件。 |
| [使用 PowerShell 將 Blob 儲存體事件路由至自訂的 Web 端點](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 示範如何使用 Azure PowerShell 傳送Blob 儲存體事件。 |
| [回應 Blob 儲存體事件](../storage/blobs/storage-blob-event-overview.md) | 整合 Blob 儲存體與事件方格的概觀。 |
| [事件結構描述](event-schema-blob-storage.md) | 顯示 Blob 儲存體事件中的欄位。 |

## <a name="next-steps"></a>後續步驟

* 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)。
* 若要快速地開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。
