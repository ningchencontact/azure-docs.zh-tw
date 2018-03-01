---
title: " 使用 Azure Site Recovery 管理 VMware 災害復原的設定伺服器 | Microsoft Docs"
description: "本文說明如何使用 Azure Site Recovery 服務管理將 VMware 災害復原到 Azure 的現有設定伺服器。"
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2018
ms.author: anoopkv
ms.openlocfilehash: 9cdabfb4e24423d76e4f247f184ac4156c3b257b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="manage-the-configuration-server"></a>管理組態伺服器

當您使用 [Azure Site Recovery](site-recovery-overview.md) 服務將 VMware VM 和實體伺服器災害復原到 Azure 時，便會設定內部部署設定伺服器。 設定伺服器會協調內部部署 VMware 與 Azure 之間的通訊，以及管理資料複寫。 本文摘要說明部署設定伺服器之後，管理設定伺服器的一般工作。

## <a name="modify-vmware-settings"></a>修改 VMware 設定

修改設定伺服器所連線 VMware 伺服器的設定。

1. 登入執行設定伺服器的電腦。
2. 從桌面捷徑啟動 [Azure Site Recovery 設定管理員]。 或者，開啟 **https://configuration-server-name/IP:44315**。
3. 按一下 [管理 vCenter Server/vSPhere ESXi 伺服器]：
    - 若要為不同的 VMware 伺服器與設定伺服器建立關聯，請按一下 [新增 vCenter Server/vSPhere ESXi 伺服器]，並指定伺服器詳細資料。
    - 若要更新用於連線到 VMware 伺服器的認證，以進行 VMware VM 自動探索，請按一下 [編輯]。 指定新的認證，然後按一下 [確定] 。

        ![修改 VMware](./media/site-recovery-vmware-to-azure-manage-configuration-server/modify-vmware-server.png)

## <a name="modify-credentials-for-mobility-service-installation"></a>修改行動服務安裝的認證

針對您啟用複寫的 VMware VM，修改在其上自動安裝行動服務所用的認證。

