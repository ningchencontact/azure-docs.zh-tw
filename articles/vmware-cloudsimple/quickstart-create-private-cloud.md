---
title: Azure 的 VMware 解決方案由 CloudSimple 快速入門-建立私人雲端
description: 了解如何建立和設定私用雲端與 Azure CloudSimple VMware 方案
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efeadd2e1600e5ee572c4a69dde0ff4c53a13cd7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577656"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>快速入門-設定私用雲端環境

在這篇文章，了解如何建立 CloudSimple 私用雲端，並設定您的私用雲端環境。

## <a name="sign-in-to-azure"></a>登入 Azure
在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。

## <a name="create-a-private-cloud"></a>建立私人雲端

1. 選取 [所有服務]。
2. 搜尋**CloudSimple 服務**。
3. 選取您要建立私用雲端的 CloudSimple 服務。
4. 從 概觀，請按一下**建立私人雲端**開啟 CloudSimple 入口網站的新瀏覽器索引標籤。  出現提示時，使用登入您的 Azure 登入認證。  

    ![從 Azure 中建立私用雲端](media/create-private-cloud-from-azure.png)

5. 在 CloudSimple 入口網站中，提供您的私用雲端名稱
6. 選取 **位置**的私用雲端
7. 選取 **節點型別**您購買 Azure 上。  您可以選擇[CS28 或 CS36 選項](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku)。 後面這個選項會包含最大的運算和記憶體容量。
8. 指定**節點計數**。  建立私用雲端需要至少三個節點

    ![建立私用雲端-基本資訊](media/create-private-cloud-basic-info.png)

9. 按一下 **下一步進階選項**。
10. 輸入 vSphere/vSAN 子網路的 CIDR 範圍。 請確定不會與任何您內部部署或其他 Azure 子網路重疊的 CIDR 範圍。

    ![建立私用雲端-進階選項](media/create-private-cloud-advanced-options.png)

11. 完成時，選取 下一步:**檢閱並建立**。
12. 檢閱設定。 如果您需要變更任何設定，按一下**Previous**。
13. 按一下頁面底部的 [新增] 。

私用雲端佈建程序將會啟動。  它可能需要最多佈建私用雲端的兩個小時。

## <a name="launch-cloudsimple-portal"></a>啟動 CloudSimple 入口網站

