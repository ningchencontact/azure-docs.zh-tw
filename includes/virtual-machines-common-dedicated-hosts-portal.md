---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 262880997c6b065dc5293a18d9a07c52ac836f37
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68702987"
---
> [!IMPORTANT]
> Azure 專用主機目前為公開預覽版。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> **已知的預覽限制**
> - 專用主機目前不支援虛擬機器擴展集。
> - 預覽初始版本支援下列 VM 系列:DSv3 和 ESv3。 


## <a name="create-a-host-group"></a>建立主機群組

**主機群組**是代表專用主機集合的新資源。 您會在區域和可用性區域中建立主機群組, 並在其中新增主機。 在規劃高可用性時, 還有其他選項。 您可以使用下列其中一個或兩個選項搭配您的專用主機: 
- 跨越多個可用性區域。 在此情況下, 您必須在想要使用的每個區域中都有一個主機群組。
- 跨越多個對應至實體機架的容錯網域。 
 
不論是哪一種情況, 您都必須提供主機群組的容錯網域計數。 如果您不想要跨越群組中的容錯網域, 請使用容錯網域計數1。 

您也可以決定使用可用性區域和容錯網域。 

在此範例中, 我們將使用1個可用性區域和2個容錯網域來建立主機群組。 


1. 開啟 Azure[入口網站](https://portal.azure.com)。
1. 選取左上角的 [**建立資源**]。
1. 搜尋 [**主機群組**], 然後從結果中選取 [**主機群組 (預覽)** ]。

    ![主機群組搜尋結果。](./media/virtual-machines-common-dedicated-hosts-portal/host-group.png)
1. 在 [**主機群組 (預覽)** ] 頁面中, 選取 [**建立**]。
1. 選取您想要使用的訂用帳戶, 然後選取 [新建] 以建立新的資源群組。
1. 輸入*myDedicatedHostsRG*作為**名稱**, 然後選取 **[確定]** 。
1. 針對 [**主機組名**], 輸入*myHostGroup*。
1. 針對 [**位置**], 選取 [**美國東部**]。
1. 針對 [**可用性區域**], 選取 [ **1**]。
1. 針對 [**容錯網域計數**], 選取 [ **2**]。
1. 選取 [審核] [ **+ 建立**], 然後等候驗證。

    ![主機群組設定](./media/virtual-machines-common-dedicated-hosts-portal/host-group-settings.png)
1. 一旦您看到 [**通過驗證**] 訊息, 請選取 [**建立**] 以建立主機群組。

建立主機群組只需要幾分鐘的時間。

## <a name="create-a-dedicated-host"></a>建立專用主機

現在, 在主機群組中建立專用主機。 除了主機的名稱之外, 您還必須提供主機的 SKU。 主機 SKU 會捕捉支援的 VM 系列, 以及專用主機的硬體世代。  在預覽期間, 我們將支援下列主機 SKU 值:DSv3_Type1 和 ESv3_Type1。

如需主機 Sku 和定價的詳細資訊, 請參閱[Azure 專用主機定價](https://aka.ms/ADHPricing)。

如果您為主機群組設定容錯網域計數, 系統會要求您指定主機的容錯網域。  

1. 選取左上角的 [**建立資源**]。
1. 搜尋 [**專用主機**], 然後從結果中選取 [**專用主機 (預覽)** ]。

    ![主機群組搜尋結果。](./media/virtual-machines-common-dedicated-hosts-portal/host.png)
1. 在 [**專用主機 (預覽)** ] 頁面中, 選取 [**建立**]。
1. 選取您想要使用的訂用帳戶。
1. 選取 [ *myDedicatedHostsRG* ] 作為 [**資源群組**]。
1. 在 [**實例詳細資料**] 中, 輸入*Myhost 代表*作為 [**名稱**], 然後選取 [*美國東部*] 作為位置。
1. 在 [**硬體設定檔**] 中, 選取 [*標準以及 es3 系列]-* 在 [**大小] 系列**中輸入 1, 針對 [**主機] 群組**選取 [ *myHostGrup* ], 然後針對 [**容錯網域**] 選取*1* 。 保留其餘欄位的預設值。
1. 當您完成時, 選取 [審核] [ **+ 建立**] 並等候驗證。

    ![主機設定](./media/virtual-machines-common-dedicated-hosts-portal/host-settings.png)
1. 一旦您看到 [**通過驗證**] 訊息, 請選取 [**建立**] 以建立主機。


