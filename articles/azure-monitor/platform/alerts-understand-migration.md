---
title: 了解如何自願性的警示移轉工具，在 Azure 監視器 works
description: 了解警示的移轉工具的運作方式，以及如果您遇到問題的疑難排解。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: a45a0cff606bc854924d5da0841b26e1cb9031bb
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632128"
---
# <a name="understand-how-the-migration-tool-works"></a>了解移轉工具的運作方式

作為[先前所宣布](monitoring-classic-retirement.md)，在 Azure 監視器傳統警示會在 2019 年 7 月即將淘汰。 主動觸發移轉的移轉工具可在 Azure 入口網站中，而且即將推出，客戶在使用傳統的警示規則。

這篇文章會引導您自願性移轉工具的運作方式。 此外，本文也將說明一些常見問題的補救的措施。

## <a name="which-classic-alert-rules-can-be-migrated"></a>可以移轉哪些傳統的警示規則？

雖然幾乎所有的傳統警示規則可以使用此工具可移轉，有一些例外狀況。 下列的警示規則不會移轉使用工具 （或在自動移轉，在 2019 年 7 月）

- 在 虛擬機器客體計量 （Windows 和 Linux） 上的傳統警示規則。 [請參閱有關如何重新建立這類警示的規則，在新的計量警示的指引](#guest-metrics-on-virtual-machines)
- 傳統對傳統儲存體度量的警示規則。 [請參閱指引，來監視您的傳統儲存體帳戶](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)
- 傳統上某些儲存體帳戶計量的警示規則。 [下面的詳細資料](#storage-account-metrics)

如果您的訂用帳戶有任何這類傳統規則，將會移轉其他規則，但這些規則將會需要以手動方式移轉。 因為我們無法提供的自動移轉，任何這類現有傳統計量警示會繼續努力將 2020 年 6 月提供您有時間將移至新的警示。 不過，任何新的傳統警示可不由 post 2019 年 6 月。

### <a name="guest-metrics-on-virtual-machines"></a>在虛擬機器上的客體計量

若要能夠建立新的計量警示上的客體計量，客體計量需要會傳送至 Azure 監視器的自訂計量存放區。 請遵循下列指示來啟用診斷設定的 Azure 監視器接收。

- [啟用適用於 Windows Vm 的客體計量](collect-custom-metrics-guestos-resource-manager-vm.md)
- [啟用適用於 Linux Vm 的客體計量](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)

完成上述步驟，就可以建立新的計量警示上的客體計量。 一旦您已重新建立新的計量警示，您就可以刪除傳統警示。

### <a name="storage-account-metrics"></a>儲存體帳戶計量

除了那些的下列度量的警示可移轉儲存體帳戶上的所有傳統警示：

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

傳統百分比度量的警示規則將會需要移轉根據[舊的和新的儲存體度量之間的對應](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)。 臨界值必須是適當地修改，因為新的計量是絕對的一個。

傳統 AnonymousThrottlingError 和 SASThrottlingError 必須分割成有兩個新警示的警示規則會提供相同的功能沒有合併的度量。 將會需要適當地調整閾值。

## <a name="roll-out-phases"></a>導入階段

移轉工具推出階段中使用傳統的警示規則的客戶。 **訂用帳戶擁有者**訂用帳戶已準備好使用此工具可移轉時，將會收到一封電子郵件。

> [!NOTE]
> 因為此工具推出階段，在早期階段，您可能會看到，這些訂用帳戶不是尚未準備好進行移轉。

目前**子集**的訂用帳戶，其中**只**傳統的警示規則會對下列資源類型會標示為準備好進行移轉。 即將推出階段中，會新增更多的資源類型的支援。

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

任何使用者都具有內建角色的**監視參與者**訂用帳戶層級將會觸發移轉。 具有下列權限的自訂角色的使用者也可以觸發移轉：

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-issues-and-remediations"></a>常見的問題及補救

您一次[觸發程序移轉](alerts-using-migration-tool.md)，我們會使用以通知您完成移轉的作業提供的電子郵件地址，或是否需要您採取動作。 下節描述一些常見的問題，以及如何予以修復

### <a name="validation-failed"></a>驗證失敗

由於傳統警示的規則，您的訂用帳戶中一些最新變更，因此無法移轉訂用帳戶。 這是暫時性的問題。 您可以之後重新啟動移轉的移轉狀態移回到**準備好進行移轉**幾天。

### <a name="policyscope-lock-preventing-us-from-migrating-your-rules"></a>原則/範圍鎖定，導致我們無法移轉您的規則

移轉的一部分，將會建立新的計量警示和新的動作群組，並 （之後會建立新的規則），便會刪除傳統的警示規則。 不過，沒有原則或範圍的鎖定，導致我們無法建立資源。 根據原則或範圍鎖定，無法移轉部分或所有的規則。 您可以藉由移除範圍鎖定/原則暫時解決此問題，並觸發一次移轉。

## <a name="next-steps"></a>後續步驟

- [如何使用移轉工具](alerts-using-migration-tool.md)
- [為移轉做準備](alerts-prepare-migration.md)
