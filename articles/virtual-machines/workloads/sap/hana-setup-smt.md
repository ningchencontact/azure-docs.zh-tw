---
title: 如何設定 SAP HANA on Azure (大型執行個體) 適用的 SMT 伺服器 | Microsoft Docs
description: 如何設定 SAP HANA on Azure (大型執行個體) 適用的 SMT 伺服器。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f387c1afe88f2bba476309b2e2e01942d2b7ae5b
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982620"
---
# <a name="setting-up-smt-server-for-suse-linux"></a>設定 SUSE Linux 的 SMT 伺服器
SAP HANA 大型執行個體無法直接連線到網際網路。 因此它不是可向作業系統提供者註冊此類單元，並且下載與套用修補程式的簡單程序。 如果是 SUSE Linux，其中一種解決方案可能是在 Azure VM 中設定 SMT 伺服器。 而 Azure VM 需要裝載於 Azure VNet 中，連接到 HANA 大型執行個體。 有了這類的 SMT 伺服器，HANA 大型執行個體單位才可能註冊並下載修補程式。 

SUSE 提供大量有關 [Subscription Management Tool for SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf) (SLES 12 SP2 訂用帳戶管理工具) 的指南。 

安裝 SMT 伺服器的先決條件是完成 HANA 大型執行個體的工作，您需要：

- 連接到 HANA 大型執行個體 ER 線路的 Azure VNet。
- 與組織相關聯的 SUSE 帳戶。 而組織會需要一些有效的 SUSE 訂用帳戶。

## <a name="installation-of-smt-server-on-azure-vm"></a>在 Azure VM 上安裝 SMT 伺服器

