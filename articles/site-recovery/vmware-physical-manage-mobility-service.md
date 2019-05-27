---
title: 管理的 VMware Vm 災害復原的伺服器和使用 Azure Site Recovery 的實體伺服器上的行動代理程式 |Microsoft Docs
description: 管理的 VMware Vm 和實體伺服器至 Azure 中使用 Azure Site Recovery 服務的嚴重損壞修復的行動服務代理程式。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 69b8e1c533747d1bade69949911ea43f299f49e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "66117315"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>管理受保護機器上的行動代理程式

您設定行動代理程式在伺服器上使用 Azure Site Recovery 針對 VMware Vm 和實體伺服器至 Azure 的災害復原時。 行動代理程式協調受保護的機器，組態伺服器/相應放大處理序伺服器之間的通訊，以及管理資料複寫。 本文摘要說明部署之後，管理行動代理程式的一般工作。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>更新行動服務，從 Azure 入口網站

1. 在開始之前，請先確定已更新設定伺服器、向外延展處理伺服器，以及要一起部署的所有主要目標伺服器，然後才更新受保護機器上的行動服務。
2. 在入口網站中，開啟保存庫 > [複寫的項目]。
3. 如果設定伺服器已是最新版本，您會看到一則通知，指出「有新的 Site Recovery 複寫代理程式更新可用。 按一下以安裝」的通知。

     ![[複寫的項目] 視窗](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. 按一下通知，然後在 [代理程式更新] 中，選取您想要升級行動服務的機器。 然後按一下 [確定] 。

     ![複寫的項目 VM 清單](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. 「更新行動服務」作業會針對每個選取的機器啟動。

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>透過 powershell 指令碼，在 Windows 伺服器上更新行動服務

請使用下列指令碼，以升級 power shell cmdlet 透過在伺服器上的行動服務

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>更新用於推入安裝行動服務的帳戶

在部署 Site Recovery 時，為了啟用行動服務推入安裝，您已指定在已為機器啟用複寫的情況下，Site Recovery 處理伺服器用來存取機器並安裝服務的帳戶。 如果您想要更新此帳戶的認證，請依照[這些指示](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)進行操作。

## <a name="uninstall-mobility-service"></a>行動服務解除安裝

### <a name="on-a-windows-machine"></a>在 Windows 機器上

從 UI 或命令提示字元解除安裝。

- **從 UI**：在機器的 [控制台] 中，選取 [程式集]。 選取 [Microsoft Azure Site Recovery 行動服務/主要目標伺服器] > [解除安裝]。
- **從命令提示字元**：以機器上的系統管理員身分開啟命令提示字元視窗。 執行以下命令： 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>在 Linux 機器上
1. 在 Linux 機器上，以 **root** 使用者身分登入。
2. 在終端機中，移至 /user/local/ASR。
3. 執行以下命令：
    ```
    uninstall.sh -Y

## Install Site Recovery VSS provider on source machine

Azure Site Recovery VSS provider is required on the source machine to generate application consistency points. If the installation of the provider didn't succeed through push installation, follow the below given guidelines to install it manually.

1. Open admin cmd window.
2. Navigate to the mobility service installation location. (Eg - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Run the script InMageVSSProvider_Uninstall.cmd . This will uninstall the service if it already exists.
4. Run the script InMageVSSProvider_Install.cmd to install the VSS provider manually.

## Next steps

- [Set up disaster recovery for VMware VMs](vmware-azure-tutorial.md)
- [Set up disaster recovery for physical servers](physical-azure-disaster-recovery.md)
