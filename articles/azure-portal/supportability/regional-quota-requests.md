---
title: 要求增加 Azure 區域 vCPU 配額限制 |Microsoft Docs
description: 區域配額增加要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e262651a6e040c40dbe240ad3437eff1914aa3e5
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896736"
---
# <a name="standard-quota-increase-limits-by-region"></a>標準配額：依區域增加限制 

Azure Resource Manager 針對虛擬機器支援兩種類型的 vCPU 配額：
* *隨用隨付 vm*和*保留的 vm 實例*受限於*標準的 vCPU 配額*。
* *找出 vm*會受到*點 vCPU 配額*的限制。 

預付型方案和保留的 VM 實例的標準 vCPU 配額會針對每個區域中的每個訂用帳戶，在兩個層級強制執行：
* 第一層是*區域個 vcpu 限制*（在所有 VM 系列中）的總計。
* 第二層是*每個 VM 的系列個 vcpu 限制*（例如 D 系列個 vcpu）。
 
每當您部署新的點 VM 時，該 VM 系列的新的和現有 vCPU 使用量總計不得超過該特定 VM 系列的已核准 vCPU 配額。 此外，在所有 VM 系列上部署的新和現有個 vcpu 的總數，不應超過訂用帳戶的核准區域 vCPU 配額總計。 如果超過上述任一配額，則不允許 VM 部署。 

您可以使用 Azure 入口網站，為 VM 系列要求增加 vCPU 配額限制。 VM 系列配額的增加會自動增加相同數量的總區域 vCPU 限制。

當您建立新的訂用帳戶時，區域個 vcpu 的預設總數目可能不等於所有個別 VM 系列的預設 vCPU 配額總計。 這項差異可能會導致訂用帳戶具有足夠的配額，可供您想要部署的每個個別 VM 系列使用。 但是，可能沒有足夠的配額可容納所有部署的總區域個 vcpu。 在此情況下，您必須提交要求，以明確增加區域個 vcpu 總數的限制。 區域 vCPU 限制總計不得超過該區域所有 VM 系列的核准配額總計。

若要深入瞭解標準 vCPU 配額，請參閱[虛擬機器 vCPU 配額](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)和[Azure 訂用帳戶和服務限制](https://aka.ms/quotalimits)。

若要深入瞭解增加點 VM vCPU 限制，請參閱[點配額：增加所有 VM 系列的限制](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)。

您可以透過兩種方式的其中一種來要求增加 vCPU 標準配額限制，如下節所述。

## <a name="request-a-quota-increase-by-region-from-the-help--support-pane"></a>在 [說明 + 支援] 窗格中，依區域要求增加配額

若要依區域要求 [說明 **+ 支援**] 窗格中的 vCPU 配額增加，請執行下列動作： 

1. 在[Azure 入口網站](https://portal.azure.com)的左窗格中，選取 [說明 **+ 支援**]。

   ![[說明 + 支援] 連結](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. 在 [說明 **+ 支援**] 窗格中，選取 [**新增支援要求**]。 

    ![新增支援要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. 在 [**問題類型**] 下拉式清單中，選取 [**服務和訂用帳戶限制（配額）** ]。

   ![[問題類型] 下拉式清單](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. 在 [**訂**用帳戶] 下拉式清單中，選取您想要增加其配額的訂用帳戶。

   ![[訂用帳戶] 下拉式清單](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. 在 [**配額類型**] 下拉式清單中，選取 [**其他要求**]。

   ![[配額類型] 下拉式清單](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. 在 [**問題詳細資料**] 窗格的 [**描述**] 方塊中，提供下列其他資訊： 

    a. 針對 [**部署模型**]，指定**Resource Manager**。  
    b. 針對 [**區域**]，指定您所需的區域（例如 [**美國東部 2**]）。  
    c. 針對 [**新限制**]，為區域指定新的 vCPU 限制。 此值不應超過此訂用帳戶的個別 SKU 系列已核准配額的總和。

    ![[問題詳細資料] 窗格](./media/resource-manager-core-quotas-request/regional-details.png)

1. 選取 [**儲存並繼續**] 繼續建立支援要求。

## <a name="request-a-quota-increase-by-region-from-the-subscriptions-pane"></a>依區域從 [訂用帳戶] 窗格要求增加配額

若要依區域要求 [訂用**帳戶] 窗格中的 vCPU**配額增加，請執行下列動作： 

1. 在[Azure 入口網站](https://portal.azure.com)的左窗格中，選取 [**訂閱**]。

   ![[訂閱] 連結](./media/resource-manager-core-quotas-request/subscriptions.png)

1. 選取您想要增加其配額的訂用帳戶。

   ![[訂閱] 窗格](./media/resource-manager-core-quotas-request/select-subscription.png)

1. 在 **\<訂**用帳戶名稱的左窗格中 > 頁面上，選取 **使用量 + 配額**。

   ![[使用量 + 配額] 連結](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. 在右上方，選取 [**要求增加**]。

   ![要求增加配額](./media/resource-manager-core-quotas-request/request-increase.png)

1. 在 [**配額類型**] 下拉式清單中，選取 [**其他要求**]。

   ![[配額類型] 下拉式清單](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. 在 [**問題詳細資料**] 窗格的 [**描述**] 方塊中，提供下列其他資訊： 

    a. 針對 [**部署模型**]，指定**Resource Manager**。  
    b. 針對 [**區域**]，指定您所需的區域（例如 [**美國東部 2**]）。  
    c. 針對 [**新限制**]，為區域指定新的 vCPU 限制。 此值不應超過此訂用帳戶的個別 SKU 系列已核准配額的總和。

    ![[問題詳細資料] 窗格](./media/resource-manager-core-quotas-request/regional-details.png)

1. 選取 [**儲存並繼續**] 繼續建立支援要求。

