---
title: Azure Kubernetes Service 中支援的 Kubernetes 版本
description: 了解 Azure Kubernetes Service (AKS) 中叢集的 Kubernetes 版本支援原則和生命週期
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: 2d555908007f4e43a38b6d0eff909ef5050878ea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069666"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 中支援的 Kubernetes 版本

Kubernetes 社群大約每隔三個月就會發行次要版本。 這些版本均包含新功能和增強功能。 修補程式版本會更頻繁地發行 (有時每週)，而且僅適用於次要版本中的重要 Bug 修正。 這些修補程式版本包括對於安全性弱點或主要 Bug 的修正，這些安全性弱點或主要 Bug 會影響到根據 Kubernetes 在生產環境中執行的大量客戶和產品。

AKS 的目標是要認證和可能的版次的穩定性的上游發行的 30 天內發行新的 Kubernetes 版本。

## <a name="kubernetes-versions"></a>Kubernetes 版本

Kubernetes 使用標準[語意版本設定](https://semver.org/)版本設定配置。 這表示，每個版本的 Kubernetes 會遵循這個編號配置：

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
  1.13.7
```

在版本中的每個數字會指出與先前版本的一般相容性：

* 主要版本變更時不相容的 API 變更，或向後相容性可能會中斷。
* 進行回溯相容至其他的次要版本的功能變更時，就會變更次要版本。
* 修補程式的版本進行變更時與舊版相容的 bug 修正。

一般情況下，使用者應該儘可能執行它們執行，例如，如果您的生產環境叢集上的次要版本的最新修補程式版本*1.13.6*並*1.13.7*是最新可用的修補程式適用於版本*1.13*系列中，您應該升級至*1.13.7* ，您就可以確保您的叢集是完全經過修補與支援。

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支援原則

AKS 支援 Kubernetes 的四個次要版本：

* 目前的次要版本已發行在 AKS (N)
* 三個先前的次要版本。 每個支援的次要版本也支援兩個穩定的修補程式。

這稱為 「 N-3"-(N （最新版本）-3 （次要版本）)。

例如，如果導入了 AKS *1.13.x*今日，為提供的支援下列版本：

新的次要版本支援版本清單
-----------------        ----------------------
1.13.x 1.12.a、 1.12.b、 1.11.a、 1.11.b、 1.10.a、 1.10.b

其中"x"和".a"和"1).b 」 是具代表性的修補程式版本。

如需有關變更版本和預期的通訊的詳細資訊，請參閱 「 通訊 」 下方。

引入新的次要版本時，最舊次要版本和修補程式版本中已被取代，且移除。 例如，如果目前支援的版本清單為：

<a name="supported-version-list"></a>支援的版本清單
----------------------
1.12.a、 1.12.b、 1.11.a、 1.11.b、 1.10.a、 1.10.b、 1.9.a、 1.9.b

AKS 釋放 1.13.x，這表示將會移除 1.9.x 版本 （所有 1.9 版） 和不受支援。

> [!NOTE]
> 請注意，，是否客戶正在執行不受支援的 Kubernetes 版本，會要求他們時要求支援叢集升級。 執行不受支援的 Kubernetes 版本的叢集未涵蓋[AKS 是否支援原則](https://docs.microsoft.com/azure/aks/support-policies)。


除了上述項目上的次要版本，AKS 支援兩個最新*修補程式** 指定的次要版本的版本。 例如，假設下列支援的版本：

<a name="supported-version-list"></a>支援的版本清單
----------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5

如果上游 Kubernetes 發行 1.12.3 和 1.11.6 而且 AKS 釋放這些修補程式版本、 被取代最舊的修補程式版本，並將其移除，並支援的版本清單也會變成：

<a name="supported-version-list"></a>支援的版本清單
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*

### <a name="communications"></a>通訊

* 對新**次要**的 Kubernetes 版本
  * 所有使用者會收到都通知的新版本，而且將會移除哪一個版本。
  * 執行版本的客戶**遭到**將會通知他們有**60 天**升級至支援的版本 （例如次要版本）。
* 對新**修補程式**的 Kubernetes 版本
  * 正在發行的新修補程式版本和升級至最新的修補程式版本，則所有使用者會收到都通知。
  * 使用者擁有**30 天**升級至較新且支援的修補程式版本。 使用者擁有**30 天**之前移除最舊，升級至支援的修補程式版本。

AKS 會定義 「 發行 」 為正式運作後，啟用所有的 SLO / 品質的服務的度量，可在所有區域。

> [!NOTE]
> 新的版本，且棄用功能，次要的版本時已被取代/移除的使用者都升級至支援的版本各有 60 天，客戶會收到通知的 Kubernetes。 修補程式版本中，如果客戶有 30 天內升級至支援的版本。

透過傳送通知：

* [AKS 版本資訊](https://aka.ms/aks/releasenotes)
* Azure 入口網站通知
* [Azure 更新通道][azure-update-channel]

### <a name="policy-exceptions"></a>原則的例外狀況

AKS 會保留新增或移除新的/現有的版本已找出有影響的 bug 或安全性問題，而不需要事先通知的一或多個重要的生產的權限。

可能會略過特定的修補程式版本，或首展加速視錯誤或安全性問題的嚴重性而定。

### <a name="azure-portal-and-cli-default-versions"></a>Azure 入口網站和 CLI 預設版本

當您部署 AKS 叢集入口網站中，或使用 Azure CLI 時，叢集一律設為最新修補程式的 N-1 次要版本。 例如，如果支援 AKS *1.13.x*， *1.12.a* + *1.12.b*， *1.11.a*  +  *1.11.b*， *1.10.a* + *1.10b*，為新叢集的預設版本*1.12.b*。

AKS 會預設為 N-1 (minor.latestPatch，例如 1.12.b) 提供客戶已知，穩定，而且根據預設，修補版本。

## <a name="list-currently-supported-versions"></a>列出目前支援的版本

若要找出目前可供您訂用帳戶和區域使用的版本，請使用 [az aks get-versions][az-aks-get-versions] 命令。 下列範例會列出 EastUS  區域的可用 Kubernetes 版本：

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

如果您是在*n-4*版本，您的支援以外，並將要求升級。 如果成功從版本到 n-4 升級至 n-3，現在您已在我們的支援原則。 例如:

- 如果支援的版本的 AKS *1.13.x*， *1.12.a* + *1.12.b*， *1.11.c*  +  *1.11 d*，和*1.10.e* + *1.10f*上，而且*1.9.g*或是*1.9.h*，即支援之外。
- 如果從升級*1.9.g*或是*1.9.h*到*1.10.e* 或是*1.10.f*成功，您已回到我們的支援原則。

不支援升級到早於 *n-4* 的版本。 在這類情況下，我們建議客戶建立新的 AKS 叢集，並重新部署其工作負載。

**[支援 Out] 代表什麼意思**

'之外支援 表示您正在執行的版本超出支援的版本 清單中，而且系統會要求您要求支援時，將叢集升級至支援的版本。 此外，AKS 不提供任何執行階段或叢集支援的版本清單之外的其他保證。

**當客戶使用不支援的次要版本調整 Kubernetes 叢集時，會發生什麼事？**

對於 AKS 不支援的次要版本，相應縮小或放大都會繼續運作，而且不會產生任何問題。

**客戶可以永遠停留在某個 Kubernetes 版本嗎？**

是。 不過，如果叢集不在其中一個 AKS 所支援的版本中，叢集就會超出 AKS 支援原則。 Azure 不會自動升級您的叢集或刪除它。

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
