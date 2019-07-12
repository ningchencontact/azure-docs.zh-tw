---
title: 將 Azure 資源移到新的訂用帳戶或資源群組時，針對錯誤進行疑難排解
description: 使用 Azure Resource Manager 將資源移到新的資源群組或訂用帳戶。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: e23d7c571a010e5bfb42e5f15368e0194272ed53
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723463"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>疑難排解將 Azure 資源移到新的資源群組或訂用帳戶

本文章提供可協助您解決問題，移動資源時的建議。

## <a name="upgrade-a-subscription"></a>升級訂用帳戶

如果您實際上是想要升級 Azure 訂用帳戶 (例如從免費切換成隨用隨付)，則必須轉換您的訂用帳戶。

* 若要將免費試用升級，請參閱[將免費試用或 Microsoft Imagine Azure 訂用帳戶升級至隨用隨付](../billing/billing-upgrade-azure-subscription.md)。
* 若要變更隨用隨付帳戶，請參閱[將您的 Azure 隨用隨付訂閱變更為其他供應項目](../billing/billing-how-to-switch-azure-offer.md)。

如果您無法轉換訂用帳戶，請[建立 Azure 支援要求](../azure-supportability/how-to-create-azure-support-request.md)。 針對問題類型選取 [訂用帳戶管理]  。

## <a name="service-limitations"></a>服務限制

移動資源時，某些服務會需要額外的考量。 如果您要移動下列服務，請確定您檢查的指導方針和限制。

* [應用程式服務](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [傳統部署模型](./move-limitations/classic-model-move-limitations.md)
* [復原服務](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [虛擬機器](./move-limitations/virtual-machines-move-limitations.md)
* [虛擬網路](./move-limitations/virtual-network-move-limitations.md)

## <a name="large-requests"></a>大型的要求

可能的話，請將大型移動細分為個別的移動作業。 單一作業超過 800 個資源時，Resource Manager 會立即傳回錯誤。 不過，移動少於 800 個資源也可能因為逾時而失敗。

## <a name="next-steps"></a>後續步驟

如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](resource-group-move-resources.md)。
