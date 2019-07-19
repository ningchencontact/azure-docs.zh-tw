---
title: Azure Kubernetes Service 中支援的 Kubernetes 版本
description: 了解 Azure Kubernetes Service (AKS) 中叢集的 Kubernetes 版本支援原則和生命週期
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: a4726444e2103228cd26be3d3a6d516b31e315ee
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304320"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 中支援的 Kubernetes 版本

Kubernetes 社群大約每隔三個月就會發行次要版本。 這些版本均包含新功能和增強功能。 修補程式版本會更頻繁地發行 (有時每週)，而且僅適用於次要版本中的重要 Bug 修正。 這些修補程式版本包括對於安全性弱點或主要 Bug 的修正，這些安全性弱點或主要 Bug 會影響到根據 Kubernetes 在生產環境中執行的大量客戶和產品。

AKS 的目標是在上游發行的30天內認證併發行新的 Kubernetes 版本, 受限於版本的穩定性。

## <a name="kubernetes-versions"></a>Kubernetes 版本

Kubernetes 使用標準的[語義版本](https://semver.org/)控制版本設定配置。 這表示每個版本的 Kubernetes 都會遵循此編號配置:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
  1.13.7
```

版本中的每個數位都表示與先前版本的一般相容性:

* 當不相容的 API 變更或回溯相容性可能中斷時, 主要版本會變更。
* 次要版本會在對其他次要版本回溯相容的功能變更時變更。
* 當發生回溯相容的 bug 修正時, 修補程式版本會變更。

一般情況下, 使用者應該投入執行中次要版本的最新修補程式版本, 例如, 如果您的生產叢集在*1.13.6* , 而*1.13.7*是適用于*1.13*系列的最新可用修補程式版本,只要能夠確保您的叢集受到完整修補和支援, 您就應該升級至*1.13.7* 。

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支援原則

AKS 支援 Kubernetes 的四個次要版本：

* 在 AKS (N) 中發行的目前次要版本
* 三個先前的次要版本。 每個支援的次要版本也支援兩個穩定的修補程式。

這就是所謂的「N-3」-(N (最新版本)-3 (次要版本))。

例如, 如果 AKS 今天引進*1.13. x* , 則會提供下列版本的支援:

新的次要版本    |    支援的版本清單
-----------------    |    ----------------------
1.13. x               |    1.12. a、1.12. b、1.11. a、1.11. b、1.10. a、1.10. b

其中 "x" 和 ". a" 和 ". b" 是代表性的修補程式版本。

如需有關版本變更和預期的通訊詳細資料, 請參閱下面的「通訊」。

引進新的次要版本時, 最舊的次要版本和支援的修補程式版本會被取代並移除。 例如, 如果目前支援的版本清單為:

```
1.12.a
1.12.b
1.11.a
1.11.b
1.10.a
1.10.b
1.9.a
1.9.b
```

而 AKS 會發行 1.13. x, 這表示將會移除 1.9. x 版 (所有1.9 版本), 且不支援。

> [!NOTE]
> 請注意, 如果客戶執行不受支援的 Kubernetes 版本, 系統會要求他們在要求叢集的支援時進行升級。 [AKS 支援原則](https://docs.microsoft.com/azure/aks/support-policies)不會涵蓋執行不受支援 Kubernetes 版本的叢集。


除了上述次要版本以外, AKS 還支援指定次要版本的兩個最新*修補程式** 版本。 例如, 假設有下列支援的版本:

```
Current Supported Version List
------------------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5
```

如果上游 Kubernetes 已發行 1.12.3, 而1.11.6 和 AKS 發行了這些修補程式版本, 則最舊的修補程式版本會被取代並移除, 而且支援的版本清單會變成:

```
New Supported Version List
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*
```

> [!NOTE]
> 客戶不應該將叢集建立、CI 或其他自動化作業釘選到特定的修補程式版本。 

### <a name="communications"></a>通訊

* 適用于 Kubernetes 的新**次要**版本
  * 所有使用者都會在新版本中公開通知, 而將移除哪個版本。
  * 發行新的修補程式版本時, 會同時移除最舊的修補程式發行。
  * 客戶從公開通知日期起算**60 天**, 以升級至支援的次要版本發行。
* 針對 Kubernetes 的新**修補程式**版本
  * 系統會通知所有使用者發行的新修補程式版本, 並升級至最新的修補程式版本。
  * 使用者有**30 天**的時間可以升級至較新的受支援修補程式版本。 在移除最舊的之前, 使用者有**30 天**的時間可以升級至支援的修補程式版本。

AKS 會將「已發行」定義為正式運作, 並在所有 SLO/服務品質測量中啟用, 並在所有區域中提供。

> [!NOTE]
> 當次要版本已被取代/移除時, 客戶會收到 Kubernetes 版本和棄用功能的通知。升級至支援的版本時, 會有60天的使用者。 在修補程式發行版本的情況下, 客戶會獲得30天的時間來升級至支援的版本。

通知會透過下列方式傳送:

* [AKS 版本資訊](https://aka.ms/aks/releasenotes)
* Azure 入口網站通知
* [Azure 更新通道][azure-update-channel]

### <a name="policy-exceptions"></a>原則例外狀況

AKS 保留新增或移除已識別為有一或多個重大生產影響 bug 或安全性問題的權利, 而不會事先通知。

視錯誤或安全性問題的嚴重性而定, 可能會略過特定的修補程式版本或首度發行加速。

### <a name="azure-portal-and-cli-default-versions"></a>Azure 入口網站和 CLI 預設版本

當您在入口網站或 Azure CLI 中部署 AKS 叢集時, 叢集一律會設定為 N-1 次要版本和最新的修補程式。 例如, 如果 AKS 支援*1.13. x*, *1.12*  + .    + 1.12. b, *1.11*. a*1.11. b*, *1.10*  +  *b*, 新叢集的預設版本為*1.12. b*.

AKS 預設為 N-1 (次要. latestPatch, 例如 1.12. b), 為客戶提供已知、穩定且已修補的版本。

## <a name="list-currently-supported-versions"></a>列出目前支援的版本

若要找出您的訂用帳戶和區域目前可用的版本, 請使用[az aks get 版本][az-aks-get-versions]命令。 下列範例會列出 EastUS  區域的可用 Kubernetes 版本：

```azurecli-interactive
az aks get-versions --location eastus --output table
```

輸出類似下列範例, 其中顯示 Kubernetes 版本*1.13.5*是可用的最新版本:

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

如果您是在*n-4*版本, 則不受支援, 將會要求您進行升級。 如果您從版本 n-4 升級至 n-3 成功, 則您現在已在我們的支援原則中。 例如:

- 如果支援的 AKS 版本為*1.13. x*、 *1.12. a*  +  *1.12. b*、 *1.11. c*  +  *1.11 d*和*1.10. e* + *1.10 f* , 而且您在*1.9. g*或*1.9. h*上, 您不在支援範圍內。
- 如果從*1.9. g*或 1.9. *h*升級至*1.10. e* 或*1.10. f*成功, 您會回到我們的支援原則中。

不支援升級到早於 *n-4* 的版本。 在這類情況下，我們建議客戶建立新的 AKS 叢集，並重新部署其工作負載。

**「不支援」的意義**

「不支援」表示您正在執行的版本不在支援的版本清單中, 而且在要求支援時, 系統會要求您將叢集升級為支援的版本。 此外, AKS 不會對支援的版本清單以外的叢集進行任何執行時間或其他保證。

**當客戶使用不支援的次要版本調整 Kubernetes 叢集時，會發生什麼事？**

對於 AKS 不支援的次要版本，相應縮小或放大都會繼續運作，而且不會產生任何問題。

**客戶可以永遠停留在某個 Kubernetes 版本嗎？**

是的。 不過, 如果叢集不在 AKS 所支援的其中一個版本上, 叢集就會超出 AKS 支援原則。 Azure 不會自動升級您的叢集或刪除它。

**如果代理程式叢集不在某個支援的 AKS 版本中，主要叢集會支援哪個版本？**

主要叢集會自動更新為最新支援版本。

## <a name="next-steps"></a>後續步驟

如需如何升級叢集的相關資訊, 請參閱[升級 Azure Kubernetes Service (AKS)][aks-upgrade]叢集。

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
