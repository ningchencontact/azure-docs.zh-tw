---
title: 使用 Go 來傳送事件 - Azure 事件中樞 | Microsoft Docs
description: 本文將逐步解說如何建立一個會從 Azure 事件中樞傳送事件的 Go 應用程式。
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 44cbea8cc40f83ed5fdd2863da0cb0a01e8dc2b8
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091055"
---
# <a name="send-events-to-event-hubs-using-go"></a>使用 Go 將事件傳送至事件中樞

Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需事件中樞的詳細概觀，請參閱[事件中樞概觀](event-hubs-about.md)和[事件中樞功能](event-hubs-features.md)。

此教學課程說明如何將事件從以 Go 撰寫的應用程式傳送到事件中樞。 

> [!NOTE]
> 您可以從 [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs) 下載此快速入門來作為範例，並以您事件中樞的值取代 `EventHubConnectionString` 和 `EventHubName` 字串，然後執行。 或者，您可以遵循本教學課程中的步驟，來建立自己的解決方案。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要下列必要條件：

* 安裝在本機的 Go。 如果有需要，請依照[這些指示](https://golang.org/doc/install) \(英文\) 執行。

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>建立事件中樞命名空間和事件中樞
第一個步驟是使用 [Azure 入口網站](https://portal.azure.com)來建立「事件中樞」類型的命名空間，然後取得您應用程式與「事件中樞」進行通訊所需的管理認證。 若要建立命名空間和事件中樞，請依照[這篇文章](event-hubs-create.md)中的程序操作。

現在，請繼續進行本教學課程中的下列步驟。

## <a name="install-go-package"></a>安裝 Go 套件

使用 `go get` 或 `dep` 取得適用於事件中樞的 Go 套件。 例如︰

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

## <a name="import-packages-in-your-code-file"></a>在您的程式碼檔案中匯入套件

若要匯入 Go 套件，請使用下列程式碼範例：

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

## <a name="create-service-principal"></a>建立服務主體

依照[使用Azure CLI 2.0 來建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)中的指示建立新的服務主體。 使用下列名稱將提供的認證儲存在您的環境中。 Azure SDK for Go 與事件中樞套件預先設定為尋找這些變數名稱：

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

現在，為使用這些認證的事件中樞用戶端建立授權提供者：

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="create-event-hubs-client"></a>建立事件中樞用戶端

下列程式碼會建立事件中樞用戶端：

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

## <a name="send-messages"></a>傳送訊息

在下列程式碼片段中，使用 (1) 從終端機以互動方式傳送訊息，或使用 (2) 在您的程式內傳送訊息：

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!")
```

## <a name="extras"></a>額外項目

取得事件中樞內的資料分割識別碼：

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n, info.PartitionIDs)
```

執行應用程式以將事件傳送到事件中樞。 

恭喜！ 您現在已將傳送訊息到事件中樞。

## <a name="next-steps"></a>後續步驟
在此快速入門中，您已使用 Go 將訊息傳送到事件中樞。 若要了解如何使用 Go 從事件中樞接收事件，請參閱[從事件中樞接收事件 - Go](event-hubs-go-get-started-receive-eph.md)。

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
