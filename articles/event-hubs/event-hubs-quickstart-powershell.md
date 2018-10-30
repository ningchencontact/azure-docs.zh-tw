---
title: Azure 快速入門 - 使用 PowerShell 建立事件中樞 | Microsoft Docs
description: 本快速入門說明如何使用 Azure PowerShell 建立事件中樞，然後使用 .NET Standard SDK 傳送和接收事件。
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 25c64b3ac2d051aac5998d23f07e149a1dd57bc9
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456219"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立事件中樞

Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需事件中樞的詳細概觀，請參閱[事件中樞概觀](event-hubs-about.md)和[事件中樞功能](event-hubs-features.md)。

在此快速入門中，您將使用 Azure PowerShell 建立事件中樞。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，請確定您具有下列項目︰

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶][]。
- [Visual Studio 2017 Update 3 (版本 15.3, 26730.01)](http://www.visualstudio.com/vs) 或更新版本。
- [.NET Standard SDK](https://www.microsoft.com/net/download/windows)，2.0 版或更新版本。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您在本機使用 PowerShell，您必須執行最新版的 PowerShell，才能完成本快速入門。 如果您需要安裝或升級，請參閱[安裝和設定 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0)。

## <a name="create-a-resource-group"></a>建立資源群組

資源群組是 Azure 資源的邏輯集合，而您需要資源群組來建立事件中樞。 

下列範例會在美國東部區域中建立一個資源群組。 以您要使用的資源群組名稱取代 `myResourceGroup`：

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>建立事件中樞命名空間

建立您的資源群組之後，在該資源群組內建立事件中樞命名空間。 事件中樞命名空間提供唯一的完整網域名稱，您可以在其中建立事件中樞。 以命名空間的唯一名稱取代 `namespace_name`：

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>建立事件中樞

現在，您已有事件中樞命名空間，接著在該命名空間內建立事件中樞：

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name
```

恭喜！ 您已使用 Azure PowerShell 建立事件中樞命名空間和該命名空間內的事件中樞。 

## <a name="next-steps"></a>後續步驟

在本文中，您已建立事件中樞命名空間，並使用應用程式範例來傳送事件到事件中樞，或從事件中樞接收事件。 如需將事件傳送至事件中樞或從事件中樞接收事件的逐步指示，請參閱下列教學課程： 

- **將事件傳送至事件中樞**：[.NET Standard](event-hubs-dotnet-standard-getstarted-send.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)、[Java](event-hubs-java-get-started-send.md)、[Python](event-hubs-python-get-started-send.md)、[Node.js](event-hubs-node-get-started-send.md)、[Go](event-hubs-go-get-started-send.md)、[C](event-hubs-c-getstarted-send.md)
- **從事件中樞接收事件**：[.NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md)、[Java](event-hubs-java-get-started-receive-eph.md)、[Python](event-hubs-python-get-started-receive.md)、[Node.js](event-hubs-node-get-started-receive.md)、[Go](event-hubs-go-get-started-receive-eph.md)、[Apache Storm](event-hubs-storm-getstarted-receive.md)

[建立免費帳戶]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
