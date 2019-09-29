---
title: 待用資料的 Azure 儲存體加密 |Microsoft Docs
description: Azure 儲存體保護您的資料，方法是在將它保存到雲端之前自動將其加密。 Azure 儲存體中的所有資料都會使用256位 AES 加密，以透明的方式加密和解密，而且符合 FIPS 140-2 標準。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2abaa994bf507c3ffb65199af9ac609ece138ea4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673052"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>待用資料的 Azure 儲存體加密

Azure 儲存體會在將資料保存到雲端時，自動將其加密。 加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。 Azure 儲存體中的資料會使用256位[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最強區塊密碼之一），以透明的方式進行加密和解密，且符合 FIPS 140-2 標準。 Azure 儲存體加密類似于 Windows 上的 BitLocker 加密。

已針對所有新的和現有的儲存體帳戶啟用 Azure 儲存體加密，且無法停用。 因為預設會保護您的資料，所以您不需要修改程式碼或應用程式，就能利用 Azure 儲存體加密。 

無論儲存體帳戶的效能層級（標準或高階）或部署模型（Azure Resource Manager 或傳統），都會進行加密。 所有 Azure 儲存體的冗余選項都支援加密，而且儲存體帳戶的所有複本都會加密。 所有 Azure 儲存體資源都會加密，包括 blob、磁片、檔案、佇列和資料表。 所有物件中繼資料也會加密。

加密不會影響 Azure 儲存體效能。 Azure 儲存體加密不會產生額外的費用。

如需 Azure 儲存體加密之基礎密碼編譯模組的詳細資訊，請參閱 @no__t 0Cryptography API：新一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)。

## <a name="key-management"></a>金鑰管理

您可以依賴 Microsoft 管理的金鑰來加密儲存體帳戶，也可以使用您自己的金鑰來管理加密，以及 Azure Key Vault。

### <a name="microsoft-managed-keys"></a>Microsoft 管理的金鑰

根據預設，您的儲存體帳戶會使用 Microsoft 管理的加密金鑰。 您可以在[Azure 入口網站](https://portal.azure.com)的 [**加密**] 區段中看到儲存體帳戶的加密設定，如下圖所示。

![查看使用 Microsoft 管理的金鑰加密的帳戶](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>客戶管理的金鑰

您可以使用客戶管理的金鑰來管理 Azure 儲存體加密。 客戶管理的金鑰可讓您更有彈性地建立、輪替、停用及撤銷存取控制。 您也可以審核用來保護資料的加密金鑰。 

使用 Azure Key Vault 來管理您的金鑰，並審核金鑰使用方式。 您可以建立自己的金鑰，並將其儲存在金鑰保存庫中，或者您可以使用 Azure Key Vault Api 來產生金鑰。 儲存體帳戶與金鑰保存庫必須位於相同區域，但可位於不同的訂用帳戶中。 如需 Azure Key Vault 的詳細資訊，請參閱[什麼是 Azure Key Vault？](../../key-vault/key-vault-overview.md)。

若要撤銷對客戶管理的金鑰的存取權，請參閱[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/)和[Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤銷存取權可有效封鎖對儲存體帳戶中所有資料的存取，因為 Azure 儲存體無法存取加密金鑰。

若要瞭解如何搭配 Azure 儲存體使用客戶管理的金鑰，請參閱下列其中一篇文章：

- [從 Azure 入口網站設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-portal.md)
- [從 PowerShell 設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-powershell.md)
- [從 Azure CLI 使用客戶管理的金鑰與 Azure 儲存體加密](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> 客戶管理的金鑰依賴 Azure 資源的受控識別，這是一項 Azure Active Directory （Azure AD）的功能。 當您將訂用帳戶從一個 Azure AD 目錄傳輸到另一個時，受控識別不會更新，而且客戶管理的金鑰可能無法再使用。 如需詳細資訊，請參閱常見問題中的 Azure AD 目錄[和 Azure 資源的受控識別的已知問題](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)中**的訂**用帳戶。  

> [!NOTE]  
> [Azure 受控磁片](../../virtual-machines/windows/managed-disks-overview.md)不支援客戶管理的金鑰。

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure 儲存體加密與磁片加密

使用 Azure 儲存體加密，所有 Azure 儲存體帳戶及其包含的資源都會加密，包括備份 Azure 虛擬機器磁片的分頁 blob。 此外，您可以使用[Azure 磁碟加密](../../security/azure-security-disk-encryption-overview.md)來加密 Azure 虛擬機器磁片。 Azure 磁碟加密在 Windows 上使用業界標準的[BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) ，而在 Linux 上則為[DM Crypt](https://en.wikipedia.org/wiki/Dm-crypt) ，以提供與 Azure Key Vault 整合之以作業系統為基礎的加密解決方案。

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 金鑰保存庫？](../../key-vault/key-vault-overview.md)
