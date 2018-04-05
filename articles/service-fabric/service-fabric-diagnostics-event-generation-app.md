---
title: Azure Service Fabric 應用程式層級監視 | Microsoft Docs
description: 了解用來監視和診斷 Azure Service Fabric 叢集的應用程式與服務層級事件和記錄。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: 258aac722aa1c94ecf2cbf0524a3e4b53b8a788c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="application-and-service-level-logging"></a>應用程式和服務層級記錄

檢測程式碼是其他監視服務層面的基礎。 為了讓您知道有問題發生，並診斷有什麼需要修正，檢測是唯一方式。 雖然技術上可以將偵錯工具連線至生產服務，但這不是常見的做法。 因此，取得詳細的檢測資料很重要。

某些產品會自動檢測您的程式碼。 雖然這些產品的效果不錯，但幾乎都還是需要手動檢測。 最後您還是要有足夠資訊，才能以抽絲剝繭的方式對應用程式進行偵錯。 本文件說明檢測程式碼的不同方法，以及在何種情況下應選擇什麼方法。

## <a name="eventsource"></a>EventSource

當您在 Visual Studio 中從範本建立 Service Fabric 解決方案時，將會產生 **EventSource** 衍生類別 (**ServiceEventSource** 或 **ActorEventSource**)。 建立的範本可讓您為應用程式或服務新增事件。 **EventSource** 名稱**必須**是獨一無二，並應該將預設範本字串 MyCompany-&lt;solution&gt;-&lt;project&gt; 重新命名。 多個同名的 **EventSource** 定義會導致執行階段發生問題。 每個已定義事件都必須有獨一無二的識別碼。 如果識別碼並非獨一無二，會發生執行階段失敗。 有些組織會預先指派識別碼的值範圍，以避免不同開發小組之間用法不一致。 如需詳細資訊，請參閱 [Vance 的部落格](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)或 [MSDN 文件](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)。

## <a name="aspnet-core-logging"></a>ASP.NET Core 記錄

必須仔細規劃您檢測程式碼的方式。 正確規劃檢測有助於避免可能使程式碼基底不穩定，導致需要重新檢測程式碼。 為了降低風險，開發人員可以選擇檢測程式庫，例如 Microsoft ASP.NET Core 中的 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)。 ASP.NET Core 提供 [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) 介面，可搭配您選擇的提供者一起使用，讓現有程式碼所受的影響降到最低。 您可以在 Windows 和 Linux 上使用 ASP.NET Core 中的程式碼，也可以使用完整 .NET Framework 中的程式碼，使檢測程式碼標準化。

## <a name="choosing-a-logging-provider"></a>選擇記錄提供者

如果您的應用程式仰賴高效能，採用 **EventSource** 通常是不錯的方法。 **EventSource**「通常」耗用較少資源，效能優於 ASP.NET Core 記錄或任何可用的協力廠商解決方案。  這對於許多服務而言並不是問題，但如果您的服務屬於效能導向，使用 **EventSource** 可能是更好的選擇。 不過，採用 **EventSource** 時，工程團隊需要投注大量心力，才能發揮結構化記錄的優勢。 可能的話，請建構幾個記錄選項的概略雛形，然後選擇最符合需求的方法。

## <a name="next-steps"></a>後續步驟

在您選擇好要用來檢測應用程式和服務的記錄提供者之後，還需要彙總這些記錄和事件，才能將其傳送到任何分析平台。 請閱讀 [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) 和 [WAD](service-fabric-diagnostics-event-aggregation-wad.md) 以深入了解一些建議的選項。