在此步驟中，您要在 Azure VM 上安裝 SMT 伺服器。 第一個量值是登入 [SUSE 客戶中心](https://scc.suse.com/)。

當您登入後，請移至 [組織]--> [Organization Credentials] \(組織認證)。 在該區段中，您應該會找到設定 SMT 伺服器所需的認證。

第三個步驟是在 Azure VNet 中安裝 SUSE Linux VM。 若要部署 VM，請使用 Azure 的 SLES 12 SP2 資源庫映像 (選取 BYOS SUSE 映像)。 在部署程序中，不要定義 DNS 名稱，也不要使用這個螢幕擷取畫面中的靜態 IP 位址

![SMT 伺服器的 VM 部署](./media/hana-installation/image3_vm_deployment.png)

已部署的 VM 是較小的 VM，會取得 Azure VNet 的內部 IP 位址 10.34.1.4。 VM 的名稱為 smtserver。 安裝之後，已檢查 HANA 大型執行個體單元的連線。 HANA 大型執行個體單位可能需要以 Azure VM 的 etc/hosts 設定解析，視您如何安排名稱解析而定。 在要用來保存修補程式的 VM 中新增額外的磁碟。 開機磁碟本身可能太小。 在範例中，磁碟已掛接到 /srv/www/htdocs，如下列螢幕擷取畫面所示。 100 GB 的磁碟應已足夠。

![SMT 伺服器的 VM 部署](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

登入 HANA 大型執行個體單位、維護 /etc/hosts，並檢查是否可以連線到應該要透過網路執行 SMT 伺服器的 Azure VM。

順利完成這項檢查之後，您需要登入應該執行 SMT 伺服器的 Azure VM。 如果您要使用 putty 登入 VM，您需要在 bash 視窗中執行這一系列的命令：

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

執行這些命令之後，重新啟動您的 bash 來啟動設定。 然後啟動 YAST。

將 VM (smtserver) 連接至 SUSE 站台。

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

VM 連線到 SUSE 站台後，安裝 smt 套件。 使用下列 putty 命令安裝 smt 套件。

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


您也可以使用 YAST 工具安裝 smt 套件。 在 YAST 中，移至軟體維護並搜尋 smt。 選取 smt，會自動切換至 yast2-smt，如下所示。

![yast 中的 SMT](./media/hana-installation/image5_smt_in_yast.PNG)


接受 smtserver 上的安裝選項。 安裝之後，請移至 SMT 伺服器組態，並輸入之前從 SUSE 客戶中心擷取的組織認證。 亦請輸入 Azure VM 主機名稱當作 SMT 伺服器 URL。 在本例中，它是 https://smtserver，如下張圖所示。

![SMT 伺服器組態](./media/hana-installation/image6_configuration_of_smtserver1.png)

下一個步驟，您需要測試 SUSE 客戶中心的連線是否作用。 如您在下圖中所見，示範案例中的連線可以運作。

![測試 SUSE 客戶中心連線](./media/hana-installation/image7_test_connect.png)

一旦 SMT 安裝程式啟動，您就需要提供資料庫密碼。 因為是新的安裝，所以您需要定義該密碼，如下張圖中所示。

![定義資料庫密碼](./media/hana-installation/image8_define_db_passwd.PNG)

下個互動是建立憑證。 如下所示完成對話方塊，執行後續步驟。

![建立 SMT 伺服器憑證](./media/hana-installation/image9_certificate_creation.PNG)

組態結尾的「執行同步處理檢查」步驟可能需要幾分鐘。 安裝和設定 SMT 伺服器之後，您應該會在掛接點 /srv/www/htdocs/ 下發現目錄儲存機制及儲存機制下的子目錄。 

使用這些命令重新啟動 SMT 伺服器及其相關服務。

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-of-packages-onto-smt-server"></a>將套件下載到 SMT 伺服器

重新啟動所有服務之後，在使用 Yast 的 SMT 管理中選取適當的套件。 套件選項取決於 HANA 大型執行個體伺服器的作業系統映像，不是執行 SMT 伺服器的 VM SLES 版次或版本。 選項畫面範例如下所示。

![選取套件](./media/hana-installation/image10_select_packages.PNG)

一旦完成套件選取，您就需要啟動所設定之 SMT 伺服器的選取套件初始複本。 如下所示，在 shell 中使用命令 smt-mirror 觸發這個複本。


![將套件下載到 SMT 伺服器](./media/hana-installation/image11_download_packages.PNG)

如上文中所見，套件應該複製到建立在掛接點 /srv/www/htdocs 下的目錄。 此程序需要一會兒。 視選取的套件數目而定，可能會超過一個小時或更久。
當此程序完成時，您需要移至 SMT 用戶端安裝程式。 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>在 HANA 大型執行個體單位上設定 SMT 用戶端

本例中的用戶端是 HANA 大型執行個體單位。 SMT 伺服器安裝程式已將指令碼 clientSetup4SMT.sh 複製到 Azure VM。 將該指令碼複製到您想與 SMT 伺服器連線的 HANA 大型執行個體單位。 使用 -h 選項啟動指令碼，並提供 SMT 伺服器的名稱當做參數。 在此範例 smtserver 中。

![設定 SMT 用戶端](./media/hana-installation/image12_configure_client.PNG)

可能有用戶端從伺服器成功載入憑證，但註冊失敗的案例，如下所示。

![用戶端註冊失敗](./media/hana-installation/image13_registration_failed.PNG)

如果註冊失敗，請參閱本 [SUSE 支援文件](https://www.suse.com/de-de/support/kb/doc/?id=7006024)並執行文中所述步驟。

> [!IMPORTANT] 
> 伺服器名稱需要使用 VM 名稱，在本例的 smtserver 中，沒有完整的網域名稱。 只有 VM 名稱作用。 

在執行這些步驟之後，您需要在 HANA 大型執行個體單位上執行下列命令。

```
SUSEConnect –cleanup
```

> [!Note] 
> 在我們的測試中，該步驟後一律必須等候幾分鐘。 在 SUSE 一文所述的更正措施之後，立即執行 clientSetup4SMT.sh，最後可能會出現憑證目前無效的訊息。 通常等 5-10 分鐘後再執行 clientSetup4SMT.sh，就可以順利完成用戶端組態。

如果碰到需要根據 SUSE 一文步驟修正的問題，您需要在 HANA 大型執行個體單位上重新啟動 clientSetup4SMT.sh。 現在它應該如下所示成功完成。

![用戶端註冊成功](./media/hana-installation/image14_finish_client_config.PNG)

在這個步驟中，您設定了 HANA 大型執行個體單位的 SMT 用戶端連線到安裝在 Azure VM 中的 SMT 伺服器。 您現在可以利用 'zypper up' 或 'zypper in' 將作業系統修補程式安裝至 HANA 大型執行個體，或安裝其他套件。 您應該了解，您只能取得之前下載到 SMT 伺服器的修補程式。

**後續步驟**
- 請參閱 [HLI 上的 HANA 安裝](hana-example-installation.md)。











