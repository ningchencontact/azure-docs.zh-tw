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
ms.date: 08/14/2019
ms.author: magoedte
ms.openlocfilehash: f8d763f8bb228a0d4d83a3776f818d59939b942d
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559072"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>適用於容器的 Azure 監視器常見問題集

此 Microsoft 常見問題集是適用於容器的 Azure 監視器常見問題清單。 若您有任何關於解決方案的其他問題，請前往[討論論壇](https://feedback.azure.com/forums/34192--general-feedback)並張貼您的問題。 當問到常見問題時，我們會將其新增至此文章，以便其他人可以快速輕鬆地找到此問題。

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>我可以使用容器的 Azure 監視器來監視 AKS 引擎叢集嗎？

容器的 Azure 監視器支援監視部署至 Azure 上裝載的 AKS 引擎 (先前稱為 ACS 引擎) 叢集的容器工作負載。 如需進一步的詳細資料, 以及針對此案例啟用監視所需步驟的總覽, 請參閱針對[AKS 引擎的容器使用 Azure 監視器](https://github.com/microsoft/OMS-docker/tree/aks-engine)。

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>為什麼我在 Log Analytics 工作區中看不到資料？

如果您無法在每天的特定時間看到 Log Analytics 工作區中的任何資料, 您可能已達到預設的 500 MB 限制, 或指定每日上限來控制要每日收集的資料量。 一天符合限制時, 資料收集只會在隔天停止並繼續。 若要根據您預期的使用模式來檢查您的資料使用量, 並更新至不同的定價層, 請參閱[記錄資料使用量和成本](../platform/manage-cost-storage.md)。 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>ContainerInventory 資料表中指定的容器狀態為何？

ContainerInventory 資料表包含已停止和執行中容器的相關資訊。 此資料表由代理程式內的工作流程填入，該工作流程會查詢所有 (執行中和已停用) 容器的 Docker，並將該資料轉送到 Log Analytics 工作區。
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>如何? 解決*遺失的訂閱註冊*錯誤？

如果您收到**Microsoft.operationsmanagement 缺少訂閱註冊**的錯誤, 您可以在定義工作區的訂用帳戶中註冊**microsoft.operationsmanagement**資源提供者來解決此問題。 您可以在[這裡](../../azure-resource-manager/resource-manager-register-provider-errors.md)找到作法的相關文件。

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>是否支援已啟用 RBAC 的 AKS 叢集？

容器監視解決方案不支援 RBAC, 但支援容器的 Azure 監視器。 解決方案詳細資料頁面不會在顯示這些叢集資料的刀鋒視窗中顯示正確的資訊。

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>我如何透過 Helm 在 kube 系統命名空間中啟用容器的記錄檔收集？

kube 系統命名空間中容器的記錄檔收集預設為停用。 透過設定 omsagent 上的環境變數，可啟用記錄檔收集。 如需詳細資訊, 請參閱[適用于容器的 Azure 監視器](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers)GitHub 頁面。 

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

當您查詢記錄時, 這項資料看起來會像下列範例中的 Azure 監視器:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

如需此問題的詳細資訊, 請參閱下列[GitHub 連結](https://github.com/moby/moby/issues/22920)。

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>當我啟用即時記錄時, 如何? 解決 Azure AD 錯誤？ 

您可能會看到下列錯誤：**要求中指定的回復 url 不符合為應用程式設定的回復 url: ' < 應用程式識別碼\>'** 。 若要解決此問題, 您可以在[如何使用容器的 Azure 監視器來即時查看容器記錄](container-insights-live-logs.md#configure-aks-with-azure-active-directory)檔一文中找到此解決方案。 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>為什麼我在上架之後無法升級叢集？

如果您在啟用 AKS 叢集的容器 Azure 監視器之後, 刪除叢集傳送其資料的 Log Analytics 工作區, 當嘗試升級叢集時, 將會失敗。 若要解決此情況, 您必須停用監視, 然後重新啟用它參考您訂用帳戶中不同的有效工作區。 當您嘗試再次執行叢集升級時, 應該會處理並順利完成。  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>我需要針對代理程式開啟/列入白名單的埠和網域為何？
- *.ods.opinsights.azure.com   443
- *.oms.opinsights.azure.com   443
- *.blob.core.windows.net      443
- dc.services.visualstudio.com 443
- *. microsoftonline.com 443
- *. monitoring.azure.com 443
- login.microsoftonline.com 443

## <a name="next-steps"></a>後續步驟

若要開始監視您的 AKS 叢集，請檢閱[如何將適用於容器的 Azure 監視器上線](container-insights-onboard.md)，以了解啟用監視的需求和可用方法。 
