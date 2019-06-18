---
title: 什麼是 Azure 磁碟加密？
description: 這篇文章提供 Azure 磁碟加密的概觀
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 9b00a5262b1e144aa47cd7fd640906225ff4fecd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068797"
---
# <a name="azure-disk-encryption-overview"></a>Azure 磁碟加密概觀

Azure 磁碟加密協助保護與防衛您的資料，以符合您組織的安全性和合規性承諾。 它會使用[BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)的 Windows 功能，而[Dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) Linux 提供的 OS 和資料磁碟的 Azure 虛擬機器 (Vm) 的磁碟區加密的功能。 它也整合了[Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/)可協助您控制和管理磁碟加密金鑰和祕密，並確保 Azure 儲存體中靜止加密的 VM 磁碟上的所有資料。 Azure 磁碟加密的 Windows 和 Linux Vm 已正式運作，所有 Azure 公用區域和 Azure Government 地區的標準 Vm 和具有 Azure 進階儲存體的 Vm。 

如果您使用「Azure 資訊安全中心」，當您有未加密的 VM 時，就對收到警示。 這些警示會顯示為「高嚴重性」，而建議就是加密這些 VM。

![「Azure 資訊安全中心」磁碟加密警示](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> 某些建議可能會增加資料、網路或計算資源的使用量，而導致額外的授權或訂用帳戶成本。


## <a name="encryption-scenarios"></a>加密案例

使用 Azure 磁碟加密，您可以藉由保護您的 Azure Vm 在待用期間使用業界標準加密技術解決組織安全性與合規性需求。 也可以設定 Vm 開機在客戶控制的金鑰和原則管理 (BYOK)，並在金鑰保存庫中稽核這些金鑰的使用方式。

Azure 磁碟加密支援下列客戶案例：

* 啟用和停用從支援的 Azure 資源庫映像建立的新 Vm 的加密。
* 啟用和停用在 Azure 中執行的現有 Vm 上的加密。
* 啟用和停用新建立的預先加密的 VHD 和加密金鑰的 Windows Vm 上的加密。
* 啟用和停用 Windows 虛擬機器的標尺上的加密設定。
* 啟用和停用加密的資料磁碟機的 Linux 虛擬機器擴展集。
* 啟用和停用加密的受控磁碟 Vm。
* 正在更新現有已加密的進階與非進階儲存體 VM 的加密設定。
* 備份和還原已加密的 Vm。
* 攜帶您自己的加密 (BYOE)，並攜帶您自己金鑰 (BYOK) 案例中，客戶使用他們自己的加密金鑰並將它們儲存在 Azure key vault。

它也支援下列案例的 Vm 時已啟用 Microsoft Azure 中：

* 與 Azure Key Vault 整合。
* [標準層 Vm](https://azure.microsoft.com/pricing/details/virtual-machines/)。 這些層內的 [Linux VM](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) 必須符合 7GB 的最小記憶體需求。 
* 啟用加密 Windows 和 Linux Vm、 受控的磁碟，擴展集 Vm 從支援的 Azure 資源庫映像。
* 停用加密的 OS 和資料磁碟機，適用於 Windows Vm，擴展集 Vm，，並受控磁碟 Vm。
* 資料磁碟機上的停用加密，針對 Linux Vm，擴展集 Vm，和受控磁碟 Vm。
* 執行 Windows 用戶端 OS 的 Vm 上啟用加密。
* 在具有掛接路徑的磁碟區上啟用加密。
* 使用等量磁碟 (RAID 使用 mdadm) 設定 Linux Vm 上啟用加密。
* 針對資料磁碟使用 LVM 的 Linux Vm 上啟用加密。
* Linux VM 的 OS 和資料磁碟上啟用加密。

   > [!NOTE]
   > 不支援某些 Linux 發行版本的 OS 磁碟機加密。 如需詳細資訊，請參閱 [Azure 磁碟加密常見問題集](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)一文。
   
* 使用 Windows Server 2016 中的 Windows 儲存空間開頭設定的 Vm 上啟用加密。
* 備份及還原加密金鑰加密金鑰 (KEK) 和非 KEK 案例的 Vm。

Azure 磁碟加密不適用於下列案例、 功能和技術：

* 加密 VM 或透過傳統的 VM 建立方法建立的 Vm 的基本層。
* 在 OS 磁碟機或 Linux VM 的 OS 磁碟機加密時的資料磁碟機上停用加密。
* 加密的 Linux 虛擬機器擴展的 OS 磁碟機設定。
* 使用軟體型 RAID 系統設定的加密 Windows Vm。
* 在 Linux Vm 上有加密的自訂映像。
* 與內部部署金鑰管理系統整合。
* Azure 檔案 (共用檔案系統)。
* 網路檔案系統 (NFS)。
* 動態磁碟區。

## <a name="encryption-features"></a>加密功能

當您啟用並部署為 Azure Vm 的 Azure 磁碟加密時，您可以設定要啟用下列功能：

* 加密 OS 磁碟區，來保護您的儲存體中的待用開機磁碟區。
* 加密資料磁碟區，以保護您的儲存體中靜止的資料磁碟區。
* 適用於 Windows Vm 的 OS 和資料磁碟機上停用加密。
* 在資料上的加密磁碟機停用適用於 Linux Vm （只有在未加密的 OS 磁碟機）。
* 保護加密金鑰和 Azure 金鑰保存庫訂用帳戶中的祕密。
* 報告已加密的 VM 的加密狀態。
* 從 VM 移除磁碟加密組態設定。
* 備份與還原加密的 Vm 使用 Azure 備份服務。

適用於 Vm 的 Windows 和 Linux 的 azure 磁碟加密包括：

* [Windows 的磁碟加密擴充功能](../virtual-machines/extensions/azure-disk-enc-windows.md)。
* [適用於 Linux 的磁碟加密擴充](../virtual-machines/extensions/azure-disk-enc-linux.md)。
* [磁碟加密 powershell](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0)。
* [Azure CLI 的磁碟加密 cmdlet](/cli/azure/vm/encryption?view=azure-cli-latest)。
* [Azure Resource Manager 的磁碟加密範本](azure-security-disk-encryption-appendix.md#resource-manager-templates)。

在執行 Windows 或 Linux 作業系統的 Vm 上支援 azure 磁碟加密。 如需有關支援之作業系統的詳細資訊，請參閱 <<c0> [ 常見問題集](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)。

> [!NOTE]
> 使用「Azure 磁碟加密」來加密 VM 磁碟完全免費。 標準 [Key Vault 價格](https://azure.microsoft.com/pricing/details/key-vault/)適用於用來儲存加密金鑰的金鑰保存庫。 

## <a name="encryption-workflow"></a>加密工作流程

若要啟用 Windows 和 Linux VM 的磁碟加密，請執行下列步驟︰

1. 選擇透過「Azure 磁碟加密」Resource Manager 範本、PowerShell Cmdlet 或 Azure CLI 命令啟用磁碟加密，並指定加密設定。

   * 針對客戶加密的 VHD 案例，請將加密的 VHD 上傳至儲存體帳戶並將加密金鑰資料上傳至金鑰保存庫。 然後，提供加密組態以在新的 VM 上啟用加密。
   * 如需從支援的資源庫映像所建立的新 Vm 和現有的 Vm 已在 Azure 中執行，提供加密組態以在 VM 上啟用加密。

1. 授與存取至 Azure 的平台，以從您在 VM 上啟用加密的金鑰保存庫讀取加密的金鑰資料 （Windows 系統的 BitLocker 加密金鑰） 和適用於 Linux 的複雜密碼。

1. Azure 會使用加密和金鑰保存庫組態更新 VM 服務模型，並建立加密的 VM。

   ![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>解密工作流程
若要停用 Vm 的磁碟加密，請完成下列高階步驟：

1. 在 Azure 中執行中 VM 上選擇 停用加密 （解密），並指定解密組態。 您可以透過「Azure 磁碟加密」Resource Manager 範本、PowerShell Cmdlet 或 Azure CLI 停用加密。

   此步驟中，會停用 OS 或資料磁碟區或兩者都在執行 Windows VM 上的加密。 如前一節所述，不支援停用 Linux 的 OS 磁碟加密。 只要 OS 磁碟機未加密，就只允許對 Linux VM 上的資料磁碟機執行解密步驟。

1. Azure 更新 VM 服務模型，且該 VM 標示為進行解密。 VM 的待用內容不會再加密。

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

   * 針對客戶加密的 VHD 案例，請將加密的 VHD 上傳至儲存體帳戶並將加密金鑰資料上傳至金鑰保存庫。 然後，提供加密組態以在新的 VM 上啟用加密。
   * 如需從 Marketplace 建立的新 Vm 和已在 Azure 中執行的現有 Vm，提供加密組態以在 VM 上啟用加密。

1. 授與存取至 Azure 的平台，以從您在 VM 上啟用加密的金鑰保存庫讀取加密的金鑰資料 （Windows 系統的 BitLocker 加密金鑰） 和適用於 Linux 的複雜密碼。

1. 提供 Azure AD 應用程式身分識別，以將加密金鑰資料寫入至金鑰保存庫。 此步驟可讓您在步驟 2 中所述的案例的 VM 上的加密。

1. Azure 會使用加密和金鑰保存庫組態更新 VM 服務模型，並建立加密的 VM。


## <a name="terminology"></a>術語
下表定義一些使用 Azure 磁碟加密文件中的一般術語：

| 術語 | 定義 |
| --- | --- |
| Azure AD | [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) 帳戶可用來從金鑰保存庫驗證、儲存及擷取祕密。 |
| Azure 金鑰保存庫 | Key Vault 是一個密碼編譯金鑰管理服務，以「美國聯邦資訊處理標準」(FIPS) 已驗證的硬體安全性模組為基礎。 這些標準可協助您保護密碼編譯金鑰和敏感性祕密。 如需詳細資訊，請參閱 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文件。 |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx)是一種業界認可 Windows 磁碟區加密技術，用於 Windows Vm 上啟用磁碟加密。 |
| BEK | BitLocker 加密金鑰 (BEK) 可用來加密 OS 開機磁碟區和資料磁碟區。 BEK 會在金鑰保存庫中作為祕密受到保護。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) 已針對從命令列管理 Azure 資源進行最佳化。|
| DM-Crypt |[Dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt)是以 Linux 為基礎、 透明磁碟加密子系統，用來在 Linux Vm 上啟用磁碟加密。 |
| 金鑰加密金鑰 (KEK) | 非對稱金鑰 (RSA 2048)，您可以使用它來保護或包裝密碼。 您可以提供硬體安全性模組 (HSM) 保護的金鑰或軟體保護的金鑰。 如需詳細資訊，請參閱 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文件。 |
| PowerShell Cmdlet | 如需詳細資訊，請參閱 [Azure PowerShell Cmdlet](/powershell/azure/overview)。 |

## <a name="next-steps"></a>後續步驟

若要開始，請參閱[Azure 磁碟加密必要條件](azure-security-disk-encryption-prerequisites.md)。

