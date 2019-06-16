---
title: 連線至對等網路中 Azure 實驗室服務 |Microsoft Docs
description: 了解如何與做為對等的另一個網路連接您的實驗室網路。 比方說，連接您的內部部署學校/大專院校網路與在 Azure 中的實驗室的虛擬網路。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: c9b305beae1b385d4714e3a80e6843c7e76a4f60
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411020"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>連接您的實驗室網路與 Azure 實驗室服務中的對等虛擬網路 
本文章提供您的實驗室網路與另一個網路對等互連的相關資訊。 

## <a name="overview"></a>概觀
虛擬網路對等互連可讓您順暢地連接 Azure 虛擬網路。 經過對等互連後，所有虛擬網路就可以作為一個整體來進行連線。 透過 Microsoft 骨幹基礎結構的對等互連的虛擬網路中的虛擬機器之間的流量會路由傳送，就像在相同的虛擬網路上，透過私人 IP 位址只有虛擬機器之間路由傳送。 如需詳細資訊，請參閱[虛擬網路對等互連](../../virtual-network/virtual-network-peering-overview.md)。

您可能需要您的實驗室網路連線與對等虛擬網路在某些情況下，包括下列項目：

- 實驗室中的虛擬機器已連接到內部部署授權伺服器，以取得授權的軟體
- 實驗室中的虛擬機器需要存取資料集 （或任何其他檔案），在大學的網路共用上。 

可能是特定的內部部署網路連線到 Azure 虛擬網路透過[ExpressRoute](../../expressroute/expressroute-introduction.md)或是[虛擬網路閘道](../../vpn-gateway/vpn-gateway-about-vpngateways.md)。 這些服務必須設定外部 Azure 實驗室服務。 若要深入了解在內部部署網路連線到 Azure 中使用 ExpressRoute，請參閱 [ExpressRoute 概觀]） (.../expressroute/expressroute-introduction.md)。 在內部部署連線能力使用虛擬網路閘道，閘道器，指定虛擬網路，和實驗室帳戶必須位於相同區域中。

## <a name="configure-at-the-time-of-lab-account-creation"></a>在實驗室帳戶建立時設定
新實驗室帳戶建立期間，您可以挑選現有的虛擬網路中顯示**對等虛擬網路**下拉式清單中。 選取的虛擬網路是 connected(peered) 實驗室帳戶下建立的實驗室。 所有虛擬機器建立後進行的實驗室中這項變更對資源的存取權的對等互連的虛擬網路。 

![選取對等互連的 VNet](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> 建立實驗室帳戶的詳細逐步指示，請參閱[設定實驗室帳戶](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>建立實驗室之後設定
相同的屬性可從**Labs 組態**索引標籤**實驗室帳戶**頁面上，如果您沒有對等網路設定實驗室帳戶建立時。 變更此設定僅適用於在變更後所建立的實驗室。 您可以看到映像中，您可以啟用或停用**對等虛擬網路**適用於實驗室帳戶中的實驗室。 

![啟用或停用建立實驗室之後，對等互連的 VNet](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

當您選取的虛擬網路**對等虛擬網路**欄位中，**挑選實驗室位置允許實驗室建立者**選項會停用。 這是因為實驗室帳戶中的實驗室，必須位於與實驗室帳戶，才能連線對等虛擬網路中的資源相同的區域。 

> [!IMPORTANT]
> 變更此設定僅適用於之後進行變更時，不是以現有的實驗室所建立的實驗室。 


## <a name="next-steps"></a>後續步驟
請參閱下列文章：

- [以管理員身分建立及管理實驗室帳戶](how-to-manage-lab-accounts.md)
- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)

