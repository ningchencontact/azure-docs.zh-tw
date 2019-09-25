---
title: Azure 資源記錄的總覽 |Microsoft Docs
description: 瞭解 Azure 資源記錄的支援服務和事件架構。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 09/20/2019
ms.author: robb
ms.subservice: logs
ms.openlocfilehash: bfcd2ded96c2679ba9177a760a8b11dc7d2c9a77
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262540"
---
# <a name="azure-resource-logs-overview"></a>Azure 資源記錄總覽
Azure 資源記錄是由 Azure 資源所發出的[平臺記錄](platform-logs-overview.md)，可描述其內部作業。 所有資源記錄都會共用通用的最上層架構，並具有每個服務的彈性，以便為自己的事件發出唯一的屬性。

> [!NOTE]
> 資源記錄先前稱為診斷記錄。

## <a name="collecting-resource-logs"></a>收集資源記錄
資源記錄會由支援的 Azure 資源自動產生，但除非您使用[診斷設定](diagnostic-settings.md)加以設定，否則不會進行收集。 為每個 Azure 資源建立診斷設定，以將記錄轉送至下列目的地：

| Destination | 狀況 |
|:---|:---|:---|
| [Log Analytics 工作區](resource-logs-collect-storage.md) | 分析具有其他監視資料的記錄，並利用 Azure 監視器的功能，例如記錄查詢和記錄警示。 |
| [Azure 儲存體](archive-diagnostic-logs.md) | 封存記錄以進行審核或備份。 |
| [事件中樞](resource-logs-stream-event-hubs.md) | 將記錄串流至協力廠商記錄和遙測系統。  |

