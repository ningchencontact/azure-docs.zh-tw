---
title: 限制 Azure Kubernetes Service 中的輸出流量（AKS）
description: 瞭解在 Azure Kubernetes Service （AKS）中控制輸出流量所需的埠和位址
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: mlearned
ms.openlocfilehash: 9476290669606f6eb6c56b51497f3026b9613698
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034953"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>預覽-限制叢集節點的輸出流量，並控制 Azure Kubernetes Service 中所需埠和服務的存取（AKS）

根據預設，AKS 叢集具有不受限制的輸出（傳出）網際網路存取。 此網路存取層級可讓您執行的節點和服務視需要存取外部資源。 如果您想要限制輸出流量，則必須能夠存取有限數目的埠和位址，才能維持狀況良好的叢集維護工作。 接著，您的叢集會設定為只使用 Microsoft Container Registry （MCR）或 Azure Container Registry （ACR）中的基本系統容器映射，而不是外部公用存放庫。 您必須設定慣用的防火牆和安全性規則，以允許這些必要的埠和位址。

本文詳細說明必要的網路埠和完整功能變數名稱（Fqdn），以及當您在 AKS 叢集中限制輸出流量時，是選擇性的。  此功能目前為預覽狀態。

> [!IMPORTANT]
> AKS 預覽功能是自助加入宣告。 預覽會以「原樣」和「可用」的方式提供，並從服務等級協定中排除，並享有有限擔保。 AKS 預覽的部分是由客戶支援，以最大的方式來涵蓋。 因此，這些功能並不適用于生產環境使用。 如需其他資訊，請參閱下列支援文章：
>
> * [AKS 支援原則][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

## <a name="before-you-begin"></a>開始之前

您需要安裝並設定 Azure CLI 版本2.0.66 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

若要建立可限制傳出流量的 AKS 叢集，請先在您的訂用帳戶上啟用功能旗標。 此功能註冊會設定您所建立的任何 AKS 叢集，以使用來自 MCR 或 ACR 的基礎系統容器映射。 若要註冊*AKSLockingDownEgressPreview*功能旗標，請使用[az feature register][az-feature-register]命令，如下列範例所示：

> [!CAUTION]
> 當您在訂用帳戶上註冊功能時，目前無法取消註冊該功能。 啟用一些預覽功能之後，預設值可能會用於在訂用帳戶中建立的所有 AKS 叢集。 請勿在生產訂用帳戶上啟用預覽功能。 使用個別的訂用帳戶來測試預覽功能並收集意見反應。

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

狀態需要幾分鐘的時間才會顯示「已註冊」。 您可以使用[az feature list][az-feature-list]命令來檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

準備好時，請使用[az provider register][az-provider-register]命令重新整理*microsoft.containerservice*資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>輸出流量概觀

基於管理和操作的目的，AKS 叢集中的節點需要存取特定埠和完整功能變數名稱（Fqdn）。 這些動作可能是與 API 伺服器通訊，或下載並安裝核心 Kubernetes 叢集元件和節點安全性更新。 根據預設，傳出（輸出）網際網路流量不會限制在 AKS 叢集中的節點。 叢集可能會從外部存放庫提取基本系統容器映射。

若要提高 AKS 叢集的安全性，您可能會想要限制傳出流量。 叢集已設定為從 MCR 或 ACR 提取基礎系統容器映射。 如果您以這種方式鎖定輸出流量，您必須定義特定埠和 Fqdn，才能讓 AKS 節點正確地與所需的外部服務進行通訊。 如果沒有這些授權的埠和 Fqdn，您的 AKS 節點就無法與 API 伺服器通訊或安裝核心元件。

您可以使用[Azure 防火牆][azure-firewall]或協力廠商防火牆設備來保護您的輸出流量，並定義這些必要的埠和位址。 AKS 不會自動為您建立這些規則。 當您在網路防火牆中建立適當的規則時，可以參考下列埠和位址。

> [!IMPORTANT]
> 當您使用 Azure 防火牆來限制輸出流量，並建立使用者定義的路由（UDR）來強制執行所有輸出流量時，請務必在防火牆中建立適當的 DNAT 規則，以正確地允許輸入流量。 使用 Azure 防火牆搭配 UDR 會中斷輸入設定，因為非對稱式路由。 （之所以會發生此問題，是因為 AKS 子網具有移至防火牆私人 IP 位址的預設路由，但您使用的是下列類型的公用負載平衡器-輸入或 Kubernetes 服務：LoadBalancer）。 在此情況下，系統會透過傳入負載平衡器流量的公用 IP 位址接收它，但傳回路徑則會通過防火牆的私人 IP 位址。 因為防火牆是具狀態的，所以它會捨棄傳回的封包，因為防火牆並不知道已建立的會話。 若要瞭解如何整合 Azure 防火牆與您的輸入或服務負載平衡器，請參閱[整合 Azure 防火牆與 azure Standard Load Balancer](https://docs.microsoft.com/en-us/azure/firewall/integrate-lb)。
>

在 AKS 中，有兩組埠和位址：

* AKS 叢集所[需的埠和位址](#required-ports-and-addresses-for-aks-clusters)會詳細說明授權輸出流量的最低需求。
* 並非所有案例都需要[選擇性的 AKS 叢集建議位址和埠](#optional-recommended-addresses-and-ports-for-aks-clusters)，但是與其他服務（例如 Azure 監視器）的整合將無法正確運作。 請參閱這份選擇性埠和 Fqdn 清單，並授權您的 AKS 叢集中使用的任何服務和元件。

> [!NOTE]
> 限制輸出流量僅適用于啟用功能旗標注冊之後所建立的新 AKS 叢集。 針對現有的叢集，請先使用`az aks upgrade`命令執行[叢集升級作業][aks-upgrade]，再限制輸出流量。

## <a name="required-ports-and-addresses-for-aks-clusters"></a>AKS 叢集所需的埠和位址

AKS 叢集需要下列輸出埠/網路規則：

* TCP 埠*443*
* TCP 埠*9000*和 tcp 埠*22* ，通道前端 pod 會與 API 伺服器上的通道結束通訊。
    * 若要取得更具體的資訊，請參閱 * 「*hcp」。\<location\>. azmk8s.io*和 * *. 執行。\<下\>* 表中的 azmk8s.io 位址。

需要下列 FQDN/應用程式規則：

| FQDN                       | 連接埠      | 使用情況      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.azmk8s.io | HTTPS：443、TCP：22、TCP：9000 | 此位址是 API 伺服器端點。 將 *\< location\>* 取代為您的 AKS 叢集部署所在的區域。 |
| *.tun.\<location\>.azmk8s.io | HTTPS：443、TCP：22、TCP：9000 | 此位址是 API 伺服器端點。 將 *\< location\>* 取代為您的 AKS 叢集部署所在的區域。 |
| aksrepos.azurecr.io        | HTTPS:443 | 需要此位址才能存取 Azure Container Registry （ACR）中的影像。 此登錄包含叢集的升級和調整期間，叢集運作所需的協力廠商映射/圖表（例如計量伺服器、核心 dns 等）|
| *.blob.core.windows.net    | HTTPS:443 | 此位址是 ACR 中所儲存映射的後端存放區。 |
| mcr.microsoft.com          | HTTPS:443 | 需要有此位址，才能存取 Microsoft Container Registry （MCR）中的映射。 此登錄包含在叢集的升級和調整期間，叢集運作所需的第一方映射/圖表（例如，moby 等） |
| *.cdn.mscr.io              | HTTPS:443 | Azure 內容傳遞網路（CDN）支援的 MCR 儲存體需要此位址。 |
| management.azure.com       | HTTPS:443 | Kubernetes 取得/PUT 作業需要此位址。 |
| login.microsoftonline.com  | HTTPS:443 | Azure Active Directory 驗證需要此位址。 |
| ntp.ubuntu.com             | UDP：123   | 在 Linux 節點上進行 NTP 時間同步處理時，需要此位址。 |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>適用于 AKS 叢集的選擇性建議位址和埠

* 適用于 DNS 的 UDP 埠*53*

建議使用下列 FQDN/應用程式規則，讓 AKS 叢集正常運作：

| FQDN                                    | 連接埠      | 使用情況      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com、azure.archive.ubuntu.com、changelogs.ubuntu.com                           | HTTP:80   | 此位址可讓 Linux 叢集節點下載所需的安全性修補程式和更新。 |
| packages.microsoft.com                  | HTTPS:443 | 此位址是用於*快取 apt-get*作業的 Microsoft 封裝存放庫。 |
| dc.services.visualstudio.com            | HTTPS:443 | 建議使用 Azure 監視器進行正確的計量和監視。 |
| *.opinsights.azure.com                  | HTTPS:443 | 建議使用 Azure 監視器進行正確的計量和監視。 |
| *.monitoring.azure.com                  | HTTPS:443 | 建議使用 Azure 監視器進行正確的計量和監視。 |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | 此位址用於 Azure 原則的正確作業（目前在 AKS 中為預覽狀態）。 |
| apt.dockerproject.org                   | HTTPS:443 | 此位址用於以 GPU 為基礎的節點上進行正確的驅動程式安裝和操作。 |
| nvidia.github.io                        | HTTPS:443 | 此位址用於以 GPU 為基礎的節點上進行正確的驅動程式安裝和操作。 |

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解在限制叢集的輸出流量時，要允許哪些埠和位址。 您也可以定義 pod 本身可以通訊的方式，以及它們在叢集內的限制。 如需詳細資訊，請參閱[在 AKS 中使用網路原則來保護 pod 之間的流量][network-policy]。

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