1. 登入執行設定伺服器的電腦。
2. 從桌面捷徑啟動 [Azure Site Recovery 設定管理員]。 或者，開啟 **https://configuration-server-name/IP:44315**。
3. 按一下 [管理虛擬機器認證]，並指定新的認證。 然後按一下 [確定] 以更新設定。

    ![修改行動服務認證](./media/site-recovery-vmware-to-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>修改 Proxy 設定

修改設定伺服器電腦用來透過網際網路存取 Azure 的 Proxy 設定。 如果您除了在設定伺服器電腦上執行的預設處理伺服器之外，還有其他處理伺服器電腦，則請修改兩部電腦上的設定。

1. 登入執行設定伺服器的電腦。
2. 從桌面捷徑啟動 [Azure Site Recovery 設定管理員]。 或者，開啟 **https://configuration-server-name/IP:44315**。
3. 按一下 [管理連線]，並更新 Proxy 值。 然後按一下 [儲存] 以更新設定。

## <a name="add-a-network-adapter"></a>新增網路介面卡

OVF 範本會部署包含單一網路介面卡的設定伺服器 VM。 您可以[將其他介面卡新增到 VM](how-to-deploy-configuration-server.md#add-an-additional-adapter)，但是必須在保存庫中註冊設定伺服器之前新增。

如果需要在保存庫中註冊設定伺服器之後新增介面卡，則必須在 VM 屬性中新增介面卡，然後在保存庫中註冊伺服器。


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>在同一個保存庫中註冊設定伺服器

如果需要，您可以在同一個保存庫中註冊設定伺服器。 如果您除了在設定伺服器電腦上執行的預設處理伺服器之外，還有其他處理伺服器電腦，則請註冊這兩部電腦。

  1. 在保存庫中，開啟 [管理] > [Site Recovery 基礎結構] > [設定伺服器]。
  2. 在 [伺服器] 中，按一下 [下載註冊金鑰]。 這會下載保存庫認證檔案。
  3. 登入設定伺服器電腦。
  4. 在 **%ProgramData%\ASR\home\svagent\bin** 中，開啟 [cspsconfigtool.exe]。
  5. 在 [保存庫註冊] 索引標籤上，按一下 [瀏覽] 並找出您下載的保存庫認證檔。
  6. 如果需要，請提供 Proxy 伺服器詳細資料。 然後按一下 [註冊]。
  7. 開啟系統管理 PowerShell 命令視窗並執行下列命令：

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```
## <a name="upgrade-the-configuration-server"></a>升級設定伺服器

您執行更新彙總來更新設定伺服器。 更新最多可以套用到 N-4 版本。 例如︰

- 如果您執行 9.7、9.8、9.9 或 9.10 - 您可以直接升級至 9.11。
- 如果您執行 9.6 或更早版本，而且想要升級至 9.11，在升級到 9.11 之前，您必須先升級到 版本 9.7。

用來升級至設定伺服器所有版本的更新彙總連結，可以在 [wiki 更新分頁](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)取得。

升級伺服器，如下所示：

1. 將更新安裝程式下載到設定伺服器上。
2. 按兩下安裝程式以執行。
3. 安裝程式會偵測電腦上執行的目前版本。
4. 按一下 [確定] 以確認，並執行升級。 


## <a name="delete-or-unregister-a-configuration-server"></a>將設定伺服器刪除或取消註冊

1. 停用此設定伺服器下所有 VM 的[停用保護](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)。
2. 將設定伺服器的所有複寫原則[解除關聯](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy)並[刪除](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy)。
3. [刪除](site-recovery-vmware-to-azure-manage-vCenter.md#delete-a-vcenter-in-azure-site-recovery)與設定伺服器關聯的所有 VCenter 伺服器/vSphere 主機。
4. 在保存庫中，開啟 [Site Recovery 基礎結構] > [設定伺服器]
5. 按一下您想要移除的設定伺服器。 然後在 [詳細資料] 頁面上按一下 [刪除] 按鈕。

    ![刪除設定伺服器](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>以 PowerShell 刪除

您可以選擇使用 PowerShell 來刪除設定伺服器：

1. [安裝](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) Azure PowerShell 模組
2. 使用命令登入您的 Azure 帳戶：
    
    `Login-AzureRmAccount`
3. 選取保存庫訂用帳戶

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  設定保存庫內容：
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. 擷取設定伺服器：

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. 刪除設定伺服器：

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> 您可以使用 Remove-AzureRmSiteRecoveryFabric 中的 **-Force** 選項來強制刪除設定伺服器。
 


## <a name="renew-ssl-certificates"></a>更新 SSL 憑證

設定伺服器有內建的 Web 伺服器，可協調它所連線的行動服務、處理伺服器和主要目標伺服器的活動。 Web 伺服器使用 SSL 憑證來驗證用戶端。 憑證會在三年後到期，且可隨時更新。

### <a name="check-expiry"></a>檢查到期日

針對在 2016 年 5 月之前部署的設定伺服器，憑證到期設定為一年。 如果您有即將到期的憑證，會發生以下狀況：

- 當到期日是兩個月或少於兩個月時，服務會開始在入口網站傳送通知，以及透過電子郵件傳送通知 (如果您已訂閱 Azure Site Recovery 通知)。
- 通知橫幅會出現在保存庫資源頁面上。 按一下橫幅可取得詳細資料。
- 如果您看到 [立即升級] 按鈕，表示您的環境中有些元件尚未升級至 9.4.xxxx.x 或更新版本。 更新憑證之前請先升級元件。 您不能在舊版上更新。

### <a name="renew-the-certificate"></a>更新憑證

1. 在保存庫中，開啟 [Site Recovery 基礎結構] > [設定伺服器]，然後按一下所需的設定伺服器。
2. 到期日顯示在 [設定伺服器健康情況] 下
3. 按一下 [更新憑證]。 


## <a name="next-steps"></a>後續步驟

檢閱將 [VMware VM](tutorial-vmware-to-azure.md) 和實體伺服器 (tutorial-physical-to-azure.md) 設定為災害復原至 Azure 的教學課程。