## <a name="compute-resources"></a>計算資源
資源記錄與 Azure 計算資源中的來賓 OS 層級記錄不同。 計算資源需要代理程式從其來賓 OS 收集記錄和計量，包括事件記錄檔、syslog 和效能計數器等資料。 使用[診斷擴充](agents-overview.md#azure-diagnostic-extension)功能，從 azure 虛擬機器和[log analytics 代理程式](agents-overview.md#log-analytics-agent)路由記錄資料，以從 azure、其他雲端和內部部署中的虛擬機器收集記錄和計量到 log analytics 工作區。 如需詳細資訊，請參閱[監視資料的來源 Azure 監視器](data-sources.md)。

## <a name="resource-logs-schema"></a>資源記錄架構
當資源記錄寫入其中一個目的地時，每個 Azure 服務都有自己的架構，但它們全都共用下表中的最上層架構。 請參閱下方的[服務特定架構](#service-specific-schemas)，以取得每個服務的架構連結。 

| Name | 必要/選用 | 描述 |
|---|---|---|
| time | 必要項 | 事件的時間戳記 (UTC)。 |
| resourceId | 必要項 | 發出事件之資源的資源識別碼。 對於租用戶服務，這是 /tenants/tenant-id/providers/provider-name 的格式。 |
| tenantId | 租用戶記錄所需的 | 此事件所繫結 Active Directory 租用戶的租用戶識別碼。 這個屬性只能用於租用戶層級記錄，並不會出現在資源層級記錄中。 |
| operationName | 必要項 | 此事件所代表的作業名稱。 如果事件代表 RBAC 作業，則這是 RBAC 作業名稱 (例如 Microsoft.Storage/storageAccounts/BlobServices/Blobs/Read)。 通常以 Resource Manager 作業形式建模，即使它們不是實際記載的 Resource Manager 作業也是一樣 (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | 選擇性 | 與作業建立關聯的 api-version，如果使用 API 執行 operationName (例如 `http://myservice.windowsazure.net/object?api-version=2016-06-01`). 如果沒有對應至此作業的 API，則版本代表該作業的版本，以防與作業建立關聯的屬性在未來變更。 |
| category | 必要項 | 事件的記錄類別。 類別是您可在特定資源上啟用或停用記錄的資料粒度。 事件之屬性 Blob 內顯示的屬性，會與特定記錄類別和資源類型內的屬性相同。 典型記錄類別是「稽核」、「作業」、「執行」和「要求」。 |
| resultType | 選擇性 | 事件的狀態。 一般值包括︰「已啟動」、「進行中」、「成功」、「失敗」、「作用中」和「已解決」。 |
| resultSignature | 選擇性 | 事件的子狀態。 如果此作業對應至 REST API 呼叫，則這是對應 REST 呼叫的 HTTP 狀態碼。 |
| resultDescription | 選擇性 | 此作業的靜態文字描述，例如 “Get storage file.” |
| durationMs | 選擇性 | 作業的持續時間 (以毫秒為單位)。 |
| callerIpAddress | 選擇性 | 呼叫端 IP 位址，如果作業對應至來自具有公開可用 IP 位址之實體的 API 呼叫。 |
| correlationId | 選擇性 | 用來將一組相關事件群組在一起的 GUID。 一般而言，如果兩個事件具有相同 operationName 但具有兩個不同狀態 (例如 "Started" 和 "Succeeded")，則會共用相同的相互關聯識別碼。 這也可能代表事件之間的其他關聯性。 |
| 身分識別 | 選擇性 | JSON Blob，描述已執行作業之使用者或應用程式的身分識別。 這通常包括來自 Active Directory 的授權和宣告/JWT 權杖。 |
| 層級 | 選擇性 | 事件的嚴重性層級。 必須是「資訊」、「警告」、「錯誤」或「嚴重」中的其中一個。 |
| 位置 | 選擇性 | 發出事件之資源的區域，例如 「美國東部」或「法國南部」 |
| properties | 選擇性 | 任何與此特定事件類別相關的擴充屬性。 所有自訂/唯一屬性都必須放在結構描述的這個「部分 B」內。 |

## <a name="service-specific-schemas"></a>服務特定的架構
資源診斷記錄的架構視`resourceId`屬性所定義的資源類型`category`和屬性而定。 下列清單顯示所有支援資源記錄的 Azure 服務，並連結至服務和類別特定的架構（如果有的話）。

| 服務 | 結構描述與文件 |
| --- | --- |
| Azure Active Directory | [總覽](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)， [Audit 記錄架構和登](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md)[入架構](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API 管理 | [API 管理診斷記錄](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| 應用程式閘道 |[應用程式閘道的診斷記錄功能](../../application-gateway/application-gateway-diagnostics.md) |
| Azure 自動化 |[Azure 自動化的記錄檔分析](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 診斷記錄](../../batch/batch-diagnostics.md) |
| 適用於 MySQL 的 Azure 資料庫 | [適用於 MySQL 的 Azure 資料庫診斷記錄](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| 適用於 PostgreSQL 的 Azure 資料庫 | [適用於 PostgreSQL 的 Azure 資料庫診斷記錄](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| 認知服務 | [Azure 認知服務的診斷記錄](../../cognitive-services/diagnostic-logging.md) |
| 內容傳遞網路 | [CDN 的 Azure 診斷記錄](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB 記錄](../../cosmos-db/logging.md) |
| Data Factory | [使用 Azure 監視器來監視 Data Factory](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[存取 Azure Data Lake Analytics 的診斷記錄](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[存取 Azure Data Lake Store 的診斷記錄](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| 事件中樞 |[Azure 事件中樞診斷記錄](../../event-hubs/event-hubs-diagnostic-logs.md) |
| 快速路由 | 無法使用結構描述。 |
| Azure 防火牆 | 無法使用結構描述。 |
| IoT 中樞 | [IoT 中樞作業](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure 金鑰保存庫記錄](../../key-vault/key-vault-logging.md) |
| 負載平衡器 |[Azure 負載平衡器的 Log Analytics](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B 自訂追蹤結構描述](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 網路安全性群組 |[網路安全性群組 (NSG) 的記錄檔分析](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS 保護 | [管理 Azure DDoS Protection Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI 專用 | [Azure 中 Power BI Embedded 的診斷記錄](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Azure 備份的資料模型](../../backup/backup-azure-reports-data-model.md)|
| 搜尋 |[啟用和使用搜尋流量分析](../../search/search-traffic-analytics.md) |
| 服務匯流排 |[Azure 服務匯流排診斷記錄](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database 診斷記錄](../../sql-database/sql-database-metrics-diag-logging.md) |
| 串流分析 |[作業診斷記錄](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| 流量管理員 | [流量管理員記錄結構描述](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| 虛擬網路 | 無法使用結構描述。 |
| 虛擬網路閘道 | 無法使用結構描述。 |

## <a name="next-steps"></a>後續步驟

* 深入瞭解您可以用來監視 Azure 的[其他 azure 平臺記錄](platform-logs-overview.md)。
