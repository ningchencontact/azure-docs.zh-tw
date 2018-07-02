---
title: 什麼是 Azure SignalR | Microsoft Docs
description: Azure SignalR 服務的概觀。
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/17/2018
ms.author: wesmc
ms.openlocfilehash: bc144fb1d7db9251871e7e181b012417a32de7e6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2018
ms.locfileid: "33868104"
---
# <a name="what-is-azure-signalr-service"></a>什麼是 Azure SignalR 服務

Microsoft Azure SignalR 服務目前處於[公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)狀態。

Azure SignalR 服務是以 [ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) 為基礎的 Azure 服務。 ASP.NET Core SignalR 是一個[開放原始碼程式庫](https://github.com/aspnet/signalr)，可以簡化透過 HTTP 將即時 Web 功能新增到應用程式的流程。 此即時功能可讓 Web 伺服器將內容更新推送至已連線的用戶端。 因此，用戶端可以更新而不需要輪詢伺服器或送出新的 HTTP 更新要求。

本文章提供 Azure SignalR 服務的概觀。 如果您要開始著手，建議從 [ASP.NET Core 快速入門](signalr-quickstart-dotnet-core.md)開始。

## <a name="what-is-signalr-service-used-for"></a>SignalR 服務的作用為何？ 

許多應用程式類型都需要即時內容更新。 以下的範例應用程式類型適合使用 Azure SignalR 服務：

* 需要經常從伺服器取得更新的應用程式。 例如遊戲、社交網路、投票、拍賣、地圖和 GPS 應用程式。
* 儀表板和監視應用程式。 範例包括公司儀表板、即時銷售更新或旅行警示。
* 共同作業應用程式。 共同作業應用程式的範例包括白板應用程式和小組會議軟體。
* 需要通知的應用程式。 社交網路、電子郵件、交談、遊戲、旅行警示和其他使用通知的應用程式。

在內部中，SignalR 是建立即時 Web 應用程式所用數種技術的抽象表示。 [WebSockets](https://wikipedia.org/wiki/WebSocket) 是最佳的傳輸，但是當其他選項無法使用時會使用 [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) 和「長輪詢」等技術。 SignalR 會根據伺服器和用戶端支援的功能，自動偵測和初始化適當的傳輸。

## <a name="developing-signalr-apps"></a>開發 SignalR 應用程式

目前，有兩個版本的 SignalR 可供您搭配 Web 應用程式使用：「適用於 ASP.NET 的 SignalR」和「ASP.NET Core SignalR」，後者為最新版本。 Azure SignalR 服務是建置在 ASP.NET Core SignalR 上的 Azure 受控服務。 

ASP.NET Core SignalR 是前一個版本重新撰寫的版本。 因此，ASP.NET Core SignalR 與先前版本的 SignalR 不回溯相容。 API 和行為不同。 ASP.NET Core SignalR SDK 是 .NET Standard，所以您仍然可以搭配 .NET Framework 使用它。 不過，您必須使用新的 API，而不是舊的。 如果您是使用 SignalR，且想要移至 ASP.NET Core SignalR 或 Azure SignalR 服務，則必須變更程式碼來處理 API 中的差異。

使用 Azure SignalR 服務時，伺服器端的 ASP.NET Core SignalR 元件是裝載在 Azure 中。 不過，因為該技術是建置在 ASP.NET Core 之上，所以您能夠在多個平台上 (Windows、Linux 和 MacOS) 執行實際的 Web 應用程式，同時使用 [Azure App Service](../app-service/app-service-web-overview.md)、[IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index)、[Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx)、[Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache)、[Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index) 裝載。 您也可以在自己的處理序中使用自我裝載。

如果您應用程式的目標包括：支援使用即時內容更新以更新 Web 用戶端的最新功能、跨多個平台 (Azure、Windows、Linux 和 MacOS) 執行，以及裝載於不同的環境中，則 Azure SignalR 服務就是您可以利用的最佳選擇。


## <a name="why-not-deploy-signalr-myself"></a>為何不自行部署 SignalR？

自行部署支援 ASP.NET Core SignalR 的 Azure 應用程式，作為您整體 Web 應用程式的後端元件仍是有效的方法。

使用 Azure SignalR 服務的其中一個主要原因是簡單。 使用 Azure SignalR 服務，您不需要處理效能、延展性、可用性等問題。 該服務以 99.9% 的服務等級協定為您處理這些問題。

此外，WebSocket 通常是支援即時內容更新所偏好使用的技術。 不過，隨著您調整，對大量持續的 WebSocket 連線進行負載平衡變成要解決的複雜問題。 常見的解決方案利用：DNS 負載平衡、硬體負載平衡器和軟體負載平衡。 Azure SignalR 服務會為您處理此問題。

另一個原因可能是您完全不需要實際上裝載一個 Web 應用程式。 您 Web 應用程式的邏輯可能會利用[無伺服器運算](https://azure.microsoft.com/overview/serverless-computing/)。 舉例來說，或許您的程式碼只在需要時才使用 [Azure 函式](https://docs.microsoft.com/azure/azure-functions/)觸發程序裝載和執行。 這種情況可能變得棘手，因為您的程式碼在需要時才執行，而且不需要維持長時間用戶端連線。 Azure SignalR 服務可以處理這種情況，因為該服務已經為您管理連線。

## <a name="how-does-it-scale"></a>它如何調整？

SignalR 通常隨 SQL Server、Azure 服務匯流排或 Redis 快取調整。 Azure SignalR 服務會為您處理調整方式。 效能和成本與這些方法相當，但沒有處理這些其他服務的複雜性。 您只需要更新服務的單位計數即可。 每個服務單位最高支援 1000 個用戶端連線。

## <a name="next-steps"></a>後續步驟
* [快速入門：使用 Azure SignalR 建立聊天室](signalr-quickstart-dotnet-core.md)  
  

