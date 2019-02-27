---
title: Azure Kubernetes Service 中支援的 Kubernetes 版本
description: 了解 Azure Kubernetes Service (AKS) 中叢集的 Kubernetes 版本支援原則和生命週期
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 02/15/2018
ms.author: saudas
ms.openlocfilehash: 37a9712749a1575f81086d28ad461a665bef36d9
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313444"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 中支援的 Kubernetes 版本

Kubernetes 社群大約每隔三個月就會發行次要版本。 這些版本均包含新功能和增強功能。 修補程式版本會更頻繁地發行 (有時每週)，而且僅適用於次要版本中的重要 Bug 修正。 這些修補程式版本包括對於安全性弱點或主要 Bug 的修正，這些安全性弱點或主要 Bug 會影響到根據 Kubernetes 在生產環境中執行的大量客戶和產品。

第一天會在 [aks-engine][aks-engine] 中提供新的 Kubernetes 次要版本。 AKS 服務等級目標 (SLO) 會將目標設定為在 30 天內發行適用於 AKS 叢集的次要版本，具體取決於版本的穩定性。

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支援原則

AKS 支援 Kubernetes 的四個次要版本：

- 已發行上游 (n) 的目前次要版本
- 三個先前的次要版本。 每個支援的次要版本也支援兩個穩定的修補程式。

例如，如果 AKS 今天引入 *1.12.x*，也會針對 *1.11.a* + *1.11.b*、*1.10.c* + *1.10d*、*1.9.e* + *1.9f* (含有字母的修補程式版本是兩個最新穩定版的組建) 提供支援。

引入新的次要版本時，即會淘汰最舊的次要版本和所支援的修補程式版本。 在發行新的次要版本和即將淘汰版本的 15 天之前，會透過 [Azure 更新頻道][azure-update-channel]宣告。 在上述發行 *1.12.x* 的範例中，已淘汰的版本為 *1.8.g* + *1.8.h*。

當您在入口網站中或使用 Azure CLI 部署 AKS 叢集時，一律會將叢集設定為 n-1 次要版本和最新修補程式。 例如，如果 AKS 支援 *1.12.x*、*1.11.a* + *1.11.b*、*1.10.c* + *1.10d*、*1.9.e* + *1.9f*，新叢集的預設版本為 *1.10.b*。

## <a name="list-currently-supported-versions"></a>列出目前支援的版本

若要找出目前可供您訂用帳戶和區域使用的版本，請使用 [az aks get-versions][az-aks-get-versions] 命令。 下列範例會列出 EastUS 區域的可用 Kubernetes 版本：

```azurecli-interactive
az aks get-versions --location eastus --output table
```

輸出類似下列範例，其中顯示 Kubernetes 版本 *1.12.5* 是可用的最新版本：

```
KubernetesVersion    Upgrades
-------------------  -----------------------
1.12.5               None available
1.12.4               1.12.5
1.11.7               1.12.4, 1.12.5
1.11.6               1.11.7, 1.12.4, 1.12.5
1.10.12              1.11.6, 1.11.7
1.10.9               1.10.12, 1.11.6, 1.11.7
1.9.11               1.10.9, 1.10.12
1.9.10               1.9.11, 1.10.9, 1.10.12
```

## <a name="faq"></a>常見問題集

**當客戶使用不支援的次要版本升級 Kubernetes 叢集時，會發生什麼事？**

如果您是在 *n-4* 版本中，您就不會在 SLO 中。 如果從版本 n-4 成功升級到 n-3，則您會回到 SLO。 例如︰

- 如果支援的 AKS 版本為 *1.12.x*、*1.11.a* + *1.11.b*、*1.10.c* + *1.10d* 與 *1.9.e* + *1.9f*，而且您使用 *1.8.g* 或 *1.8.h*，您就不會在 SLO 中。
- 如果從 *1.8.g* 或 *1.8.h* 成功升級到 *1.9.e* 或 *1.9.f*，則您會回到 SLO。

不支援升級到早於 *n-4* 的版本。 在這類情況下，我們建議客戶建立新的 AKS 叢集，並重新部署其工作負載。

**當客戶使用不支援的次要版本調整 Kubernetes 叢集時，會發生什麼事？**

對於 AKS 不支援的次要版本，相應縮小或放大都會繼續運作，而且不會產生任何問題。

**客戶可以永遠停留在某個 Kubernetes 版本嗎？**

是。 不過，如果叢集不在 AKS 所支援的某個版本中，叢集就不會在 AKS SLO 中。 Azure 不會自動升級您的叢集或刪除它。

**如果代理程式叢集不在某個支援的 AKS 版本中，主要叢集會支援哪個版本？**

主要叢集會自動更新為最新支援版本。

## <a name="next-steps"></a>後續步驟

如需如何升級叢集的相關資訊，請參閱[升級 Azure Kubernetes Service (AKS) 叢集][aks-upgrade]。

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
