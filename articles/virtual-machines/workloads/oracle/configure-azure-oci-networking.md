---
title: Oracle 雲端基礎結構與連線的 Azure ExpressRoute |Microsoft Docs
description: 若要啟用跨雲端應用程式解決方案，Oracle 的 Oracle 雲端基礎結構 (OCI) FastConnect 連接 Azure ExpressRoute
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/24/2019
ms.author: rogirdh
ms.openlocfilehash: 671d7c8eb9f10e346b49056e1cc117c9882bb6e8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707629"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>設定 Azure 與 Oracle 雲端基礎結構之間的直接互相連線  

若要建立[整合的多重雲端體驗](oracle-oci-overview.md)（預覽）、 Microsoft 和 Oracle 提供 Azure 與 Oracle 雲端基礎結構 (OCI) 透過之間的直接互連[ExpressRoute](../../../expressroute/expressroute-introduction.md)和[FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)。 透過 ExpressRoute 和 FastConnect 互相連線的橋樑，客戶可能會導致低延遲、 高輸送量、 兩個雲端之間的私人直接連線。

> [!IMPORTANT]
> Microsoft Azure 與 OCI 之間的連線處於預覽階段。 若要讓 Azure 與 OCI 之間的低延遲連線，OCI 租用戶與 Azure 訂用帳戶必須先針對這項功能列入允許清單。

下圖顯示互連的高階概觀：

