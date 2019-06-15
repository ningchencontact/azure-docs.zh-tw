---
title: 了解 Azure 監視器警示的自願性移轉工具的運作方式
description: 了解警示移轉工具的運作方式，並針對問題進行疑難排解。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 9d872a6d753a206dcfb03761e50e5854db4f146e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071598"
---
# <a name="understand-how-the-migration-tool-works"></a>了解移轉工具的運作方式

作為[先前所宣布](monitoring-classic-retirement.md)，即將在 2019 年 9 月淘汰 Azure 監視器中的傳統警示 (已原先於 2019 年 7 月)。 客戶使用傳統的警示規則的人員，而且想要自行觸發移轉至 Azure 入口網站中使用移轉工具。

這篇文章說明自願性移轉工具的運作方式。 此外，本文也將說明一些常見問題的補救方式。

> [!NOTE]
> 因為導入的移轉工具中的延遲，所以傳統警示移轉的停用日期已[延伸到 2019 年 8 月 31 日](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)從最初宣布於 2019 年 6 月 30 日的日期。

## <a name="which-classic-alert-rules-can-be-migrated"></a>可以移轉哪些傳統的警示規則？

雖然此工具可以移轉幾乎所有傳統警示規則，但有一些例外狀況。 使用工具 （或在自動移轉，在 2019 年 9 月），不會移轉下列的警示規則：

- 在 虛擬機器客體計量 （Windows 和 Linux） 上的傳統警示規則。 請參閱[指導方針來重新建立這類警示的規則，在新的計量警示](#guest-metrics-on-virtual-machines)本文稍後。
- 傳統對傳統儲存體度量的警示規則。 請參閱[監視您的傳統儲存體帳戶的指引](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)。
- 傳統上某些儲存體帳戶計量的警示規則。 請參閱[詳細資料](#storage-account-metrics)本文稍後。

如果您的訂用帳戶有任何這類傳統規則，您必須手動移轉。 我們無法提供的自動移轉，因為這些類型的任何現有、 傳統計量警示會繼續運作，直到 2020 年 6 月為止。 此延伸模組可讓您有時間將移至新的警示。 不過，可以在 2019 年 8 月之後不建立任何新的傳統警示。

### <a name="guest-metrics-on-virtual-machines"></a>在虛擬機器上的客體計量

您可以建立新的計量警示上的客體計量之前，客體計量必須傳送至 Azure 監視器的自訂計量存放區。 請遵循這些指示來啟用診斷設定的 Azure 監視器接收：

- [啟用適用於 Windows Vm 的客體計量](collect-custom-metrics-guestos-resource-manager-vm.md)
- [啟用適用於 Linux Vm 的客體計量](collect-custom-metrics-linux-telegraf.md)

完成這些步驟之後，您可以建立新的計量警示上的客體計量。 並建立新的計量警示之後，您可以刪除傳統警示。

### <a name="storage-account-metrics"></a>儲存體帳戶計量

除了這些度量的警示，可以移轉儲存體帳戶上的所有傳統警示：

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

百分比的計量規則必須移轉的傳統警示依據[舊的和新的儲存體度量之間的對應](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)。 需要因為可用的新計量是絕對適當地修改臨界值。

傳統的警示規則上 AnonymousThrottlingError、 SASThrottlingError 和 ThrottlingError 必須分割成兩個新的警示，因為沒有任何合併的度量會提供相同的功能。 將會需要適當地調整閾值。

## <a name="rollout-phases"></a>首度發行階段

移轉工具推出階段中使用傳統的警示規則的客戶。 訂用帳戶已準備好使用此工具進行移轉時，訂用帳戶擁有者會收到一封電子郵件。

> [!NOTE]
> 因為此工具推出階段中，您可能會看到該訂用帳戶的大部分還沒準備要移轉的先期階段。

目前的訂用帳戶的子集會標示為準備好進行移轉。 子集包含傳統的警示規則只對下列資源類型，這些訂用帳戶。 即將推出階段中，會新增更多的資源類型的支援。

- Microsoft.apimanagement/service
- Microsoft.batch/batchaccounts
- Microsoft.cache/redis
- Microsoft.datafactory/datafactories
- Microsoft.dbformysql/servers
- Microsoft.dbforpostgresql/servers
- Microsoft.eventhub/namespaces
- Microsoft.logic/workflows
- Microsoft.network/applicationgateways
- Microsoft.network/dnszones
- Microsoft.network/expressroutecircuits
- Microsoft.network/loadbalancers
- Microsoft.network/networkwatchers/connectionmonitors
- Microsoft.network/publicipaddresses
- Microsoft.network/trafficmanagerprofiles
- Microsoft.network/virtualnetworkgateways
- Microsoft.search/searchservices
- Microsoft.servicebus/namespaces
- Microsoft.streamanalytics/streamingjobs
- Microsoft.timeseriesinsights/environments
- Microsoft.web/hostingenvironments/workerpools
- Microsoft.web/serverfarms
- Microsoft.web/sites

## <a name="who-can-trigger-the-migration"></a>誰可以觸發移轉？

任何擁有訂用帳戶層級的內建的監視參與者 」 角色的使用者可以觸發移轉。 具有下列權限的自訂角色的使用者也可以觸發移轉：

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-problems-and-remedies"></a>常見的問題和解決方式

之後您[觸發程序移轉](alerts-using-migration-tool.md)，您會收到電子郵件到您提供給通知您移轉已完成，或您是否需要任何動作的位址。 本章節描述一些常見的問題，以及如何處理它們。

### <a name="validation-failed"></a>驗證失敗

由於傳統警示的規則，您的訂用帳戶中一些最新變更，因此無法移轉訂用帳戶。 這個問題是暫時性的。 移轉狀態回復之後，您可以重新啟動移轉**準備好進行移轉**幾天。

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>原則或範圍的鎖定，導致我們無法移轉您的規則

移轉的一部分，將會建立新的計量警示和新的動作群組，與傳統的警示規則將被刪除。 不過，沒有原則或範圍的鎖定，導致我們無法建立資源。 根據原則或範圍鎖定，無法移轉部分或所有的規則。 您可以解決這個問題，暫時移除範圍鎖定或原則，並觸發一次移轉。

## <a name="next-steps"></a>後續步驟

- [如何使用移轉工具](alerts-using-migration-tool.md)
- [為移轉做準備](alerts-prepare-migration.md)
