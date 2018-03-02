---
title: "將記錄從 Azure 資源整合到 SIEM 系統 | Microsoft Docs"
description: "了解 Azure 記錄整合、其主要功能及運作方式。"
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: e427e2f7dafb6db9bc5c15d841fbbf83a02fc0e1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2018
---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Microsoft Azure 記錄整合簡介

在尚無法從您的 SIEM 廠商取得連接 [Azure 監視器](../monitoring-and-diagnostics/monitoring-get-started.md)連接器的情況下，Azure 記錄整合可讓您將來自您的 Azure 資源的原始記錄與內部部署安全性資訊和事件管理 (SIEM) 系統整合。

整合 Azure 記錄檔的慣用方法是使用 SIEM 廠商的 Azure 監視器連接器，並遵循這些[指示](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)。 不過，如果您的 SIEM 廠商沒有提供 Azure 監視器連接器，在這類連接器可供使用之前，您可以使用 Azure 記錄整合作為暫時解決方案 (如果 Azure 記錄整合支援您的 SIEM)。

>[!IMPORTANT]
>如果您的主要興趣在於收集虛擬機器記錄檔，大多數 SIEM 廠商會將此包含在其解決方案中。 使用 SIEM 廠商的連接器應永遠為慣用的替代做法。

Azure 記錄整合會從 Windows 事件檢視器記錄收集 Windows 事件，從 Azure 資源收集 [Azure 活動記錄](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)、[Azure 資訊安全中心警示](../security-center/security-center-intro.md)和 [Azure 診斷記錄](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)。 這項整合協助您的 SIEM 方案提供內部部署或在雲端中所有資產統一的儀表板，以便您彙總、相互關聯、分析和警示安全性事件。

>[!NOTE]
目前唯一支援的雲端是 Azure 商業和 Azure Government。 不支援其他雲端。

![Azure 記錄整合][1]

## <a name="what-logs-can-i-integrate"></a>可以整合哪些記錄檔？

Azure 會為每項 Azure 服務產生大量記錄。 這些記錄檔表示三種類型的記錄檔︰

* **控制/管理記錄檔**：可讓您看到 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 的 CREATE、UPDATE 和 DELETE 作業。 Azure 活動記錄是本類型的一個範例。
* **資料平面記錄檔**：可讓您看到使用 Azure 資源時所引發的事件。 本類型的記錄檔範例是 Windows 事件檢視器**系統**、**安全性**和 Windows 虛擬機器中的**應用程式**頻道。 另一個範例是透過 Azure 監視設定的診斷記錄
* **處理事件**提供分析的事件與以您的名義處理的警示資訊。 本事件類型的範例是 Azure 安全性中心警示，其中 Azure 資訊安全中心已處理並分析您的訂用帳戶，以提供與您目前安全性狀態相關的警示。

Azure 記錄整合支援 ArcSight、QRadar 及 Splunk。 不論在何種情況下，請向您的 SIEM 廠商確認，以了解對方是否有原生的連接器。 有原生連接器可供使用時，您就不應使用 Azure 記錄整合。

如果沒有其他可用選項，則可以考慮使用 Azure 記錄整合。 下表包含我們的建議。

|**SIEM** | **客戶已在使用記錄整合器** | **客戶正在調查 SIEM 整合選項**|
|---------|--------------------------|-------------------------------------------|
|**SPLUNK** | 開始移轉到 [Splunk 的 Azure 監視器附加元件](https://splunkbase.splunk.com/app/3534/) | 使用 [SPLUNK 連接器](https://splunkbase.splunk.com/app/3534/) |
|**IBM QRADAR** | 移轉至或開始使用 http://aka.ms/azmoneventhub 結尾記載的 QRadar 連接器 | 使用 http://aka.ms/azmoneventhub 結尾記載的 QRadar 連接器  |
|**ARCSIGHT** | 繼續使用連接器，直到有可用的記錄整合器為止，然後移轉至以連接器為基礎的解決方案。  | 考慮使用 Azure Log Analytics 作為替代方案。 除非您願意在連接器可供使用時完成移轉程序，否則請不要上架到 Azure 記錄整合。 |

>[!NOTE]
>雖然 Azure 記錄整合是免費的解決方案，記錄檔資訊的儲存體仍會產生 Azure 儲存體成本。

如果需要協助，您可以開啟[支援要求](../azure-supportability/how-to-create-azure-support-request.md)。 若要這樣做，請選取 [記錄整合] 作為您要求支援的服務。

## <a name="next-steps"></a>後續步驟

在本文中為您介紹了 Azure 記錄整合。 若要深入了解 Azure 記錄整合和支援的記錄檔的類型，請參閱下列各項︰

* [開始使用 Azure 記錄整合](security-azure-log-integration-get-started.md) - 本教學課程將逐步引導您安裝 Azure 記錄整合，和來自 Azure WAD 儲存體、Azure 活動記錄、Azure 資訊安全中心警示以及 Azure Active Directory 稽核記錄檔。
* [Azure 記錄整合常見問題集 (FAQ)](security-azure-log-integration-faq.md) - 此常見問題集會回答有關 Azure 記錄整合的問題。
* [將 Azure 監視資料串流至事件中樞以供外部工具取用](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
