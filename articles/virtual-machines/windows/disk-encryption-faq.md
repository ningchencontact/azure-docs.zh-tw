---
title: Windows Vm 的常見問題-Azure 磁碟加密 |Microsoft Docs
description: 本文提供有關 Windows IaaS Vm Microsoft Azure 磁片加密常見問題的解答。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: b6cd7e93451de9c5761db22024ac82abaaa0949e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245262"
---
# <a name="azure-disk-encryption-for-windows-vms-faq"></a>適用于 Windows Vm 的 Azure 磁碟加密常見問題

本文提供有關 Windows Vm Azure 磁碟加密的常見問題（FAQ）的解答。 如需此服務的詳細資訊，請參閱[Azure 磁碟加密總覽](disk-encryption-overview.md)。

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Azure 磁碟加密會在那裡正式運作 (GA)？

Azure 磁碟加密已在所有 Azure 公用區域正式推出。

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure 磁碟加密有哪些使用者體驗？

Azure 磁碟加密 GA 支援 Azure Resource Manager 範本、Azure PowerShell 和 Azure CLI。 不同的使用者體驗可為您賦予彈性。 您有三個不同的選項可為您的 Vm 啟用磁片加密。 如需 Azure 磁碟加密中可用之使用者體驗和逐步指引的詳細資訊，請參閱[適用于 Windows 的 Azure 磁碟加密案例](disk-encryption-windows.md)。

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure 磁碟加密如何收費？

