---
title: 快速入門-建立私用雲端上的 VMware VM
description: 描述如何建立和 VMware VM CloudSimple 私用雲端
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5837fd615b8dbf32ff289a2e0d09da9db51a908d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481500"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>建立私用雲端上的 VMware 虛擬機器

若要建立私用雲端的虛擬機器，開始從 Azure 入口網站存取 CloudSimple 入口網站。

## <a name="sign-in-to-azure"></a>登入 Azure

在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。

## <a name="access-the-cloudsimple-portal"></a>存取 CloudSimple 入口網站

1. 選取 [所有服務]  。
2. 搜尋**CloudSimple 服務**。
3. 選取您要建立私用雲端的 CloudSimple 服務。
4. 從**概觀**頁面上，按一下**移至 CloudSimple 入口網站**開啟 CloudSimple 入口網站的新瀏覽器索引標籤。  出現提示時，使用登入您的 Azure 登入認證。  

    ![啟動 CloudSimple 入口網站](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>啟動 vCenter web ui

您現在可以啟動 vCenter 對設定虛擬機器和原則。

若要存取 vCenter，請從 CloudSimple 入口網站啟動。 在首頁上，底下**常見工作**，按一下**啟動 vSphere 用戶端**。  選取 私用雲端，然後按一下**啟動 vSphere 用戶端**私用雲端。

   ![啟動 vSphere 用戶端](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>上傳的 ISO 或 vSphere 的範本

> [!WARNING]
> ISO 上傳、 使用 vSphere HTML5 用戶端。  使用 Flash 用戶端，可能會導致錯誤。

1. 取得您想要上傳至 vCenter 來建立虛擬機器，並讓它可在您的本機系統的 ISO 或 vSphere 範本。

2. 在 vCenter 中，按一下**磁碟**圖示，然後選取**vsanDatastore**。 按一下 **檔案**，然後按一下**新資料夾**。

    ![vCenter ISO](media/vcenter-create-folder.png)

3. 建立資料夾來存放 ISO 檔案。

4. 瀏覽至新建立的資料夾，然後按一下**上傳檔案**。 請遵循螢幕上指示上傳的 ISO。

## <a name="create-a-virtual-machine-in-vcenter"></a>在 vCenter 中建立虛擬機器

1. 在 vCenter 中，按一下**主機和叢集**圖示。

2. 以滑鼠右鍵按一下**工作負載**，然後選取**新的虛擬機器**。
    
    ![建立虛擬機器](media/create-vcenter-virtual-machine-01.png)

3. 選取 [**建立新的虛擬機器**然後按一下**下一步]** 。

    ![新的虛擬機器精靈](media/create-vcenter-virtual-machine-02.png)

4. 名稱的電腦，選取**工作負載的 VM**資料夾，然後按一下**下一步**。

    ![選取名稱和資料夾](media/create-vcenter-virtual-machine-03.png)

5. 選取 [**工作負載**計算資源，然後按一下**下一步]** 。

    ![選取的計算資源](media/create-vcenter-virtual-machine-04.png)

6. 選取 [ **vsanDatastore**然後按一下**下一步]** 。

    ![選取儲存體](media/create-vcenter-virtual-machine-05.png)

7. 您可以保留預設 ESXi 6.5 相容性選項，然後按一下 [**下一步]** 。

    ![選取 相容性](media/create-vcenter-virtual-machine-06.png)

8. 選取虛擬機器 ISO 的客體 OS，然後按一下**下一步**。

    ![自訂客體作業系統](media/create-vcenter-virtual-machine-07.png)

9. 選取的硬碟和網路選項。 針對新的 CD/DVD 光碟機中，選取**資料存放區的 ISO 檔案**。  如果您想要允許從公用 IP 位址給此虛擬機器的流量，選取為網路**vm-1**。

    ![選取硬體自訂](media/create-vcenter-virtual-machine-08.png)

10. 選取範圍視窗隨即開啟。 選取您先前上傳到 Iso 和範本資料夾的檔案，然後按一下**確定**。

    ![選取 ISO](media/create-vcenter-virtual-machine-10.png)

11. 檢閱設定，然後按一下**確定**來建立虛擬機器。

    ![檢閱選項](media/create-vcenter-virtual-machine-11.png)

虛擬機器現在已新增至工作負載的計算資源，並可供使用。 

![在 vCenter 中新的虛擬機器](media/create-vcenter-virtual-machine-12.png)

基本的安裝程式現在已完成的。 您可以開始使用私用雲端類似於您如何使用您的內部部署虛擬機器基礎結構。

下一節包含設定 DNS 和 DHCP 伺服器的私用雲端工作負載和修改預設的網路組態的選擇性資訊。

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>將使用者和身分識別來源加入 vCenter （選擇性）

CloudSimple 指派預設的 vCenter 使用者帳戶具有使用者名稱 **cloudowner@cloudsimple.local** 。 需要您開始進行任何額外的帳戶設定不。  CloudSimple 通常會指派系統管理員執行正常作業所需的權限。  設定您的內部部署 active directory 或 Azure AD[額外的身分識別來源](https://docs.azure.cloudsimple.com/set-vcenter-identity/)私用雲端。

## <a name="create-a-dns-and-dhcp-server-optional"></a>建立 DNS 和 DHCP 伺服器 （選擇性）

應用程式和私用雲端環境中執行的工作負載需要名稱解析和查閱和 IP 位址指派的 DHCP 服務。 適當的 DHCP 和 DNS 基礎結構，才能提供這些服務。 您可以在 vCenter 來提供這些服務以您的私用雲端環境中設定虛擬機器。

### <a name="prerequisites"></a>必要條件

* VLAN 設定分散式的連接埠群組

* 若要在內部部署或以網際網路為基礎的 DNS 伺服器設定的路由

* 虛擬機器範本或建立虛擬機器的 ISO

下列連結提供指引，設定 Linux 和 Windows 上的 DHCP 和 DNS 伺服器。

### <a name="linux-based-dns-server-setup"></a>以 Linux 為基礎的 DNS 伺服器設定

Linux 提供不同的套件，來設定 DNS 伺服器。  以下是開放原始碼繫結的 DNS 伺服器所設定的指示的連結。

[範例設定](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>以 Windows 為基礎的安裝程式

這些 Microsoft 文章說明如何設定 Windows 伺服器做為 DNS 伺服器和 DHCP 伺服器。
<br>
[Windows Server 做為 DNS 伺服器](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server 做為 DHCP 伺服器](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>自訂網路設定 （選擇性）

CloudSimple 入口網站中的 [網路] 頁面可讓您指定防火牆的資料表的組態和虛擬機器的公用 IP 位址。

### <a name="allocate-public-ips"></a>配置的公用 Ip

1. 瀏覽至**網路 > 公用 IP** CloudSimple 入口網站中。
2. 按一下 **配置公用 IP**。
3. 輸入名稱來識別 IP 位址項目。
4. 選取您的私用雲端的位置。
5. 若要變更的閒置逾時，如有必要，使用滑桿。
6. 輸入您要指派的公用 IP 位址的本機 IP 位址。
7. 如有需要，請輸入相關聯的 DNS 名稱。
8. 按一下 [完成]  。

    ![公用 IP](media/quick-create-pc-public-ip.png)

配置的公用 IP 位址的作業開始。 您可以檢查工作狀態**活動 > 工作**頁面。 當配置完成時，新的項目會顯示在 [公用 Ip] 頁面上。

此 IP 位址必須對應至虛擬機器必須設定為使用上述指定的本機位址。 若要設定的 IP 位址的程序是特定虛擬機器的作業系統。 虛擬機器作業系統的正確程序，請參閱文件。

#### <a name="example"></a>範例

例如，以下是 Ubuntu 16.04 的詳細資料。

將靜態方法加入至 inet 位址家族中的組態檔案```/etc/network/interfaces```。 變更地址、 netmask 和閘道的值。 此範例中，我們會使用 eth0 介面、 內部 IP 位址 192.168.24.10、 閘道位址 192.168.24.1 和網路遮罩 255.255.255.0。 

編輯 ```interfaces``` 檔案。

```
sudo vi /etc/network/interfaces
```

更新中的下列區段```interfaces```檔案。

```
auto eth0
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```

停用的介面。

```
sudo ifdown eth0
```

重新啟用的介面。

```
sudo ifup eth0
```

根據預設，所有來自網際網路的連入流量是**拒絕**。 如果您想要開啟任何其他連接埠，建立[防火牆資料表](https://docs.azure.cloudsimple.com/firewall/)。

設定為靜態 IP 位址的內部 IP 位址之後, 請確認您可以透過網際網路從虛擬機器內。

```
ping 8.8.8.8
```

確認您可以連線到虛擬機器從網際網路使用公用 IP 位址。

請確定虛擬機器上的任何防火牆 (iptable) 」 規則並未封鎖連接埠 80 輸入。

```
netstat -an | grep 80
```

啟動接聽連接埠 80 的 http 伺服器。
       
```
python2.7 -m SimpleHTTPServer 80
```

或

```
python3 -m http.server 80
```

在您的桌面上啟動瀏覽器並指向連接埠 80 的公用 IP 位址來瀏覽您的虛擬機器上的檔案。 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>預設 CloudSimple 公用 ip 的防火牆規則

* VPN 流量：允許 （來回） VPN 和所有工作負載網路和管理網路之間的所有流量。
* 私用雲端的內部流量：允許所有東-西流量之間 （來回） 工作負載網路和管理網路 （如上所示）。
* 網際網路流量：
    * 拒絕來自網際網路的所有連入流量工作負載網路和管理網路。
    * 允許所有傳出到網際網路的流量工作負載網路或管理網路。

您也可以修改的方式保護您的流量，使用防火牆規則的功能。 如需詳細資訊，請參閱 <<c0> [ 設定防火牆的資料表和規則](https://docs.azure.cloudsimple.com/firewall/)。

## <a name="install-solutions-optional"></a>安裝解決方案 （選擇性）
您可以在 CloudSimple 私用雲端來充分利用私用雲端部署 vCenter 環境上安裝解決方案。 您可以設定備份、 災害復原、 複寫和其他函式來保護您的虛擬機器。 範例包括 VMware 站台復原管理員 (VMware SRM) 和 Veeam Backup & Replication。

若要安裝方案，您必須在有限期間內要求額外的權限。 請參閱[提升權限](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges)。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 上的 VMware 虛擬機器](quickstart-create-vmware-virtual-machine.md)
* [連線到內部部署網路使用 Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
* [設定 CloudSimple 網路上的 VPN 閘道](https://docs.azure.cloudsimple.com/vpn-gateway)
