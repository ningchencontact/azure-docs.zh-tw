---
title: 使用 Azure 入口網站檢視服務健康情況通知
description: 藉由服務健康情況通知，您可以檢視由 Microsoft Azure 發佈的服務健康情況訊息。
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: 8cf8c3eb86f55b5595ef9a1af83ea50580bf638b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069342"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>使用 Azure 入口網站檢視服務健康情況通知

Azure 基礎結構會發佈服務健康狀態通知。 它們包含訂用帳戶資源的相關資訊。 這些通知是活動的子類別記錄事件，並因此也可以找到活動記錄檔中。 根據不同類別，服務健康情況通知可以是告知性質或是可操作的性質。

如需有關各類別遙控器的服務健康情況通知的詳細資訊，請參閱[服務健康情況通知屬性](../../service-health/service-health-notifications-properties.md)。

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>在 Azure 入口網站中檢視您的服務健康情況通知

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [監視]  。

    ![Azure 入口網站功能表的螢幕擷取畫面與選取的監視器](./media/service-notifications/home-monitor.png)

    Azure 監視器會將您所有的監視設定和資料結合成一個彙總檢視。 它會先開啟 [活動記錄檔]  區段。

1. 選取 [警示]  。

    ![監視器活動記錄的螢幕擷取畫面與選取的警示](./media/service-notifications/service-health-summary.png)

1. 選取 [+新增活動記錄警示]  並設定警示，以確保您會在未來收到服務通知。 如需詳細資訊，請參閱[建立服務通知的活動記錄警示](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。

## <a name="next-steps"></a>後續步驟

* 每當[發佈服務健康情況通知時，即接收警示通知](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。  
* 深入了解[活動記錄警示](../../azure-monitor/platform/activity-log-alerts.md)。
