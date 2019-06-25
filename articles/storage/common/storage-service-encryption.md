---
title: Azure 儲存體加密待用資料 |Microsoft Docs
description: Azure 儲存體自動加密，保護您的資料之前將它保存到雲端。 Azure 儲存體中的所有資料會加密和解密以透明的方式使用 256 位元 AES 加密，並且是 FIPS 140-2 相容。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1e95adbd1a564fb34d3f0506ac1cc25bc5a63c62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65790060"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>待用資料的 azure 儲存體加密

Azure 儲存體保存至雲端時，會自動加密您的資料。 加密可保護您的資料，並協助您符合組織安全性與合規性承諾。 Azure 儲存體中的資料加密和解密透明地使用 256 位元[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)、 其中一個最強大區塊編碼器可用，且 FIPS 140-2 相容。 Azure 儲存體加密會類似於在 Windows 上的 BitLocker 加密。

Azure 儲存體加密會啟用所有新的和現有的儲存體帳戶，而且無法停用。 因為您的資料受到保護預設情況下，您不需要修改您的程式碼或應用程式，以利用 Azure 儲存體加密。 

不論他們的效能層級 （標準或進階） 或部署模型 （Azure Resource Manager 或傳統），會加密儲存體帳戶。 所有的 Azure 儲存體備援選項支援加密，並會加密儲存體帳戶的所有複本。 會加密所有的 Azure 儲存體資源，包括 blob、 磁碟、 檔案、 佇列和資料表。 此外，也會加密所有的物件中繼資料。

加密並不會影響 Azure 儲存體效能。 沒有任何額外的成本，Azure 儲存體加密。

如需有關基礎 Azure 儲存體加密的密碼編譯模組的詳細資訊，請參閱[密碼編譯 API:新一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)。

## <a name="key-management"></a>金鑰管理

您可以依賴 Microsoft 管理的儲存體帳戶，加密的金鑰，或您可以使用您自己的金鑰，以及 Azure Key Vault 來管理加密。

### <a name="microsoft-managed-keys"></a>Microsoft 管理的金鑰

預設情況下，您的儲存體帳戶會使用 Microsoft 管理的加密金鑰。 您可以看到您的儲存體帳戶中的加密設定**加密**一節[Azure 入口網站](https://portal.azure.com)，如下圖所示。

![使用 Microsoft 受控金鑰加密的檢視帳戶](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>客戶管理的金鑰

您可以使用客戶管理的金鑰，來管理 Azure 儲存體加密。 客戶管理金鑰可賦予您更靈活地建立、 輪替、 停用，和撤銷的存取控制。 您也可以稽核用於保護資料的加密金鑰。 

您可以使用 Azure 金鑰保存庫來管理您的金鑰並稽核金鑰的使用方式。 您可以建立您自己的金鑰，並將它們儲存在金鑰保存庫，或您可以使用 Azure 金鑰保存庫 Api，來產生金鑰。 儲存體帳戶與金鑰保存庫必須位於相同區域，但可位於不同的訂用帳戶中。 如需有關 Azure 金鑰保存庫的詳細資訊，請參閱 <<c0> [ 什麼是 Azure 金鑰保存庫？](../../key-vault/key-vault-overview.md)。

若要撤銷存取權到客戶管理的金鑰，請參閱[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/)並[Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤銷存取權有效封鎖對儲存體帳戶中的所有資料存取，因為無法存取 Azure 儲存體加密金鑰。

若要了解如何使用 Azure 儲存體的客戶管理的金鑰，請參閱這些文件：

- [設定客戶管理的金鑰，從 Azure 入口網站的 Azure 儲存體加密](storage-encryption-keys-portal.md)
- [設定客戶管理的金鑰，從 PowerShell 的 Azure 儲存體加密](storage-encryption-keys-powershell.md)
- [使用客戶管理的金鑰，以從 Azure CLI 的 Azure 儲存體加密](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> 受管理的身分識別，如需 Azure 資源，Azure Active Directory (Azure AD) 的一項功能依賴客戶管理的金鑰。 當您轉移訂用帳戶從一個 Azure AD 目錄到另一個、 受管理的身分識別不會更新及客戶管理的金鑰可能無法再運作。 如需詳細資訊，請參閱 < **Azure AD 目錄之間轉移訂閱**中[常見問題和已知的問題與管理 Azure 資源的身分識別](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。  

> [!NOTE]  
> 不支援客戶管理的金鑰[Azure 受控磁碟](../../virtual-machines/windows/managed-disks-overview.md)。

## <a name="azure-storage-encryption-versus-disk-encryption"></a>與磁碟加密的 azure 儲存體加密

使用 Azure 儲存體加密時，所有的 Azure 儲存體帳戶和所包含的資源都會加密，包括備份 Azure 虛擬機器磁碟的分頁 blob。 此外，可利用加密 Azure 虛擬機器磁碟[Azure 磁碟加密](../../security/azure-security-disk-encryption-overview.md)。 Azure 磁碟加密會使用業界標準[BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)在 Windows 上並[Dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt)上 Linux 作業系統為基礎的加密解決方案與 Azure 金鑰保存庫整合。

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 金鑰保存庫？](../../key-vault/key-vault-overview.md)
