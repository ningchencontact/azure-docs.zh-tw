---
title: Azure Kubernetes Service 中支援的 Kubernetes 版本
description: 了解 Azure Kubernetes Service (AKS) 中叢集的 Kubernetes 版本支援原則和生命週期
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: a7b3176e39ccaa0f9ddb1ef45c33ec6902e62f1c
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956318"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 中支援的 Kubernetes 版本

Kubernetes 社群大約每隔三個月就會發行次要版本。 這些版本均包含新功能和增強功能。 修補程式版本會更頻繁地發行 (有時每週)，而且僅適用於次要版本中的重要 Bug 修正。 這些修補程式版本包括對於安全性弱點或主要 Bug 的修正，這些安全性弱點或主要 Bug 會影響到根據 Kubernetes 在生產環境中執行的大量客戶和產品。

第一天會在 [aks-engine][aks-engine] 中提供新的 Kubernetes 次要版本。 AKS 服務等級目標 (SLO) 會將目標設定為在 30 天內發行適用於 AKS 叢集的次要版本，具體取決於版本的穩定性。

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支援原則

AKS 支援 Kubernetes 的四個次要版本：

- 已發行上游 (n) 的目前次要版本
- 三個先前的次要版本。 每個支援的次要版本也支援兩個穩定的修補程式。

例如，如果導入了 AKS *1.13.x*今天，也提供支援*1.12.a* + *1.12.b*， *1.11.c*  + *1.11 d*， *1.10.e* + *1.10f* （字母的修補程式版本是兩個最新穩定的組建）。

引入新的次要版本時，即會淘汰最舊的次要版本和所支援的修補程式版本。 30 天前的版本新的次要版本與即將推出的版本淘汰公告透過進行[Azure 的更新管道][azure-update-channel]。 在上例中何處*1.13.x*是發行時，已淘汰的版本會*1.9.g* + *1.9.h*。

當您在入口網站中或使用 Azure CLI 部署 AKS 叢集時，一律會將叢集設定為 n-1 次要版本和最新修補程式。 例如，如果支援 AKS *1.13.x*， *1.12.a* + *1.12.b*， *1.11.c*  +  *1.11 d*， *1.10.e* + *1.10f*，新叢集的預設版本是否*1.11.b*。

## <a name="list-currently-supported-versions"></a>列出目前支援的版本

若要找出目前可供您訂用帳戶和區域使用的版本，請使用 [az aks get-versions][az-aks-get-versions] 命令。 下列範例會列出 EastUS 區域的可用 Kubernetes 版本：

```azurecli-interactive
az aks get-versions --location eastus --output table
```

輸出會類似下列的範例中，會顯示該 Kubernetes 版本*1.13.5*可用的最新版本：

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.13.5               None available
1.12.7               1.13.5
1.12.6               1.12.7, 1.13.5
1.11.9               1.12.6, 1.12.7
1.11.8               1.11.9, 1.12.6, 1.12.7
1.10.13              1.11.8, 1.11.9
1.10.12              1.10.13, 1.11.8, 1.11.9
```

## <a name="faq"></a>常見問題集

**當客戶使用不支援的次要版本升級 Kubernetes 叢集時，會發生什麼事？**

如果您是在 *n-4* 版本中，您就不會在 SLO 中。 如果從版本 n-4 成功升級到 n-3，則您會回到 SLO。 例如：

- 如果支援的版本的 AKS *1.13.x*， *1.12.a* + *1.12.b*， *1.11.c*  +  *1.11 d*，和*1.10.e* + *1.10f*上，而且*1.9.g*或是*1.9.h*，您不在 SLO。
- 如果從升級*1.9.g*或是*1.9.h*到*1.10.e* 或是*1.10.f*成功，您已回到 SLO。

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
