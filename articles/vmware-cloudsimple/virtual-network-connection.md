---
title: 使用 ExpressRoute 將 Azure 虛擬網路連線到 CloudSimple
description: 說明如何取得 Azure 虛擬網路與 CloudSimple 環境之間連接的對等互連資訊
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ba2d2fd9c943fe55e82956d022f6ba9840a550f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536595"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>使用 ExpressRoute 將 Azure 虛擬網路連線到 CloudSimple

您可以將私人雲端網路擴充至您的 Azure 虛擬網路和 Azure 資源。 ExpressRoute 連線可讓您從私人雲端存取在 Azure 訂用帳戶中執行的資源。

## <a name="request-authorization-key"></a>要求授權金鑰

您的私用雲端與 Azure 虛擬網路之間的 ExpressRoute 連線需要授權金鑰。 若要取得金鑰, 請向<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支援</a>提出票證。  在要求中使用下列資訊:

* 問題類型：**技術**
* 訂閱:選取 CloudSimple 服務部署所在的訂用帳戶 * *
* 服務：**VMware 解決方案 (依 CloudSimple)**
* 問題類型:**服務要求**
* 問題子類型:**Azure VNET 連線的授權金鑰**
* 主體:**要求 Azure VNET 連線的授權金鑰**

## <a name="obtain-peering-information-for-azure-virtual-network-to-cloudsimple-connection"></a>取得 Azure 虛擬網路到 CloudSimple 連線的對等互連資訊

若要設定連線, 您必須在 Azure 虛擬網路與 CloudSimple 環境之間建立連結。  在此過程中, 您必須提供對等線路 URI 和授權金鑰。 從[CloudSimple 入口網站](access-cloudsimple-portal.md)取得 URI 和授權金鑰。  選取側邊功能表上的 [**網路**], 然後選取 [ **Azure 網路**連線]。 或選取側邊功能表上的 [**帳戶**], 然後選取 [ **Azure 網路**連線]。

請注意對等線路 URI 的複製圖示, 以及每個區域的授權金鑰。 針對您想要連接的每個私人雲端:

* 按一下 [**複製**] 以複製 URI。 將它貼入可供新增至 Azure 入口網站的檔案中。  
* 按一下 [**複製**] 來複製授權金鑰, 並將它貼到檔案中。

![虛擬網路連接 頁面](media/network-virt-conn-page.png)

如需設定 Azure 虛擬網路至 CloudSimple 連結的詳細資訊, 請參閱[使用 ExpressRoute 將您的 CloudSimple 私用雲端環境連接到 Azure 虛擬網路](azure-expressroute-connection.md)。
