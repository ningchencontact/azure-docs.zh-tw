---
title: 使用 Azure 備份來備份復原服務保存庫中的 Azure VM
description: 說明如何使用 Azure 備份來備份復原服務保存庫中的 Azure VM
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: e782afb971f95a654119d9817edeef02642bee9e
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447560"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>備份復原服務保存庫中的 Azure VM

本文說明如何透過在復原服務保存庫部署和啟用備份，以使用 [Azure 備份](backup-overview.md)來備份 Azure VM。 

在本文中，您將了解：

> [!div class="checklist"]
> * 確認支援的案例和必要條件。
> * 準備 Azure VM。 視需要安裝 Azure VM 代理程式，並確認 VM 的輸出存取。
> * 建立保存庫。
> * 設定保存庫的儲存體
> * 探索 VM，設定備份設定和原則。
> * 啟用 Azure VM 的備份


> [!NOTE]
   > 本文說明如何藉由設定保存庫和選取要備份的 VM 來備份 Azure VM。 這有助於您備份多個 VM。 您也可以直接從 VM 設定來[備份 Azure VM](backup-azure-vms-first-look-arm.md)。

## <a name="before-you-start"></a>開始之前

為機器上執行的 Azure VM 代理程式安裝擴充功能，以 Azure 備份來備份 Azure VM。

