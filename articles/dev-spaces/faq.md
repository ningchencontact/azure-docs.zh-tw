---
title: 關於 Azure Dev Spaces 的常見問題
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: 尋找一些關於 Azure Dev Spaces 常見問題的解答
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s '
ms.openlocfilehash: c904ae5809a36859ba6428bf026c9016a1a8f747
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867183"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>關於 Azure Dev Spaces 的常見問題

這會解決 Azure Dev Spaces 的常見問題。

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>哪些 Azure 區域目前提供 Azure Dev Spaces？

如需可用區域的完整清單，請參閱[支援的區域][supported-regions]。

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>我可以在沒有公用 IP 位址的情況下使用 Azure Dev Spaces 嗎？

否，您無法在沒有公用 IP 的 AKS 叢集上布建 Azure Dev Spaces。 [Azure Dev Spaces 路由需要][dev-spaces-routing]公用 IP。

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>我可以搭配 Azure Dev Spaces 使用自己的輸入嗎？

是，您可以設定您自己的輸入，Azure Dev Spaces 建立一個輸入。 例如，您可以使用[traefik][ingress-traefik]。

## <a name="can-i-use-https-with-azure-dev-spaces"></a>我可以搭配使用 HTTPS 與 Azure Dev Spaces 嗎？

是，您可以使用[traefik][ingress-https-traefik]來設定您自己的輸入與 HTTPS。

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>我可以在使用 CNI 而非 kubenet 的叢集上使用 Azure Dev Spaces 嗎？ 

是，您可以在使用 CNI 進行網路功能的 AKS 叢集上使用 Azure Dev Spaces。 例如，您可以使用 AKS 叢集上的 Azure Dev Spaces 搭配[現有的 Windows 容器][windows-containers]，其使用 CNI 的網路功能。 如需使用 CNI 與 Azure Dev Spaces 進行網路功能的詳細資訊，請參閱[這裡](configure-networking.md#using-azure-container-networking-with-azure-dev-spaces)。

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>我可以搭配 Windows 容器使用 Azure Dev Spaces 嗎？

目前，Azure Dev Spaces 僅適用于 Linux pod 和節點，但您可以在具有[現有 Windows 容器][windows-containers]的 AKS 叢集上執行 Azure Dev Spaces。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>我可以在已啟用 API 伺服器授權 IP 位址範圍的 AKS 叢集上使用 Azure Dev Spaces 嗎？

是，您可以在已啟用[API 伺服器授權 IP 位址範圍][aks-auth-range]的 AKS 叢集上使用 Azure Dev Spaces。 如需使用已啟用 Azure Dev Spaces 的 API 伺服器授權 IP 位址範圍之 AKS 叢集的詳細資訊，請參閱[這裡](configure-networking.md#using-api-server-authorized-ip-ranges-with-azure-dev-spaces)。

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>我可以在 AKS 叢集上使用具有叢集節點之受限制輸出流量的 Azure Dev Spaces 嗎？

是的，您可以在 AKS 叢集上使用 Azure Dev Spaces，並在允許正確的 Fqdn 之後啟用叢集[節點的受限制輸出流量][aks-restrict-egress-traffic]。 如需有關使用 Azure Dev Spaces 啟用的叢集節點之受限制輸出流量的詳細資訊，請參閱[這裡](configure-networking.md#ingress-and-egress-network-traffic-requirements)。

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md