您可以從 Azure 入口網站存取 CloudSimple 入口網站。  CloudSimple 入口網站將會啟動您的 azure 登入使用單一登入 (SSO) 認證。  存取 CloudSimple 入口網站需要您授權**CloudSimple Service 授權**應用程式。  如需有關如何授與權限的詳細資訊，請參閱[同意 CloudSimple Service 授權應用程式](https://docs.azure.cloudsimple.com/access-cloudsimple-portal/#consent-to-cloudsimple-service-authorization-application)

1. 選取 [所有服務]。
2. 搜尋**CloudSimple 服務**。
3. 選取您要建立私用雲端的 CloudSimple 服務。
4. 從 概觀，請按一下**移至 CloudSimple 入口網站**開啟 CloudSimple 入口網站的新瀏覽器索引標籤。  出現提示時，使用登入您的 Azure 登入認證。  

    ![啟動 CloudSimple 入口網站](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>建立點對站 VPN

點對站 VPN 連線是最簡單的方式，從您的電腦連線到您的私用雲端。 如果您要從遠端連線至私人雲端，請使用點對站 VPN 連線。  快速存取私用雲端，請遵循下列步驟。  從您的內部部署網路存取 CloudSimple 區域可以在使用[站對站 VPN](https://docs.azure.cloudsimple.com/vpn-gateway/)或是[Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)。

### <a name="create-gateway"></a>建立閘道

1. 啟動入口網站，然後選取 CloudSimple**網路**。
2. 選取  **VPN 閘道**。
3. 按一下 **新的 VPN 閘道**。

    ![建立 VPN 閘道](media/create-vpn-gateway.png)

4. 針對**閘道組態**，指定下列設定，然後按一下**下一步**。

    * 選取 **點對站 VPN**作為閘道類型。
    * 輸入要找出閘道的名稱。
    * 選取您的 CloudSimple 服務部署所在的 Azure 位置。
    * 指定點對站閘道的子網路用戶端。  當您連接時，將從這個子網路提供 DHCP 位址。

5. 針對**每次使用者的連接**，指定下列設定，然後按一下**下一步**。

    * 若要自動允許透過此點對站閘道存取私用雲端的所有目前和未來使用者，請選取**自動將所有使用者都加入**。 當您選取此選項時，會自動選取 [使用者] 清單中的所有使用者。 若要覆寫 [自動] 選項，可以取消選取清單中的個別使用者。
    * 若要選取個別的使用者，請按一下核取方塊，在使用者清單中。

6. Vlan/子網路 區段可讓您指定管理和使用者 Vlan/子網路的閘道和連線。

    * **自動新增**設定此閘道的全域原則的選項。 設定適用於目前的閘道。 設定可以覆寫**選取**區域。
    * 選取 **新增 Vlan/子網路的私人雲端的管理**。 
    * 若要新增所有使用者定義的 Vlan/子網路，請按一下**新增使用者定義的 Vlan/子網路**。 
    * **選取 **設定會覆寫下方的全域設定**自動加入**。 

7. 按一下 [**下一步]** 檢閱設定。 按一下編輯圖示，即可進行任何變更。
8. 按一下 **建立**建立 VPN 閘道。

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>連接到 CloudSimple 使用點對站 VPN

從您的電腦連線到 CloudSimple 需要 VPN 用戶端。  下載[OpenVPN 用戶端](https://openvpn.net/community-downloads/)的 Windows 或[黏性](https://www.sparklabs.com/viscosity/download/)適用於 macOS 和 OS X。

1. 啟動入口網站，然後選取 CloudSimple**網路**。
2. 選取  **VPN 閘道**。
3. 從 VPN 閘道的清單中，按一下 點對站 VPN 閘道。
4. 選取 [使用者]。
5. 按一下**下載我的 VPN 設定**

    ![下載 VPN 組態](media/download-p2s-vpn-configuration.png)

6. 匯入您的 VPN 用戶端上的設定

    * 指示[匯入 Windows 用戶端上的設定](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * 指示[匯入在 macOS 或 OS X 上的設定](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. 連接到 CloudSimple

## <a name="create-a-vlan-for-your-workload-vms"></a>建立您的工作負載 Vm 的 VLAN

建立私人雲端之後, 建立的 VLAN，您要在其中部署您的工作負載/應用程式的 Vm。

1. 在 CloudSimple 入口網站中，選取**網路**。
2. 按一下  **VLAN/子網路**。
3. 按一下 **建立 VLAN/子網路**

    ![建立 VLAN/子網路](media/create-new-vlan-subnet.png)

4. 選取 **私用雲端**新的 VLAN/子網路。
5. 從清單中選取的 VLAN ID。  
6. 輸入可識別的子網路的子網路名稱。
7. 指定的子網路 CIDR 範圍和遮罩。  此範圍不得與任何現有的子網路重疊。
8. 按一下 [提交] 。

    ![建立 VLAN/子網路詳細資料](media/create-new-vlan-subnet-details.png)

將建立 VLAN/子網路。  您現在可以使用此 VLAN ID，在您的私用雲端 vCenter 上建立分散式的連接埠群組。 

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>將環境連接到 Azure 虛擬網路

CloudSimple 為您提供為 ExpressRoute 線路的私人雲端。 您可以連接您在 Azure 上的虛擬網路的 ExpressRoute 線路。 如需完整有關設定連接的詳細資訊，請依照下列中的步驟[Azure 使用 ExpressRoute 虛擬網路連線](https://docs.azure.cloudsimple.com/azure-er-connection)

## <a name="sign-in-to-vcenter"></a>登入 vCenter

您現在可以登入 vCenter 來設定虛擬機器和原則。

1. 若要存取 vCenter，請從 CloudSimple 入口網站啟動。 在首頁上，底下**常見工作**，按一下**啟動 vSphere 用戶端**。  選取 私用雲端，然後按一下**啟動 vSphere 用戶端**私用雲端。

    ![啟動 vSphere 用戶端](media/launch-vcenter-from-cloudsimple-portal.png)

2. 選取您的慣用的 vSphere 用戶端存取 vCenter，並使用您的使用者名稱和密碼登入。  預設值為：
    * 使用者名稱： **CloudOwner@cloudsimple.local**
    * 密碼：**CloudSimple123!**  

在下一個程序中的 vCenter 畫面是來自 vSphere (HTML5) 用戶端。

## <a name="change-your-vcenter-password"></a>變更您 vCenter 的密碼

CloudSimple 建議您變更您的密碼登入 vCenter 的第一次。  
您所設定的密碼必須符合下列需求：

* 最長存留期：密碼必須變更每個 365 天
* 限制重複使用：使用者不能重複使用任何先前的五個密碼
* 長度：8-20 個字元
* 特殊字元：至少 1 個特殊字元
* 字母的字元：至少一個大寫字元、 A-Z、0-9 和至少一個小寫字元、 a-z、0-9
* 數字：至少一個數字 0-9
* 最大相同相鄰的字元：三

    範例：CC 或 CCC 是可接受的密碼，一部分，但 CCCC 不。

如果您設定的密碼不符合需求：

* 如果您使用 vSphere Flash 用戶端，則會回報錯誤
* 如果您使用 HTML5 用戶端，而不會回報錯誤。 用戶端不接受變更，舊的密碼會繼續運作。

## <a name="change-nsx-administrator-password"></a>變更 NSX 系統管理員密碼

NSX manager 會使用預設密碼部署。  我們建議您建立私用雲端之後，變更密碼。

   * 使用者名稱：**系統管理員**
   * 密碼：**CloudSimple123!**

您可以 CloudSimple 入口網站上找到的完整的網域名稱 (FQDN) 和 NSX manager IP 位址。

1. 啟動入口網站，然後選取 CloudSimple**資源**。
2. 按一下您想要使用私用雲端。
3. 選取**vSphere 管理網路**
4. 使用的 FQDN 或 IP 位址**NSX Manager**並使用網頁瀏覽器連接。 

    ![尋找 NSX Manager FQDN](media/private-cloud-nsx-manager-fqdn.png)

若要變更密碼，請依照下列中的指示[NSX Manager 安裝](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.2/com.vmware.nsxt.install.doc/GUID-A65FE3DD-C4F1-47EC-B952-DEDF1A3DD0CF.html)。

## <a name="create-a-port-group"></a>建立通訊埠群組

若要在 vSphere 建立分散式的連接埠群組：

1. 遵循 [新增分散式通訊埠群組] 中的指示[vSphere 網路功能指南](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf)。
2. 設定時的分散式通訊埠群組，提供在中建立的 VLAN ID[的工作負載 Vm 建立 VLAN](#create-a-vlan-for-your-workload-vms)。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 上的 VMware Vm](https://docs.azure.cloudsimple.com/quickstart-create-vmware-virtual-machine)
* [使用 Azure 上的 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* [連線到內部部署網路使用 Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [設定從內部部署的站對站 VPN](https://docs.azure.cloudsimple.com/vpn-gateway/)
