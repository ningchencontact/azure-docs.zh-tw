---
title: 適用於容器的 Azure 監視器常見問題集 | Microsoft Docs
description: 適用於容器的 Azure 監視器是一種解決方案，可監視 Azure 中 AKS 叢集與容器執行個體的健康狀情況。 本文將回答常見問題。
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: afa332b40884a79b5114b3b8093cd27108c39984
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65780014"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>適用於容器的 Azure 監視器常見問題集

此 Microsoft 常見問題集是適用於容器的 Azure 監視器常見問題清單。 若您有任何關於解決方案的其他問題，請前往[討論論壇](https://feedback.azure.com/forums/34192--general-feedback)並張貼您的問題。 當問到常見問題時，我們會將其新增至此文章，以便其他人可以快速輕鬆地找到此問題。

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>為什麼沒有看到資料在我的 Log Analytics 工作區？

如果您看不到特定的時間每天在 Log Analytics 工作區中的任何資料時，您可能已達到預設 500 MB 的限制或控制的每日收集的資料量指定每日上限。 當一天達到上限時，資料收集會停止，而且只在下一步 繼續。 若要檢閱您的資料使用量，並更新至不同的定價層，根據您預期的使用模式，請參閱[記錄資料使用量和成本](../platform/manage-cost-storage.md)。 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>ContainerInventory 表格中所指定的容器狀態有哪些？

ContainerInventory 資料表包含已停止和執行中容器的相關資訊。 此資料表由代理程式內的工作流程填入，該工作流程會查詢所有 (執行中和已停用) 容器的 Docker，並將該資料轉送到 Log Analytics 工作區。
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>如何解決**遺漏訂用帳戶註冊**錯誤？

如果您收到錯誤**遺漏訂用帳戶註冊 Microsoft.OperationsManagement**，您可以註冊資源提供者來解決**Microsoft.OperationsManagement**中定義工作區所在的訂用帳戶。 您可以在[這裡](../../azure-resource-manager/resource-manager-register-provider-errors.md)找到作法的相關文件。

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>是否有支援 RBAC 啟用 AKS 叢集？

容器監視解決方案不支援 RBAC，但它使用適用於容器的 Azure 監視器支援。 解決方案詳細資料頁面不會在顯示這些叢集資料的刀鋒視窗中顯示正確的資訊。

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>我如何透過 Helm 在 kube 系統命名空間中啟用容器的記錄檔收集？

kube 系統命名空間中容器的記錄檔收集預設為停用。 透過設定 omsagent 上的環境變數，可啟用記錄檔收集。 如需詳細資訊，請參閱 <<c0> [ 適用於容器的 Azure 監視器](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers)GitHub 頁面。 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>如何將 omsagent 更新為最新的發行版本？

若要了解如何升級代理程式，請參閱[代理程式管理](container-insights-manage-agent.md)。

## <a name="how-do-i-enable-multi-line-logging"></a>如何啟用多行記錄？

適用於容器的 Azure 監視器目前不支援多行記錄，但有可用的因應措施。 您可以設定使用 JSON 格式寫入所有的服務，之後 Docker/Moby會將所有服務寫成一行。

例如，您可以將記錄檔包裝在 JSON 物件，如以下範例中範例 node.js 應用程式所示：

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

這項資料看起來像下列範例在 Azure 監視器中的記錄檔中，當您查詢：

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

如需此問題的詳細探討，請檢閱下列 [github 連結](https://github.com/moby/moby/issues/22920) \(英文\)。

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>當我啟用即時記錄檔時如何解決 Azure AD 錯誤？ 

您可能會看到下列錯誤：**回覆要求中指定的 url 不符合為應用程式設定的回覆 url: '< 應用程式識別碼\>'** 。 來解決此問題的解決方案可在發行項[如何檢視容器記錄檔使用即時 Azure 監視器容器](container-insights-live-logs.md#configure-aks-with-azure-active-directory)。 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>為什麼無法升級叢集上架後？

如果您啟用 Azure 監視器適用於 AKS 叢集中的容器之後，您刪除 Log Analytics 工作區的叢集中已傳送其資料，當您嘗試將叢集升級時它將會失敗。 若要解決這個問題，您必須停用監視，然後再重新啟用它參考不同的有效工作區，您的訂用帳戶中。 當您嘗試再次執行叢集升級時，應該處理並順利完成。  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>哪些連接埠及網域我要開啟/允許清單的代理程式？
- *.ods.opinsights.azure.com   443
- *.oms.opinsights.azure.com   443
- *.blob.core.windows.net      443
- dc.services.visualstudio.com 443

## <a name="next-steps"></a>後續步驟

若要開始監視您的 AKS 叢集，請檢閱[如何將適用於容器的 Azure 監視器上線](container-insights-onboard.md)，以了解啟用監視的需求和可用方法。 
