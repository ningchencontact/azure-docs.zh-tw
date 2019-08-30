---
title: 什麼是 Azure 磁碟加密？
description: 本文提供 Azure 磁碟加密的總覽
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 07/29/2019
ms.custom: seodec18
ms.openlocfilehash: 1dfcc69d90daae5869c3b69c922e99eab3585e14
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164627"
---
# <a name="azure-disk-encryption-overview"></a>Azure 磁碟加密總覽

Azure 磁碟加密有助於保護和保護您的資料, 以符合組織的安全性和合規性承諾。 它會使用 Windows 的[BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)功能和 Linux 的[DM Crypt](https://en.wikipedia.org/wiki/Dm-crypt)功能, 為 Azure 虛擬機器 (vm) 的 OS 和資料磁片提供磁片區加密。 它也會與[Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/)整合, 協助您控制及管理磁片加密金鑰和密碼, 並確保 VM 磁片上的所有資料在 Azure 儲存體中都已加密。 適用于 Windows 和 Linux Vm 的 Azure 磁碟加密已在所有 Azure 公用區域中正式運作, 而適用于標準 Vm 和 Vm 的 Azure Government 區域則具備 Azure 進階儲存體。 

如果您使用「Azure 資訊安全中心」，當您有未加密的 VM 時，就對收到警示。 這些警示會顯示為「高嚴重性」，而建議就是加密這些 VM。

![「Azure 資訊安全中心」磁碟加密警示](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> 某些建議可能會增加資料、網路或計算資源的使用量，而導致額外的授權或訂用帳戶成本。


## <a name="encryption-scenarios"></a>加密案例

透過 Azure 磁碟加密, 您可以使用業界標準的加密技術來保護待用的 Azure Vm, 以解決組織的安全性與合規性需求。 您也可以將 Vm 設定為在客戶控制的金鑰和原則 (BYOK) 下開機, 並在金鑰保存庫中審核這些金鑰的使用方式。

Azure 磁碟加密支援下列客戶案例:

* 在從支援的 Azure 資源庫映射建立的新 Vm 上啟用和停用加密。
* 在 Azure 中執行的現有 Vm 上啟用和停用加密。
* 在從預先加密的 VHD 和加密金鑰建立的新 Windows Vm 上啟用和停用加密。
* 在 Windows 虛擬機器擴展集上啟用和停用加密。
* 在 Linux 虛擬機器擴展集的資料磁片磁碟機上啟用和停用加密。
* 啟用和停用受控磁片 Vm 的加密。
* 正在更新現有加密 Premium 和非進階儲存體 VM 的加密設定。
* 備份和還原已加密的 Vm。
* 攜帶您自己的加密 (BYOE) 和攜帶您自己的金鑰 (BYOK) 案例, 其中客戶會使用自己的加密金鑰, 並將其儲存在 Azure 金鑰保存庫中。

當 Vm 在 Microsoft Azure 中啟用時, 它也支援下列案例:

* 與 Azure Key Vault 整合。
* 符合[最低記憶體需求](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes)的[標準層 vm](https://azure.microsoft.com/pricing/details/virtual-machines/) 。 
* 從支援的 Azure 資源庫映射在 Windows 和 Linux Vm、受控磁片和擴展集 Vm 上啟用加密。
* 在 Windows Vm、擴展集 Vm 及受控磁片 Vm 的 OS 和資料磁片磁碟機上停用加密。
* 在 Linux Vm、擴展集 Vm 及受控磁片 Vm 的資料磁片磁碟機上停用加密。
* 在執行 Windows 用戶端作業系統的 Vm 上啟用加密。
* 在具有掛接路徑的磁片區上啟用加密。
* 使用 mdadm 在設定了磁片等量 (RAID) 的 Linux Vm 上啟用加密。
* 在使用 LVM 做為資料磁片的 Linux Vm 上啟用加密。
* 在 Linux VM 作業系統和資料磁片上啟用加密。

   > [!NOTE]
   > 不支援某些 Linux 發行版本的 OS 磁碟機加密。 如需詳細資訊, 請[參閱 Azure 磁碟加密支援的作業系統:Linux](azure-security-disk-encryption-prerequisites.md#linux)。
   
* 在從 Windows Server 2016 開始設定 Windows 儲存空間的 Vm 上啟用加密。 尚不支援儲存空間直接存取 (S2D)。
* 針對金鑰加密金鑰 (KEK) 和非 KEK 案例, 備份和還原已加密的 Vm。

Azure 磁碟加密不適用於下列案例、功能和技術:

* 將基本層 VM 或透過傳統 VM 建立方法建立的 Vm 加密。
* 當 OS 磁片磁碟機加密時, 在 OS 磁片磁碟機或 Linux VM 的資料磁片磁碟機上停用加密。
* 將 Linux 虛擬機器擴展集的 OS 磁片磁碟機加密。
* 加密以軟體為基礎的 RAID 系統設定的 Windows Vm。
* 加密 Linux Vm 上的自訂映射。
* 與內部部署金鑰管理系統整合。
* Azure 檔案 (共用檔案系統)。
* 網路檔案系統 (NFS)。
* 動態磁碟區。
* 暫時的 OS 磁片。
* 共用/分散式檔案系統的加密, 例如 (但不限於):DFS、GFS、DRDB、CephFS 等

## <a name="encryption-features"></a>加密功能

當您啟用並部署 Azure Vm 的 Azure 磁碟加密時, 您可以設定要啟用下列功能:

* 加密 OS 磁片區以保護儲存體中的待用開機磁碟區。
* 加密資料磁片區以保護儲存體中的待用資料磁片區。
* 在 Windows Vm 的 OS 和資料磁片磁碟機上停用加密。
* 在 Linux Vm 的資料磁片磁碟機上停用加密 (僅限於 OS 磁片磁碟機未加密時)。
* 保護 Azure Key Vault 訂用帳戶中的加密金鑰和密碼。
* 報告已加密 VM 的加密狀態。
* 正在從 VM 移除磁片加密設定。
* 使用 Azure 備份服務來備份和還原已加密的 Vm。

適用于 Windows 和 Linux 的 Vm Azure 磁碟加密包括:

* [適用于 Windows 的磁片加密延伸](../virtual-machines/extensions/azure-disk-enc-windows.md)模組。
* [適用于 Linux 的磁片加密延伸](../virtual-machines/extensions/azure-disk-enc-linux.md)模組。
* [PowerShell 磁片加密 Cmdlet](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0)。
* [Azure CLI 磁片加密 Cmdlet](/cli/azure/vm/encryption?view=azure-cli-latest)。
* [Azure Resource Manager 磁片加密範本](azure-security-disk-encryption-appendix.md#resource-manager-templates)。

> [!NOTE]
> 使用「Azure 磁碟加密」來加密 VM 磁碟完全免費。 標準 [Key Vault 價格](https://azure.microsoft.com/pricing/details/key-vault/)適用於用來儲存加密金鑰的金鑰保存庫。 

## <a name="encryption-workflow"></a>加密工作流程

若要啟用 Windows 和 Linux VM 的磁碟加密，請執行下列步驟︰

1. 選擇透過「Azure 磁碟加密」Resource Manager 範本、PowerShell Cmdlet 或 Azure CLI 命令啟用磁碟加密，並指定加密設定。

   * 針對客戶加密的 VHD 案例，請將加密的 VHD 上傳至儲存體帳戶並將加密金鑰資料上傳至金鑰保存庫。 然後, 提供加密設定以在新的 VM 上啟用加密。
   * 針對從支援的資源庫映射建立的新 Vm, 以及已在 Azure 中執行的現有 Vm, 提供加密設定以在 VM 上啟用加密。

1. 授與 Azure 平臺的存取權, 以從您的金鑰保存庫讀取加密金鑰資料 (適用于 Windows 系統的 BitLocker 加密金鑰和 Linux 的複雜密碼), 以在 VM 上啟用加密。

1. Azure 會使用加密和金鑰保存庫組態更新 VM 服務模型，並建立加密的 VM。

   ![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>解密工作流程
若要停用 Vm 的磁片加密, 請完成下列高階步驟:

1. 選擇在 Azure 中執行中的 VM 上停用加密 (解密), 並指定解密設定。 您可以透過「Azure 磁碟加密」Resource Manager 範本、PowerShell Cmdlet 或 Azure CLI 停用加密。

   此步驟會在執行中的 Windows VM 上停用 OS 或資料磁片區的加密。 如前一節所述，不支援停用 Linux 的 OS 磁碟加密。 只要 OS 磁碟機未加密，就只允許對 Linux VM 上的資料磁碟機執行解密步驟。

1. Azure 會更新 VM 服務模型, 而 VM 會標示為已解密。 VM 的待用內容不會再加密。

   > [!NOTE]
   > 停用加密作業並不會刪除您的金鑰保存庫和加密金鑰資料 (適用於 Windows 系統的 BitLocker 加密金鑰和適用於 Linux 的複雜密碼)。
   >
   > 不支援停用 Linux 的 OS 磁碟加密。 只允許對 Linux VM 上的資料磁碟機執行解密步驟。
   >
   > 在已加密 OS 磁碟機的情況下，不支援停用 Linux 的資料磁碟加密。


## <a name="encryption-workflow-previous-release"></a>加密工作流程 (舊版)

新版「Azure 磁碟加密」不需要提供 Azure Active Directory (Azure AD) 應用程式參數，即可啟用 VM 磁碟加密。 藉由新版本，您不再需要在啟用加密步驟期間提供 Azure AD 認證。 使用新版本時，必須在不使用 Azure AD 應用程式參數的情況下加密所有新 VM。 已經使用 Azure AD 應用程式參數進行加密的 VM 仍然受支援，且應該繼續使用 Azure AD 語法進行維護。 若要啟用 Windows 和 Linux VM (舊版) 的磁碟加密，請執行下列步驟︰

1. 從[加密案例](#encryption-scenarios)一節所列的案例中選擇一個加密案例。

1. 選擇透過「Azure 磁碟加密」Resource Manager 範本、PowerShell Cmdlet 或 Azure CLI 命令啟用磁碟加密，並指定加密設定。

   * 針對客戶加密的 VHD 案例，請將加密的 VHD 上傳至儲存體帳戶並將加密金鑰資料上傳至金鑰保存庫。 然後, 提供加密設定以在新的 VM 上啟用加密。
   * 針對從 Marketplace 建立的新 Vm 和已在 Azure 中執行的現有 Vm, 提供加密設定以在 VM 上啟用加密。

1. 授與 Azure 平臺的存取權, 以從您的金鑰保存庫讀取加密金鑰資料 (適用于 Windows 系統的 BitLocker 加密金鑰和 Linux 的複雜密碼), 以在 VM 上啟用加密。

1. 提供 Azure AD 應用程式身分識別，以將加密金鑰資料寫入至金鑰保存庫。 此步驟會針對步驟2中所述的案例, 在 VM 上啟用加密。

1. Azure 會使用加密和金鑰保存庫組態更新 VM 服務模型，並建立加密的 VM。


## <a name="terminology"></a>術語
下表定義 Azure 磁片加密檔中所使用的一些常見詞彙:

| 術語 | 定義 |
| --- | --- |
| Azure AD | [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) 帳戶可用來從金鑰保存庫驗證、儲存及擷取祕密。 |
| Azure Key Vault | Key Vault 是一個密碼編譯金鑰管理服務，以「美國聯邦資訊處理標準」(FIPS) 已驗證的硬體安全性模組為基礎。 這些標準可協助您保護密碼編譯金鑰和敏感性祕密。 如需詳細資訊，請參閱 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文件。 |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx)是業界認可的 windows 磁片區加密技術, 可用來在 Windows vm 上啟用磁片加密。 |
| BEK | BitLocker 加密金鑰 (BEK) 可用來加密 OS 開機磁碟區和資料磁碟區。 BEK 會在金鑰保存庫中作為祕密受到保護。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) 已針對從命令列管理 Azure 資源進行最佳化。|
| DM-Crypt |[DM Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt)是以 linux 為基礎的透明磁片加密子系統, 用來在 linux vm 上啟用磁片加密。 |
| 金鑰加密金鑰 (KEK) | 您可以用來保護或包裝秘密的非對稱金鑰 (RSA 2048)。 您可以提供硬體安全性模組 (HSM) 保護的金鑰或軟體保護的金鑰。 如需詳細資訊，請參閱 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文件。 |
| PowerShell Cmdlet | 如需詳細資訊，請參閱 [Azure PowerShell Cmdlet](/powershell/azure/overview)。 |

## <a name="next-steps"></a>後續步驟

若要開始使用, 請參閱[Azure 磁碟加密必要條件](azure-security-disk-encryption-prerequisites.md)。

