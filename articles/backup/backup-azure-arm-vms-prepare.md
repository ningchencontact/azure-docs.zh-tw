---
title: 備份 Azure Vm 的復原服務保存庫中使用 Azure 備份
description: 描述如何備份 Azure Vm 的復原服務保存庫中使用 Azure 備份
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: 3342b15511305ab337d9b5032080e205e36150d3
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049808"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>備份復原服務保存庫中的 Azure VM

本文說明如何備份 Azure Vm 的復原服務保存庫中使用[Azure 備份](backup-overview.md)服務。 

在本文中，您將了解：

> [!div class="checklist"]
> * 請確認支援和備份的必要條件。
> * 準備 Azure VM。 視需要安裝 Azure VM 代理程式，並確認 VM 的輸出存取。
> * 建立保存庫。
> * 探索 Vm，並設定備份原則。
> * 啟用 Azure Vm 的備份。


> [!NOTE]
> 本文說明如何設定保存庫，然後選取要備份的 Vm。 這有助於您備份多個 VM。 或者，您可以[來備份單一 Azure VM](backup-azure-vms-first-look-arm.md)直接從 VM 設定。

## <a name="before-you-start"></a>開始之前


- [檢閱](backup-architecture.md#architecture-direct-backup-of-azure-vms)Azure VM 備份架構。
- [深入了解](backup-azure-vms-introduction.md) Azure VM 備份和備份擴充功能。
- [檢閱](backup-support-matrix-iaas.md) Azure VM 備份的支援矩陣。


## <a name="prepare-azure-vms"></a>準備 Azure VM

在某些情況下，您可能需要設定 Azure Vm 上的 Azure VM 代理程式，或明確允許在 VM 上的 輸出存取。

### <a name="install-the-vm-agent"></a>安裝 VM 代理程式 

為機器上執行的 Azure VM 代理程式安裝擴充功能，以 Azure 備份來備份 Azure VM。 如果您的 VM 建立自 Azure Marketplace 映像，代理程式已安裝並執行。 如果您建立自訂的 VM，或在內部部署機器移轉，您可能需要以手動方式，在資料表中摘要說明安裝代理程式。

**VM** | **詳細資料**
--- | ---
** Windows** | 1.[下載並安裝](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)代理程式 MSI 檔案。<br/><br/> 2.以機器的系統管理員權限安裝。<br/><br/> 3.驗證安裝。 在  *C:\WindowsAzure\Packages*的 VM 上，以滑鼠右鍵按一下**WaAppAgent.exe** > **屬性**。 在 **詳細資料**索引標籤上，**產品版本**應為 2.6.1198.718 或更高版本。<br/><br/> 如果您正在更新代理程式，請確定會執行任何備份作業，並[重新安裝代理程式](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
** Linux** | 從您的散發套件存放庫使用 RPM 或 DEB 封裝安裝。 這是安裝和升級 Azure Linux 代理程式的慣用的方法。 所有[認可的散發套件提供者](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)都會將 Azure Linux 代理程式套件整合於本身的映像和儲存機制中。 代理程式可從 [GitHub](https://github.com/Azure/WALinuxAgent) 取得，但不建議從該處安裝。<br/><br/> 如果您正在更新代理程式，請確定任何備份作業正在執行，以及更新二進位檔案。


### <a name="establish-network-connectivity"></a>建立網路連線

在 VM 上執行的備份擴充功能需要 Azure 公用 IP 位址的輸出存取。

一般而言，您不需要明確地允許 Azure VM 的輸出網路存取，讓它能夠與 Azure 備份通訊。
如果您的 Vm 無法連線，而且如果您看到錯誤**ExtensionSnapshotFailedNoNetwork**，您應該明確地允許存取。 備份擴充功能接著可與 Azure 的備份流量的公用 IP 位址通訊。


#### <a name="explicitly-allow-outbound-access"></a>明確允許輸出存取

如果 VM 無法連線到備份服務中，明確允許輸出存取，使用其中一個資料表中摘要說明的方法。

**選項** | ** 動作** | **詳細資料** 
--- | --- | --- 
**設定 NSG 規則** | 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 | 而不是允許和管理每個位址範圍，您可以新增網路安全性群組 (NSG) 規則，可允許使用 Azure 備份服務的存取權[服務標籤](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure)。 [深入了解](../virtual-network/security-overview.md#service-tags)。<br/><br/> 有不需要額外費用。<br/><br/> 規則很容易就能使用服務標籤。
**部署 proxy** | 部署 HTTP Proxy 伺服器來路由傳送流量。 | 這個方法會提供整個 Azure 中，並不只是儲存體的存取。<br/><br/> 可精確控制儲存體 URL。<br/><br/> 沒有適用於 Vm 的單一點的網際網路存取。<br/><br/> 沒有 proxy 的額外成本。
**設定 Azure 防火牆** | 使用 Azure 備份服務的 FQDN 標記的 VM 上，允許流量通過 Azure 防火牆。 |  這個方法很簡單，您可以在虛擬網路子網路中設定的 Azure 防火牆時使用。<br/><br/> 您無法建立您自己的 FQDN 標籤，或修改在標記中的 Fqdn。<br/><br/> 如果您使用 Azure 受控磁碟，您可能需要在防火牆上開啟其他連接埠 (連接埠 8443)。

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>設定 NSG 規則以允許對 Azure 的輸出存取

如果 NSG 管理的 VM 存取權，讓所需的範圍和連接埠的備份儲存體的輸出存取。

1. 在 VM 屬性 > **Networking**，選取**新增輸出連接埠規則**。
2. 在 **新增輸出安全性規則**，選取**進階**。
3. 在 [來源] 中，選取 [VirtualNetwork]。
4. 在 **來源連接埠範圍**，輸入星號 （*） 以允許從任何連接埠的輸出存取。
5. 在 [目的地] 中，選取 [服務標記]。 從清單中選取 [儲存體區域]。 區域是保存庫和您想要備份 Vm 的所在位置。
6. 在 [目的地連接埠範圍] 中，選取連接埠。
    - 使用未加密儲存體帳戶的未受控 VM：80
    - 使用加密儲存體帳戶的未受控 VM：443 (預設設定)
    - 受控 VM：8443。
7. 在 [通訊協定] 中，選取 [TCP]。
8. 在 [優先順序] 中，指定比任何優先順序較高的拒絕規則都小的優先順序值。
   
   如果您有一個規則，拒絕存取時，新的允許規則必須較高。 例如，如果您有優先順序設為 1000 的 **Deny_All** 規則，則新規則必須設為小於 1000。
9. 提供的名稱和描述的規則，然後選取**確定**。

您可以將 NSG 規則套用至多個 VM 來允許輸出存取。 這段影片將逐步說明其程序。

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


##### <a name="route-backup-traffic-through-a-proxy"></a>透過 Proxy 路由備份流量

您可以透過 Proxy 來路由備份流量，然後提供對所需 Azure 範圍的 Proxy 存取權。 設定 proxy 以允許下列 VM:

- Azure VM 應透過 Proxy 路由所有連往公用網際網路的 HTTP 流量。
- Proxy 應該適用的虛擬網路中，以允許從 Vm 的連入流量。
- NSG **NSF-lockdown** 需要一個規則，以允許從 Proxy VM 輸出的網際網路流量。

###### <a name="set-up-the-proxy"></a>設定 Proxy

如果您沒有系統帳戶 Proxy，請以下列方式設定一個：

1. 下載 [PsExec](https://technet.microsoft.com/sysinternals/bb897553)。
2. 執行 **PsExec.exe -i -s cmd.exe**，在系統帳戶下執行命令提示字元。
3. 在系統內容中執行瀏覽器。 例如，使用 **%PROGRAMFILES%\Internet Explorer\iexplore.exe** Internet explorer。  
4. 定義 Proxy 設定。
   - 在 Linux 機器上：
     - 將以下這一行新增至 **/etc/environment** 檔案：
       - **http_proxy = http: \/ /proxy IP 位址： proxy 連接埠**
     - 將以下幾行新增至 **/etc/waagent.conf** 檔案：
         - **HttpProxy.Host=proxy IP 位址**
         - **HttpProxy.Port=proxy port**
   - 在 Windows 機器上，在瀏覽器設定中指定應使用 Proxy。 如果您目前使用使用者帳戶上的 Proxy，您可以使用此指令碼，將此設定套用至系統帳戶層級。
       ```powershell
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>允許 Proxy 上的連入連線

在 Proxy 設定中允許連入連線。

1. 在 Windows 防火牆中開啟**具有進階安全性的 Windows 防火牆**。
2. 以滑鼠右鍵按一下 [輸入規則] > [新增規則]。
3. 在 [**規則類型**，選取**自訂** > **下一步]**。
4. 在 [程式] 中，選取 [所有程式] > [下一步]。
5. 在 **通訊協定和連接埠**:
   - 將類型設為**TCP**。
   - 設定**本機連接埠**要**特定連接埠**。
   - 設定**遠端連接埠**要**所有連接埠**。
  
6. 完成精靈，並指定規則的名稱。

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>新增 Proxy 的 NSG 例外規則

在 NSG **NSF-lockdown** 上，允許從 10.0.0.5 上的任何連接埠傳輸至 80 (HTTP) 或 443 (HTTPS) 連接埠上的任何網際網路位址的流量。

下列 PowerShell 指令碼提供允許流量的範例。
而不是允許輸出至所有的公用網際網路位址，您可以指定 IP 位址範圍 (`-DestinationPortRange`)，或使用 storage.region 服務標籤。   

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

### <a name="allow-firewall-access-by-using-an-fqdn-tag"></a>允許使用 FQDN 標記防火牆存取

您可以設定 Azure 防火牆以允許備份至 Azure 備份的網路流量的輸出存取。

- [了解](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)如何部署 Azure 防火牆。
- [閱讀](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN 標記的相關資訊。

## <a name="modify-storage-replication-settings"></a>修改儲存體複寫設定

根據預設，保存庫會有[異地備援儲存體 (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。 我們建議您的主要備份 GRS。 您可以使用[本地備援儲存體 (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)成本較低的選項。

修改儲存體複寫類型如下所示：

1. 在入口網站中，選取新的保存庫。 底下**設定**，選取**屬性**。
2. 在 **屬性**下方**備份設定**，選取**更新**。
3. 選取儲存體複寫類型，然後選取**儲存**。

![為新保存庫設定儲存體組態](./media/backup-try-azure-backup-in-10-mins/full-blade.png)


## <a name="configure-a-backup-policy"></a>設定備份原則

探索訂用帳戶中的 VM，並設定備份。

1. 在保存庫 >**概觀**，選取 **+ 備份**。

   ![備份按鈕](./media/backup-azure-arm-vms-prepare/backup-button.png)

   [備份] 和 [備份目標] 窗格隨即開啟。

2. 在 [備份目標] > [工作負載的執行位置?] 下方，選取 [Azure]。 在 [您要備份什麼?] 中，選取 [虛擬機器] >  [確定]。

   ![備份和備份目標窗格](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   此步驟會向保存庫註冊 VM 擴充功能。 [備份目標] 窗格隨即關閉，然後開啟 [備份原則] 窗格。

3. 在 [備份原則] 中，選取要與保存庫產生關聯的原則。 然後選取 [確定]。
    - 預設原則的詳細資料便會列在下拉式功能表之下。
    - 選取 **新建**建立原則。 [深入了解](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)如何定義原則。

    ![[備份] 和 [備份原則] 窗格](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. 在 [**選取虛擬機器**] 窗格中，選取將使用指定的備份原則的 Vm >**確定**。

   選取的 VM 會接受驗證。 您只能選取與保存庫位於相同區域中的 VM。 VM 只能在單一保存庫中備份。

   ![[選取虛擬機器] 窗格](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. 在 [備份] 中，選取 [啟用備份]。

   此步驟會將原則部署到保存庫和 Vm。 它也會在 Azure VM 上執行的 VM 代理程式上安裝備份擴充功能。
   
   此步驟並不會建立 VM 的初始復原點。

   ![[啟用備份] 按鈕](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

啟用之後，您備份：

- 初始備份會根據您的備份排程執行。
- 無論 VM 是否在執行，備份服務都會安裝備份擴充功能。

執行中的 VM 提供了取得應用程式一致復原點的絕佳機會。 不過，即使 VM 關閉，且無法安裝擴充功能，VM 仍會備份。 因此也稱為離線 VM。 在此情況下，您將會損毀一致復原點。
    
> [!NOTE]
> Azure 備份在進行 Azure VM 備份時不支援依據日光節約變更而自動調整時鐘。 請視需要手動修改備份原則。

## <a name="run-the-initial-backup"></a>執行初始備份

初始備份會根據您的排程執行，除非您立即手動執行。 手動執行，如下所示：

1. 在 [保存庫] 功能表中，選取**備份項目**。
2. 在 **備份項目**，選取**Azure 虛擬機器**。
3. 在 **備份項目**清單中，選取省略符號 (**...**).
4. 選取 **立即備份**。
5. 在 **立即備份**，使用日曆控制項選取的復原點保留的最後一天 >**確定**。
6. 監視入口網站通知。 您可以在保存庫儀表板中監視作業進度 > [備份作業] > [進行中]。 根據您的 VM 大小，建立初始備份可能需要一段時間。



## <a name="next-steps"></a>後續步驟

- 針對任何問題進行疑難排解[Azure VM 代理程式](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)或是[Azure VM 備份](backup-azure-vms-troubleshoot.md)。
- [還原](backup-azure-arm-restore-vms.md)Azure Vm。

