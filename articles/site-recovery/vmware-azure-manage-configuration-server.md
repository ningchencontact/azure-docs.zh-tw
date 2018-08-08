---
title: 使用 Azure Site Recovery 管理 VMware 災害復原的設定伺服器 | Microsoft Docs
description: 本文說明如何使用 Azure Site Recovery 管理將 VMware 災害復原到 Azure 的現有設定伺服器。
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: df5b2ecce2a5c9d7c263ee0acc3a49b859b93f7f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346115"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>管理 VMware VM 的設定伺服器

當您使用 [Azure Site Recovery](site-recovery-overview.md) 將 VMware VM 和實體伺服器災害復原到 Azure 時，便會設定內部部署設定伺服器。 設定伺服器會協調內部部署 VMware 與 Azure 之間的通訊，以及管理資料複寫。 本文摘要說明部署設定伺服器之後，管理設定伺服器的一般工作。



## <a name="modify-vmware-settings"></a>修改 VMware 設定

您可以依照下列方式存取組態伺服器：
    - 登入該伺服器部署所在的 VM，然後從桌面捷徑啟動 [Azure Site Recovery 組態管理員]。
    - 或者，您可以經由 **https://*ConfigurationServerName*/:44315/** 從遠端存取組態伺服器。 使用系統管理員認證登入。
   
### <a name="modify-vmware-server-settings"></a>修改 VMware 伺服器設定

1. 若要為不同的 VMware 伺服器與設定伺服器建立關聯，請在登入後選取 [新增 vCenter Server/vSphere ESXi 伺服器]。
2. 輸入詳細資料，然後選取 [確定]。


### <a name="modify-credentials-for-automatic-discovery"></a>修改自動探索所需的認證

1. 若要更新用來連線至 VMware 伺服器以進行 VMware VM 自動探索的認證，請在登入後選取 [編輯]。
2. 輸入新的認證，然後選取 [確定] 。

    ![修改 VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)


## <a name="modify-credentials-for-mobility-service-installation"></a>修改行動服務安裝的認證

針對您啟用複寫的 VMware VM，修改在其上自動安裝行動服務所用的認證。

