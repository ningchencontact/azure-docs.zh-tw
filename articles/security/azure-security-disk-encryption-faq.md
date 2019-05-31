---
title: 常見問題集 - IaaS VM 適用的 Azure 磁碟加密 | Microsoft Docs
description: 本文提供 Windows 和 Linux IaaS VM 適用的 Microsoft Azure 磁碟加密常見問題集的解答。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 04/16/2019
ms.custom: seodec18
ms.openlocfilehash: 5c49a639c0426b4535c4c2e88d36d447c10826f1
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416701"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>IaaS VM 適用的 Azure 磁碟加密常見問題集

本文提供 Windows 和 Linux IaaS VM 適用的 Microsoft Azure 磁碟加密常見問題集 (FAQ) 的解答。 如需此服務的詳細資訊，請參閱 [Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密](azure-security-disk-encryption-overview.md)。

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Azure 磁碟加密會在那裡正式運作 (GA)？

Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密會在所有 Azure 公用區域正式運作。

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure 磁碟加密有哪些使用者體驗？

Azure 磁碟加密 GA 支援 Azure Resource Manager 範本、Azure PowerShell 和 Azure CLI。 不同的使用者體驗可為您賦予彈性。 您會有三個不同選項可啟用您 IaaS VM 的磁碟加密。 如需 Azure 磁碟加密中可用的使用者體驗和逐步指引的詳細資訊，請參閱[啟用適用於 Windows 的 Azure 磁碟加密](azure-security-disk-encryption-windows.md)和[啟用適用於 Linux 的 Azure 磁碟加密](azure-security-disk-encryption-linux.md)。

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure 磁碟加密如何收費？

