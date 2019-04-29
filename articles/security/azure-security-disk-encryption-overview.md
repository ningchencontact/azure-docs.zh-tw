---
title: 概觀 - IaaS VM 適用的 Azure 磁碟加密 | Microsoft Docs
description: 本文提供 IaaS VM 適用的 Microsoft Azure 磁碟加密概觀。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/16/2019
ms.custom: seodec18
ms.openlocfilehash: 66d788aec83e3e57a49b063f2ca80484360f639d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60612071"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>IaaS VM 適用的 Azure 磁碟加密

Microsoft Azure 致力於確保您的資料隱私權和資料主權。 Azure 可讓您透過一系列進階技術來加密、控制和管理加密金鑰，以及稽核資料存取，以控制裝載於 Azure 的資料。 此控制可讓 Azure 客戶靈活選擇最符合其業務需求的解決方案。 本文為您介紹技術解決方案：「適用於 Windows 和 Linux IaaS 虛擬機器 (VM) 的 Azure 磁碟加密。」 此技術可協助您保護資料安全，以符合組織安全性和合規性承諾。 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>概觀

「Azure 磁碟加密」是一項可協助您將 Windows 和 Linux IaaS VM 磁碟加密的功能。 「磁碟加密」利用 Windows 的業界標準 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 功能和 Linux 的 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能，為 OS 和資料磁碟提供磁碟區加密。 此解決方案與 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) 整合，協助您控制及管理磁碟加密金鑰和祕密。 此解決方案也可確保 VM 磁碟上的所有待用資料都會在您的 Azure 儲存體中加密。

「適用於 Windows 和 Linux IaaS VM 的磁碟加密」已在所有 Azure 公用區域及 Azure Government 區域正式推出，可供「標準 VM」和「具有 Azure 進階儲存體的 VM」使用。 當您套用「磁碟加密」管理解決方案時，可以滿足下列業務需求：

* 待用 IaaS VM 會受到業界標準的加密技術保護，以因應組織的安全性和合規性需求。
* IaaS VM 會在客戶控制的金鑰和原則下開機。 您可以在金鑰保存庫中稽核其使用狀況。

如果您使用「Azure 資訊安全中心」，當您有未加密的 VM 時，就對收到警示。 這些警示會顯示為「高嚴重性」，而建議就是加密這些 VM。