使用 Azure 磁碟加密加密 VM 磁片不需付費，但使用 Azure Key Vault 時，會產生相關費用。 如需 Azure Key Vault 成本的詳細資訊，請參閱 [Key Vault 價格](https://azure.microsoft.com/pricing/details/key-vault/)頁面。

## <a name="how-can-i-start-using-azure-disk-encryption"></a>如何開始使用 Azure 磁碟加密？

若要開始使用，請參閱 [Azure 磁碟加密概觀](disk-encryption-overview.md)。

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>哪些 VM 大小和作業系統支援 Azure 磁碟加密？

[Azure 磁碟加密總覽](disk-encryption-overview.md)文章列出支援 Azure 磁碟加密的[Vm 大小](disk-encryption-overview.md#supported-vm-sizes)和[vm 作業系統](disk-encryption-overview.md#supported-operating-systems)。

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>是否可以使用 Azure 磁碟加密來加密開機和資料磁碟區？

您可以加密開機和資料磁片區，但不需要先將 OS 磁片區加密，就無法加密資料。

在您將 OS 磁片區加密之後，就不支援在 OS 磁片區上停用加密。

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>我可以使用 Azure 磁碟加密加密卸載的磁片區嗎？

否，Azure 磁碟加密只會加密已掛接的磁片區。

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>如何? 輪替秘密或加密金鑰？

若要輪替秘密，只要呼叫您原先用來啟用磁片加密的相同命令，並指定不同的 Key Vault。 若要輪替金鑰加密金鑰，請呼叫您原先用來啟用磁片加密的相同命令，並指定新的金鑰加密。 

>[!WARNING]
> - 如果您先前已使用[Azure 磁碟加密搭配 Azure AD 應用程式](disk-encryption-windows-aad.md)，請指定 Azure AD 認證來加密此 vm，則必須繼續使用此選項來加密您的 vm。 您無法在此加密的 VM 上使用 Azure 磁碟加密，因為這不是支援的案例，亦即不支援從 AAD 應用程式針對此加密 VM 進行切換。

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>如果我原本未使用金鑰加密金鑰，如何? 新增或移除它？

若要新增金鑰加密金鑰，請再次呼叫 enable 命令，並傳遞金鑰加密金鑰參數。 若要移除金鑰加密金鑰，請在不使用金鑰加密金鑰參數的情況下，再次呼叫 enable 命令。

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure 磁碟加密可讓您使用攜帶自己的金鑰 (BYOK) 嗎？

是，您可以提供自己的金鑰加密金鑰。 這些金鑰會在 Azure Key Vault 中受到保護，這是 Azure 磁碟加密的金鑰儲存區。 如需金鑰加密金鑰支援案例的詳細資訊，請參閱[建立和設定 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>是否可以使用 Azure 建立的金鑰加密金鑰？

是，您可以使用 Azure Key Vault 來產生金鑰加密金鑰以供 Azure 磁碟加密使用。 這些金鑰會在 Azure Key Vault 中受到保護，這是 Azure 磁碟加密的金鑰儲存區。 如需金鑰加密金鑰的詳細資訊，請參閱[建立和設定 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>是否可以使用內部部署金鑰管理服務或 HSM 來保護加密金鑰？

您無法使用內部部署金鑰管理服務或 HSM 來保護 Azure 磁碟加密的加密金鑰。 只能使用 Azure Key Vault 服務來保護加密金鑰。 如需金鑰加密金鑰支援案例的詳細資訊，請參閱[建立和設定 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>設定 Azure 磁碟加密的必要條件為何？

Azure 磁碟加密有其先決條件。 請參閱[建立和設定 Azure 磁碟加密的金鑰保存庫一](disk-encryption-key-vault.md)文，以建立新的金鑰保存庫，或設定現有的金鑰保存庫以進行磁片加密存取以啟用加密，以及保護秘密和金鑰。 如需金鑰加密金鑰支援案例的詳細資訊，請參閱[建立和設定 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>使用 Azure AD 應用程式 (舊版) 來設定 Azure 磁碟加密的必要條件為何？

Azure 磁碟加密有其先決條件。 請參閱[具有 Azure AD](disk-encryption-windows-aad.md)內容的 Azure 磁碟加密，以建立 Azure Active Directory 應用程式、建立新的金鑰保存庫，或設定現有的金鑰保存庫以進行磁片加密存取，以啟用加密並保護秘密和金鑰。 如需金鑰加密金鑰支援案例的詳細資訊，請參閱[建立和設定具有 Azure AD 之 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault-aad.md)。

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>是否仍支援使用 Azure AD 應用程式 (舊版) 來進行 Azure 磁碟加密？
是的。 仍支援使用 Azure AD 應用程式來進行磁碟加密。 不過，在加密新的 VM 時，建議您使用新的方法，而不要使用 Azure AD 應用程式來加密。 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>是否可以將使用 Azure AD 應用程式加密的 VM，遷移至未使用 Azure AD 應用程式的加密？
  目前沒有直接的移轉途徑，可將使用 Azure AD 應用程式加密的機器移轉至未使用 Azure AD 應用程式的加密。 此外，也沒有直接的途徑可從未使用 Azure AD 應用程式的加密移轉至使用 AD 應用程式的加密。 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure 磁碟加密支援 Azure PowerShell 的什麼版本？

使用最新版的 Azure PowerShell SDK 來設定 Azure 磁碟加密。 下載最新版的 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)。 Azure SDK 1.1.0 版「不」支援 Azure 磁碟加密。

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>什麼是磁碟 "Bek Volume" 或 "/mnt/azure_bek_disk"？

"Bek volume" 是本機資料磁片區，可安全地儲存加密 Azure Vm 的加密金鑰。

> [!NOTE]
> 請勿刪除或編輯此磁碟中的任何內容。 由於 IaaS VM 上的任何加密作業都需要加密金鑰存在，因此請勿卸載該磁碟。

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Azure 磁碟加密會使用何種加密方法？

Azure 磁碟加密使用 BitLocker AES256 Encryption 方法（在 Windows Server 2012 之前的版本上在使用 aes256withdiffuser）。 

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>如果我使用 EncryptFormatAll，並指定所有的磁碟區類型，是否會因此從已加密的資料磁碟機上清除資料？
否，不會從已使用「Azure 磁碟加密」進行加密的資料磁碟機中清除資料。 就像 EncryptFormatAll 不會重新加密 OS 磁碟機一樣，它也不會重新加密已加密的資料磁碟機。 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>我可以備份和還原已加密的 VM 嗎？ 

Azure 備份提供一種機制，可在相同的訂用帳戶和區域內備份和還原已加密的 VM。  如需指示，請參閱[使用 Azure 備份備份和還原已加密的虛擬機器](../../backup/backup-azure-vms-encryption.md)。  目前不支援將加密的 VM 還原到不同的區域。  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>我可以在哪裡提出問題或意見反應？

您可以在 [Azure 磁碟加密論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)提出問題或意見反應。

## <a name="next-steps"></a>後續步驟
在本文件中，您已了解有關 Azure 磁碟加密的常見問題。 如需此服務的詳細資訊，請參閱下列文章：

- [Azure 磁碟加密概觀](disk-encryption-overview.md)
- [在 Azure 資訊安全中心套用磁碟加密](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [待用 Azure 資料加密](../../security/fundamentals/encryption-atrest.md)
