---
title: Azure SignalR 中的訊息和連線
description: 概略說明與 Azure SignalR Service 中的訊息和連線有關的重要概念。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: c2348df7a1a55584807a03216e294486ddadfc52
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352592"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Azure SignalR Service 中的訊息和連線

Azure SignalR Service 具有以連線數目和訊息數目為基礎的計費模型。 以下說明訊息和連線的定義及其計費方面的計數方式。

## <a name="message-formats-supported"></a>支援的訊息格式

Azure SignalR Service 所支援的格式與 ASP.NET Core SignalR 相同：[JSON](https://www.json.org/) 和 [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>訊息大小

Azure SignalR Service 沒有訊息大小限制。

在實務上，大型訊息會分割成分別不超過 2 KB 的較小訊息，並以個別訊息的形式傳輸。 SDK 會處理訊息的分割和組合。 開發人員不需要投入這方面的人力。

但訊息過大會對傳訊效能產生負面影響。 請盡可能使用較小的訊息大小，並經由測試選擇每個使用案例的的最佳訊息大小。

## <a name="how-to-count-messages-for-billing-purpose"></a>在計費時如何計算訊息數？

我們只會計算從 SignalR Service 輸出的訊息，而忽略用戶端與伺服器之間的 Ping 訊息。

大於 2 KB 的訊息會計為多則 2 KB 的訊息。 Azure 入口網站中的訊息計數圖表會在每個中樞達到 100 則訊息時更新一次。

例如，假設您有三個用戶端和一個應用程式伺服器。 一個用戶端傳送了一則 4 KB 的訊息，讓伺服器廣播到所有用戶端。 訊息計數是 8：有一則訊息從服務傳送至應用程式伺服器，三則訊息從服務傳送至用戶端，而每則訊息分別計為兩則 2 KB 的訊息。

在累計超過 100 則之前，Azure 入口網站中顯示的訊息計數都會是 0。

## <a name="how-to-count-connections"></a>如何計算連線數？

連線分成伺服器連線和用戶端連線。 根據預設，每個應用程式伺服器的每個中樞有五個 SignalR Service 的連線，而每個用戶端則有一個 SignalR Service 的用戶端連線。

Azure 入口網站中顯示的連線計數包括伺服器連線和用戶端連線。

例如，假設您有兩個應用程式伺服器，並且在程式碼中定義了五個中樞。 伺服器連線計數是 50：2 個應用程式伺服器 * 5 個中樞 * 5 個連線/中樞。

ASP.NET SignalR 計算伺服器連線的方式有所不同。 除了客戶定義的中樞以外，它還有一個預設中樞。 每個應用程式伺服器依預設還另需 5 個伺服器連線。 預設中樞的連線計數會與其他中樞保持一致。

## <a name="how-to-count-inbound-traffic--outbound-traffic"></a>如何計算輸入流量/輸出流量的數目

輸入/輸出是從 SignalR Service 的觀點來看的。 流量會以位元組為單位計算。 如同訊息計數，流量也有其取樣率。 Azure 入口網站中的輸入/輸出圖表會在每個中樞達到 100 KB 時更新一次。

## <a name="related-resources"></a>相關資源

- [Azure 監視器中的彙總類型](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR 組態](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)