1. 在登入後，選取 [管理虛擬機器認證]
2. 輸入新的認證，然後選取 [確定] 。

    ![修改行動服務認證](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>修改 Proxy 設定

修改設定伺服器電腦用來透過網際網路存取 Azure 的 Proxy 設定。 如果您除了在設定伺服器電腦上執行的預設處理伺服器之外還有處理伺服器電腦，則請修改兩部電腦上的設定。

1. 在登入組態伺服器後，選取 [管理連線]。
2. 更新 Proxy 值。 然後選取 [儲存] 以更新設定。

## <a name="add-a-network-adapter"></a>新增網路介面卡

開放式虛擬化格式 (OVF) 範本會部署包含單一網路介面卡的設定伺服器 VM。

- 您可以[將其他介面卡新增至 VM](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter)，但是必須在保存庫中註冊設定伺服器之前新增。
- 若要在保存庫中註冊設定伺服器之後新增介面卡，請在 VM 屬性中新增介面卡。 然後，您必須在保存庫中註冊伺服器。


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>在同一個保存庫中註冊設定伺服器

如果需要，您可以在同一個保存庫中註冊設定伺服器。 如果您除了在設定伺服器電腦上執行的預設處理伺服器之外，還有其他處理伺服器電腦，則請註冊這兩部電腦。


  1. 在保存庫中，開啟 [管理] > [Site Recovery 基礎結構] > [設定伺服器]。
  2. 在 [伺服器] 中，選取 [下載註冊金鑰] 以下載保存庫認證檔案。
  3. 登入設定伺服器電腦。
  4. 在 **%ProgramData%\ASR\home\svsystems\bin** 中，開啟 **cspsconfigtool.exe**。
  5. 在 [保存庫註冊] 索引標籤上，選取 [瀏覽] 並找出您下載的保存庫認證檔。
  6. 如果需要，請提供 Proxy 伺服器詳細資料。 接著，選取 [註冊]。
  7. 開啟系統管理 PowerShell 命令視窗並執行下列命令：
   ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

      >[!NOTE] 
      >若要從設定伺服器**提取最新憑證**以相應放大伺服器，請執行命令 *“<安裝磁碟機\Microsoft Azure Site Recovery\agent\cdpcli.exe>" --registermt*

  8. 最後，執行下列命令以重新啟動 obengine。
  ```
          net stop obengine
          net start obengine

## Upgrade the configuration server

You run update rollups to update the configuration server. Updates can be applied for up to N-4 versions. For example:

- If you run 9.7, 9.8, 9.9, or 9.10, you can upgrade directly to 9.11.
- If you run 9.6 or earlier and you want to upgrade to 9.11, you must first upgrade to version 9.7. before 9.11.

Links to update rollups for upgrading to all versions of the configuration server are available in the [wiki updates page](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgrade the server as follows:

1. In the vault, go to **Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.
2. If an update is available, a link appears in the **Agent Version** > column.
    ![Update](./media/vmware-azure-manage-configuration-server/update2.png)
3. Download the update installer file to the configuration server.

    ![Update](./media/vmware-azure-manage-configuration-server/update1.png)

4. Double-click to run the installer.
5. The installer detects the current version running on the machine. Click **Yes** to start the upgrade.
6. When the upgrade completes the server configuration validates.

    ![Update](./media/vmware-azure-manage-configuration-server/update3.png)
    
7. Click **Finish** to close the installer.

## Delete or unregister a configuration server

1. [Disable protection](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) for all VMs under the configuration server.
2. [Disassociate](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) and [delete](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) all replication policies from the configuration server.
3. [Delete](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) all vCenter servers/vSphere hosts that are associated with the configuration server.
4. In the vault, open **Site Recovery Infrastructure** > **Configuration Servers**.
5. Select the configuration server that you want to remove. Then, on the **Details** page, select **Delete**.

    ![Delete configuration server](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### Delete with PowerShell

You can optionally delete the configuration server by using PowerShell.

1. [Install](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) the Azure PowerShell module.
2. Sign in to your Azure account by using this command:
    
    `Connect-AzureRmAccount`
3. Select the vault subscription.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Set the vault context.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault> Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Retrieve the configuration server.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Delete the configuration server.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> You can use the **-Force** option in Remove-AzureRmSiteRecoveryFabric for forced deletion of the configuration server.
 
## Generate configuration server Passphrase

1. Sign in to your configuration server, and then open a command prompt window as an administrator.
2. To change the directory to the bin folder, execute the command **cd %ProgramData%\ASR\home\svsystems\bin**
3. To generate the passphrase file, execute **genpassphrase.exe -v > MobSvc.passphrase**.
4. Your passphrase will be stored in the file located at **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## Renew SSL certificates

The configuration server has an inbuilt web server, which orchestrates activities of the Mobility Service, process servers, and master target servers connected to it. The web server uses an SSL certificate to authenticate clients. The certificate expires after three years and can be renewed at any time.

### Check expiry

For configuration server deployments before May 2016, certificate expiry was set to one year. If you have a certificate that is going to expire, the following occurs:

- When the expiry date is two months or less, the service starts sending notifications in the portal, and by email (if you subscribed to Site Recovery notifications).
- A notification banner appears on the vault resource page. For more information, select the banner.
- If you see an **Upgrade Now** button, it indicates that some components in your environment haven't been upgraded to 9.4.xxxx.x or higher versions. Upgrade the components before you renew the certificate. You can't renew on older versions.

### Renew the certificate

1. In the vault, open **Site Recovery Infrastructure** > **Configuration Server**. Select the required configuration server.
2. The expiry date appears under **Configuration Server health**.
3. Select **Renew Certificates**. 


## Next steps

Review the tutorials for setting up disaster recovery of [VMware VMs](vmware-azure-tutorial.md) to Azure.
