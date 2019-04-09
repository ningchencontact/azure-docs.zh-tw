---
title: 使用 Azure 備份復原服務保存庫中備份 Azure Vm
description: 描述如何使用 Azure 備份復原服務保存庫中備份 Azure Vm
service: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: raynew
ms.openlocfilehash: 142ffdadf4adb1ee07f3592624cbdddfb310b580
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264551"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>備份復原服務保存庫中的 Azure VM

本文說明如何備份 Azure Vm 中的復原服務保存庫中，使用[Azure 備份](backup-overview.md)服務。 

在本文中，您將了解：

> [!div class="checklist"]
> * 準備 Azure VM。
> * 建立保存庫。
> * 探索 Vm，並設定備份原則。
> * 啟用 Azure Vm 的備份。
> * 執行初始備份。


> [!NOTE]
> 本文說明如何設定保存庫，然後選取要備份的 Vm。 這有助於您備份多個 VM。 或者，您可以[來備份單一 Azure VM](backup-azure-vms-first-look-arm.md)直接從 VM 設定。

## <a name="before-you-start"></a>開始之前


- [檢閱](backup-architecture.md#architecture-direct-backup-of-azure-vms)Azure VM 備份架構。
- [深入了解](backup-azure-vms-introduction.md) Azure VM 備份和備份擴充功能。
- [請檢閱支援對照表](backup-support-matrix-iaas.md)設定備份之前。

此外，有一些您可能需要在某些情況下執行的事項：

- **在 VM 上安裝 VM 代理程式**:為機器上執行的 Azure VM 代理程式安裝擴充功能，以 Azure 備份來備份 Azure VM。 如果您的 VM 建立自 Azure marketplace 映像，代理程式已安裝並執行。 如果您建立自訂的 VM，或在內部部署機器移轉，您可能需要[手動安裝代理程式](#install-the-vm-agent)。
- **明確允許輸出存取**:一般而言，您不需要明確允許輸出網路存取的 Azure VM，才能讓它與 Azure 備份通訊。 不過，某些 Vm 可能會遇到連線問題，顯示**ExtensionSnapshotFailedNoNetwork**嘗試連線時發生錯誤。 如果發生這種情況，您應該[明確允許輸出存取](#explicitly-allow-outbound-access)，所以 Azure 備份擴充功能可以與 Azure 的備份流量的公用 IP 位址通訊。


## <a name="create-a-vault"></a>建立保存庫

 保存庫會儲存在一段時間內建立的備份和復原點，並儲存與備份的機器相關聯的備份原則。 請依照下列方式建立保存庫：    

1. 登入 [Azure 入口網站](https://portal.azure.com/)。    
2. 在搜尋中，輸入**復原服務**。 底下**Services**，按一下**復原服務保存庫**。   

     ![搜尋復原服務保存庫](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/> 

3. 在 **復原服務保存庫**功能表上，按一下 **+ 新增**。    

     ![建立復原服務保存庫的步驟 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)   

4. 在 **復原服務保存庫**，來識別保存庫中的易記名稱的型別。   
    - 必須是 Azure 訂用帳戶中唯一的名稱。   
    - 它可以包含 2 到 50 個字元。    
    - 該名稱必須以字母開頭，而且只可以包含字母、數字和連字號。   
5. 選取 Azure 訂用帳戶、 資源群組，以及應該在其中建立保存庫的地理區域。 接著，按一下 [建立]。    
    - 建立保存庫可能需要一些時間。  
    - 請監視入口網站右上方區域中的狀態通知。   


 在建立保存庫之後，它會顯示復原服務保存庫清單中。 如果您沒有看到保存庫，請選取 [重新整理]。
 
![備份保存庫的清單](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)    

### <a name="modify-storage-replication"></a>修改儲存體複寫

根據預設，保存庫使用[異地備援儲存體 (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

- 如果保存庫是您主要的備份機制，我們建議使用 GRS。
- 您可以使用[本地備援儲存體 (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)成本較低的選項。

修改儲存體複寫類型如下所示：

1. 在新的保存庫中，按一下**屬性**中**設定**一節。
2. 在 **屬性**下方**備份設定**，按一下**更新**。
3. 選取儲存體複寫類型，然後按一下**儲存**。

      ![為新保存庫設定儲存體組態](./media/backup-try-azure-backup-in-10-mins/full-blade.png)
> [!NOTE]
   > 設定保存庫，並包含備份的項目之後，您無法修改儲存體複寫類型。 如果您想要執行這項操作必須重新建立保存庫。 

## <a name="apply-a-backup-policy"></a>套用備份原則

設定保存庫的備份原則。

1. 在 保存庫中，按一下 **+ 備份**中**概觀**一節。

   ![備份按鈕](./media/backup-azure-arm-vms-prepare/backup-button.png)


2. 在 **備份目標** > **地方執行您的工作負載？** 選取**Azure**。 在 **怎麼想要備份？** 選取**虛擬機器** >  **確定**。 這會在保存庫中註冊 VM 擴充功能。

   ![備份和備份目標窗格](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. 在 [備份原則] 中，選取要與保存庫產生關聯的原則。 
    - 預設的原則一天一次備份 VM。 每日備份會保留 30 天。 立即復原快照集會保留兩天。
    - 如果您不想要使用的預設原則，請選取**新建**，並在下一個程序中所述，建立自訂的原則。

      ![預設備份原則](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. 在 **選取虛擬機器**，選取您想要使用的原則備份的 Vm。 然後按一下 [確定] 。

   - 選取的 Vm 進行驗證。
   - 您只能選取與保存庫位於相同區域中的 VM。
   - VM 只能在單一保存庫中備份。

     ![[選取虛擬機器] 窗格](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. 在 **備份**，按一下**啟用備份**。 這會將原則部署到保存庫和 VM，並在執行於 Azure VM 的 VM 代理程式上安裝備份擴充功能。
     
     ![[啟用備份] 按鈕](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

啟用備份後：

- 無論 VM 是否在執行，備份服務都會安裝備份擴充功能。
- 根據您的備份排程，將會執行初始備份。
- 當備份執行時，請注意:
    - 正在執行的 VM 有擷取的應用程式一致復原點的絕佳機會。
    - 不過，即使 VM 已關閉它會備份。 這類 VM 稱為離線 VM。 在此情況下，您將會損毀一致復原點。
    

### <a name="create-a-custom-policy"></a>建立自訂原則

如果您選取要建立新的備份原則，填入 [原則] 設定。

1. 在 **原則名稱**，指定有意義的名稱。
2. 在 **備份排程**指定時應採取的備份。 您可以採取每日或每週備份 Azure vm。
2. 在 **立即還原**，指定您想要保留在本機的 「 立即還原的快照集的時間長度。
    - 當您還原時，備份 vm 磁碟複製從儲存體，透過網路與復原儲存體位置。 使用 「 立即還原，您可以利用儲存在本機的建立期間備份作業，而不需等待要傳輸到保存庫的備份資料的快照集。
    - 您可以保留的一到五天之間的立即還原的快照集。 兩天是預設設定。
3. 在 **保留範圍**，指定您想要保留每日或每週備份點的時間長度。
4. 在 **每月備份點保留期**，指定您是否要保留每月每日或每週備份的備份。 
5. 按一下 [ **確定** ] 儲存原則。

    ![新的備份原則](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure 備份在進行 Azure VM 備份時不支援依據日光節約變更而自動調整時鐘。 發生時間變更時，修改以手動方式視需要的備份原則。

## <a name="trigger-the-initial-backup"></a>觸發初始備份

初始備份會依照排程進行，但您可以執行它立即，如下所示：

1. 在保存庫功能表中，按一下 [備份項目]。
2. 在 [備份項目] 中，按一下 [Azure 虛擬機器]。
3. 在 **備份項目**清單中，按一下省略符號 （...）。
4. 按一下 [立即備份]。
5. 在 **立即備份**，使用日曆控制項選取的復原點保留的最後一天。 然後按一下 [確定] 。
6. 監視入口網站通知。 您可以在保存庫儀表板中監視作業進度 > [備份作業] > [進行中]。 根據您的 VM 大小，建立初始備份可能需要花一點時間。

## <a name="optional-steps-install-agentallow-outbound"></a>（安裝代理程式/允許輸出） 的選擇性步驟
### <a name="install-the-vm-agent"></a>安裝 VM 代理程式

為機器上執行的 Azure VM 代理程式安裝擴充功能，以 Azure 備份來備份 Azure VM。 如果您的 VM 建立自 Azure Marketplace 映像，代理程式已安裝並執行。 如果您建立自訂的 VM，或在內部部署機器移轉，您可能需要以手動方式，在資料表中摘要說明安裝代理程式。

**VM** | **詳細資料**
--- | ---
** Windows** | 1.[下載並安裝](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)代理程式 MSI 檔案。<br/><br/> 2.以機器的系統管理員權限安裝。<br/><br/> 3.驗證安裝。 在  *C:\WindowsAzure\Packages*的 VM 上，以滑鼠右鍵按一下**WaAppAgent.exe** > **屬性**。 在 **詳細資料**索引標籤上，**產品版本**應為 2.6.1198.718 或更高版本。<br/><br/> 如果您正在更新代理程式，請確定會執行任何備份作業，並[重新安裝代理程式](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
** Linux** | 從您的散發套件存放庫使用 RPM 或 DEB 封裝安裝。 這是安裝和升級 Azure Linux 代理程式的慣用的方法。 所有[認可的散發套件提供者](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)都會將 Azure Linux 代理程式套件整合於本身的映像和儲存機制中。 代理程式可從 [GitHub](https://github.com/Azure/WALinuxAgent) 取得，但不建議從該處安裝。<br/><br/> 如果您正在更新代理程式，請確定任何備份作業正在執行，以及更新二進位檔案。

### <a name="explicitly-allow-outbound-access"></a>明確允許輸出存取

在 VM 上執行的備份擴充功能需要 Azure 公用 IP 位址的輸出存取。

- 通常您不需要明確允許輸出網路存取的 Azure VM，才能讓它與 Azure 備份通訊。
- 如果您遇到困難與 Vm 連線，或如果您看到錯誤**ExtensionSnapshotFailedNoNetwork**時嘗試連線，您應該明確地允許存取讓備份擴充功能可以與 Azure 公用 IP 進行通訊備份流量的的位址。 下表摘要說明存取方法。


**選項** | ** 動作** | **詳細資料** 
--- | --- | --- 
**設定 NSG 規則** | 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。<br/><br/> 而不是允許和管理每個位址範圍，您可以新增規則，以允許存取 Azure 備份服務會使用[服務標籤](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure)。 | [深入了解](../virtual-network/security-overview.md#service-tags)服務標記。<br/><br/> 服務標記簡化存取管理，並不會產生額外費用。
**部署 proxy** | 部署 HTTP Proxy 伺服器來路由傳送流量。 | 提供整個 Azure 的存取權，而不只是儲存體的存取權。<br/><br/> 可精確控制儲存體 URL。<br/><br/> VM 具有單一網際網路存取點。<br/><br/> Proxy 需要額外成本。
**設定 Azure 防火牆** | 使用 Azure 備份服務的 FQDN 標記，允許流量通過 VM 上的 Azure 防火牆 | 容易使用，如果您在 VNet 子網路中設定的 Azure 防火牆。<br/><br/> 您無法建立您自己的 FQDN 標籤，或修改在標記中的 Fqdn。<br/><br/> 如果您的 Azure Vm 具有受控磁碟，您可能需要開啟其他防火牆上連接埠 (8443)。

#### <a name="establish-network-connectivity"></a>建立網路連線

建立具有 NSG 的連線 proxy，或透過防火牆

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

##### <a name="allow-firewall-access-with-an-fqdn-tag"></a>允許使用 FQDN 標記的防火牆存取

您可以設定 Azure 防火牆以允許備份至 Azure 備份的網路流量的輸出存取。

- [了解](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)如何部署 Azure 防火牆。
- [閱讀](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN 標記的相關資訊。



## <a name="next-steps"></a>後續步驟

- 針對任何問題進行疑難排解[Azure VM 代理程式](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)或是[Azure VM 備份](backup-azure-vms-troubleshoot.md)。
- [還原](backup-azure-arm-restore-vms.md)Azure Vm。

