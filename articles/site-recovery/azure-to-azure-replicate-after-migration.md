---
title: 使用 Azure Site Recovery 移轉至 Azure 之後，設定 Azure VM 的災害復原 | Microsoft Docs
description: 本文說明如何準備機器，以在使用 Azure Site Recovery 移轉至 Azure 之後，設定 Azure 區域之間的災害復原。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 4b764c8e7c3d97ff521add05033265f705c4136f
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399527"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>移轉至 Azure 之後，設定 Azure VM 的災害復原 


如果您已經遵循這篇文章[內部部署機器移轉至 Azure Vm](tutorial-migrate-on-premises-to-azure.md)使用[Site Recovery](site-recovery-overview.md)服務，且您現在想要取得 Vm 設定災害復原至次要 Azure 區域。 本文將告訴您如何確保 Azure VM 代理程式安裝在已移轉的 Vm，以及如何移除已不再需要在移轉後的 Site Recovery 行動服務。



## <a name="verify-migration"></a>驗證移轉

設定災害復原之前，請確定移轉已如預期般完成。 若要成功完成移轉，您就要在容錯移轉之後，針對您需要移轉的每部機器，選取 [完成移轉]  選項。 

## <a name="verify-the-azure-vm-agent"></a>確認 Azure VM 代理程式

每個 Azure VM 必須有[Azure VM 代理程式](../virtual-machines/extensions/agent-windows.md)安裝。 若要將 Azure Vm 複寫，Site Recovery 會在代理程式上安裝擴充功能。

- 如果電腦執行 9.7.0.0 版或更新版本的 Site Recovery 行動服務，Azure VM 代理程式會自動安裝的 Windows Vm 上的行動服務。 在舊版的行動服務中，您需要自動安裝代理程式。
- 針對 Linux Vm，您必須手動安裝 Azure VM 代理程式。您只需要安裝 Azure VM 代理程式，如果在移轉後的機器上安裝行動服務是 9.6 或更早版本。


### <a name="install-the-agent-on-windows-vms"></a>在 Windows Vm 上安裝代理程式

如果您執行早於 9.7.0.0 開始，Site Recovery 行動服務的版本，或您有手動安裝代理程式的其他需求，執行下列作業：  

1. 確定您在 VM 上具有系統管理員權限。
2. 下載[VM 代理程式安裝程式](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
3. 執行安裝程式檔案。

#### <a name="validate-the-installation"></a>驗證安裝
若要檢查已安裝的代理程式：

1. 在 Azure VM 的 C:\WindowsAzure\Packages 資料夾中，您應該會看到 WaAppAgent.exe 檔案。
2. 在該檔案上按一下滑鼠右鍵，並在 [屬性]  中選取 [詳細資料]  索引標籤。
3. 確認 [產品版本]  欄位顯示 2.6.1198.718 或更高版本。

[了解更多](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)關於 Windows 的代理程式安裝。

### <a name="install-the-agent-on-linux-vms"></a>在 Linux Vm 上安裝代理程式

安裝[Azure Linux VM](../virtual-machines/extensions/agent-linux.md)代理程式，以手動方式，如下所示：

1. 請確定您已在電腦上的系統管理員權限。
2. 我們強烈建議您安裝 Linux VM 代理程式從您的散發套件存放庫使用 RPM 或 DEB 封裝。 所有[認可的散發套件提供者](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)都會將 Azure Linux 代理程式套件整合於本身的映像和儲存機制中。
    - 我們強烈建議您更新代理程式只透過發佈存放庫。
    - 我們不建議直接從 GitHub 安裝 Linux VM 代理程式，並加以更新。
    -  如果最新的代理程式不適用於您的散發套件，請連絡散發套件支援以取得如何進行安裝的指示。 

#### <a name="validate-the-installation"></a>驗證安裝 

1. 執行此命令： **ps-e**確定 Azure 代理程式正在執行 Linux VM 上。
2. 如果此程序不在執行中，請使用下列命令來重新啟動它：
    - 針對 Ubuntu:**服務 walinuxagent 開始**
    - 針對其他散發套件： **service waagent start**


## <a name="uninstall-the-mobility-service"></a>將行動服務解除安裝

1. 從 Azure VM，使用下列方法之一，手動解除安裝行動服務。 
    - 對於 Windows，請在 [控制台] > [新增/移除程式]  中，將 **Microsoft Azure Site Recovery 行動服務/主要目標伺服器**解除安裝。 在提升權限的命令提示字元上，執行：
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - 若為 Linux，請以根使用者身分登入。 在終端機中，移至 **/user/local/ASR**，然後執行下列命令：
        ```
        ./uninstall.sh -Y
        ```
2. 設定複寫之前，請將 VM 重新啟動。

## <a name="next-steps"></a>後續步驟

[檢閱疑難排解](site-recovery-extension-troubleshoot.md)Site Recovery 延伸模組，在 Azure VM 代理程式上。
將 Azure VM [快速複寫](azure-to-azure-quickstart.md)到次要地區。
