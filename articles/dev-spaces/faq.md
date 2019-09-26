---
title: 關於 Azure Dev Spaces 的常見問題
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: 尋找一些關於 Azure Dev Spaces 常見問題的解答
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s '
ms.openlocfilehash: de44bf323b300a4467dc8b30555798f557898494
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/26/2019
ms.locfileid: "71305973"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>關於 Azure Dev Spaces 的常見問題

這會解決 Azure Dev Spaces 的常見問題。

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>哪些 Azure 區域目前提供 Azure Dev Spaces？

如需可用區域的完整清單，請參閱[支援的區域和][supported-regions]設定。

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>我可以在沒有公用 IP 位址的情況下使用 Azure Dev Spaces 嗎？

否，您無法在沒有公用 IP 的 AKS 叢集上布建 Azure Dev Spaces。 [Azure Dev Spaces 路由需要][dev-spaces-routing]公用 IP。

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>我可以搭配 Azure Dev Spaces 使用自己的輸入嗎？

是，您可以設定您自己的輸入，Azure Dev Spaces 建立一個輸入。 例如，您可以使用[traefik][ingress-traefik]。

## <a name="can-i-use-https-with-azure-dev-spaces"></a>我可以搭配使用 HTTPS 與 Azure Dev Spaces 嗎？

是，您可以使用[traefik][ingress-https-traefik]來設定您自己的輸入與 HTTPS。

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>我可以在使用 CNI 而非 kubenet 的叢集上使用 Azure Dev Spaces 嗎？ 

是，您可以在使用 CNI 進行網路功能的 AKS 叢集上使用 Azure Dev Spaces。 例如，您可以使用 AKS 叢集上的 Azure Dev Spaces 搭配[現有的 Windows 容器][windows-containers]，其使用 CNI 的網路功能。

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>我可以搭配 Windows 容器使用 Azure Dev Spaces 嗎？

目前，Azure Dev Spaces 僅適用于 Linux pod 和節點，但您可以在具有[現有 Windows 容器][windows-containers]的 AKS 叢集上執行 Azure Dev Spaces。


[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md