使用 Azure 磁碟加密加密 VM 磁碟不需付費，但如果使用 Azure Key Vault 則需付費。 如需 Azure Key Vault 成本的詳細資訊，請參閱 [Key Vault 價格](https://azure.microsoft.com/pricing/details/key-vault/)頁面。


## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>Azure 磁碟加密支援哪些虛擬機器層？

Azure 磁碟加密可用於標準層 VM，包括 [A、D、DS、G、GS 及 F](https://azure.microsoft.com/pricing/details/virtual-machines/) 系列 IaaS VM。 也適用於具有進階儲存體的 VM。 它並不適用於基本層 VM。

## <a name="bkmk_LinuxOSSupport"></a> Azure 磁碟加密支援哪些 Linux 散發套件？

子集上支援 azure 磁碟加密[經 Azure 背書的 Linux 散發套件](../virtual-machines/linux/endorsed-distros.md)、 哪些是本身的所有 Linux 伺服器可能散發套件子集。

 ![支援 Azure 磁碟加密的文氏圖的 Linux 伺服器散發套件](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

不由 Azure 背書的 Linux 伺服器散發套件不支援 Azure 磁碟加密，而且這些所背書的只有下列散發套件和版本支援 Azure 磁碟加密：

| Linux 散發套件 | 版本 | 支援加密的磁碟區類型|
| --- | --- |--- |
| Ubuntu | 18.04| 作業系統和資料磁碟 |
| Ubuntu | 16.04| 作業系統和資料磁碟 |
| Ubuntu | 14.04.5</br>[搭配更新至 4.15 或更新版本的 Azure 調整核心](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | 作業系統和資料磁碟 |
| RHEL | 7.6 | 作業系統和資料磁碟 （請參閱下列附註） |
| RHEL | 7.5 | 作業系統和資料磁碟 （請參閱下列附註） |
| RHEL | 7.4 | 作業系統和資料磁碟 （請參閱下列附註） |
| RHEL | 7.3 | 作業系統和資料磁碟 （請參閱下列附註） |
| RHEL | 7.2 | 作業系統和資料磁碟 （請參閱下列附註） |
| RHEL | 6.8 | 資料磁碟 （請參閱下列附註） |
| RHEL | 6.7 | 資料磁碟 （請參閱下列附註） |
| CentOS | 7.5 | 作業系統和資料磁碟 |
| CentOS | 7.4 | 作業系統和資料磁碟 |
| CentOS | 7.3 | 作業系統和資料磁碟 |
| CentOS | 7.2n | 作業系統和資料磁碟 |
| CentOS | 6.8 | 資料磁碟 |
| openSUSE | 42.3 | 資料磁碟 |
| SLES | 12-SP4 | 資料磁碟 |
| SLES | 12-SP3 | 資料磁碟 |

> [!NOTE]
> 新的 ADE 實作適用於 RHEL OS 和資料磁碟 RHEL7 隨用隨付映像的支援。 ADE 目前不支援 RHEL 自備訂用帳戶 (BYOS) 映像。 請參閱[適用於 Linux 的 Azure 磁碟加密](azure-security-disk-encryption-linux.md)如需詳細資訊。

## <a name="how-can-i-start-using-azure-disk-encryption"></a>如何開始使用 Azure 磁碟加密？

若要開始使用，請參閱 [Azure 磁碟加密概觀](azure-security-disk-encryption-overview.md)。

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>是否可以使用 Azure 磁碟加密來加密開機和資料磁碟區？

是，您可以加密 Windows 和 Linux IaaS VM 適用的開機和資料磁碟區。 針對 Windows VM，您若未先將 OS 磁碟區加密，就無法將資料加密。 針對 Linux VM，您無須先將 OS 磁碟區加密，即可為資料磁碟區加密。 您將 Linux 適用的 OS 磁碟區加密之後，就不支援為 Linux IaaS VM 適用的 OS 磁碟區停用加密。 適用於 Linux Vm 擴展集內，可以加密資料磁碟區。

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>取消掛接的磁碟區使用 Azure 磁碟加密可以加密？

否，Azure 磁碟加密僅加密已掛接的磁碟區。

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>如何旋轉祕密或加密金鑰？

若要輪替使用祕密，只會呼叫您原先用來啟用磁碟加密的相同命令，指定不同的金鑰保存庫。 若要輪替金鑰加密金鑰，呼叫相同的命令，您原先用來啟用磁碟加密，指定新的金鑰加密。 

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>如何新增或移除金鑰加密金鑰，如果我原本未使用其中一個？

若要加入金鑰加密金鑰，呼叫一次傳遞金鑰加密金鑰參數的 [啟用] 命令。 若要移除的金鑰加密金鑰，呼叫一次不使用金鑰加密金鑰參數的 [啟用] 命令。

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure 磁碟加密可讓您使用攜帶自己的金鑰 (BYOK) 嗎？

是，您可以提供自己的金鑰加密金鑰。 這些金鑰會在 Azure Key Vault 中受到保護，這是 Azure 磁碟加密的金鑰儲存區。 如需金鑰加密金鑰支援案例的詳細資訊，請參閱 [Azure 磁碟加密的先決條件](azure-security-disk-encryption-prerequisites.md)。

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>是否可以使用 Azure 建立的金鑰加密金鑰？

是，您可以使用 Azure Key Vault 來產生金鑰加密金鑰以供 Azure 磁碟加密使用。 這些金鑰會在 Azure Key Vault 中受到保護，這是 Azure 磁碟加密的金鑰儲存區。 如需金鑰加密金鑰的詳細資訊，請參閱 [Azure 磁碟加密的先決條件](azure-security-disk-encryption-prerequisites.md)。

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>是否可以使用內部部署金鑰管理服務或 HSM 來保護加密金鑰？

您無法使用內部部署金鑰管理服務或 HSM 來保護 Azure 磁碟加密的加密金鑰。 只能使用 Azure Key Vault 服務來保護加密金鑰。 如需金鑰加密金鑰支援案例的詳細資訊，請參閱 [Azure 磁碟加密的先決條件](azure-security-disk-encryption-prerequisites.md)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>設定 Azure 磁碟加密的必要條件為何？

Azure 磁碟加密有其先決條件。 請參閱 [Azure 磁碟加密的先決條件](azure-security-disk-encryption-prerequisites.md)，以建立新的金鑰保存庫，或針對磁碟加密存取設定現有的金鑰保存庫，從而啟用加密並保護祕密和金鑰。 如需金鑰加密金鑰支援案例的詳細資訊，請參閱 [Azure 磁碟加密概觀](azure-security-disk-encryption-overview.md)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>使用 Azure AD 應用程式 (舊版) 來設定 Azure 磁碟加密的必要條件為何？

Azure 磁碟加密有其先決條件。 請參閱 [Azure 磁碟加密的先決條件](azure-security-disk-encryption-prerequisites-aad.md)，以建立 Azure Active Directory 應用程式、建立新的金鑰保存庫，或針對磁碟加密存取設定現有的金鑰保存庫，從而啟用加密並保護祕密和金鑰。 如需金鑰加密金鑰支援案例的詳細資訊，請參閱 [Azure 磁碟加密概觀](azure-security-disk-encryption-overview.md)。

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>是否仍支援使用 Azure AD 應用程式 (舊版) 來進行 Azure 磁碟加密？
是。 仍支援使用 Azure AD 應用程式來進行磁碟加密。 不過，在加密新的 VM 時，建議您使用新的方法，而不要使用 Azure AD 應用程式來加密。 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>是否可以將使用 Azure AD 應用程式加密的 VM，遷移至未使用 Azure AD 應用程式的加密？
  目前沒有直接的移轉途徑，可將使用 Azure AD 應用程式加密的機器移轉至未使用 Azure AD 應用程式的加密。 此外，也沒有直接的途徑可從未使用 Azure AD 應用程式的加密移轉至使用 AD 應用程式的加密。 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure 磁碟加密支援 Azure PowerShell 的什麼版本？

使用最新版的 Azure PowerShell SDK 來設定 Azure 磁碟加密。 下載最新版的 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)。 Azure SDK 1.1.0 版「不」  支援 Azure 磁碟加密。

> [!NOTE]
> Linux Azure 磁碟加密預覽擴充功能已被取代。 如需詳細資訊，請參閱[淘汰 Linux IaaS VM 適用的 Azure 磁碟加密預覽擴充功能](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/)。

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>是否可以在我的自訂 Linux 映像上套用 Azure 磁碟加密？

您無法對自訂 Linux 映像套用 Azure 磁碟加密。 只有前述支援散發版本的資源庫 Linux 映像受到支援。 目前不支援自訂 Linux 映像。

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>是否可使用 Yum 更新將更新套用至 Linux Red Hat VM？

是，您可以執行 yum 更新將 Red Hat Linux VM 上。  如需詳細資訊，請參閱 <<c0> [ 防火牆後方的 Linux 套件管理](azure-security-disk-encryption-tsg.md#linux-package-management-behind-a-firewall)。

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>若為 Linux，建議使用何種 Azure 磁碟加密工作流程？

建議採取下列工作流程，即可在 Linux 上獲得最佳結果：
* 先從與所需的作業系統發行版本和版本相對應的未修改內建資源庫映像開始
* 將會被加密的任何掛接磁碟機備份。  此備份可在發生失敗時用來進行復原，例如，VM 在加密完成前重新開機時。
* 加密 (可能需要數小時或甚至數天，取決於 VM 特性和附加的任何資料磁碟的大小)
* 自訂軟體，並且視需要將軟體新增至映像。

如果此工作流程不可行，靠著平台儲存體帳戶層[的儲存體服務加密](../storage/common/storage-service-encryption.md) (SSE)，可能可以替代使用 dm crypt 的完整磁碟加密。

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>什麼是磁碟 "Bek Volume" 或 "/mnt/azure_bek_disk"？

Windows 的 "Bek volume" 或 Linux 的 "/mnt/azure_bek_disk" 均為本機資料磁碟區，會安全地儲存用於已加密 Azure IaaS VM 的加密金鑰。
> [!NOTE]
> 請勿刪除或編輯此磁碟中的任何內容。 由於 IaaS VM 上的任何加密作業都需要加密金鑰存在，因此請勿卸載該磁碟。


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Azure 磁碟加密會使用何種加密方法？

在 Windows 上，ADE 會使用 BitLocker AES256 加密方法 (在 Windows Server 2012 之前的版本上會使用 AES256WithDiffuser)。 在 Linux 上，ADE 會使用 256 位元磁碟區主要金鑰解密 aes-xts-plain64 預設值。

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>如果我使用 EncryptFormatAll，並指定所有的磁碟區類型，是否會因此從已加密的資料磁碟機上清除資料？
否，不會從已使用「Azure 磁碟加密」進行加密的資料磁碟機中清除資料。 就像 EncryptFormatAll 不會重新加密 OS 磁碟機一樣，它也不會重新加密已加密的資料磁碟機。 如需詳細資訊，請參閱 [EncryptFormatAll 準則](azure-security-disk-encryption-linux.md#bkmk_EFACriteria)。        

## <a name="is-xfs-filesystem-supported"></a>是否支援 XFS 檔案系統？
只能搭配 EncryptFormatAll 的資料磁碟加密支援 XFS 磁碟區。 這會重新格式化磁碟區，並清除任何資料之前發生。 如需詳細資訊，請參閱 [EncryptFormatAll 準則](azure-security-disk-encryption-linux.md#bkmk_EFACriteria)。

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>我可以備份與還原加密的 VM？ 

Azure 備份提供一個機制來備份與還原加密的 VM 的相同的訂用帳戶和區域內。  如需指示，請參閱[備份與還原加密的虛擬機器，使用 Azure 備份](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)。  目前不支援加密的 VM 還原至不同的區域。  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>我可以在哪裡提出問題或意見反應？

您可以在 [Azure 磁碟加密論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)提出問題或意見反應。

## <a name="next-steps"></a>後續步驟
在本文件中，您已了解有關 Azure 磁碟加密的常見問題。 如需此服務的詳細資訊，請參閱下列文章：

- [Azure 磁碟加密概觀](azure-security-disk-encryption-overview.md)
- [在 Azure 資訊安全中心套用磁碟加密](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [待用 Azure 資料加密](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
