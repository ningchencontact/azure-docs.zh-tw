---
title: 將 Azure 傳統部署資源移至新的訂用帳戶或資源群組
description: 使用 Azure Resource Manager 將傳統部署資源移至新的資源群組或訂用帳戶。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 4770f957b6b9eea75b50776a7491b1ca479e50e2
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723502"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>移動傳統部署模型資源的指引

移動透過傳統模型所部署的資源的步驟根據無論您移動訂用帳戶內或新的訂用帳戶的資源而有所不同。

## <a name="move-in-the-same-subscription"></a>在相同的訂用帳戶中移動

將資源從一個資源群組移到相同訂用帳戶內的另一個資源群組時，適用下列限制︰

* 無法移動虛擬網路 (傳統)。
* 虛擬機器 (傳統) 必須與雲端服務一起移動。
* 只有當移動作業包含雲端服務的所有虛擬機器時，才能移動雲端服務。
* 一次只能移動一個雲端服務。
* 一次只能移動一個儲存體帳戶 (傳統)。
* 透過相同的作業，儲存體帳戶 (傳統) 不能與虛擬機器或雲端服務一起移動。

若要將傳統資源移至新的資源群組相同的訂用帳戶中，使用[標準移動作業](../resource-group-move-resources.md)透過入口網站、 Azure PowerShell、 Azure CLI 或 REST API。 當您移動 Resource Manager 資源時，您會使用相同的作業。

## <a name="move-across-subscriptions"></a>在 訂用帳戶之間移動

將資源移到新訂用帳戶時，適用下列限制︰

* 必須透過相同的作業移動訂用帳戶中的所有傳統資源。
* 目標訂用帳戶不得有其他任何傳統資源。
* 只能透過適用於傳統移動的個別 REST API 來要求移動。 將傳統資源移到新的訂用帳戶時，標準 Resource Manager 移動命令無法運作。

若要將傳統資源移到新的訂用帳戶，請使用傳統資源特定的 REST 作業。 若要使用 REST，執行下列步驟：

1. 請檢查來源訂用帳戶是否可以參與跨訂用帳戶移動。 請使用下列作業：

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     在要求本文中包含：

   ```json
   {
    "role": "source"
   }
   ```

     驗證作業的回應格式如下︰

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. 請檢查目的地訂用帳戶是否可以參與跨訂用帳戶移動。 請使用下列作業：

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     在要求本文中包含：

   ```json
   {
    "role": "target"
   }
   ```

     回應的格式與來源訂用帳戶驗證的格式相同。
1. 如果兩個訂用帳戶都通過驗證，使用下列作業將所有傳統資源從某個訂用帳戶移到另一個訂用帳戶︰

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    在要求本文中包含：

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

這項作業可能需要幾分鐘的時間執行。

## <a name="next-steps"></a>後續步驟

如果您無法移動傳統資源，請連絡[支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](../resource-group-move-resources.md)。
