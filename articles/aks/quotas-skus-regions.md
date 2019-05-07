---
title: 配額、 SKU 和區域可用性的 Azure Kubernetes Service (AKS)
description: 深入了解預設配額、 限制的節點 VM SKU 大小和區域可用性的 Azure Kubernetes Service (AKS)。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: abeb9ef6e467b62cf7332e01e1b77c710b9ba4f4
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072887"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>配額、 虛擬機器的大小限制和區域可用性的 Azure Kubernetes Service (AKS)

所有 Azure 服務都包含資源和功能的特定預設限制和配額。 針對節點的大小，某些虛擬機器 (VM) 的 Sku 為然後僅限使用。

本文詳細說明 Azure Kubernetes Service (AKS) 資源，以及可用性的 Azure 區域中 AKS 服務的預設資源限制。

## <a name="service-quotas-and-limits"></a>服務配額和限制

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>佈建的基礎結構

所有其他網路、計算和儲存體限制都適用於佈建的基礎結構。 如需相關限制，請參閱 [Azure 訂用帳戶和服務限制](../azure-subscription-service-limits.md)。

## <a name="restricted-vm-sizes"></a>受限制的 VM 大小

在 AKS 叢集中的每個節點包含固定的運算資源，例如個 vCPU 和記憶體數量。 如果 AKS 節點包含足夠的計算資源，可能無法正確執行的 pod。 若要確定所需*kube 系統*pod 和您的應用程式可以可靠地排程，以下 VM Sku 無法使用 AKS 中：

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

如需有關 VM 類型和其計算資源的詳細資訊，請參閱 <<c0> [ 在 Azure 中的虛擬機器的大小][vm-skus]。

## <a name="region-availability"></a>區域可用性

如需位置的最新清單部署和執行叢集，請參閱[AKS 區域可用性][region-availability]。

## <a name="next-steps"></a>後續步驟

某些預設限制和配額可以增加。 若要要求增加一或多個支援此類增加的資源，請提交 [Azure 支援要求][azure-support] (針對 [問題類型] 選取 [配額])。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
