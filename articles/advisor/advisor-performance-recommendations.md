---
title: Azure 建議程式效能建議 | Microsoft Docs
description: 使用 Advisor 將 Azure 部署的效能最佳化。
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 3331c795cbb1c45820d4c86d287ef57b54f0ae6b
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247636"
---
# <a name="advisor-performance-recommendations"></a>建議程式效能建議

Advisor 效能建議有助於提升業務關鍵應用程式的速度和回應能力。 您可以在 Advisor 儀表板的 [效能] 索引標籤上，取得 Advisor 的效能建議。

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>減少 DNS 在流量管理員設定檔上的存留時間，以更快速容錯移轉至健康情況良好的端點上

流量管理員設定檔上的[存留時間 (TTL) 設定](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations)可讓您在指定端點停止回應查詢時，指定切換至端點的速度。 減少 TTL 值表示用戶端將會更快速路由傳送至運作的端點。

Azure Advisor 會識別設定較長 TTL 的流量管理員設定檔，並且建議將 TTL 設定為 20 秒或 60 秒，取決於是否設定[快速容錯移轉](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)的設定檔。

## <a name="improve-database-performance-with-sql-db-advisor"></a>使用 SQL DB Advisor 來改善資料庫效能

建議程式可針對所有的 Azure 資源提供一致的合併建議檢視。 它會與 SQL Database 建議程式整合，以提供改善 SQL Azure 資料庫效能的相關建議。 SQL Database 建議程式會藉由分析您的使用歷程記錄來評估 SQL Azure 資料庫的效能。 接著會提供最適合用於執行資料庫之一般工作負載的建議事項。 

> [!NOTE]
> 若要取得建議，資料庫必須持續使用一週，而且那一週之內必須有一些一致的活動。 相較於隨機蹦出的活動，一致的查詢模式更有利於 SQL Database Advisor 最佳化。

如需 SQL Database Advisor 的詳細資訊，請參閱 [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)。

## <a name="improve-redis-cache-performance-and-reliability"></a>改善 Redis 快取的效能和可靠性

Advisor 會識別高記憶體使用量、伺服器負載、網路頻寬或大量用戶端連線會對其效能造成負面影響的 Redis 快取執行個體。 Advisor 也提供最佳做法建議來協助您避免潛在的問題。 如需 Redis 快取建議的詳細資訊，請參閱 [Redis 快取建議程式](https://azure.microsoft.com/documentation/articles/cache-configure/#redis-cache-advisor)。


## <a name="improve-app-service-performance-and-reliability"></a>改善 App Service 的效能和可靠性

Azure 建議程式整合了最佳作法建議，以供提升應用程式服務體驗和探索相關的平台功能。 應用程式服務建議的範例如下︰
* 偵測應用程式執行階段與緩和選項已耗盡記憶體或 CPU 資源的執行個體。
* 偵測共置資源 (如 Web 應用程式和資料庫) 可改善效能並降低成本的執行個體。 

如需應用程式服務建議的詳細資訊，請參閱 [Azure App Service 的最佳作法](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)。

## <a name="how-to-access-performance-recommendations-in-advisor"></a>如何在建議程式中存取效能建議

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

2.  在 Advisor 儀表板上，按一下 [效能] 索引標籤。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：

* [Advisor 簡介](advisor-overview.md)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 成本建議](advisor-performance-recommendations.md)
* [Advisor 高可用性建議](advisor-high-availability-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)

