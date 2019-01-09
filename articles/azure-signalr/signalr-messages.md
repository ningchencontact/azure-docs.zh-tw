---
title: Azure SignalR 中的訊息和連線
description: 概略說明與 Azure SignalR Service 中的訊息和連線有關的重要概念。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 5a0430e9ad124319147342c49fc51e11472ac8ff
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2018
ms.locfileid: "53812708"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Azure SignalR Service 中的訊息和連線

Azure SignalR Service 具有以連線數目和訊息數目為基礎的計費模型。 以下說明訊息和連線的定義及其計費方面的計數方式。

## <a name="message-formats-supported"></a>支援的訊息格式

Azure SignalR Service 所支援的格式與 ASP.NET Core SignalR 相同：[JSON](https://www.json.org/) 和 [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>訊息大小

Azure SignalR Service 沒有訊息大小限制。

在實務上，大型訊息會分割成分別不超過 2 KB 的較小訊息，並以個別訊息的形式傳輸。 訊息的分割和組合由 SDK 處理。 開發人員不需要投入這方面的人力。

但訊息過大會對傳訊效能產生負面影響。 請盡可能使用較小的訊息大小，並經由測試選擇每個使用案例的的最佳訊息大小。

## <a name="how-to-count-messages-for-billing-purpose"></a>在計費時如何計算訊息數？

我們只會計算從 SignalR Service 輸出的訊息，而忽略用戶端與伺服器之間的 Ping 訊息。

大於 2 KB 的訊息會計為多則 2 KB 的訊息。 Azure 入口網站中的訊息計數圖表會在每個中樞達到 100 則訊息時更新一次。

例如，假設某個使用者有 3 個用戶端和 1 個應用程式伺服器。 一個用戶端傳送了一則 4 KB 的訊息，讓伺服器廣播到所有用戶端。 訊息計數將是 8：有 1 則訊息從服務傳送至應用程式伺服器，3 則訊息從服務傳送至用戶端，而每則訊息分別計為 2 則 2 KB 的訊息。

在累計超過 100 則之前，Azure 入口網站中顯示的訊息計數都會是 0。

## <a name="how-to-count-connections"></a>如何計算連線數？

連線分成伺服器連線和用戶端連線。 根據預設，每個應用程式伺服器的每個中樞有 5 個 SignalR Service 的連線，而每個用戶端則有 1 個 SignalR Service 的用戶端連線。

Azure 入口網站中顯示的連線計數包括伺服器連線和用戶端連線。

例如，假設某個使用者有兩個應用程式伺服器，並且在程式碼中定義了 5 個中樞。 Azure 入口網站中顯示的伺服器連線計數將是 2 個應用程式伺服器 * 5 中樞 * 5 連線/中樞 = 50 個伺服器連線。

## <a name="related-resources"></a>相關資源

- [ASP.NET Core SignalR 組態](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)