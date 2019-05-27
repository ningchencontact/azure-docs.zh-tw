---
title: 配額、 Sku 和區域可用性的 Azure Kubernetes Service (AKS)
description: 深入了解預設配額、 限制的節點 VM SKU 大小和區域可用性的 Azure Kubernetes Service (AKS)。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: 8d4ed8f791858747814972bcf16a9672a7f12610
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65901449"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>配額、 虛擬機器的大小限制和區域可用性的 Azure Kubernetes Service (AKS)

所有 Azure 服務設定預設限制和配額的資源和功能。 特定虛擬機器 (VM) 的 Sku 也會使用受限制的。

本文詳細說明 Azure Kubernetes Service (AKS) 資源，以及可用性的 Azure 區域中 AKS 的預設資源限制。

## <a name="service-quotas-and-limits"></a>服務配額和限制

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>佈建的基礎結構

所有其他網路、計算和儲存體限制都適用於佈建的基礎結構。 如需相關的限制，請參閱[Azure 訂用帳戶和服務限制](../azure-subscription-service-limits.md)。

> [!IMPORTANT]
> 當您升級 AKS 叢集時，暫時耗用額外的資源。 這些資源包括可用的 IP 位址在虛擬網路子網路或虛擬機器 vCPU 配額。 如果您使用 Windows Server 容器 （目前在 AKS 中的預覽） 時，將最新的更新套用至節點的唯一背書的方法是執行升級作業。 失敗的叢集升級程序可能表示您沒有可用 IP 位址空間或 vCPU 的配額來處理這些暫時性的資源。 如需有關 Windows Server 節點升級程序的詳細資訊，請參閱 <<c0> [ 升級 AKS 中的節點集區][nodepool-upgrade]。

## <a name="restricted-vm-sizes"></a>受限制的 VM 大小

在 AKS 叢集中的每個節點包含固定的運算資源，例如個 vCPU 和記憶體數量。 如果 AKS 節點包含足夠的計算資源，pod 可能無法正確執行。 若要確定所需*kube 系統*pod 和您的應用程式可以可靠地排程，請不要使用 AKS 中的以下 VM Sku:

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

某些預設限制和配額可以增加。 如果您的資源可支援增加，要求透過增加[Azure 支援要求][ azure-support] (如**問題類型**，選取**配額**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
