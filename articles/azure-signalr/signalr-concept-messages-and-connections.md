---
title: Azure SignalR Service 中的訊息和連線
description: 概略說明與 Azure SignalR Service 中的訊息和連線有關的重要概念。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: e82ce8f5c97aed7e2cb832d8e808ff84691f7c9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61401197"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Azure SignalR Service 中的訊息和連線

Azure SignalR Service 的計費模型是以連線數目和訊息數目為基礎。 本文說明如何定義及計算訊息和連線，以供計費。


## <a name="message-formats"></a>訊息格式 

Azure SignalR Service 所支援的格式與 ASP.NET Core SignalR 相同：[JSON](https://www.json.org/) 和 [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)。

## <a name="message-size"></a>訊息大小

Azure SignalR Service 沒有訊息大小限制。

大型訊息會分割成各自不超過 2 KB 的較小訊息，且個別地傳輸。 SDK 會處理訊息的分割和組合。 開發人員不需要投入這方面的人力。

訊息過大會對傳訊效能造成負面影響。 請盡可能使用較小的訊息，並經由測試來決定每個使用案例的的最佳訊息大小。

## <a name="how-messages-are-counted-for-billing"></a>如何計算訊息以供計費

為了計費，只會計算僅來自 Azure SignalR Service 的輸出訊息。 用戶端與伺服器之間的 Ping 訊息會被忽略。

系統會將大於 2 KB 的訊息計算為多則 2 KB 的訊息。 Azure 入口網站中的訊息計數圖表會在每個中樞達到 100 則訊息時更新一次。

例如，假設您有三個用戶端和一個應用程式伺服器。 一個用戶端傳送了一則 4 KB 的訊息，讓伺服器廣播到所有用戶端。 訊息計數為 8：有一則訊息從服務傳送至應用程式伺服器，三則訊息從服務傳送至用戶端。 每則訊息分別計為兩則 2 KB 的訊息。

在累計超過 100 之前，Azure 入口網站中顯示的訊息計數會保持為 0。

## <a name="how-connections-are-counted"></a>如何計算連線

連線分成伺服器連線和用戶端連線。 根據預設，每個應用程式伺服器的每個中樞有五個 Azure SignalR Service 的連線，而每個用戶端則有一個 Azure SignalR Service 的用戶端連線。

Azure 入口網站中顯示的連線計數包括伺服器連線和用戶端連線。

例如，假設您有兩個應用程式伺服器，並且在程式碼中定義了五個中樞。 伺服器連線計數會是 50：2 個應用程式伺服器 * 5 個中樞 * 每個中樞 5 個連線。

ASP.NET SignalR 會以不同的方式計算伺服器連線。 除了您定義的中樞以外，它還包含一個預設中樞。 根據預設，每個應用程式伺服器另需 5 個伺服器連線。 預設中樞的連線計數會與其他中樞保持一致。

## <a name="how-inboundoutbound-traffic-is-counted"></a>如何計算輸入/輸出流量

輸入流量與輸出流量之間的差別是從 Azure SignalR Service 的觀點來看。 流量會以位元組為單位計算。 如同訊息計數，流量也有取樣率。 Azure 入口網站中的輸入/輸出圖表會在每個中樞達到 100 KB 時更新一次。

## <a name="related-resources"></a>相關資源

- [Azure 監視器中的彙總類型](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR 組態](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)