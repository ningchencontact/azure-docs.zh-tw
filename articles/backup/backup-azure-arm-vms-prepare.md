---
title: 使用 Azure 備份來備份復原服務保存庫中的 Azure VM
description: 說明如何使用 Azure 備份來備份復原服務保存庫中的 Azure VM
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: 3133f22a4d9ecd8a0ee4bff9f8b0be9c1f4eb705
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403658"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>備份復原服務保存庫中的 Azure VM

本文說明如何備份與復原服務保存庫中的 Azure Vm [Azure 備份](backup-overview.md)服務。 

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


- [檢閱](backup-architecture.md#architecture-direct-backup-of-azure-vms) Azure VM 備份架構。
- [深入了解](backup-azure-vms-introduction.md) Azure VM 備份和備份擴充功能。
- [檢閱](backup-support-matrix-iaas.md) Azure VM 備份的支援矩陣。


## <a name="prepare-azure-vms"></a>準備 Azure VM

在某些情況下，您可能需要設定 Azure Vm 上的 Azure VM 代理程式，或明確允許在 VM 上的 輸出存取。

### <a name="install-the-vm-agent"></a>安裝 VM 代理程式 

為機器上執行的 Azure VM 代理程式安裝擴充功能，以 Azure 備份來備份 Azure VM。 如果您的 VM 建立自 Azure marketplace 映像，代理程式已安裝並執行。 如果您建立自訂的 VM，或在內部部署機器移轉，您可能需要以手動方式，在資料表中摘要說明安裝代理程式。

**VM** | **詳細資料**
--- | ---
**Windows VM** | 1.[下載並安裝](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)代理程式 MSI 檔案。<br/><br/> 2.以機器的系統管理員權限安裝。<br/><br/> 3.驗證安裝。 在 [ *C:\WindowsAzure\Packages*的 VM 上，以滑鼠右鍵按一下 WaAppAgent.exe >**屬性**，>**詳細資料**] 索引標籤。[產品版本] 應為 2.6.1198.718 或更新版本。<br/><br/> 如果您正在更新代理程式，請確保沒有執行任何備份作業，並[重新安裝代理程式](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
**Linux VM** | 從您的散發套件存放庫使用 RPM 或 DEB 封裝安裝。 這是安裝和升級 Azure Linux 代理程式的慣用的方法。 所有[認可的散發套件提供者](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)都會將 Azure Linux 代理程式套件整合於本身的映像和儲存機制中。 代理程式可從 [GitHub](https://github.com/Azure/WALinuxAgent) 取得，但不建議從該處安裝。<br/><br/> 如果您正在更新代理程式，請確定任何備份作業正在執行，以及更新二進位檔案。


### <a name="establish-network-connectivity"></a>建立網路連線

在 VM 上執行，備份擴充功能需要 Azure 公用 IP 位址的輸出存取。

- 通常您不需要明確允許輸出網路存取的 Azure VM，才能讓它與 Azure 備份通訊。
- 如果您果真遇到問題與 Vm 連線，而且您會看到錯誤**ExtensionSnapshotFailedNoNetwork**時嘗試連線，您應該明確地允許存取讓備份擴充功能可以與 Azure 公用進行通訊備份流量的的 IP 位址。


#### <a name="explicitly-allow-outbound-access"></a>明確允許輸出存取

如果您的 VM 無法連線到備份服務中，明確允許輸出存取，使用其中一個資料表中摘要說明的方法。

**選項** | **Action** | **詳細資料** 
--- | --- | --- 
**設定 NSG 規則** | 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 | 而不是允許和管理每個位址範圍，您可以新增規則，以允許存取 Azure 備份服務會使用[服務標籤](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure)。 [深入了解](../virtual-network/security-overview.md#service-tags)。<br/><br/> 沒有額外的成本。<br/><br/> 輕鬆地管理與服務標籤。
**部署 Proxy** | 部署 HTTP Proxy 伺服器來路由傳送流量。 | 提供整個 Azure 的存取權，而不只是儲存體的存取權。<br/><br/> 可精確控制儲存體 URL。<br/><br/> VM 具有單一網際網路存取點。<br/><br/> Proxy 需要額外成本。
**設定 Azure 防火牆** | 使用 Azure 備份服務的 FQDN 標記，允許流量通過 VM 上的 Azure 防火牆。 |  如果您已在 VNet 子網路中設定 Azure 防火牆，則易於使用<br/><br/> 您無法建立您自己的 FQDN 標籤，或修改在標記中的 Fqdn。<br/><br/> 如果您使用 Azure 受控磁碟，您可能需要在防火牆上開啟其他連接埠 (連接埠 8443)。

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>設定 NSG 規則以允許對 Azure 的輸出存取

如果 VM 存取由 NSG，允許必要的範圍和連接埠的備份儲存體的輸出存取。

1. 在 VM 屬性 > **Networking**，按一下**新增輸出連接埠規則**。
2. 在 [新增輸出安全性規則] 中，按一下 [進階]。
3. 在 [來源] 中，選取 [VirtualNetwork]。
4. 在 [來源連接埠範圍] 中，輸入星號 (*) 以允許從任何連接埠進行輸出存取。
5. 在 [目的地] 中，選取 [服務標記]。 從清單中選取 [儲存體區域]。 區域是保存庫和您要備份的 VM 所在的區域。
6. 在 [目的地連接埠範圍] 中，選取連接埠。
    - 使用未加密儲存體帳戶的未受控 VM：80
    - 使用加密儲存體帳戶的未受控 VM：443 (預設設定)
    - 受控 VM：8443。
7. 在 [通訊協定] 中，選取 [TCP]。
8. 在 [優先順序] 中，指定比任何優先順序較高的拒絕規則都小的優先順序值。
   - 如果您有拒絕存取的規則，新的允許規則必須具有較高的優先順序。
   - 例如，如果您有優先順序設為 1000 的 **Deny_All** 規則，則新規則必須設為小於 1000。
9. 提供規則的名稱和描述，然後按一下 [確定]。

您可以將 NSG 規則套用至多個 VM 來允許輸出存取。 這段影片將逐步說明其程序。

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


##### <a name="route-backup-traffic-through-a-proxy"></a>透過 Proxy 路由備份流量

您可以透過 Proxy 來路由備份流量，然後提供對所需 Azure 範圍的 Proxy 存取權。 設定 proxy 以允許下列 VM:

- Azure VM 應透過 Proxy 路由所有連往公用網際網路的 HTTP 流量。
- Proxy 應允許從可用虛擬網路 (VNet) 中的 VM 傳入的流量。
- NSG **NSF-lockdown** 需要一個規則，以允許從 Proxy VM 輸出的網際網路流量。

###### <a name="set-up-the-proxy"></a>設定 Proxy

如果您沒有系統帳戶 Proxy，請以下列方式設定一個：

1. 下載 [PsExec](https://technet.microsoft.com/sysinternals/bb897553)。
2. 執行 **PsExec.exe -i -s cmd.exe**，在系統帳戶下執行命令提示字元。
3. 在系統內容中執行瀏覽器。 例如：Internet Explorer 的 **PROGRAMFILES%\Internet Explorer\iexplore.exe**。  
4. 定義 Proxy 設定。
   - 在 Linux 機器上：
     - 將以下這一行新增至 **/etc/environment** 檔案：
       - **http_proxy = http: \/ /proxy IP 位址： proxy 連接埠**
     - 將以下幾行新增至 **/etc/waagent.conf** 檔案：
         - **HttpProxy.Host=proxy IP address**
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

1，在 [Windows 防火牆] 中，開啟**具有進階安全性的 Windows 防火牆**。
2. 以滑鼠右鍵按一下 [輸入規則] > [新增規則]。
3. 在 [規則類型] 中，選取 [自訂] > [下一步]。
4. 在 [程式] 中，選取 [所有程式] > [下一步]。
5. 在 **通訊協定和連接埠**:
   - 將類型設為**TCP**
   - 設定**本機連接埠**到**特定連接埠**
   - 設定**遠端連接埠**要**所有連接埠**。
  
6. 完成精靈，並指定規則的名稱。

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>新增 Proxy 的 NSG 例外規則

在 NSG **NSF-lockdown** 上，允許從 10.0.0.5 上的任何連接埠傳輸至 80 (HTTP) 或 443 (HTTPS) 連接埠上的任何網際網路位址的流量。

- 下列 PowerShell 指令碼提供允許流量的範例。
- 您可以指定 IP 位址範圍 (-DestinationPortRange)，或使用 storage.region 服務標記，而不要允許輸出至所有公用網際網路位址。   

    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```

### <a name="allow-firewall-access-with-fqdn-tag"></a>允許使用 FQDN 標記的防火牆存取

您可以設定 Azure 防火牆，以允許對 Azure 備份網路流量的輸出存取。

- [了解](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)如何部署 Azure 防火牆。
- [閱讀](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN 標記的相關資訊。

## <a name="modify-storage-replication-settings"></a>修改儲存體複寫設定

根據預設，保存庫會有[異地備援儲存體 (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

- 我們建議您的主要備份 GRS。
- 您可以使用[本地備援儲存體 (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)成本較低的選項。

修改儲存體複寫類型如下所示：

1. 在入口網站中，按一下 新的保存庫。 底下**設定**區段中，按一下**屬性**。
2. 在 **屬性**下方**備份設定**，按一下**更新**。
3. 選取儲存體複寫類型，然後按一下**儲存**。

      ![為新保存庫設定儲存體組態](./media/backup-try-azure-backup-in-10-mins/full-blade.png)


## <a name="configure-a-backup-policy"></a>設定備份原則

探索訂用帳戶中的 VM，並設定備份。

1. 在保存庫 > [概觀] 中，按一下 [+備份]

   ![備份按鈕](./media/backup-azure-arm-vms-prepare/backup-button.png)

   [備份] 和 [備份目標] 窗格隨即開啟。

2. 在 [備份目標]> [工作負載的執行位置?] 下方，選取 [Azure]。 在 [您要備份什麼?] 中，選取 [虛擬機器] >  [確定]。 這會在保存庫中註冊 VM 擴充功能。

   ![備份和備份目標窗格](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   此步驟會向保存庫註冊 VM 擴充功能。 [備份目標] 窗格隨即關閉，然後開啟 [備份原則] 窗格。

3. 在 [備份原則] 中，選取要與保存庫產生關聯的原則。 然後按一下 [確定] 。
    - 預設原則的詳細資料便會列在下拉式功能表之下。
    - 按一下 [新建] 以建立原則。 [深入了解](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)如何定義原則。

      ![[備份] 和 [備份原則] 窗格](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. 在 [選取虛擬機器] 窗格中，選取將使用指定備份原則的 VM > [確定]。

   - 選取的 VM 會接受驗證。
   - 您只能選取與保存庫位於相同區域中的 VM。 VM 只能在單一保存庫中備份。

     ![[選取虛擬機器] 窗格](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. 在 [備份] 中，選取 [啟用備份]。

   - 這會將原則部署到保存庫和 VM，並在執行於 Azure VM 的 VM 代理程式上安裝備份擴充功能。
   - 此步驟並不會建立 VM 的初始復原點。

     ![[啟用備份] 按鈕](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

啟用備份後：

- 初始備份會根據您的備份排程執行。
- 無論 VM 是否在執行，備份服務都會安裝備份擴充功能。
    - 執行中的 VM 提供了取得應用程式一致復原點的絕佳機會。
    -  不過，即使 VM 關閉，且無法安裝擴充功能，VM 仍會備份。 因此也稱為離線 VM。 在此情況下，您將會損毀一致復原點。 [了解更多]()請注意，Azure 備份不支援自動時鐘調整的 Azure VM 備份的日光節約變更。 請視需要手動修改備份原則。

## <a name="run-the-initial-backup"></a>執行初始備份

初始備份會根據您的排程執行，除非您立即手動執行。 手動執行，如下所示：

1. 在保存庫功能表中，按一下 [備份項目]。
2. 在 [備份項目] 中，按一下 [Azure 虛擬機器]。
3. 在 [備份項目] 清單中，按一下省略符號 **...**。
4. 按一下 [立即備份]。
5. 在 [立即備份] 中，使用行事曆控制項來選取復原點應該保留的最後一天 > [確定]。
6. 監視入口網站通知。 您可以在保存庫儀表板中監視作業進度 > [備份作業] > [進行中]。 根據您的 VM 大小，建立初始備份可能需要花一點時間。



## <a name="next-steps"></a>後續步驟

- 針對任何問題進行疑難排解[Azure VM 代理程式](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)或是[Azure VM 備份](backup-azure-vms-troubleshoot.md)。
- [還原](backup-azure-arm-restore-vms.md)Azure Vm。

