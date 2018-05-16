---
title: Azure 事件方格事件處理常式
description: 描述支援的 Azure 事件方格事件處理常式
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/25/2018
ms.author: tomfitz
ms.openlocfilehash: ccbabed3fd1df39ac2ba732064392a1a0ecede2a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="event-handlers-in-azure-event-grid"></a>Azure 事件方格中的事件處理常式

事件處理常式是傳送事件的位置。 處理常式會採取一些進一步的動作來處理事件。 許多 Azure 服務都已自動設定為可處理事件。 您也可以使用任何 Webhook 來處理事件。 Webhook 不需要裝載於 Azure 來處理事件。

本文會提供每個事件處理常式的內容連結。

## <a name="azure-automation"></a>Azure 自動化

使用 Azure 自動化，透過自動化的 Runbook 來處理事件。

|標題  |說明  |
|---------|---------|
|[將 Azure 自動化與事件方格和 Microsoft Teams 整合](ensure-tags-exists-on-new-virtual-machines.md) |建立會傳送事件的虛擬機器。 事件會觸發標記虛擬機器的自動化 Runbook，以及觸發傳送給 Microsoft Teams 通道的訊息。 |

## <a name="azure-functions"></a>Azure Functions

使用 Azure Functions 對事件進行無伺服器回應。

|標題  |說明  |
|---------|---------|
| [Azure Functions 的事件方格觸發程序](../azure-functions/functions-bindings-event-grid.md) | 在 Functions 中使用事件方格觸發程序的概觀。 |
| [使用 Event Grid 自動調整已上傳映像的大小](resize-images-on-storage-blob-upload-event.md) | 使用者透過 Web 應用程式將映像上傳至儲存體帳戶。 建立儲存體 Blob 時，事件方格會傳送一個事件給函式應用程式，讓其調整上傳映像的大小。 |
| [將巨量資料串流處理至資料倉儲](event-grid-event-hubs-integration.md) | 當事件中樞建立「擷取」檔案時，事件方格會傳送一個事件至函式應用程式。 應用程式會對「擷取」檔案進行擷取，並將資料遷移到資料倉儲。 |
| [Azure 服務匯流排與 Azure 事件方格的整合範例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件方格會從服務匯流排主題傳送訊息至函式應用程式和邏輯應用程式。 |

## <a name="logic-apps"></a>Logic Apps

使用 Logic Apps 來自動化回應事件的商務程序。

|標題  |說明  |
|---------|---------|
| [使用 Azure Event Grid 和 Logic Apps 監視虛擬機器變更](monitor-virtual-machine-changes-event-grid-logic-app.md) | 邏輯應用程式會監視虛擬機器的變更，並傳送有關這些變更的電子郵件。 |
| [使用 Logic Apps 來傳送 Azure IoT 中樞事件的相關電子郵件通知](publish-iot-hub-events-to-logic-apps.md) | 每當有裝置新增至您的 IoT 中樞時，邏輯應用程式就會傳送電子郵件通知。 |
| [Azure 服務匯流排與 Azure 事件方格的整合範例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件方格會從服務匯流排主題傳送訊息至函式應用程式和邏輯應用程式。 |

## <a name="queue-storage"></a>佇列儲存體

使用佇列儲存體來接收需要提取的事件。

|標題  |說明  |
|---------|---------|
| [使用 Azure CLI 和事件方格將自訂事件路由至 Azure 佇列](custom-event-to-queue-storage.md) | 描述如何將自訂事件傳送至佇列儲存體。 |

## <a name="webhooks"></a>Webhook

針對回應事件的自訂端點使用 Webhook。

|標題  |說明  |
|---------|---------|
| [接收 HTTP 端點的事件](receive-events.md) | 說明如何驗證 HTTP 端點以從事件訂閱接收事件，然後接收和還原序列化事件。 |

## <a name="next-steps"></a>後續步驟

* 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)。
* 若要快速地開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。