![跨雲端網路連線](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>必要條件

* 若要建立 Azure 和 OCI 之間的連線，您必須擁有使用中的 Azure 訂用帳戶和作用中的 OCI 租用戶。

* 連線能力才可能其中的 Azure ExpressRoute 對等互連位置是在鄰近性或與 OCI FastConnect 相同的對等互連位置。 請參閱[預覽限制](oracle-oci-overview.md#preview-limitations)。

* 您的 Azure 訂用帳戶必須是此預覽功能的允許清單。 請連絡您的 Microsoft 代表您的訂用帳戶上啟用此功能。

* 您的 OCI 租用戶必須是此預覽功能的允許清單。 如需詳細資訊，請連絡您的 Oracle 代表。

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>設定 ExpressRoute 和 FastConnect 之間的直接連線

1. Azure 訂用帳戶資源群組下建立標準的 ExpressRoute 線路。 
    * 在建立 ExpressRoute 時，選擇**Oracle 雲端 FastConnect**做為服務提供者。 若要建立 ExpressRoute 線路，請參閱[逐步指南](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)。
    * Azure ExpressRoute 線路提供細微的頻寬選項，而 FastConnect 支援 1、 2、 5 或 10 Gbps。 因此，建議您選擇其中一個 ExpressRoute 下比對的頻寬選項。

    ![建立 ExpressRoute 線路](media/configure-azure-oci-networking/exr-create-new.png)
1. 記下您的 ExpressRoute**服務金鑰**。 您需要設定 FastConnect 線路時提供金鑰。

    ![ExpressRoute 服務金鑰](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > 您還是必須支付 ExpressRoute 費用為 ExpressRoute 線路佈建 (即使**提供者狀態**是**不會佈建**)。

1. 規劃每個，不會與您的 Azure 虛擬網路或 OCI 雲端虛擬網路的 IP 位址空間重疊的/30 的兩個私人 IP 位址空間。 我們會將第一個 IP 位址空間，做為主要的位址空間和次要位址空間的第二個 IP 位址空間。 請記下設定 FastConnect 線路時，您需要的位址。
1. 建立動態路由閘道 (DRG)。 建立 FastConnect 線路時，您將需要此。 如需詳細資訊，請參閱 <<c0> [ 動態路由閘道](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)文件。
1. 建立您的 Oracle 租用戶下 FastConnect 線路。 如需詳細資訊，請參閱 < [Oracle 文件集](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)。
  
    * FastConnect 設定 底下選取**Microsoft Azure:ExpressRoute**作為提供者。
    * 選取上一個步驟中的動態路由閘道，您佈建。
    * 選取要佈建的頻寬。 為了達到最佳效能，頻寬必須符合建立 ExpressRoute 線路時選取的頻寬。
    * 在 **提供者服務金鑰**，貼上的 ExpressRoute 服務金鑰。
    * 私人 IP 位址空間劃分為上一個步驟中使用第一個/30**主要的 BGP IP 位址**和第二個/30 的私人 IP 位址空間**次要 BGP IP**位址。
        * 將指派兩個範圍的第一個可用位址 Oracle BGP IP 位址 （主要和次要） 和第二個位址給客戶 BGP IP 位址 （從 FastConnect 觀點來看）。 第一個可用的 IP 位址是第二個 IP 位址的/30 的位址的空間 （第一個 IP 位址由 Microsoft 所保留的）。
    * 按一下 [建立]  。
1. 完成將 FastConnect 連結至您透過動態路由閘道，使用路由表的 Oracle 租用戶下的虛擬雲端網路。
1. 瀏覽至 Azure，並確定**提供者狀態**為您的 ExpressRoute 電路已變更為**已佈建**且型別對等互連**Azure 私用**已佈建。 這是必要條件，如下列步驟。

    ![ExpressRoute 提供者狀態](media/configure-azure-oci-networking/exr-provider-status.png)
1. 按一下  **Azure 私用**對等互連。 您會看到對等互連的詳細資料自動設定根據您輸入時設定 FastConnect 線路上的資訊。

    ![私用對等互連設定](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>連接到 ExpressRoute 的虛擬網路

1. 如果您還沒有這麼做，請建立虛擬網路和虛擬網路閘道。 如需詳細資訊，請參閱 <<c0> [ 逐步指南](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。
1. 設定虛擬網路閘道與 ExpressRoute 線路之間的連線，藉由執行[Terraform 指令碼](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2)或執行 PowerShell 命令，以[設定 ExpressRoute 快速](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)。

完成網路組態之後，您可以按一下來確認您的組態的有效性**取得 ARP 記錄**並**取得路由表**下 [ExpressRoute 私人對等互連] 刀鋒視窗Azure 入口網站中。

## <a name="automation"></a>自動化

Microsoft 已建立 Terraform 指令碼，以啟用網路相互連接的自動的部署。 Terraform 指令碼需要向 Azure 進行驗證之前執行，因為它們需要 Azure 訂用帳戶有足夠的權限。 驗證可以使用執行[Azure Active Directory 服務主體](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)或使用 Azure CLI。 如需詳細資訊，請參閱 < [Terraform 文件](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)。

在此，則可以找到的 Terraform 指令碼和相關的文件，以部署 inter-connect [GitHub 存放庫](https://aka.ms/azureociinterconnecttf)。

## <a name="monitoring"></a>監視

在這兩個雲端上安裝代理程式，您可以利用 Azure[網路效能監控 (NPM)](../../../expressroute/how-to-npm.md)監視端對端網路的效能。 NPM 可協助您輕易地找出網路問題，並協助您排除它們。

## <a name="delete-the-interconnect-link"></a>刪除相互連接的連結

若要刪除的相互連接，下列步驟必須遵循，給定的特定順序。 若要這樣做會導致 「 失敗的狀態 」 ExpressRoute 線路。

1. 刪除 ExpressRoute 連線。 刪除連線，方法是按一下**刪除**連線頁面上的圖示。 如需詳細資訊，請參閱 < [ExpressRoute 文件](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet)。
1. Oracle FastConnect 從 Oracle 雲端主控台刪除。
1. 一旦 Oracle FastConnect 電路已遭刪除，您可以刪除的 Azure ExpressRoute 線路。

此時，刪除和取消佈建程序已完成。

## <a name="next-steps"></a>後續步驟

* 如需有關跨雲端 OCI 與 Azure 之間連線的詳細資訊，請參閱[Oracle 文件集](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)。
* 使用[Terraform 指令碼](https://aka.ms/azureociinterconnecttf)透過 Azure，部署目標的 Oracle 應用程式的基礎結構和設定網路相互連接。 