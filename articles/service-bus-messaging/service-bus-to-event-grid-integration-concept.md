---
title: "Azure 服務匯流排與 Event Grid 的整合概觀 | Microsoft Docs"
description: "描述服務匯流排傳訊與 Event Grid 的整合"
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: bf771428505081cb60ca4417f87a4f6c2afbd25d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2018
---
# <a name="azure-service-bus-to-azure-event-grid-integration-overview"></a>Azure 服務匯流排與 Azure Event Grid 的整合概觀

Azure 服務匯流排已開始與 Azure 進行全新整合。 此功能可達成的主要案例，就是具有少量訊息的服務匯流排佇列或訂用帳戶不必讓接收者隨時輪詢訊息。 當佇列或訂用帳戶中有訊息，但沒有任何接收者存在時，服務匯流排現在可以將事件發出至 Azure Event Grid。 您可以在服務匯流排命名空間中建立 Azure Event Grid 訂用帳戶，然後接聽這些事件並啟動接收者來回應事件。 利用此功能，服務匯流排可使用於反應靈敏的程式設計模型中。

若要啟用此功能，您需要下列事項：

* 具有至少一個服務匯流排佇列的 Azure 服務匯流排進階命名空間或具有至少一個訂用帳戶的服務匯流排主題。
* Azure 服務匯流排命名空間的參與者存取權。
* 此外，您還需要適用於服務匯流排命名空間的 Azure Event Grid 訂用帳戶。 此訂用帳戶會從有訊息可供挑選的 Azure Event Grid 收取通知。 典型訂閱者可能是 Logic Apps、Azure Functions，或聯繫 Web 應用程式的 Web Hook，而這些訂閱者會接著處理訊息。 

![19][]

### <a name="verify-that-you-have-contributor-access"></a>確認您具有參與者存取權

瀏覽至您的服務匯流排命名空間，然後選取如下所示的 [存取控制 (IAM)]：

![1][]

### <a name="events-and-event-schemas"></a>事件和事件結構描述

Azure 服務匯流排目前會針對兩種案例傳送事件。

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#dead-lettered-messages-available-event)

此外它會使用標準 Azure Event Grid 安全性和[驗證機制](https://docs.microsoft.com/en-us/azure/event-grid/security-authentication)。

若要取得 Event Grid 事件結構描述的詳細資訊，請遵循[這個](https://docs.microsoft.com/en-us/azure/event-grid/event-schema)連結。

#### <a name="active-messages-available-event"></a>作用中訊息可用事件

如果佇列或訂用帳戶有作用中的訊息，但沒有接聽的接收者，就會產生此事件。

此事件的結構描述如下所示︰

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="dead-lettered-messages-available-event"></a>無法傳送的訊息可用事件

您可對每個無效信件佇列取得至少一個事件，而此種佇列具有訊息，但沒有作用中的接收者。

此事件的結構描述如下所示︰

```JSON
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-often-and-how-many-events-are-emitted"></a>發出事件的頻率和數量為何？

如果命名空間中有多個佇列和主題 / 訂用帳戶，您會對每個佇列和每個訂用帳戶取得至少一個事件。 如果服務匯流排實體中沒有任何訊息，且每兩分鐘就有一則新訊息送達，系統就會立即發出事件 (除非 Azure 服務匯流排偵測到作用中的接收者)。 瀏覽訊息並不會干擾事件。

依預設，Azure 服務匯流排會針對命名空間中的所有實體發出事件。 如果您只想取得特定實體的事件，請參閱下列篩選一節。

### <a name="filtering-limiting-from-where-you-get-events"></a>篩選，限制您從何處取得事件

如果您只想針對命名空間內的一個佇列或一個訂用帳戶取得事件，您可以使用 Azure Event Grid 所提供的「開頭為」或「結尾為」篩選條件。 在某些介面中，篩選條件稱為「前置」和「後置詞」篩選條件。 如果您想取得多個佇列和訂用帳戶 (但並非所有佇列和訂用帳戶) 的事件，您可以建立多個不同的 Azure Event Grid 訂用帳戶並為每個訂用帳戶提供一個篩選條件。

## <a name="how-to-create-azure-event-grid-subscriptions-for-service-bus-namespaces"></a>如何為服務匯流排命名空間建立 Azure Event Grid 訂用帳戶

為服務匯流排命名空間建立 Event Grid 訂用帳戶的方法有三種。

* [Azure 入口網站](#portal-instructions)
* [Azure CLI](#azure-cli-instructions)
* [PowerShell](#powershell-instructions)

## <a name="portal-instructions"></a>入口網站指示

若要建立新的 Azure Event Grid 訂用帳戶，請在 Azure 入口網站中瀏覽至您的命名空間並選取 [Event Grid] 刀鋒視窗。 按一下底下的 [+ 事件訂用帳戶] 可顯示一個命名空間，而該命名空間已經有一些 Event Grid 訂用帳戶。

![20][]

下列螢幕擷取畫面顯示如何訂閱 Azure 函式或 Web Hook (不使用任何特定篩選條件) 的範例：

![21][]

## <a name="azure-cli-instructions"></a>Azure CLI 指示

先確定您至少已安裝 Azure CLI 2.0 版。 您可以在這裡下載安裝程式。 然後按 "Windows + X"，並以系統管理員權限開啟新的 PowerShell 主控台。 或者，也可以在 Azure 入口網站中使用命令 shell。

執行以下程式碼：

```PowerShell
Az login

Aa account set -s “THE SUBSCRIPTION YOU WANT TO USE”

$namespaceid=(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name “<yourNamespace>“--resource-group “<Your Resource Group Name>” --query id --output tsv)

az eventgrid event-subscription create --resource-id $namespaceid --name “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” --endpoint “<your_function_url>” --subject-ends-with “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

## <a name="powershell-instructions"></a>PowerShell 指示

確定您已安裝 Azure PowerShell。 您可以在這裡找到該程式。 然後按 "Windows + X"，並以系統管理員權限開啟新的 PowerShell 主控台。 或者，也可以在 Azure 入口網站中使用命令 shell。

```PowerShell
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module AzureRM.ServiceBus

$NSID = (Get-AzureRmServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id 

New-AzureRmEVentGridSubscription -EventSubscriptionName “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

從這裡，您可以瀏覽其他安裝程式選項或[測試事件是否流動](#test-that-events-are-flowing)。

## <a name="next-steps"></a>後續步驟

* 服務匯流排和 Event Grid [範例](service-bus-to-event-grid-integration-example.md)。
* 深入了解 [Azure Event Grid](https://docs.microsoft.com/en-us/azure/azure-functions/)。
* 深入了解 [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/)。
* 深入了解 [Azure Logic Apps](https://docs.microsoft.com/en-us/azure/logic-apps/)。
* 深入了解 [Azure 服務匯流排](https://docs.microsoft.com/en-us/azure/azure-functions/)。

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png