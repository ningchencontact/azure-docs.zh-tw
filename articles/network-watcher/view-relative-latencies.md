---
title: "檢視特定位置的相對延遲的 Azure 地區 |Microsoft 文件"
description: "了解如何從特定位置到 Azure 的區域檢視相對延遲跨網際網路提供者。"
services: network-watcher
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: a6c2ffa619eeff8b455df8a8b2157525af12c640
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>檢視相對延遲到 Azure 的區域從特定位置

在本教學課程，了解如何使用 Azure[網路監看員](network-watcher-monitoring-overview.md)服務可協助您決定哪一個 Azure 地區來部署您的應用程式或服務中，根據您的使用者人數統計。 此外，您可以使用它來協助您評估對 Azure 的服務提供者的連線。  
        
## <a name="create-a-network-watcher"></a>建立網路監看員

如果您已有網路監看員中至少一個 Azure[區域](https://azure.microsoft.com/regions)，您可以略過本節中的工作。 建立網路監看員的資源群組。 在此範例中，資源群組會建立在美東地區中，但您可以在任何 Azure 區域中建立資源群組。

```powershell
New-AzureRmResourceGroup -Name NetworkWatcherRG -Location eastus
```

建立網路監看員。 您必須至少一個 Azure 區域中建立網路監看員。 在此範例中，網路監看員會建立 East US Azure 區域中。

```powershell
New-AzureRmNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>比較以單一 Azure 區域相對的網路延遲，從特定位置

評估服務提供者，或疑難排解報告問題，例如 「 站台而變慢，「 使用者從特定位置的服務部署所在的 azure 地區。 例如，下列命令會傳回華盛頓州的美國地區之間的 West US 2 Azure 地區之間 13-15，2017 年 12 月平均相對網際網路服務提供者延遲：

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> 您在前一個命令中指定的區域不需要擷取網路監看員時所指定的區域相同。 前一個命令只會需要您指定現有的網路監看員。 網路監看員可以位於任何區域。 如果您指定的值`-Country`和`-State`，它們必須是有效。 這些值會區分大小寫。 使用有限數目的國家/地區、 州和城市的資料。 執行中的命令[檢視可用的國家/地區、 狀態、 城市及提供者](#view-available)以檢視可用的國家/地區、 城市及狀態，以使用與前一個命令的清單。 

> [!WARNING]
> 您必須指定日期之後 2017 年 11 月 14，如`-StartTime`和`-EndTime`。 指定日期之前 2017 年 11 月 14，會傳回任何資料。 

前一個命令的輸出如下：

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

在傳回的輸出中的值**分數**跨區域和提供者時的相對延遲。 分數為 1 是最差 （最高） 的延遲，而 100 是最低的延遲。 一天平均計算的相對延遲。 在上述範例中，而它有清楚的延遲都是相同的兩天和兩個提供者的延遲之間有些許不同，它也有清楚的兩個提供者的延遲很低的 1-100 標尺上。 這預期行為，因為華盛頓州的美國實際上是接近 West US 2 Azure 區域，而有時結果未如預期般。 您指定較大的日期範圍，更平均延遲，以及在一段時間。

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>比較跨 Azure 區域從特定位置相對的網路延遲

如果為，而不是指定的特定位置與特定的 Azure 區域，使用之間的相對延遲`-Location`，您想要從特定的實體位置決定所有的 Azure 區域的相對延遲，您可以這麼。 例如，下列命令可協助您評估哪一個 azure 地區部署中的服務，如果您主要的使用者是位在華盛頓州的 Comcast 使用者：

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

>[!NOTE]
與不同的是當您指定單一位置，如果您不指定位置，或指定多個位置，例如 「 美國西部 US2 」，「 美國西部 」，您必須指定網際網路服務提供者，執行命令時。 

## <a name="view-available"></a>檢視可用的國家/地區、 狀態、 城市及提供者

使用特定的網際網路服務提供者、 國家/地區、 州和城市的資料。 若要檢視所有可用的網際網路服務提供者的清單，國家/地區、 州和城市，您可以檢視資料，請輸入下列命令：

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

只使用國家/地區、 州和城市先前命令所傳回的資料。 前一個命令會要求您指定現有的網路監看員。 指定範例*NetworkWatcher_eastus*名為資源群組中的網路監看員*NetworkWatcherRG*，但是您可以指定任何現有的網路監看員。 如果您沒有現有的網路監看員，建立一個完成的工作中[建立網路監看員](#create-a-network-watcher)。 

執行前一個命令後，您可以篩選指定有效的值傳回的輸出**國家/地區**，**狀態**，和**縣 （市)**視。  例如，若要檢視在美國境內的西雅圖，華盛頓州中可用的網際網路服務提供者的清單，請輸入下列命令：

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> 指定的值**國家/地區**必須是大寫和小寫。 指定的值**狀態**和**縣 （市)**必須是小寫。 值必須列在 執行命令的任何值之後，傳回的輸出**國家/地區**，**狀態**，和**縣 （市)**。 如果您指定不正確的情況下，或指定的值**國家/地區**，**狀態**，或**縣 （市)**不在執行此命令不會有值，這些後傳回的輸出屬性，傳回的輸出是空的。
