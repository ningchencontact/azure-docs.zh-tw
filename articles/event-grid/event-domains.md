---
title: Azure 事件方格中的事件網域
description: 說明如何使用事件網域來管理 Azure 事件方格中的主題。
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: fe66ca8b8f5b4474290e302f73b35868dce68caa
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669325"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>了解用於管理事件方格主題的事件網域

此文章說明如何使用事件網域，來管理自訂事件到您各種不同商業組織、客戶或應用程式的流程。 使用事件網域：

* 大規模管理多租用戶事件架構。
* 管理您的驗證和授權。
* 分割您的主題，而不個別管理每個主題。
* 避免個別發行到您的每個主題端點。

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]


## <a name="event-domains-overview"></a>事件網域概觀

事件網域是適用於與同一個應用程式相關之大量事件方格主題的管理工具。 您可以將它視為可包含數千個個別主題的中繼主題。

事件網域讓 Azure 服務 (例如，儲存體和 IoT 中樞) 用來發行可供您使用之事件的架構可在您自己的系統上使用。 它們讓您能夠將事件發行到數千個主題。 網域也會為您提供對每個主題的授權和驗證控制，讓您能夠分割租用戶。

### <a name="example-use-case"></a>使用案例範例

說明事件網域最容易的方式是使用範例。 假設您經營 Contoso Construction Machinery，您在那裡製造牽引機、挖掘設備及其他重型機械。 在業務營運的過程中，您會為客戶推送有關設備維護、系統健全狀況、合約更新等即時資訊。此資訊全都會傳送到各種端點，包括您的應用程式、客戶端點，以及客戶已設定的其他基礎結構。

事件網域可讓您為 Contoso Construction Machinery 建立模型以作為單一事件實體。 您的每位客戶都會表示為網域/驗證內的主題，並使用 Azure Active Directory 來處理授權。 每位客戶都可訂閱他們的主題，並將他們的事件傳遞給他們。 透過事件網域的受控存取，確保他們只能存取自己的主題。

它也會為您提供單一端點，讓您可將所有客戶事件發行到其中。 事件方格將負責確保每個主題只知道範圍為其租用戶的事件。

![Contoso Construction 範例](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>存取管理

 使用網域，您就能透過 Azure 角色型存取檢查 (RBAC) 來微調對每個主題的授權和驗證控制。 您可以使用這些角色，將應用程式中的每個租用戶限制為只有您想要為其授與存取權的主題。

事件網域中角色型存取檢查 (RBAC) 的運作方式，與[受控存取控制](https://docs.microsoft.com/azure/event-grid/security-authentication#management-access-control)在事件方格和 Azure 其餘部分中運作的方式相同。 使用 RBAC，在事件網域中建立和強制執行自訂角色定義。

### <a name="built-in-roles"></a>內建角色

事件方格具有兩個內建角色定義，可使 RBAC 更容易：

#### <a name="eventgrid-eventsubscription-contributor-preview"></a>EventGrid EventSubscription 參與者 (預覽)

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

#### <a name="eventgrid-eventsubscription-reader-preview"></a>EventGrid EventSubscription 讀者 (預覽)

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="subscribing-to-topics"></a>訂閱主題

在事件網域內的主題上訂用事件，與[在自訂主題上建立事件訂閱](./custom-event-quickstart.md)或訂閱 Azure 所提供的任何內建事件發行者一樣。

### <a name="domain-scope-subscriptions"></a>網域範圍訂用帳戶

事件網域也允許網域範圍訂用帳戶。 不論事件是否會傳送到該主題，事件網域上的事件訂用帳戶都將接收到已傳送到網域的所有事件。 網域範圍訂用帳戶對於管理和稽核用途非常實用。

## <a name="publishing-to-an-event-domain"></a>發行到事件網域

當您建立事件網域時，您會收到類似於您是否已在事件方格中建立主題的發行端點。 

若要將事件發行到事件網域中的任何主題，請[以您針對自訂主題所使用的相同方式](./post-to-custom-topic.md)來將事件推送到網域端點。 唯一的差別是必須指定您想要將事件傳遞到其中的主題。

例如，發行下列事件陣列會將 `"id": "1111"` 的事件傳送到主題 `foo`，同時將 `"id": "2222"` 的事件傳送到主題 `bar`：

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

事件網域會為您處理發行到主題。 不要將事件個別發行到您所管理的每個主題，您可以改為將所有事件發行到網域端點，而事件方格負責確保會將每個事件傳送到正確的主題。

## <a name="limits-and-quotas"></a>限制和配額

### <a name="control-plane"></a>控制平面

在預覽期間，系統會將事件網域限制為某個網域內的 1,000 個主題，以及某個網域內每個主題 50 個事件訂用帳戶。 事件網域範圍訂用帳戶也會限制為 50。

### <a name="data-plane"></a>資料平面

在預覽期間，將事件網域的事件輸送量限制為與自訂主題限制相同的每秒擷取速率 5,000 個事件。

## <a name="pricing"></a>價格

在預覽期間，事件網域所使用的[作業價格](https://azure.microsoft.com/pricing/details/event-grid/)將會與事件方格中所有其他功能所使用的一樣。

作業在事件網域中的運作方式，與它們在自訂主題中所做的一樣。 每個對事件網域的事件輸入都是一項作業，而每次對於事件的傳遞嘗試都是一項作業。

## <a name="next-steps"></a>後續步驟

* 若要了解如何設定事件網域、建立主題、建立事件訂用帳戶，以及發行事件，請參閱[管理事件網域](./how-to-event-domains.md)。