![「Azure 資訊安全中心」磁碟加密警示](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> 某些建議可能會增加資料、網路或計算資源的使用量，而導致額外的授權或訂用帳戶成本。


## <a name="encryption-scenarios"></a>加密案例

「磁碟加密」解決方案支援下列客戶案例：

* 在從預先加密之 VHD 和加密金鑰建立的新 Windows IaaS VM 上啟用加密。
* 在從受支援「Azure 資源庫」映像建立的新 IaaS VM 上啟用加密。
* 在於 Azure 中執行的現有 IaaS VM 上啟用加密。
* 在 Windows 虛擬機器擴展集上啟用加密。
* 在 Linux 虛擬機器擴展集的資料磁碟機上啟用加密。
* 在 Windows IaaS VM 上停用加密。
* 在 Linux IaaS VM 的資料磁碟機上停用加密。
* 在 Windows 虛擬機器擴展集上停用加密。
* 在 Linux 虛擬機器擴展集的資料磁碟機上停用加密。
* 允許加密受控磁碟 VM。
* 更新現有已加密進階與非進階儲存體 VM 的加密設定。
* 備份和還原已加密的 VM。

在 Microsoft Azure 中啟用時，解決方案會對 IaaS VM 支援下列案例：

* 與 Azure Key Vault 整合。
* 標準層 VM：[A、D、DS、G、GS、F 等系列的 IaaS VM](https://azure.microsoft.com/pricing/details/virtual-machines/)。 這些層內的 [Linux VM](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) 必須符合 7GB 的最小記憶體需求。
* 在來自受支援「Azure 資源庫」映像的 Windows 和 Linux IaaS VM、受控磁碟及擴展集 VM 上啟用加密。
* 在 Windows IaaS VM、擴展集 VM 及受控磁碟 VM 的 OS 和資料磁碟機上停用加密。
* 在 Linux IaaS VM、擴展集 VM 及受控磁碟 VM 的資料磁碟機上停用加密。
* 在執行 Windows Client OS 的 IaaS VM 上啟用加密。
* 在具有掛接路徑的磁碟區上啟用加密。
* 在使用 mdadm 設定了磁碟等量 (RAID) 的 Linux VM 上啟用加密。
* 在針對資料磁碟使用 LVM 的 Linux VM 上啟用加密。
* 在 Linux VM OS 和資料磁碟上啟用加密。

   > [!NOTE]
   > 不支援某些 Linux 發行版本的 OS 磁碟機加密。 如需詳細資訊，請參閱 [Azure 磁碟加密常見問題集](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)一文。
   
* 使用 Windows Server 2016 中的 Windows 儲存空間開頭設定的 Vm 上啟用加密。
* 更新現有已加密進階與非進階儲存體 VM 的加密設定。
* 針對金鑰加密金鑰 (KEK) 與非 KEK 案例備份和還原已加密的 VM。
* 支援所有 Azure 公用區域與 Azure Government 區域。

此解決方案不支援下列案例、功能和技術：

* 基本層 IaaS VM。
* 在 Linux IaaS VM 的 OS 磁碟機上停用加密。
* 在已加密 Linux IaaS VM 之 OS 磁碟機的情況下，於資料磁碟機上停用加密。
* Linux 虛擬機器擴展的 OS 磁碟機加密設定。
* 使用傳統 VM 建立方法來建立的 IaaS VM。
* 在 Linux IaaS VM 上啟用客戶自訂映像加密。
* 與您的內部部署金鑰管理系統整合。
* Azure 檔案 (共用檔案系統)。
* 網路檔案系統 (NFS)。
* 動態磁碟區。
* 已設定軟體型 RAID 系統的 Windows VM。

## <a name="encryption-features"></a>加密功能

為 Azure IaaS VM 啟用並部署「磁碟加密」時，會視所提供的設定而定，啟用下列功能：

* 加密 OS 磁碟區以保護儲存體中的待用開機磁碟區。
* 加密資料磁碟區以保護儲存體中的待用資料磁碟區。
* 在 Windows IaaS VM 的 OS 和資料磁碟機上停用加密。
* 在 Linux IaaS VM 的資料磁碟機上停用加密 (僅限在 OS 磁碟機未加密的情況下)。
* 保護 Azure Key Vault 訂用帳戶中的加密金鑰和祕密。
* 回報已加密 IaaS VM 的加密狀態。
* 從 IaaS VM 移除磁碟加密組態設定。
* 使用 Azure 備份服務來備份和還原已加密的 VM。

Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密解決方案包含：

* Windows 適用的磁碟加密擴充功能。
* Linux 適用的磁碟加密擴充功能。
* PowerShell 磁碟加密 Cmdlet。
* Azure CLI 磁碟加密 Cmdlet。
* Azure Resource Manager 磁碟加密範本。

在執行 Windows 或 Linux OS 的 IaaS VM 上支援「Azure 磁碟加密」解決方案。 如需支援的作業系統相關詳細資訊，請參閱[必要條件](azure-security-disk-encryption-prerequisites.md)一文。

> [!NOTE]
> 使用「Azure 磁碟加密」來加密 VM 磁碟完全免費。 標準 [Key Vault 價格](https://azure.microsoft.com/pricing/details/key-vault/)適用於用來儲存加密金鑰的金鑰保存庫。 


## <a name="encryption-workflow"></a>加密工作流程

若要啟用 Windows 和 Linux VM 的磁碟加密，請執行下列步驟︰

1. 從[加密案例](#encryption-scenarios)一節所列的案例中選擇一個加密案例。

1. 選擇透過「Azure 磁碟加密」Resource Manager 範本、PowerShell Cmdlet 或 Azure CLI 命令啟用磁碟加密，並指定加密設定。

   * 針對客戶加密的 VHD 案例，請將加密的 VHD 上傳至儲存體帳戶並將加密金鑰資料上傳至金鑰保存庫。 接著，提供加密組態資訊以在新的 IaaS VM 上啟用加密。
   * 針對從 Marketplace 建立的新 VM 和已在 Azure 中執行的現有 VM，提供加密設定以在 IaaS VM 上啟用加密。

1. 授與對 Azure 平台的存取權，以從您的金鑰保存庫讀取加密金鑰資料 (適用於 Windows 系統的 BitLocker 加密金鑰和適用於 Linux 的複雜密碼)，以便在 IaaS VM 上啟用加密。

1. Azure 會使用加密和金鑰保存庫組態更新 VM 服務模型，並建立加密的 VM。

   ![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>解密工作流程
若要停用 IaaS VM 的磁碟加密，請完成下列高階步驟：

1. 選擇在 Azure 中的執行中 IaaS VM 上停用加密 (解密)，並指定解密組態。 您可以透過「Azure 磁碟加密」Resource Manager 範本、PowerShell Cmdlet 或 Azure CLI 停用加密。

   此步驟會在執行中 Windows IaaS VM 上停用 OS 或資料磁碟區或兩者的加密。 如前一節所述，不支援停用 Linux 的 OS 磁碟加密。 只要 OS 磁碟機未加密，就只允許對 Linux VM 上的資料磁碟機執行解密步驟。

1. Azure 會更新 VM 服務模型，而 IaaS VM 則會標示為已解密。 VM 的待用內容不會再加密。

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

   * 針對客戶加密的 VHD 案例，請將加密的 VHD 上傳至儲存體帳戶並將加密金鑰資料上傳至金鑰保存庫。 接著，提供加密組態資訊以在新的 IaaS VM 上啟用加密。
   * 針對從 Marketplace 建立的新 VM 和已在 Azure 中執行的現有 VM，提供加密設定以在 IaaS VM 上啟用加密。

1. 授與對 Azure 平台的存取權，以從您的金鑰保存庫讀取加密金鑰資料 (適用於 Windows 系統的 BitLocker 加密金鑰和適用於 Linux 的複雜密碼)，以便在 IaaS VM 上啟用加密。

1. 提供 Azure AD 應用程式身分識別，以將加密金鑰資料寫入至金鑰保存庫。 此步驟可針對步驟 2 中所述的案例，在 IaaS VM 上啟用加密。

1. Azure 會使用加密和金鑰保存庫組態更新 VM 服務模型，並建立加密的 VM。


## <a name="terminology"></a>術語
下表定義了此技術中所使用的一些常見字詞：

| 術語 | 定義 |
| --- | --- |
| Azure AD | [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) 帳戶可用來從金鑰保存庫驗證、儲存及擷取祕密。 |
| Azure 金鑰保存庫 | Key Vault 是一個密碼編譯金鑰管理服務，以「美國聯邦資訊處理標準」(FIPS) 已驗證的硬體安全性模組為基礎。 這些標準可協助您保護密碼編譯金鑰和敏感性祕密。 如需詳細資訊，請參閱 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文件。 |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) 是一種業界認可的 Windows 磁碟區加密技術，用來在 Windows IaaS VM 上啟用磁碟加密。 |
| BEK | BitLocker 加密金鑰 (BEK) 可用來加密 OS 開機磁碟區和資料磁碟區。 BEK 會在金鑰保存庫中作為祕密受到保護。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) 已針對從命令列管理 Azure 資源進行最佳化。|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 是基於 Linux 的透明磁碟加密子系統，用來在 Linux IaaS VM 上啟用磁碟加密。 |
| KEK | 金鑰加密金鑰 (KEK) 是非對稱金鑰 (RSA 2048)，可用來保護或包裝祕密。 您可以提供硬體安全性模組 (HSM) 保護的金鑰或軟體保護的金鑰。 如需詳細資訊，請參閱 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文件。 |
| PowerShell Cmdlet | 如需詳細資訊，請參閱 [Azure PowerShell Cmdlet](/powershell/azure/overview)。 |

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [Azure 磁碟加密的先決條件](azure-security-disk-encryption-prerequisites.md)
