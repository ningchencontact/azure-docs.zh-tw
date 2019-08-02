---
title: 使用 Azure 備份備份復原服務保存庫中的 Azure Vm
description: 說明如何使用 Azure 備份在復原服務保存庫中備份 Azure Vm
service: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: dacurwin
ms.openlocfilehash: 9a6ea961f7433f511ef22a6ac9aaefa51b5df8aa
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663696"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>備份復原服務保存庫中的 Azure VM

本文說明如何使用[Azure 備份](backup-overview.md)服務來備份復原服務保存庫中的 Azure vm。

在本文中，您將了解：

> [!div class="checklist"]
> * 準備 Azure VM。
> * 建立保存庫。
> * 探索 Vm 並設定備份原則。
> * 啟用 Azure Vm 的備份。
> * 執行初始備份。


> [!NOTE]
> 本文說明如何設定保存庫, 並選取要備份的 Vm。 這有助於您備份多個 VM。 或者, 您可以直接從 VM 設定來[備份單一 AZURE vm](backup-azure-vms-first-look-arm.md) 。

## <a name="before-you-start"></a>開始之前


- 請[參閱](backup-architecture.md#architecture-direct-backup-of-azure-vms)Azure VM 備份架構。
- [深入了解](backup-azure-vms-introduction.md) Azure VM 備份和備份擴充功能。
- 設定備份之前, 請先[參閱支援矩陣](backup-support-matrix-iaas.md)。

此外, 在某些情況下, 您可能還需要做幾件事:

- 在**vm 上安裝 vm 代理程式**:為機器上執行的 Azure VM 代理程式安裝擴充功能，以 Azure 備份來備份 Azure VM。 如果您的 VM 是從 Azure marketplace 映射建立的, 則代理程式已安裝且正在執行。 如果您建立自訂 VM, 或遷移內部部署機器, 您可能需要[手動安裝代理程式](#install-the-vm-agent)。
- **明確允許輸出存取**:一般來說, 您不需要明確地允許 Azure VM 的輸出網路存取, 以便與 Azure 備份進行通訊。 不過, 有些 Vm 可能會遇到連線問題, 並顯示嘗試連線時的**ExtensionSnapshotFailedNoNetwork**錯誤。 如果發生這種情況, 您應該[明確地允許輸出存取](#explicitly-allow-outbound-access), 因此 Azure 備份擴充功能可以與 AZURE 公用 IP 位址進行通訊, 以進行備份流量。


## <a name="create-a-vault"></a>建立保存庫

 保存庫會儲存在一段時間內建立的備份和復原點，並儲存與備份的機器相關聯的備份原則。 請依照下列方式建立保存庫：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [搜尋] 中, 輸入**Recovery Services**。 在 [**服務**] 下, 按一下 [復原**服務保存庫**]。

     ![搜尋復原服務保存庫](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

3. 在 [復原**服務保存庫**] 功能表中, 按一下 [ **+ 新增**]。

     ![建立復原服務保存庫的步驟 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. 在 [復原**服務保存庫**] 中, 輸入易記名稱來識別保存庫。
    - 必須是 Azure 訂用帳戶中唯一的名稱。
    - 它可以包含 2 到 50 個字元。
    - 該名稱必須以字母開頭，而且只可以包含字母、數字和連字號。
5. 選取要在其中建立保存庫的 Azure 訂用帳戶、資源群組和地理區域。 接著，按一下 [建立]。
    - 建立保存庫可能需要一些時間。
    - 請監視入口網站右上方區域中的狀態通知。


 保存庫建立之後, 它會出現在 [復原服務保存庫] 清單中。 如果您沒有看到保存庫，請選取 [重新整理]。

![備份保存庫的清單](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

> [!NOTE]
> Azure 備份服務會建立個別的資源群組 (而不是 VM 資源群組) 來儲存快照集, 其命名格式為**AzureBackupRG_geography_number** (範例:AzureBackupRG_northeurope_1)。 此資源群組中的資料將會保留在 Azure 虛擬機器備份原則的 [*保留立即復原快照*集] 區段中所指定的持續時間 (以天為單位)。  對此資源群組套用鎖定可能會導致備份失敗。<br>
此資源群組也應從任何名稱/標記限制中排除, 因為限制原則會再次阻止建立資源點集合, 進而造成備份失敗。


### <a name="modify-storage-replication"></a>修改儲存體複寫

根據預設, 保存庫會使用[異地多餘儲存體 (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

- 如果保存庫是您的主要備份機制, 建議使用 GRS。
- 您可以使用[本機-多餘儲存體 (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)來取得較便宜的選項。

修改儲存體複寫類型, 如下所示:

1. 在新保存庫中, 按一下 [**設定**] 區段中的 [**屬性**]。
2. 在 [**屬性**] 的 [**備份**設定] 底下, 按一下 [**更新**]。
3. 選取 [儲存體] 複寫類型, 然後按一下 [儲存]。

      ![為新保存庫設定儲存體組態](./media/backup-try-azure-backup-in-10-mins/full-blade.png)
> [!NOTE]
   > 設定保存庫並包含備份專案之後, 即無法修改儲存體複寫類型。 如果您想要這樣做, 您必須重新建立保存庫。

## <a name="apply-a-backup-policy"></a>套用備份原則

設定保存庫的備份原則。

1. 在保存庫中, 按一下 [**總覽**] 區段中的 [ **+ 備份**]。

   ![備份按鈕](./media/backup-azure-arm-vms-prepare/backup-button.png)


2. 在**您的工作負載執行所在**的**備份目標** > ？選取 [ **Azure**]。 在 [**您要備份什麼？** ] 中, 選取 [**虛擬機器** >   **]** 。 這會在保存庫中註冊 VM 擴充功能。

   ![備份和備份目標窗格](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. 在 [備份原則] 中，選取要與保存庫產生關聯的原則。
    - 預設原則會每天備份一次 VM。 每日備份會保留30天。 立即修復快照集會保留兩天。
    - 如果您不想要使用預設原則, 請選取 [**建立新**的], 然後建立自訂原則, 如下一個程式所述。

      ![預設備份原則](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. 在 [**選取虛擬機器**] 中, 選取您想要使用原則備份的 vm。 然後按一下 [確定]。

   - 系統會驗證選取的 Vm。
   - 您只能選取與保存庫位於相同區域中的 VM。
   - VM 只能在單一保存庫中備份。

     ![[選取虛擬機器] 窗格](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. 在 [**備份**] 中, 按一下 [**啟用備份**]。 這會將原則部署到保存庫和 VM，並在執行於 Azure VM 的 VM 代理程式上安裝備份擴充功能。

     ![[啟用備份] 按鈕](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

啟用備份後：

- 無論 VM 是否在執行，備份服務都會安裝備份擴充功能。
- 初始備份會根據您的備份排程執行。
- 當備份執行時, 請注意:
    - 執行的 VM 具有最大的機會來捕獲應用程式一致的復原點。
    - 不過, 即使 VM 已關閉, 也會進行備份。 這類 VM 稱為離線 VM。 在此情況下, 復原點將會是損毀一致。


### <a name="create-a-custom-policy"></a>建立自訂原則

如果您選擇建立新的備份原則, 請填寫原則設定。

1. 在 [**原則名稱**] 中, 指定有意義的名稱。
2. 在 [**備份排程**] 中, 指定應採用備份的時間。 您可以針對 Azure Vm 進行每日或每週備份。
2. 在 [**立即還原**] 中, 指定您想要在本機保留快照集以進行立即還原的時間長度。
    - 當您還原時, 備份的 VM 磁片會從儲存體複製到復原儲存位置。 使用「立即還原」, 您可以利用在備份作業期間所建立的本機儲存快照集, 而不需要等待備份資料傳輸到保存庫。
    - 您可以保留快照集, 以便在一到五天之間進行立即還原。 預設設定為兩天。
3. 在 [**保留範圍**] 中, 指定您想要保留每日或每週備份點的時間長度。
4. 在 [**保留每月備份點**] 中, 指定是否要保留每日或每週備份的每月備份。
5. 按一下 [ **確定** ] 儲存原則。

    ![新增備份原則](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure 備份在進行 Azure VM 備份時不支援依據日光節約變更而自動調整時鐘。 當發生時間變更時, 請視需要手動修改備份原則。

## <a name="trigger-the-initial-backup"></a>觸發初始備份

初始備份會根據排程執行, 但您可以立即執行, 如下所示:

1. 在保存庫功能表中，按一下 [備份項目]。
2. 在 [備份項目] 中，按一下 [Azure 虛擬機器]。
3. 在 [**備份專案**] 清單中, 按一下省略號 (...)。
4. 按一下 [立即備份]。
5. 在 [**立即備份**] 中, 使用行事曆控制項選取復原點應保留的最後一天。 然後按一下 [確定]。
6. 監視入口網站通知。 您可以在保存庫儀表板中監視作業進度 > [備份作業] > [進行中]。 根據您的 VM 大小，建立初始備份可能需要花一點時間。

## <a name="verify-backup-job-status"></a>確認備份作業狀態

每個 VM 備份的備份作業詳細資料包含2個階段,**快照**集階段, 後面接著 [將**資料傳送至保存庫**] 階段。<br/>
快照集階段可保證與磁片一併儲存的復原點可用性, 以進行**立即還原**, 而且最多可以有5天的時間, 視使用者所設定的快照集保留而定。 將資料傳輸至保存庫會在保存庫中建立復原點以長期保留。 只有在快照集階段完成之後, 才會開始將資料傳輸到保存庫。

  ![備份作業狀態](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

後端有兩**個子**工作, 一個用於前端備份作業, 可以從 [**備份作業**詳細資料] 分頁中檢查, 如下所示:

  ![備份作業狀態](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

將**資料傳輸至保存庫**階段可能需要數天的時間才能完成, 視磁片大小、每個磁片的變換和其他數個因素而定。

作業狀態會根據下列案例而有所不同:

**快照集** | **將資料傳輸到保存庫** | **作業狀態**
--- | --- | ---
已完成 | 進行中 | 進行中
已完成 | 已略過 | 已完成
已完成 | 已完成 | 已完成
已完成 | 已失敗 | 已完成但有警告
已失敗 | 已失敗 | 已失敗


現在有了這項功能, 針對相同的 VM, 兩個備份可以平行執行, 但是在任一階段 (快照集, 將資料傳送到保存庫) 中, 只有一個子工作可以執行。 因此, 在某些情況下, 進行中的備份作業會導致下一天的備份失敗, 並透過這項分離功能來避免。 如果前一天的備份作業正在進行中, 則在將**資料傳輸到保存庫**時, 會略過後續日的備份。
在保存庫中建立的增量復原點將會從保存庫中建立的最後一個復原點來捕獲所有變換。 使用者不會產生任何成本影響。


## <a name="optional-steps-install-agentallow-outbound"></a>選擇性步驟 (安裝代理程式/允許輸出)
### <a name="install-the-vm-agent"></a>安裝 VM 代理程式

為機器上執行的 Azure VM 代理程式安裝擴充功能，以 Azure 備份來備份 Azure VM。 如果您的 VM 是從 Azure Marketplace 映射建立的, 則代理程式已安裝且正在執行。 如果您建立自訂 VM, 或遷移內部部署機器, 您可能需要手動安裝代理程式, 如下表中摘要說明。

**VM** | **詳細資料**
--- | ---
**Windows** | 1.[下載並安裝](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)代理程式 MSI 檔案。<br/><br/> 2.以機器的系統管理員權限安裝。<br/><br/> 3.確認安裝。 在 VM 上的*C:\WindowsAzure\Packages*中, 以滑鼠右鍵按一下 [ **WaAppAgent**  > ] [**屬性**]。 在 [**詳細資料**] 索引標籤上,**產品版本**應為2.6.1198.718 或更高。<br/><br/> 如果您要更新代理程式, 請確定沒有任何備份作業正在執行, 然後再[重新安裝代理程式](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
**Linux** | 從散發套件存放庫使用 RPM 或 DEB 套件進行安裝。 這是安裝和升級 Azure Linux 代理程式的慣用方法。 所有[認可的散發套件提供者](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)都會將 Azure Linux 代理程式套件整合於本身的映像和儲存機制中。 代理程式可從 [GitHub](https://github.com/Azure/WALinuxAgent) 取得，但不建議從該處安裝。<br/><br/> 如果您要更新代理程式, 請確定沒有任何備份作業正在執行, 並更新二進位檔。

### <a name="explicitly-allow-outbound-access"></a>明確允許輸出存取

在 VM 上執行的備份擴充功能需要 Azure 公用 IP 位址的輸出存取權。

- 一般來說, 您不需要明確地允許 Azure VM 的輸出網路存取, 以便與 Azure 備份進行通訊。
- 如果您在連接 Vm 時遇到問題, 或如果您在嘗試連線時看到錯誤**ExtensionSnapshotFailedNoNetwork** , 您應該明確地允許存取, 讓備份擴充功能可以與 AZURE 公用 IP 位址通訊以進行備份流量. 下表摘要說明存取方法。


**選項** | **動作** | **詳細資料**
--- | --- | ---
**設定 NSG 規則** | 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。<br/><br/> 您可以新增規則, 允許使用[服務](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure)標籤來存取 Azure 備份服務, 而不是允許及管理每個位址範圍。 | [深入了解](../virtual-network/security-overview.md#service-tags)服務標記。<br/><br/> 服務標記會簡化存取管理, 而不會產生額外的成本。
**部署 Proxy** | 部署 HTTP Proxy 伺服器來路由傳送流量。 | 提供整個 Azure 的存取權，而不只是儲存體的存取權。<br/><br/> 可精確控制儲存體 URL。<br/><br/> VM 具有單一網際網路存取點。<br/><br/> Proxy 需要額外成本。
**設定 Azure 防火牆** | 使用 Azure 備份服務的 FQDN 標記，允許流量通過 VM 上的 Azure 防火牆 | 如果您已在 VNet 子網中設定 Azure 防火牆, 則可輕鬆使用。<br/><br/> 您無法建立自己的 FQDN 標記, 或修改標記中的 Fqdn。<br/><br/> 如果您的 Azure Vm 具有受控磁片, 您可能需要在防火牆上開啟額外的埠 (8443)。

#### <a name="establish-network-connectivity"></a>建立網路連線

建立與 NSG、proxy 或防火牆的連線能力

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>設定 NSG 規則以允許對 Azure 的輸出存取

如果 NSG 管理虛擬機器存取權, 則允許將備份儲存體的輸出存取權提供給所需的範圍和埠。

1. 在 [VM 屬性 >**網路**] 中, 選取 [**新增輸出連接埠規則**]。
2. 在 [**新增輸出安全性規則**] 中, 選取 [ **Advanced**]。
3. 在 [來源] 中，選取 [VirtualNetwork]。
4. 在 [**來源埠範圍**] 中, 輸入星號 (*), 以允許來自任何埠的輸出存取。
5. 在 [目的地] 中，選取 [服務標記]。 從清單中選取 [儲存體區域]。 區域是保存庫和您想要備份之 Vm 所在的位置。
6. 在 [目的地連接埠範圍] 中，選取連接埠。
    - 使用未加密儲存體帳戶的非受控磁片的 VM:80
    - 使用非受控磁片與加密儲存體帳戶的 VM:443 (預設設定)
    - 使用受控磁片的 VM:8443。
7. 在 [通訊協定] 中，選取 [TCP]。
8. 在 [優先順序] 中，指定比任何優先順序較高的拒絕規則都小的優先順序值。

   如果您有拒絕存取的規則, 新的允許規則必須更高。 例如，如果您有優先順序設為 1000 的 **Deny_All** 規則，則新規則必須設為小於 1000。
9. 提供規則的 [名稱] 和 [描述], 然後選取 **[確定]** 。

您可以將 NSG 規則套用至多個 VM 來允許輸出存取。 這段影片將逐步說明其程序。

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


##### <a name="route-backup-traffic-through-a-proxy"></a>透過 Proxy 路由備份流量

您可以透過 Proxy 來路由備份流量，然後提供對所需 Azure 範圍的 Proxy 存取權。 設定 proxy VM 以允許下列各項:

- Azure VM 應透過 Proxy 路由所有連往公用網際網路的 HTTP 流量。
- Proxy 應允許來自適用虛擬網路中 Vm 的連入流量。
- NSG **NSF-lockdown** 需要一個規則，以允許從 Proxy VM 輸出的網際網路流量。

###### <a name="set-up-the-proxy"></a>設定 Proxy

如果您沒有系統帳戶 Proxy，請以下列方式設定一個：

1. 下載 [PsExec](https://technet.microsoft.com/sysinternals/bb897553)。
2. 執行 **PsExec.exe -i -s cmd.exe**，在系統帳戶下執行命令提示字元。
3. 在系統內容中執行瀏覽器。 例如, 針對 Internet Explorer 使用 **%PROGRAMFILES%\Internet Explorer\iexplore.exe** 。  
4. 定義 Proxy 設定。
   - 在 Linux 機器上：
     - 將以下這一行新增至 **/etc/environment** 檔案：
       - **HTTP_proxy = HTTP:\//proxy IP 位址: proxy 埠**
     - 將以下幾行新增至 **/etc/waagent.conf** 檔案：
         - **HttpProxy.Host=proxy IP address**
         - **HttpProxy.Port=proxy port**
   - 在 Windows 機器上，在瀏覽器設定中指定應使用 Proxy。 如果您目前使用使用者帳戶上的 Proxy，您可以使用此指令碼，將此設定套用至系統帳戶層級。
       ```powershell
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>允許 Proxy 上的連入連線

在 Proxy 設定中允許連入連線。

1. 在 [Windows 防火牆] 中, 開啟 [**具有 Advanced Security 的 Windows 防火牆**]。
2. 以滑鼠右鍵按一下 [輸入規則] > [新增規則]。
3. 在 [**規則類型**] 中, 選取 [**自訂** > **下一個]** 。
4. 在 [程式] 中，選取 [所有程式] > [下一步]。
5. 在 [**通訊協定和埠**] 中:
   - 將 [類型] 設定為 [ **TCP**]。
   - 將 [**本機埠**] 設定為 [**特定埠**]。
   - 將 [**遠端埠**] 設定為 [**所有埠**]。

6. 完成精靈，並指定規則的名稱。

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>新增 Proxy 的 NSG 例外規則

在 NSG **NSF-lockdown** 上，允許從 10.0.0.5 上的任何連接埠傳輸至 80 (HTTP) 或 443 (HTTPS) 連接埠上的任何網際網路位址的流量。

下列 PowerShell 指令碼提供允許流量的範例。
您可以指定 IP 位址範圍 (`-DestinationPortRange`), 或使用 storage. region 服務標籤, 而不是允許輸出至所有公用網際網路位址。   

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

##### <a name="allow-firewall-access-with-an-fqdn-tag"></a>允許使用 FQDN 標記的防火牆存取

您可以設定 Azure 防火牆, 以允許網路流量的輸出存取 Azure 備份。

- [了解](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)如何部署 Azure 防火牆。
- [閱讀](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN 標記的相關資訊。



## <a name="next-steps"></a>後續步驟

- 針對[AZURE vm 代理](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)程式或[azure vm 備份](backup-azure-vms-troubleshoot.md)的任何問題進行疑難排解。
- [還原](backup-azure-arm-restore-vms.md)Azure Vm。
