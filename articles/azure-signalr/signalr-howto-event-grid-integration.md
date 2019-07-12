---
title: 如何將 Azure SignalR 服務事件傳送至 Event Grid
description: 輔助線為各位示範如何啟用 Event Grid 事件 SignalR 服務，然後用戶端連線連接/中斷連線將事件傳送至範例應用程式。
services: azure-signalr
author: chenyl
ms.service: azure-signalr
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: chenyl
ms.openlocfilehash: 2d782306938136ce6d21a331185f591316f58a29
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789172"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>如何將事件從 Azure SignalR 服務傳送至 Event Grid

Azure Event Grid 是一種完全受控的事件路由服務，提供統一事件耗用量使用發佈-訂閱模型。 本指南中，您可以使用 Azure CLI 建立 Azure SignalR 服務、 訂閱連接事件，然後部署範例 web 應用程式，以將事件接收。 最後，您可以連接及中斷連線並查看範例應用程式中的事件裝載。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶][azure-account] 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

本文中的 Azure CLI 命令已針對 **Bash** 殼層進行格式化。 如果您使用不同的殼層 (例如 PowerShell 或命令提示字元)，您可能需要據以調整行接續字元或變數指派行。 本文使用變數來將需要編輯的命令數量降至最低。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是一種邏輯容器，您可在其中部署與管理 Azure 資源。 下列[az 群組建立][az-group-create]命令會建立名為的資源群組*myResourceGroup*中*eastus*區域。 如果您想要針對資源群組使用不同名稱，請將 `RESOURCE_GROUP_NAME` 設定為不同的值。

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>建立 SignalR 服務

接下來，將 Azure Signalr 服務部署到資源群組，使用下列命令。
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

一旦建立 SignalR 服務之後，Azure CLI 會傳回類似下列輸出：

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>建立事件端點

在本節中，您可以使用位於 GitHub 存放庫的 Resource Manager 範本，來將預先建置的範例 Web 應用程式部署到 Azure App Service。 您稍後會訂閱登錄的事件方格事件，並指定此應用程式作為要將事件傳送至其中的端點。

若要部署範例應用程式，請將 `SITE_NAME` 設為您 Web 應用程式的唯一名稱，然後執行下列命令。 網站名稱在 Azure 中必須是唯一的，因為它會構成 Web 應用程式完整網域名稱 (FQDN) 的一部分。 在後續小節中，您會在網頁瀏覽器中瀏覽至應用程式的 FQDN，以檢視您的登錄事件。

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

部署成功之後 （可能需要幾分鐘的時間），開啟瀏覽器並瀏覽至您的 web 應用程式，並確定它正在執行：

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>訂閱登錄事件

在事件方格中，您可以訂閱「主題」  ，以告知它您想要追蹤的事件，以及要將它們傳送至何處。 下列[az eventgrid 事件訂用帳戶建立][az-eventgrid-event-subscription-create]命令會訂閱您所建立，並指定它應該傳送事件至其中之端點的 web 應用程式的 URL 時，Azure SignalR 服務。 您在先前小節中所填入的環境變數會在此處重複使用，因此不需進行任何編輯。

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

完成訂閱時，您應該會看見類似下列內容的輸出：

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>觸發登錄事件

切換至的服務模式`Serverless Mode`並設定 SignalR 服務的用戶端連接。 您可以採取[無伺服器的範例](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless)做為參考。

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a seperate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>檢視登錄事件

您現在已連線至 SignalR 服務的用戶端。 瀏覽至您的事件方格檢視器 web 應用程式，而且您應該會看到`ClientConnectionConnected`事件。 如果您要終止用戶端，您也會看到`ClientConnectionDisconnected`事件。

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
