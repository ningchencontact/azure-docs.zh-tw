---
title: 疑難排解 - IaaS VM 適用的 Azure 磁碟加密 | Microsoft Docs
description: 本文提供的疑難排解秘訣適用於 Windows 及 Linux IaaS VM 適用的 Microsoft Azure 磁碟加密。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: 35d494702673d59290a0073c55135138f533b8bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956703"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure 磁碟加密疑難排解指南

本指南適用於組織採用 Azure 磁碟加密的 IT 專業人員、資訊安全性分析師和雲端系統管理員。 本文旨在協助磁碟加密相關問題的疑難排解。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="troubleshooting-linux-os-disk-encryption"></a>針對 Linux OS 磁碟加密進行疑難排解

Linux 作業系統 (OS) 磁碟加密必須先將 OS 磁碟機取消掛接後，才能透過完整磁碟加密流程加以執行。 如果無法取消掛接磁碟機，則可能出現「後續無法取消掛接...」錯誤訊息。

在已從支援的庫存資源庫映像變更的目標 VM 環境中嘗試 OS 磁碟加密時，可能發生此錯誤。 來自受支援映像的偏差可能會干擾 OS 磁碟機取消掛接擴充功能。 偏差的範例可能包括下列項目：
- 不再符合支援的檔案系統或資料分割配置的自訂映像。
- 在加密之前已於 OS 中安裝且執行諸如 SAP、MongoDB 或 Apache Cassandra 與 Docker 等大型應用程式時，則不支援這些應用程式。 在依要求準備 OS 磁碟機進行磁碟加密時，Azure 磁碟加密無法安全地關閉這些處理序。 如果仍然有作用中的處理序持有 OS 磁碟機的開啟檔案控制代碼，則 OS 磁碟機無法取消掛接，因而造成無法加密 OS 磁碟機。 
- 自訂指令碼與要啟用的加密在相近時間下執行，或在加密流程期間對 VM 進行任何其他變更。 當 Azure Resource Manager 範本定義要同時執行的多個擴充功能，或當自訂指令碼擴充功能或其他動作同時對磁碟加密執行時，會發生此衝突。 將這些步驟序列化和隔離，就可解決此問題。
- 啟用加密之前尚未停用安全性增強 Linux (SELinux) 時，取消掛接步驟就會失敗。 完成加密後，即可重新啟用 SELinux。
- OS 磁碟會使用邏輯磁碟區管理員 (LVM) 配置。 雖然可以使用有限的 LVM 資料磁碟支援，但是 LVM OS 磁碟並沒有。
- 未符合最小記憶體需求 (OS 磁碟加密建議為 7 GB)。
- 資料磁碟機已遞迴掛接於 /mnt/ directory 下，或彼此掛接 (例如，/mnt/data1、/mnt/data2、/data3 + /data3/data4)。
- 未符合其他 Linux 適用的 Azure 磁碟加密[必要條件](azure-security-disk-encryption-prerequisites.md)。

## <a name="bkmk_Ubuntu14"></a> 更新 Ubuntu 14.04 LTS 的預設核心

