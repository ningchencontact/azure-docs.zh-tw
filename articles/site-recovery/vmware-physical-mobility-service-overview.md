---
title: 關於與 Azure Site Recovery 搭配使用來進行 VMware VM 和實體伺服器災害復原的行動服務 | Microsoft Docs
description: 了解與 Azure Site Recovery 服務搭配使用來進行將 VMware VM 和實體伺服器復原到 Azure 之災害復原的行動服務代理程式。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/04/2018
ms.author: ramamill
ms.openlocfilehash: efbbe048456c969f0045a0588bc5b64d1138ee15
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976956"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>關於適用於 VMware VM 和實體伺服器的行動服務

當您使用 [Azure Site Recovery](site-recovery-overview.md) 為 VMware VM 和實體伺服器設定災害復原時，會在每個內部部署 VMware VM 和實體伺服器上安裝 Site Recovery 行動服務。  行動服務會擷取機器上的資料寫入，然後將它們轉送給 Site Recovery 處理伺服器。 您可以使用下列方法來部署「行動服務」︰

[推入安裝](vmware-azure-install-mobility-service.md)：設定 Site Recovery 以執行行動服務推入安裝：若要這樣做，當您設定災害復原時，需一併設定可供 Site Recovery 處理伺服器用來存取 VM 或實體伺服器以安裝該服務的帳戶。
[手動安裝](vmware-physical-mobility-service-install-manual.md)：您可以使用 UI 或命令提示字元，在每部機器上手動安裝行動服務。
[自動化部署](vmware-azure-mobility-install-configuration-mgr.md)：您可以使用 System Center Configuration Manager 之類的軟體部署工具來自動執行安裝。

## <a name="azure-virtual-machine-agent"></a>Azure 虛擬機器代理程式

- **Windows VM**：從行動服務 9.7.0.0 版開始，會由行動服務安裝程式安裝 [Azure VM 代理程式](../virtual-machines/extensions/features-windows.md#azure-vm-agent)。 這可確保當機器容錯移轉至 Azure 時，Azure VM 會符合使用任何 VM 延伸模組的代理程式安裝先決條件。
- **Linux VM**：在容錯移轉之後，必須在 Azure VM 上手動安裝 [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent)。

## <a name="installer-files"></a>安裝程式檔案

下表摘要說明每個 VMware VM 和實體伺服器作業系統的安裝程式檔案。 您可以先檢閱[支援的作業系統](vmware-physical-azure-support-matrix.md#replicated-machines)，再開始進行操作。


**安裝程式檔案** | **作業系統 (僅限 64 位元)** 
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016；Windows Server 2012 R2；Windows Server 2012；Windows Server 2008 R2 SP1 
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.* 
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1、SP2、SP3 
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4、6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS 伺服器
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="anti-virus-on-replicated-machines"></a>在複寫的機器上防毒

如果您要複寫的機器正在執行作用中的防毒軟體，請務必從防毒作業中排除行動性服務安裝資料夾 (C:\ProgramData\ASR\agent)。 這可確保複寫運作正常。

## <a name="update-the-mobility-service"></a>更新行動服務

1. 在開始之前，請先確定已更新設定伺服器、向外延展處理伺服器，以及要一起部署的所有主要目標伺服器，然後才更新受保護機器上的行動服務。
2. 在入口網站中，開啟保存庫 > [複寫的項目]。
3. 如果設定伺服器已是最新版本，您會看到一則通知，指出「有新的 Site Recovery 複寫代理程式更新可用。 按一下以安裝」的通知。

     ![[複寫的項目] 視窗](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)

4. 按一下通知，然後在 [代理程式更新] 中，選取您想要升級行動服務的機器。 然後按一下 [確定] 。

     ![複寫的項目 VM 清單](.\media\vmware-azure-install-mobility-service\update-okpng.png)

5. 「更新行動服務」作業會針對每個選取的機器啟動。

## <a name="update-the-acount-used-for-push-installation-of-the-mobility-service"></a>更新用於行動服務推入安裝的帳戶

在部署 Site Recovery 時，為了啟用行動服務推入安裝，您已指定在已為機器啟用複寫的情況下，Site Recovery 處理伺服器用來存取機器並安裝服務的帳戶。 如果您想要更新此帳戶的認證，請依照[這些指示](vmware-azure-manage-configuration-server.md)進行操作。

## <a name="uninstall-the-mobility-service"></a>將行動服務解除安裝

### <a name="on-a-windows-machine"></a>在 Windows 機器上

從 UI 或命令提示字元解除安裝。

- **從 UI**：在機器的 [控制台] 中，選取 [程式集]。 選取 [Microsoft Azure Site Recovery 行動服務/主要目標伺服器] > [解除安裝]。
- **從命令提示字元**：以機器上的系統管理員身分開啟命令提示字元視窗。 執行以下命令： 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="on-a-linux-machine"></a>在 Linux 機器上
1. 在 Linux 機器上，以 **root** 使用者身分登入。
2. 在終端機中，移至 /user/local/ASR。
3. 執行以下命令：

    ```
    uninstall.sh -Y
    ```

## <a name="next-steps"></a>後續步驟

[設定行動服務的推入安裝](vmware-azure-install-mobility-service.md)。
