---
title: 使用 Azure 备份将 Azure VM 备份到恢复服务保管库中
description: 介绍如何使用 Azure 备份将 Azure VM 备份到恢复服务保管库中
service: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: raynew
ms.openlocfilehash: 98934216c0860c79575874df26603b1187e35978
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60647496"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>備份復原服務保存庫中的 Azure VM

本文介绍如何使用 [Azure 备份](backup-overview.md)服务将 Azure VM 备份到恢复服务保管库中。 

在本文中，您將了解：

> [!div class="checklist"]
> * 準備 Azure VM。
> * 建立保存庫。
> * 发现 VM 并配置备份策略。
> * 为 Azure VM 启用备份。
> * 執行初始備份。


> [!NOTE]
> 本文将会介绍如何设置保管库以及选择要备份的 VM。 這有助於您備份多個 VM。 或者，可以直接从 VM 设置[备份单个 Azure VM](backup-azure-vms-first-look-arm.md)。

## <a name="before-you-start"></a>開始之前


- [查看](backup-architecture.md#architecture-direct-backup-of-azure-vms) Azure VM 备份体系结构。
- [深入了解](backup-azure-vms-introduction.md) Azure VM 備份和備份擴充功能。
- [請檢閱支援對照表](backup-support-matrix-iaas.md)設定備份之前。

此外，在某些情况下，还需要完成几项操作：

- **在 VM 上安装 VM 代理**：為機器上執行的 Azure VM 代理程式安裝擴充功能，以 Azure 備份來備份 Azure VM。 如果 VM 是从 Azure 市场映像创建的，则代理已安装并正在运行。 如果创建了自定义 VM 或者迁移了本地计算机，则可能需要[手动安装代理](#install-the-vm-agent)。
- **显式允许出站访问**：一般情况下，无需显式允许 Azure VM 的出站网络访问即可让它与 Azure 备份进行通信。 但是，某些 VM 在尝试进行连接时可能会遇到连接问题并显示 **ExtensionSnapshotFailedNoNetwork** 错误。 如果发生这种情况，应该[显式允许出站访问](#explicitly-allow-outbound-access)，使 Azure 备份扩展能够与备份流量的 Azure 公共 IP 地址通信。


## <a name="create-a-vault"></a>建立保存庫

 保存庫會儲存在一段時間內建立的備份和復原點，並儲存與備份的機器相關聯的備份原則。 請依照下列方式建立保存庫：    

1. 登入 [Azure 入口網站](https://portal.azure.com/)。    
2. 在搜索框中键入“恢复服务”。 在“服务”下，单击“恢复服务保管库”。   

     ![搜索恢复服务保管库](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/> 

3. 在“恢复服务保管库”菜单中，单击“+添加”。    

     ![建立復原服務保存庫的步驟 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)   

4. 在“恢复服务保管库”中，键入一个易记名称用于标识保管库。   
    - 必須是 Azure 訂用帳戶中唯一的名稱。   
    - 它可以包含 2 到 50 個字元。    
    - 該名稱必須以字母開頭，而且只可以包含字母、數字和連字號。   
5. 选择要在其中创建保管库的 Azure 订阅、资源组和地理区域。 接著，按一下 [建立]。    
    - 建立保存庫可能需要一些時間。  
    - 請監視入口網站右上方區域中的狀態通知。   


 创建保管库后，它会显示在恢复服务保管库列表中。 如果您沒有看到保存庫，請選取 [重新整理]。
 
![備份保存庫的清單](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)    

### <a name="modify-storage-replication"></a>修复存储复制

默认情况下，保管库使用[异地冗余存储 (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

- 如果保管库是主要备份机制，我们建议使用 GRS。
- [本地冗余存储 (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 的费用更低。

按如下所述修改存储复制类型：

1. 在新保管库的“设置”部分单击“属性”。
2. 在“属性”中的“备份配置”下，单击“更新”。
3. 选择存储复制类型，然后单击“保存”。

      ![為新保存庫設定儲存體組態](./media/backup-try-azure-backup-in-10-mins/full-blade.png)
> [!NOTE]
   > 在保管库经过设置并且包含备份项之后，无法修改存储复制类型。 若要修改，需要重新创建保管库。 

## <a name="apply-a-backup-policy"></a>应用备份策略

配置保管库的备份策略。

1. 在保管库的“概述”部分，单击“+备份”。

   ![備份按鈕](./media/backup-azure-arm-vms-prepare/backup-button.png)


2. 在“备份目标” > “工作负荷在哪里运行?”中，选择“Azure”。 在“要备份哪些内容?”中，选择“虚拟机” >  “确定”。 這會在保存庫中註冊 VM 擴充功能。

   ![備份和備份目標窗格](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. 在 [備份原則] 中，選取要與保存庫產生關聯的原則。 
    - 默认策略每天备份 VM 一次。 每日备份将保留 30 天。 即时恢复快照将保留两天。
    - 如果不想要使用默认策略，请选择“新建”，然后按下一过程中所述创建自定义策略。

      ![默认备份策略](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. 在“选择虚拟机”中，选择要使用该策略备份的 VM。 然後按一下 [確定] 。

   - 随后将验证选定的 VM。
   - 您只能選取與保存庫位於相同區域中的 VM。
   - VM 只能在單一保存庫中備份。

     ![[選取虛擬機器] 窗格](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. 在“备份”中，单击“启用备份”。 這會將原則部署到保存庫和 VM，並在執行於 Azure VM 的 VM 代理程式上安裝備份擴充功能。
     
     ![[啟用備份] 按鈕](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

啟用備份後：

- 無論 VM 是否在執行，備份服務都會安裝備份擴充功能。
- 初始备份将根据备份计划运行。
- 运行备份时，请注意：
    - 正在运行的 VM 最有可能捕获应用程序一致的恢复点。
    - 但是，即使 VM 已关闭，也仍会将其备份。 此类 VM 称为脱机 VM。 在这种情况下，恢复点是崩溃一致的。
    

### <a name="create-a-custom-policy"></a>建立自訂原則

如果已选择创建新的备份策略，请填写策略设置。

1. 在“策略名称”中指定有意义的名称。
2. 在“备份计划”中指定何时创建备份。 可为 Azure VM 创建每日或每周备份。
2. 在“即时还原”中，指定要在本地将用于即时还原的快照保留多长时间。
    - 还原时，已备份的 VM 磁盘将通过网络从存储复制到恢复存储位置。 使用即时还原时，可以利用在执行备份作业期间创建的存储于本地的快照，而无需等待将备份数据传输到保管库。
    - 可将用于即时还原的快照保留一到五天。 默认设置为两天。
3. 在“保留期”中，指定要将每日或每周备份点保留多长时间。
4. 在“保留每月备份点”中，指定是否要保留每日或每周备份的每月备份。 
5. 按一下 [ **確定** ] 儲存原則。

    ![新建备份策略](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure 備份在進行 Azure VM 備份時不支援依據日光節約變更而自動調整時鐘。 发生时间变化时，可根据需要手动修改备份策略。

## <a name="trigger-the-initial-backup"></a>触发初始备份

初始备份将根据计划运行，但你可以按如下所述手动运行：

1. 在保存庫功能表中，按一下 [備份項目]。
2. 在 [備份項目] 中，按一下 [Azure 虛擬機器]。
3. 在“备份项”列表中，单击省略号 (...)。
4. 按一下 [立即備份]。
5. 在“立即备份”中，使用日历控件选择恢复点的最后保留日期。 然後按一下 [確定] 。
6. 監視入口網站通知。 您可以在保存庫儀表板中監視作業進度 > [備份作業] > [進行中]。 根據您的 VM 大小，建立初始備份可能需要花一點時間。

## <a name="optional-steps-install-agentallow-outbound"></a>可选步骤（安装代理/允许出站访问）
### <a name="install-the-vm-agent"></a>安裝 VM 代理程式

為機器上執行的 Azure VM 代理程式安裝擴充功能，以 Azure 備份來備份 Azure VM。 如果 VM 是从 Azure 市场映像创建的，则代理已安装并正在运行。 如果创建了自定义 VM 或者迁移了本地计算机，则可能需要根据下表中的摘要手动安装代理。

**VM** | **詳細資料**
--- | ---
**Windows** | 1.[下載並安裝](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)代理程式 MSI 檔案。<br/><br/> 2.以機器的系統管理員權限安裝。<br/><br/> 3.验证安装。 在 VM 上的“C:\WindowsAzure\Packages”中，右键单击“WaAppAgent.exe” >  选择“属性”。 在“详细信息”选项卡上，“产品版本”应为 2.6.1198.718 或更高。<br/><br/> 若要更新代理，请确保没有备份操作正在运行，并[重新安装代理](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
**Linux** | 使用分发版包存储库中的 RPM 或 DEB 包安装代理。 这是安装和升级 Azure Linux 代理的首选方法。 所有[認可的散發套件提供者](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)都會將 Azure Linux 代理程式套件整合於本身的映像和儲存機制中。 代理程式可從 [GitHub](https://github.com/Azure/WALinuxAgent) 取得，但不建議從該處安裝。<br/><br/> 若要更新代理，请确保没有备份操作正在运行，并更新二进制文件。

### <a name="explicitly-allow-outbound-access"></a>显式允许出站访问

VM 上运行的备份扩展需要对 Azure 公共 IP 地址进行出站访问。

- 一般情况下，无需显式允许 Azure VM 的出站网络访问即可让它与 Azure 备份进行通信。
- 如果 VM 连接遇到问题，或者在尝试连接时出现 **ExtensionSnapshotFailedNoNetwork** 错误，应显式允许访问，使备份扩展能够与备份流量的 Azure 公共 IP 地址通信。 下表汇总了访问方法。


**選項** | **Action** | **詳細資料** 
--- | --- | --- 
**設定 NSG 規則** | 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。<br/><br/> 可以添加一个允许使用[服务标记](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure)访问 Azure 备份服务的规则，而无需允许并管理每个地址范围。 | [深入了解](../virtual-network/security-overview.md#service-tags)服務標記。<br/><br/> 服务标记可以简化访问管理，且不会产生额外的费用。
**部署 Proxy** | 部署 HTTP Proxy 伺服器來路由傳送流量。 | 提供整個 Azure 的存取權，而不只是儲存體的存取權。<br/><br/> 可精確控制儲存體 URL。<br/><br/> VM 具有單一網際網路存取點。<br/><br/> Proxy 需要額外成本。
**設定 Azure 防火牆** | 使用 Azure 備份服務的 FQDN 標記，允許流量通過 VM 上的 Azure 防火牆 | 如果在 VNet 子网中设置了 Azure 防火墙，则 FQDN 标记很容易使用。<br/><br/> 无法创建自己的 FQDN 标记，无法修改标记中的 FQDN。<br/><br/> 如果 Azure VM 包含托管磁盘，则你可能需要在防火墙上额外打开一个端口 (8443)。

#### <a name="establish-network-connectivity"></a>建立網路連線

通过代理或防火墙与 NSG 建立连接

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>設定 NSG 規則以允許對 Azure 的輸出存取

如果对 VM 的访问由 NSG 管理，请允许通过所需的范围和端口对备份存储进行出站访问。

1. 在 VM 属性 >“网络”中，选择“添加出站端口规则”。
2. 在“添加出站安全规则”中，选择“高级”。
3. 在 [來源] 中，選取 [VirtualNetwork]。
4. 在“源端口范围”中输入一个星号 (*)，以允许从任何端口进行出站访问。
5. 在 [目的地] 中，選取 [服務標記]。 從清單中選取 [儲存體區域]。 区域是保管库和要备份的 VM 所在的位置。
6. 在 [目的地連接埠範圍] 中，選取連接埠。
    - 使用未加密儲存體帳戶的未受控 VM：80
    - 使用加密儲存體帳戶的未受控 VM：443 (預設設定)
    - 受控 VM：8443。
7. 在 [通訊協定] 中，選取 [TCP]。
8. 在 [優先順序] 中，指定比任何優先順序較高的拒絕規則都小的優先順序值。
   
   如果某个规则拒绝访问，则新的允许规则的优先级必须更高。 例如，如果您有優先順序設為 1000 的 **Deny_All** 規則，則新規則必須設為小於 1000。
9. 提供规则的名称和说明，然后选择“确定”。

您可以將 NSG 規則套用至多個 VM 來允許輸出存取。 這段影片將逐步說明其程序。

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


##### <a name="route-backup-traffic-through-a-proxy"></a>透過 Proxy 路由備份流量

您可以透過 Proxy 來路由備份流量，然後提供對所需 Azure 範圍的 Proxy 存取權。 将代理 VM 配置为允许以下操作：

- Azure VM 應透過 Proxy 路由所有連往公用網際網路的 HTTP 流量。
- 代理应该允许适用虚拟网络中的 VM 传入的流量。
- NSG **NSF-lockdown** 需要一個規則，以允許從 Proxy VM 輸出的網際網路流量。

###### <a name="set-up-the-proxy"></a>設定 Proxy

如果您沒有系統帳戶 Proxy，請以下列方式設定一個：

1. 下載 [PsExec](https://technet.microsoft.com/sysinternals/bb897553)。
2. 執行 **PsExec.exe -i -s cmd.exe**，在系統帳戶下執行命令提示字元。
3. 在系統內容中執行瀏覽器。 例如：对于 Internet Explorer，请使用 **%PROGRAMFILES%\Internet Explorer\iexplore.exe**。  
4. 定義 Proxy 設定。
   - 在 Linux 機器上：
     - 將以下這一行新增至 **/etc/environment** 檔案：
       - **http_proxy=http:\//代理 IP 地址:代理端口**
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

1. 在 Windows 防火墙中，打开“高级安全 Windows 防火墙”。
2. 以滑鼠右鍵按一下 [輸入規則] > [新增規則]。
3. 在“规则类型”中，选择“自定义” > “下一步”。
4. 在 [程式] 中，選取 [所有程式] > [下一步]。
5. 在“协议和端口”中：
   - 将类型设置为“TCP”。
   - 将“本地端口”设置为“特定端口”。
   - 将“远程端口”设置为“所有端口”。
  
6. 完成精靈，並指定規則的名稱。

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>新增 Proxy 的 NSG 例外規則

在 NSG **NSF-lockdown** 上，允許從 10.0.0.5 上的任何連接埠傳輸至 80 (HTTP) 或 443 (HTTPS) 連接埠上的任何網際網路位址的流量。

下列 PowerShell 指令碼提供允許流量的範例。
如果不想要允许对所有公共 Internet 地址进行出站访问，可以指定 IP 地址范围 (`-DestinationPortRange`)，或使用 storage.region 服务标记。   

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

##### <a name="allow-firewall-access-with-an-fqdn-tag"></a>使用 FQDN 标记允许通过防火墙访问

可以设置 Azure 防火墙，以允许网络流量对 Azure 备份进行出站访问。

- [了解](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)如何部署 Azure 防火牆。
- [閱讀](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN 標記的相關資訊。



## <a name="next-steps"></a>後續步驟

- 排查 [Azure VM 代理](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)或 [Azure VM 备份](backup-azure-vms-troubleshoot.md)出现的任何问题。
- [还原](backup-azure-arm-restore-vms.md) Azure VM。