預設核心版本 4.4 隨附 Ubuntu 14.04 LTS 映像。 此核心版本有已知問題，其中的 Out of Memory Killer 在 OS 加密程序期間不當地終止 dd 命令。 最近的 Azure 微調 Linux 核心已修正這個錯誤 (bug)。 若要避免這個錯誤，在啟用映像加密之前，請使用下列命令，更新為 [Azure 微調核心 4.15](https://packages.ubuntu.com/trusty/linux-azure)或更新版本：

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

在 VM 重新啟動到新的核心之後，可以使用下列命令來確認新的核心版本：

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>更新 Azure 虛擬機器代理程式和擴充功能版本

Azure 磁碟加密作業使用不受支援的版本的 Azure 虛擬機器代理程式的虛擬機器映像上可能會失敗。 啟用加密之前您應該更新與代理程式版本早於 2.2.38 的 Linux 映像。 如需詳細資訊，請參閱 <<c0> [ 如何更新 Azure Linux 代理程式，在 VM 上](../virtual-machines/extensions/update-linux-agent.md)並[在 Azure 中的虛擬機器代理程式的最小版本支援](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)。

也需要 Microsoft.Azure.Security.AzureDiskEncryption 或 Microsoft.Azure.Security.AzureDiskEncryptionForLinux 的客體代理程式擴充功能的正確版本。 可維護及平台符合 Azure 虛擬機器代理程式的必要條件，並可支援的虛擬機器代理程式版本時自動更新延伸模組版本。

Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux 延伸模組已被取代，不再支援。  

## <a name="unable-to-encrypt-linux-disks"></a>無法將 Linux 磁碟加密

在某些情況下，Linux 磁碟加密似乎會卡在「已啟動的 OS 磁碟加密」且停用 SSH。 在庫存資源庫映像上，此加密程序可能需要 3-16 個小時才能完成。 如果新增多 TB 大小的資料磁碟，此程序可能需要數天的時間。

Linux OS 磁碟加密順序會暫時取消掛接 OS 磁碟機。 然後會對整個 OS 磁碟執行逐區塊加密，再於它處於已加密狀態時重新掛接。 不同於 Windows 上的 Azure 磁碟加密，Linux 磁碟加密不允許加密進行時，同時使用 VM。 VM 的效能特性在完成加密所需的時間上有顯著差異。 這些特性包括磁碟的大小以及儲存體帳戶是標準或進階 (SSD) 儲存體。

若要檢查加密狀態，輪詢**ProgressMessage**從傳回的欄位[Get AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus)命令。 加密 OS 磁碟機時，VM 會進入服務狀態且會停用 SSH，從而避免中斷進行中的流程。 **EncryptionInProgress** 訊息報告加密正在進行時的大部分時間。 數個小時之後，**VMRestartPending** 訊息會提示您重新啟動 VM。 例如:


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

系統提示您重新啟動 VM，且 VM 重新啟動之後，您必須等待 2-3 分鐘讓重新啟動和最後步驟在目標上執行。 加密最終完成時，狀態訊息會變更。 可以使用此訊息之後，加密的 OS 磁碟機應該會就緒可供使用，且 VM 也就緒可再次使用。

在下列情況中，我們建議您將 VM 還原至加密前進行的快照集或備份：
   - 如果先前所述的重新啟動順序未發生。
   - 如果開機資訊、進度訊息或其他錯誤指標報告 OS 加密在此處理序中間失敗。 訊息的範本是本指南中所述的「無法取消掛接」錯誤。

在下一次嘗試之前，請重新評估 VM 的特性，並確定所有必要條件都已滿足。

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>針對防火牆後方的 Azure 磁碟加密進行疑難排解
當連線能力受防火牆、Proxy 需求或網路安全性群組 (NSG) 設定限制時，擴充功能執行所需工作的能力可能會受到中斷。 這個中斷會產生狀態訊息 (例如「無法在 VM 上使用擴充功能狀態」)。 在預期的情節中，無法完成加密。 以下各節有一些常見的防火牆問題，您可能需要進行調查。

### <a name="network-security-groups"></a>網路安全性群組
任何套用的網路安全性群組設定仍然必須允許端點，從而符合磁碟加密的記載網路設定[必要條件](azure-security-disk-encryption-prerequisites.md#bkmk_GPO)。

### <a name="azure-key-vault-behind-a-firewall"></a>防火牆後方的 Azure Key Vault

使用 [Azure AD 認證](azure-security-disk-encryption-prerequisites-aad.md)啟用加密時，目標 VM 必須允許連線到 Azure Active Directory 端點和金鑰保存庫端點。 目前的 Azure Active Directory 驗證端點列在 [Office 365 URL 與 IP 位址範圍](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges)文件的第 56 和 59 節。 如需金鑰保存庫的指示，請參閱文件中的如何[在防火牆後存取 Azure 金鑰保存庫](../key-vault/key-vault-access-behind-firewall.md)。

### <a name="azure-instance-metadata-service"></a>Azure 執行個體中繼資料服務 
VM 必須能夠存取 [Azure 執行個體中繼資料服務](../virtual-machines/windows/instance-metadata-service.md)端點；此端點會使用只能從 VM 內存取且無法路由的已知 IP 位址 (`169.254.169.254`)。  不支援 alter 到這個地址 （例如，新增 X 轉送的標頭） 的本機 HTTP 流量的 proxy 組態。

### <a name="linux-package-management-behind-a-firewall"></a>防火牆後方的 Linux 套件管理

在執行階段，適用於 Linux 的 Azure 磁碟加密需依賴目標發佈的套件管理系統先行安裝所需的必要條件元件後，才能啟用加密。 如果防火牆設定造成 VM 無法下載及安裝這些元件，就會發生後續的失敗。 設定此套件管理系統的步驟會因散發套件而異。 在 Red Hat 上，在需要 Proxy 時，您必須確保已正確地設定您的訂用帳戶管理員和 Yum。 如需詳細資訊，請參閱[如何針對訂用帳戶管理員和 Yum 問題進行疑難排解](https://access.redhat.com/solutions/189533)。  


## <a name="troubleshooting-windows-server-2016-server-core"></a>針對 Windows Server 2016 Server Core 進行疑難排解

在 Windows Server 2016 Server Core 上，預設無法使用 bdehdcfg 元件。 Azure 磁碟加密需要此元件。 此元件用來從作業系統磁碟區中分割系統磁碟區，在 VM 的存留時間內僅會進行一次。 在之後的加密作業期間，不需要這些二進位檔。

若要解決這個問題，請將下列 4 個檔案從 Windows Server 2016 資料中心 VM 複製到 Server Core 的同一個位置：

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. 輸入下列命令：

   ```
   bdehdcfg.exe -target default
   ```

1. 此命令會建立一個 550MB 系統磁碟分割。 重新啟動系統。

1. 使用 DiskPart 來檢查磁碟區，然後再繼續作業。  

例如:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
<!-- ## Troubleshooting encryption status

If the expected encryption state does not match what is being reported in the portal, see the following support article:
[Encryption status is displayed incorrectly on the Azure Management Portal](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por) --> 

## <a name="troubleshooting-encryption-status"></a>針對加密狀態進行疑難排解 

在入口網站可能會顯示磁碟加密即使已在 VM 內未加密。  原因有可能是因為低層級的命令可用來直接解密從磁碟中的 VM，而不是使用較高的層級的 Azure 磁碟加密管理命令。  較高的層級的命令解密磁碟的 VM 內，不僅 VM 之外，他們也更新重要的平台層級加密設定並與 VM 相關聯的延伸模組設定。  如果這些不會保持一致，平台將無法報告加密狀態，或正確佈建 VM。   

若要停用使用 PowerShell 的 Azure 磁碟加密，請使用[停用 AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption)後面[移除 AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension)。 執行移除 AzVMDiskEncryptionExtension 之前已停用加密將會失敗。

若要停用 Azure 磁碟加密，使用 CLI，請使用[az vm 加密停用](/cli/azure/vm/encryption)。 

## <a name="next-steps"></a>後續步驟

在本文件中，您已深入了解 Azure 磁碟加密中的一些常見問題，以及如何進行疑難排解。 如需此服務和其功能的相關資訊，請參閱下列文章：

- [在 Azure 資訊安全中心套用磁碟加密](../security-center/security-center-apply-disk-encryption.md)
- [待用 Azure 資料加密](azure-security-encryption-atrest.md)
