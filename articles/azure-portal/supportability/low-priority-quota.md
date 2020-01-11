---
title: 找出虛擬機器配額 |Microsoft Docs
description: 藉由建立點配額要求來增加配額限制
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9567df95e4e66d9c6b82b29cb9bba9adaf39349d
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897295"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>點配額：增加所有 VM 系列的限制

點虛擬機器（Vm）提供不同的 Azure 使用量模型。 它們可讓您在 exchange 中承擔較低的成本，讓 Azure 在隨用隨付或保留的 VM 實例部署時，視需要移除 Vm。 如需有關如何找出 Vm 的詳細資訊，請參閱[虛擬機器擴展集的 Azure 位置 vm](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)。

Azure Resource Manager 針對虛擬機器支援兩種類型的 vCPU 配額：
* *隨用隨付 vm*和*保留的 vm 實例*受限於*標準的 vCPU 配額*。
* *找出 vm*會受到*點 vCPU 配額*的限制。 

對於 [*點 vCPU] 配額*類型，Resource Manager vCPU 配額會在所有可用的 VM 系列中強制執行，做為單一區域限制。

每當您部署新的點 VM 時，所有點 VM 實例的新的和現有的 vCPU 使用量，都不得超過已核准的點 vCPU 配額限制。 如果超過了位置配額，則不允許點 VM 部署。 

本文討論如何使用 Azure 入口網站來要求增加點 vCPU 配額限制。 

若要深入瞭解標準 vCPU 配額，請參閱[虛擬機器 vCPU 配額](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)和[Azure 訂用帳戶和服務限制](https://aka.ms/quotalimits)。 

若要瞭解如何依區域增加 vCPU 限制，請參閱[標準配額：區域 vCPU 限制增加](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)。

## <a name="request-a-quota-limit-increase-from-the-help--support-pane"></a>從 [說明 + 支援] 窗格要求增加配額限制 

若要針對 [說明 **+ 支援**] 窗格中的所有 VM 系列要求增加位置配額限制，請執行下列動作：

> [!NOTE]
> 您也可以透過單一支援案例，要求增加多個區域的配額限制。 如需詳細資訊，請參閱步驟8。 

1. 在[Azure 入口網站](https://portal.azure.com)的左窗格中，選取 [說明 **+ 支援**]。

   ![[說明 + 支援] 連結](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. 在 [說明 **+ 支援**] 窗格中，選取 [**新增支援要求**]。 

    ![新增支援要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. 在 [**問題類型**] 下拉式清單中，選取 [**服務和訂用帳戶限制（配額）** ]。

   ![[問題類型] 下拉式清單](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. 在 [**訂**用帳戶] 下拉式清單中，選取您想要增加其配額的訂用帳戶。

   ![[訂用帳戶] 下拉式清單](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. 在 [**配額類型**] 下拉式清單中，選取 [**計算-VM （核心-個 vcpu）訂用帳戶限制增加**]。 

   ![[配額類型] 下拉式清單](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 選取 [**詳細資料**] 索引標籤，然後在 [**問題詳細資料**] 底下選取 [**提供詳細資料**]，然後輸入其他資訊來協助處理您的要求。

   ![[提供詳細資料] 連結](./media/resource-manager-core-quotas-request/provide-details.png)
   
1. 在右上方的 [**配額詳細資料**] 窗格中，執行下列動作：

   ![[配額詳細資料] 窗格](./media/resource-manager-core-quotas-request/3-7.png)

   a. 在 [**部署模型**] 下拉式清單中，選取適當的模型。

   b. 在 [**位置**] 下拉式清單中，選取 [位置]。 針對選取的位置，在 [**類型**] 下的 [**選取類型**] 方塊中，輸入 [**位置**]。 
   
   ![[新增支援要求] 詳細資料索引標籤](./media/resource-manager-core-quotas-request/3-8.png)

    在 [**類型**] 底下，您可以透過多重選取支援，從單一支援案例要求標準和點配額類型。 
    
    如需詳細資訊，請參閱[標準配額：每個 VM 的系列 vCPU 限制增加](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)。

   c. 輸入您要用於此訂用帳戶的新配額限制。 
 
   ![[新增 vCPU 限制] 文字方塊](./media/resource-manager-core-quotas-request/3-9.png)

1. 若要要求增加一個以上位置的配額，請在下拉式清單中選取其他位置，然後選取適當的 VM 類型。 然後，您可以輸入適用于其他位置的限制。

   ![[配額詳細資料] 窗格中的其他位置](./media/resource-manager-core-quotas-request/3-10.png)

1. 選取 [**儲存並繼續**] 繼續建立支援要求。

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>要求從 [訂用帳戶] 窗格增加配額限制

若要針對 [訂用帳戶] 窗格**中的所有**VM 系列要求增加位置配額限制，請執行下列動作：

> [!NOTE]
> 您也可以透過單一支援案例，要求增加多個區域的配額限制。 如需詳細資訊，請參閱步驟7。 

1. 在[Azure 入口網站](https://portal.azure.com)的左窗格中，選取 [**訂閱**]。

   ![[訂閱] 連結](./media/resource-manager-core-quotas-request/subscriptions.png)

1. 選取您想要增加其配額的訂用帳戶。

   ![[訂閱] 窗格](./media/resource-manager-core-quotas-request/select-subscription.png)

1. 在 **\<訂**用帳戶名稱的左窗格中 > 頁面上，選取 **使用量 + 配額**。

   ![[使用量 + 配額] 連結](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. 在右上方，選取 [**要求增加**]。

   ![要求增加配額](./media/resource-manager-core-quotas-request/request-increase.png)

1. 在 [**配額類型**] 下拉式清單中，選取 [**計算-VM （核心-個 vcpu）訂用帳戶限制增加**]。

   ![[配額類型] 下拉式清單](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 在右上方的 [**配額詳細資料**] 窗格中，執行下列動作：

   ![[配額詳細資料] 窗格](./media/resource-manager-core-quotas-request/3-2-6.png)
 
   a. 在 [**部署模型**] 下拉式清單中，選取適當的模型。

   b. 在 [**位置**] 下拉式清單中，選取 [位置]。 
   
   c. 針對選取的位置，在 [**類型**] 下的 [**選取類型**] 方塊中，輸入 [**位置**]。

   ![[配額詳細資料] 窗格](./media/resource-manager-core-quotas-request/3-2-7.png)

   如需詳細資訊，請參閱[標準配額：每個 VM 的系列 vCPU 限制增加](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)。

   d. 輸入您要用於此訂用帳戶的新配額限制。

   ![[新增 vCPU 限制] 文字方塊](./media/resource-manager-core-quotas-request/3-2-8.png)
 
1. 若要要求增加一個以上位置的配額，請在下拉式清單中選取其他位置，然後選取適當的 VM 類型。 然後，您可以輸入適用于其他位置的限制。

   ![[配額詳細資料] 窗格中的其他位置](./media/resource-manager-core-quotas-request/3-2-9.png)
 
1. 選取 [**儲存並繼續**] 繼續建立支援要求。