1. [檢閱](backup-architecture.md#architecture-direct-backup-of-azure-vms) Azure VM 備份架構。
[深入了解](backup-azure-vms-introduction.md) Azure VM 備份和備份擴充功能。
2. [檢閱](backup-support-matrix-iaas.md) Azure VM 備份的支援矩陣。
3. 準備 Azure VM。 請安裝 VM 代理程式 (如果尚未安裝)，並確認您想要備份的 VM 的輸出存取。


## <a name="prepare-azure-vms"></a>準備 Azure VM

視需要安裝 Azure 代理程式，並確認 VM 的輸出存取。

### <a name="install-the-vm-agent"></a>安裝 VM 代理程式 
如有需要，請依照下列方式安裝代理程式。

**VM** | **詳細資料**
--- | ---
**Windows VM** | [下載並安裝](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)代理程式 MSI 檔案。 以機器的系統管理員權限安裝。<br/><br/> 若要驗證安裝，請在 VM 的 *C:\WindowsAzure\Packages* 上，以滑鼠右鍵按一下 WaAppAgent.exe > [屬性] > [詳細資料] 索引標籤。[產品版本] 應為 2.6.1198.718 或更新版本。<br/><br/> 如果您正在更新代理程式，請確保沒有執行任何備份作業，並[重新安裝代理程式](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
**Linux VM** | 安裝和升級 Azure Linux 代理程式時，建議使用散發套件的封裝儲存機制所提供的 RPM 或 DEB 封裝來安裝。 所有[認可的散發套件提供者](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)都會將 Azure Linux 代理程式套件整合於本身的映像和儲存機制中。 代理程式可從 [GitHub](https://github.com/Azure/WALinuxAgent) 取得，但不建議從該處安裝。<br/><br/> 如果您正在更新代理程式，請確保沒有執行任何備份作業，並更新二進位檔。 


### <a name="establish-network-connectivity"></a>建立網路連線

在 VM 上執行的備份擴充功能必須具有對 Azure 公用 IP 位址的輸出存取權。

- 不需要明確的輸出網路存取權，即可讓 Azure VM 與 Azure 備份服務通訊。
- 不過，某些較舊的虛擬機器可能會遇到問題，並在嘗試連線時因為 **ExtensionSnapshotFailedNoNetwork** 錯誤而失敗。 在此情況下，請使用下列其中一個選項，讓備份擴充功能可以為備份流量與 Azure 公用 IP 位址進行通訊。

   **選項** | **動作** ** | **優點** | **缺點**
   --- | --- | --- | ---
   **設定 NSG 規則** | 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。<br/><br/>  您可以新增可使用[服務標記](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure)允許存取 Azure 備份服務的規則，而不要個別允許和管理每個位址範圍。 [深入了解](../virtual-network/security-overview.md#service-tags)服務標記。 | 沒有額外的成本。 易於使用服務標記來管理
   **部署 Proxy** | 部署 HTTP Proxy 伺服器來路由傳送流量。 | 提供整個 Azure 的存取權，而不只是儲存體的存取權。 可精確控制儲存體 URL。<br/><br/> VM 具有單一網際網路存取點。<br/><br/> Proxy 需要額外成本。<br/><br/> 
   **設定 Azure 防火牆** | 使用 Azure 備份服務的 FQDN 標記，允許流量通過 VM 上的 Azure 防火牆。|  如果您已在 VNet 子網路中設定 Azure 防火牆，則易於使用 | 無法建立您自己的 FQDN 標記，或修改標記中的 FQDN。<br/><br/> 如果您使用 Azure 受控磁碟，您可能需要在防火牆上開啟其他連接埠 (連接埠 8443)。

#### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>設定 NSG 規則以允許對 Azure 的輸出存取

如果您以 NSG 管理 Azure VM 的存取，請允許備份儲存體對於必要範圍和連接埠的輸出存取。

1. 在 VM > [網路] 中，按一下 [新增輸出連接埠規則]。
2. 在 [新增輸出安全性規則] 中，按一下 [進階]。
3. 在 [來源] 中，選取 [VirtualNetwork]。
4. 在 [來源連接埠範圍] 中，輸入星號 (*) 以允許從任何連接埠進行輸出存取。
5. 在 [目的地] 中，選取 [服務標記]。 從清單中選取 [儲存體區域]。 區域是保存庫和您要備份的 VM 所在的區域。
6. 在 [目的地連接埠範圍] 中，選取連接埠。
    - 使用未加密儲存體帳戶的未受控 VM：80
    - 使用加密儲存體帳戶的未受控 VM：443 (預設設定)
    - 受控 VM：8443。
7. 在 [通訊協定] 中，選取 [TCP]。
8. 在 [優先順序] 中，指定比任何優先順序較高的拒絕規則都小的優先順序值。 如果您有拒絕存取的規則，新的允許規則必須具有較高的優先順序。 例如，如果您有優先順序設為 1000 的 **Deny_All** 規則，則新規則必須設為小於 1000。
9. 提供規則的名稱和描述，然後按一下 [確定]。

您可以將 NSG 規則套用至多個 VM 來允許輸出存取。

這段影片將逐步說明其程序。

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


#### <a name="route-backup-traffic-through-a-proxy"></a>透過 Proxy 路由備份流量

您可以透過 Proxy 來路由備份流量，然後提供對所需 Azure 範圍的 Proxy 存取權。
您應將 Proxy VM 設定為允許下列事項：

- Azure VM 應透過 Proxy 路由所有連往公用網際網路的 HTTP 流量。
- Proxy 應允許從可用虛擬網路 (VNet) 中的 VM 傳入的流量。
- NSG **NSF-lockdown** 需要一個規則，以允許從 Proxy VM 輸出的網際網路流量。

##### <a name="set-up-the-proxy"></a>設定 Proxy
如果您沒有系統帳戶 Proxy，請以下列方式設定一個：

1. 下載 [PsExec](https://technet.microsoft.com/sysinternals/bb897553)。

2. 執行 **PsExec.exe -i -s cmd.exe**，在系統帳戶下執行命令提示字元。
3. 在系統內容中執行瀏覽器。 例如：Internet Explorer 的 **PROGRAMFILES%\Internet Explorer\iexplore.exe**。  
4. 定義 Proxy 設定。
    - 在 Linux 機器上：
        - 將以下這一行新增至 **/etc/environment** 檔案：
            - **http_proxy=http://proxy IP address:proxy port**
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

##### <a name="allow-incoming-connections-on-the-proxy"></a>允許 Proxy 上的連入連線

在 Proxy 設定中允許連入連線。

- 例如，開啟**具有進階安全性的 Windows 防火牆**。
    - 以滑鼠右鍵按一下 [輸入規則] > [新增規則]。
    - 在 [規則類型] 中，選取 [自訂] > [下一步]。
    - 在 [程式] 中，選取 [所有程式] > [下一步]。
    - 在 [通訊協定和連接埠] 中，將類型設為 [TCP]、[本機連接埠] 設為 [特定連接埠]，並將 [遠端連接埠] 設為 [所有連接埠]。
    - 完成精靈，並指定規則的名稱。

##### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>新增 Proxy 的 NSG 例外規則

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

## <a name="create-a-vault"></a>建立保存庫

保存庫會儲存在一段時間內建立的備份和復原點，並儲存與備份的機器相關聯的備份原則。 請依照下列方式建立保存庫：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [中樞] 功能表上選取 [瀏覽]，然後輸入 [復原服務]。 選取 [復原服務保存庫]。

    ![在方塊中輸入資料並在結果中選取 [復原服務保存庫]](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

3. 在 [復原服務保存庫] 功能表上，選取 [新增]。

    ![建立復原服務保存庫的步驟 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    ![[復原服務保存庫] 窗格](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. 在 [復原服務保存庫] >  [名稱] 中，輸入用來識別保存庫的易記名稱。
    - 必須是 Azure 訂用帳戶中唯一的名稱。
    - 它可以包含 2 到 50 個字元。
    - 該名稱必須以字母開頭，而且只可以包含字母、數字和連字號。
5. 選取 [訂用帳戶] 以查看可用的訂用帳戶清單。 如果您不確定要使用哪個訂用帳戶，請使用預設 (或建議) 的訂用帳戶。 只有在您的公司或學校帳戶與多個 Azure 訂用帳戶相關聯時，才會有多個選擇。
6. 選取 [資源群組] 以查看可用的資源群組清單，或選取 [新增] 以建立新的資源群組。 [深入了解](../azure-resource-manager/resource-group-overview.md)資源群組。
7. 選取 [位置] 以選取保存庫的地理區域。 保存庫*必須*與您要備份的虛擬機器位於相同區域。
8. 選取 [建立] 。
    - 建立保存庫可能需要一些時間。
    - 請監視入口網站右上方區域中的狀態通知。
    ![備份保存庫的清單](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

保存庫建立之後，就會出現在 [復原服務保存庫] 的清單中。 如果您沒有看到保存庫，請選取 [重新整理]。

## <a name="set-up-storage-replication"></a>設定儲存體複寫

根據預設，保存庫會有[異地備援儲存體 (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。 建議您使用 GRS 進行主要備份，但您也可以使用[本地備援儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)，這是成本較低的選項。

依照下列方式修改儲存體複寫：

1. 在保存庫 > [備份基礎結構] 中，按一下 [備份組態]

   ![備份保存庫的清單](./media/backup-azure-arm-vms-prepare/full-blade.png)

2. 在 [備份組態] 中，視需要修改儲存體備援方法，然後選取 [儲存]。


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
    -  不過，即使 VM 關閉，且無法安裝擴充功能，VM 仍會備份。 這稱為「離線 VM」。 在此情況下，復原點將會是「當機時保持一致」 。
    請注意，Azure 備份在進行 Azure VM 備份時不支援依據日光節約變更而自動調整時鐘。 請視需要手動修改備份原則。
  
 ## <a name="run-the-initial-backup"></a>執行初始備份

初始備份會根據您的排程執行，除非您立即手動執行。 手動執行，如下所示：

1. 在保存庫功能表中，按一下 [備份項目]。
2. 在 [備份項目] 中，按一下 [Azure 虛擬機器]。
3. 在 [備份項目] 清單中，按一下省略符號 **...**。
4. 按一下 [立即備份]。
5. 在 [立即備份] 中，使用行事曆控制項來選取復原點應該保留的最後一天 > [確定]。
6. 監視入口網站通知。 您可以在保存庫儀表板中監視作業進度 > [備份作業] > [進行中]。 根據您的 VM 大小，建立初始備份可能需要花一點時間。



## <a name="next-steps"></a>後續步驟

- 對 [Azure VM 代理程式](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)或 [Azure VM 備份](backup-azure-vms-troubleshoot.md)所發生的任何問題進行疑難排解。
- [備份 Azure VM](backup-azure-vms-first-look-arm.md)
