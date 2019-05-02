---
title: 管理處理序伺服器用於 VMware Vm 和實體伺服器至 Azure Site Recovery 與 Azure 的災害復原 |Microsoft Docs
description: 這篇文章說明管理的 VMware Vm 和實體伺服器至使用 Azure Site Recovery 的 Azure 災害復原設定的處理伺服器。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 2c27779719c73adf4d7fc1a61a0c77d03df71815
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925587"
---
# <a name="manage-process-servers"></a>管理處理序伺服器

此文章說明管理 Site Recovery 處理伺服器的一般工作。

處理序伺服器用來接收、 最佳化以及將複寫資料傳送至 Azure。 它也會執行推送安裝行動服務上的 VMware Vm 和實體伺服器，您想要複寫，並執行自動探索內部部署機器。 為了將內部部署 VMware Vm 或實體伺服器複寫至 Azure，處理序伺服器被安裝設定伺服器電腦上的預設值。 

- 針對大型部署，您可能需要額外的內部部署處理序伺服器才能調整容量。
- 從 Azure 容錯回復到內部部署，您必須設定 Azure 中的暫存處理序伺服器。 容錯回復完成後，您可以刪除此 VM。 

深入了解處理序伺服器。


## <a name="upgrade-a-process-server"></a>升級處理序伺服器

當您部署處理序伺服器內部部署，或為容錯回復 Azure VM，安裝處理序伺服器的最新版本。 Site Recovery 小組會定期發行修正程式和增強功能，我們建議您將處理序伺服器保持在最新狀態。 您可以升級處理序伺服器，如下所示：

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>移動至處理序伺服器負載平衡的 Vm

平衡負載，方法是兩個處理序伺服器之間移動 Vm 時，也將，如下所示：

1. 在 保存庫底下**管理**按一下  **Site Recovery 基礎結構**。 底下**適用於 VMware 與實體機器**，按一下**組態伺服器**。
2. 按一下 使用的處理序伺服器會註冊組態伺服器上。
3. 按一下您要負載平衡流量的處理序伺服器。

    ![LoadBalance](media/vmware-azure-manage-process-server/LoadBalance.png)

4. 按一下 **負載平衡**，選取您要移動機器的目標處理序伺服器。 然後按一下  **確定**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. 按一下 **選取機器**，然後選擇您想要從目前移動至目標處理序伺服器的機器。 系統會針對每個虛擬機器顯示平均資料變更的詳細資料。 然後按一下 [確定] 。 
3. 在保存庫，來監視底下之作業的進度**監視** > **Site Recovery 作業**。

它將需要大約 15 分鐘才會反映在入口網站中的變更。 如需更快的效果，[重新整理設定伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。

## <a name="switch-an-entire-workload-to-another-process-server"></a>整個工作負載切換至另一個處理序伺服器

移至不同的處理序伺服器、 處理序伺服器，如下所示處理整個工作負載：

1. 在 保存庫底下**管理**按一下  **Site Recovery 基礎結構**。 底下**適用於 VMware 與實體機器**，按一下**組態伺服器**。
2. 按一下 使用的處理序伺服器會註冊組態伺服器上。
3. 按一下您要切換的工作負載的處理序伺服器。
4. 按一下 **交換器**，選取您要移動工作負載的目標處理序伺服器。 然後按一下  **確定**

    ![Switch](media/vmware-azure-manage-process-server/Switch.PNG)

5. 在保存庫，來監視底下之作業的進度**監視** > **Site Recovery 作業**。

它將需要大約 15 分鐘才會反映在入口網站中的變更。 如需更快的效果，[重新整理設定伺服器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。



## <a name="reregister-a-process-server"></a>重新註冊處理序伺服器

重新註冊內部部署上執行的處理序伺服器或與組態伺服器，如下所示的 Azure VM 上：

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

儲存設定之後，請執行下列作業：

1. 在處理序伺服器上，開啟系統管理員命令提示字元。
2. 瀏覽至 **%PROGRAMDATA%\ASR\Agent** 資料夾，然後執行命令：

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>修改內部部署處理序伺服器的 Proxy 設定

如果內部處理序伺服器會使用 proxy 來連線到 Azure，您可以修改的 proxy 設定，如下所示：

1. 登入處理序伺服器機器。 
2. 開啟系統管理 PowerShell 命令視窗並執行下列命令：
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. 瀏覽至資料夾 **%PROGRAMDATA%\ASR\Agent**，並執行此命令：
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>移除處理序伺服器

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>排除資料夾不進行防毒軟體

如果向外延展處理序伺服器 （或主要目標伺服器） 上執行防毒軟體，請從防毒程式作業排除下列資料夾：


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- 處理序伺服器安裝目錄。 例如︰C:\Program Files (x86)\Microsoft Azure Site Recovery