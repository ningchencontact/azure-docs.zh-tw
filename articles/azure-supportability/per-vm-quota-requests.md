---
title: 針對每個 Azure VM 系列要求增加 vCPU 配額限制 |Microsoft Docs
description: 本文討論如何為每個 VM vCPU 要求增加配額限制。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: fc59ffda245834c716067bc63e6c3745fa5d23b3
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750189"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>標準配額：依 VM 系列增加限制

Azure Resource Manager 針對虛擬機器支援兩種類型的 vCPU 配額：
* *隨用隨付 vm*和*保留的 vm 實例*受限於*標準的 vCPU 配額*。
* *找出 vm*會受到*點 vCPU 配額*的限制。 

預付型方案和保留的 VM 實例的標準 vCPU 配額會針對每個區域中的每個訂用帳戶，在兩個層級強制執行：
* 第一層是*區域個 vcpu 限制*（在所有 VM 系列中）的總計。
* 第二層是*每個 VM 的系列個 vcpu 限制*（例如 Dv3 系列個 vcpu）。 

每當您部署新的點 VM 時，該 VM 系列的新的和現有 vCPU 使用量總計不得超過該特定 VM 系列的已核准 vCPU 配額。 此外，在所有 VM 系列上部署的新和現有個 vcpu 的總數，不應超過訂用帳戶的核准區域 vCPU 配額總計。 如果超過上述任一配額，則不允許 VM 部署。

您可以使用 Azure 入口網站，為 VM 系列要求增加 vCPU 配額限制。 VM 系列配額的增加會自動增加相同數量的總區域 vCPU 限制。 

若要深入瞭解標準 vCPU 配額，請參閱[虛擬機器 vCPU 配額](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)和[Azure 訂用帳戶和服務限制](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)。 

若要瞭解如何針對標準配額增加依區域的 vCPU 限制，請參閱[標準配額：依區域增加限制](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)。 

若要深入瞭解增加點 VM vCPU 限制，請參閱[點配額：增加所有 VM 系列的限制](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)。

您可以使用兩種方式的其中一種，要求每個 VM 系列增加標準 vCPU 配額限制，如下一節所述。

## <a name="request-a-standard-quota-increase-from-the-help--support-pane"></a>從 [說明 + 支援] 窗格要求增加標準配額

若要從 [說明 **+ 支援**] 窗格要求每個 VM 系列的標準 vCPU 配額增加，請執行下列動作： 

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

1. 在 [**詳細資料**] 索引標籤的 [**問題詳細資料**] 底下，選取 [**提供詳細資料**]，然後輸入其他資訊以協助處理您的要求。

   ![[提供詳細資料] 連結](./media/resource-manager-core-quotas-request/provide-details.png)

1. 在右上方的 [**配額詳細資料**] 窗格中，執行下列動作：

   ![[配額詳細資料] 窗格](./media/resource-manager-core-quotas-request/1-7.png)

   a. 在 [**部署模型**] 下拉式清單中，選取適當的模型。

   b. 在 [**位置**] 下拉式清單中，選取 [位置]。 針對選取的位置，在 [**類型**] 下的 [**選取類型**] 方塊中，輸入**Standard**。

   ![[配額詳細資料] 窗格-配額類型](./media/resource-manager-core-quotas-request/1-8.png)

   在 [**類型**] 底下，您可以透過多重選取支援，從單一支援案例要求標準和點配額類型。
   
   如需增加位置配額限制的詳細資訊，請參閱[虛擬機器擴展集的 Azure 位置 vm](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)。

   c. 在 [**標準**] 下拉式清單下方，選取您想要增加其配額的 SKU 系列。

   ![[配額詳細資料] 窗格-SKU 系列](./media/resource-manager-core-quotas-request/1-9.png)

   d. 輸入您要用於此訂用帳戶的新配額限制。 若要從清單中移除 SKU，請清除 SKU 旁的核取方塊，或選取 [**刪除**] （X）圖示。 

   ![[新增 vCPU 限制] 文字方塊](./media/resource-manager-core-quotas-request/1-10.png)

1. 若要要求增加一個以上位置的配額，請在下拉式清單中選取其他位置，然後選取適當的 VM 類型。 然後，您可以輸入適用于其他位置的限制。

   ![[配額詳細資料] 窗格中的其他位置](./media/resource-manager-core-quotas-request/1-11.png)
   
1. 選取 [**儲存並繼續**] 繼續建立支援要求。

## <a name="request-a-standard-quota-increase-from-the-subscriptions-pane"></a>從 [訂用帳戶] 窗格要求增加標準配額

若要從 [訂用帳戶] 窗格要求每個 VM**系列的標準**vCPU 配額增加，請執行下列動作：

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

   ![[配額詳細資料] 窗格](./media/resource-manager-core-quotas-request/1-1-6.png)

   a. 在 [**部署模型**] 下拉式清單中，選取適當的模型。

   b. 在 [**位置**] 下拉式清單中，選取 [位置]。 
   
   c. 針對選取的位置，在 [**類型**] 底下選取 [**選取類型**]，然後選取 [**標準**] 核取方塊。

   ![[標準] 核取方塊](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   在 [**類型**] 底下，您可以透過多重選取支援，從單一支援案例要求標準和低優先順序的配額類型。
   
   如需增加位置配額限制的詳細資訊，請參閱[虛擬機器擴展集的 Azure 位置 vm](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)。

   d. 在 [**標準**] 下拉式清單下方，選取您想要增加其配額的 SKU 系列。

   ![[配額詳細資料] 窗格-SKU 系列](./media/resource-manager-core-quotas-request/1-1-8.png)

   e. 輸入您要用於此訂用帳戶的新配額限制。 若要從清單中移除 SKU，請清除 SKU 旁的核取方塊，或選取 [**刪除**] （X）。 

   ![[新增 vCPU 限制] 文字方塊](./media/resource-manager-core-quotas-request/1-1-9.png)
   
1. 若要要求增加一個以上位置的配額，請在下拉式清單中選取其他位置，然後選取適當的 VM 類型。 

   此步驟會預先載入您為先前的位置選取的 SKU 系列。 輸入您想要套用至其他數列的配額限制。
   
   ![[配額詳細資料] 窗格中的其他位置](./media/resource-manager-core-quotas-request/1-1-10.png)
 
1. 選取 [**儲存並繼續**] 繼續建立支援要求。
