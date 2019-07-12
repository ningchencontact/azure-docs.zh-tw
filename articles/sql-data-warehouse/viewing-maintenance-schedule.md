---
title: Azure 維護排程 (預覽版) | Microsoft Docs
description: 維護排程可讓客戶規劃必要的排程維護事件，Azure SQL 資料倉儲服務會使用它們來推出新功能、升級與修補程式。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/27/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: f0b9b59ec09445a170d1f93181c2b432eafb60bf
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839991"
---
# <a name="view-a-maintenance-schedule"></a>檢視維護排程 

## <a name="portal"></a>入口網站

根據預設，所有新建立的 Azure SQL 資料倉儲執行個體都擁有已在部署期間套用的八小時主要和次要維護時段。 您可以在完成部署之後立即變更時段。 如果沒有事先通知，就不會在指定的維護時段以外進行維護。

若要檢視已套用到您資料倉儲的維護排程，請完成下列步驟：

1.  登入 [Azure 入口網站](https://portal.azure.com/)。
2.  選取您要檢視的資料倉儲。 
3.  所選取的資料倉儲即會在概觀刀鋒視窗上開啟。 維護排程套用至資料倉儲，請參閱下文**維護排程**。

![[概觀] 刀鋒視窗](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>後續步驟
- [深入了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage)如何使用 Azure 監視器來建立、檢視和管理警示。
- [深入了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)適用於記錄警示規則的 Webhook 動作。
- [深入了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)如何建立及管理動作群組。
- [深入了解](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure 服務健康狀態。


