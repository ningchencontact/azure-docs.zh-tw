---
title: Azure 資源記錄的總覽 |Microsoft Docs
description: 瞭解 Azure 資源記錄的支援服務和事件架構。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/22/2019
ms.openlocfilehash: e1bf6a55568671ddb8f6999356cc9be43ce6a728
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804018"
---
# <a name="azure-resource-logs-overview"></a>Azure 資源記錄總覽
Azure 資源記錄是由 Azure 資源所發出的[平臺記錄](platform-logs-overview.md)，可描述其內部作業。 所有資源記錄都會共用通用的最上層架構，並具有每個服務的彈性，以便為自己的事件發出唯一的屬性。

> [!NOTE]
> 資源記錄先前稱為診斷記錄。

## <a name="collecting-resource-logs"></a>收集資源記錄
資源記錄會由支援的 Azure 資源自動產生，但除非您使用[診斷設定](diagnostic-settings.md)加以設定，否則不會進行收集。 為每個 Azure 資源建立診斷設定，以將記錄轉送至下列目的地：

| 目的地 | 案例 |
|:---|:---|:---|
| [Log Analytics 工作區](resource-logs-collect-storage.md) | 分析具有其他監視資料的記錄，並利用 Azure 監視器的功能，例如記錄查詢和記錄警示。 |
| [Azure 儲存體](archive-diagnostic-logs.md) | 封存記錄以進行審核或備份。 |
| [事件中樞](resource-logs-stream-event-hubs.md) | 將記錄串流至協力廠商記錄和遙測系統。  |

## <a name="compute-resources"></a>計算資源
資源記錄與 Azure 計算資源中的來賓 OS 層級記錄不同。 計算資源需要代理程式從其來賓 OS 收集記錄和計量，包括事件記錄檔、syslog 和效能計數器等資料。 使用[診斷擴充](agents-overview.md#azure-diagnostic-extension)功能，從 azure 虛擬機器和[log analytics 代理程式](agents-overview.md#log-analytics-agent)路由記錄資料，以從 azure、其他雲端和內部部署中的虛擬機器收集記錄和計量到 log analytics 工作區。 如需詳細資訊，請參閱[監視資料的來源 Azure 監視器](data-sources.md)。

## <a name="resource-logs-schema"></a>資源記錄架構
如需資源記錄架構和類別的詳細資訊，請參閱[資源記錄架構](diagnostic-logs-schema.md)。 

## <a name="next-steps"></a>後續步驟

* 深入瞭解您可以用來監視 Azure 的[其他 azure 平臺記錄](platform-logs-overview.md)。
