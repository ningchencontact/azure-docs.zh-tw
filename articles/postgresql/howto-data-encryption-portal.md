---
title: 使用入口網站適用於 PostgreSQL 的 Azure 資料庫單一伺服器的資料加密
description: 瞭解如何使用 Azure 入口網站設定和管理適用於 PostgreSQL 的 Azure 資料庫單一伺服器的資料加密
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: 51c99ca0788900397c922e31e44f121a7ae9caa6
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904259"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-using-portal"></a>使用入口網站適用於 PostgreSQL 的 Azure 資料庫單一伺服器的資料加密

在本文中，您將瞭解如何設定和管理，以使用 Azure 入口網站來設定適用於 PostgreSQL 的 Azure 資料庫單一伺服器的資料加密。

## <a name="prerequisites-for-powershell"></a>PowerShell 的必要條件

* 您必須具有 Azure 訂用帳戶，並且是該訂用帳戶的系統管理員。
* 您必須已安裝且正在執行 Azure PowerShell。
* 建立要用於客戶管理金鑰的 Azure Key Vault 和金鑰。
* 金鑰保存庫必須具有下列屬性，才能當做客戶管理的金鑰使用
    * [虛刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

        ```azurecli-interactive
        az resource update --id $(az keyvault show --name \ <key_valut_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
        ```
    
    * [清除受保護](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)

        ```azurecli-interactive
        az keyvault update --name <key_valut_name> --resource-group <resource_group_name>  --enable-purge-protection true
        ```

* 金鑰必須具有下列屬性，才能用於客戶管理的金鑰。
    * 沒有到期日
    * 未停用
    * 能夠執行取得、包裝金鑰、解除包裝金鑰作業

## <a name="setting-the-right-permissions-for-key-operations"></a>設定金鑰作業的正確許可權

1. 在 Azure Key Vault 上，選取 **存取原則** 和 **新增存取原則** 

   ![存取原則總覽](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. 在 [**金鑰許可權**] 底下，選取 [**取得**]、[**包裝**]、[解除封裝] 和 [**主體** **]，這**是于 postgresql 伺服器的名稱。

   ![存取原則總覽](media/concepts-data-access-and-security-data-encryption/access-policy-warp-unwrap.png)

3. **儲存**設定。

## <a name="setting-data-encryption-for-azure-database-for-postgresql-single-server"></a>設定適用於 PostgreSQL 的 Azure 資料庫單一伺服器的資料加密

1. 在 **適用於 PostgreSQL 的 Azure 資料庫**上，選取 **資料加密** 來設定客戶管理的金鑰設定。

   ![設定資料加密](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. 您可以選取**Key Vault**和**金鑰**組，或傳遞**金鑰識別碼**。

   ![設定 Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **儲存**設定。

4. 若要確保所有檔案（包括暫存檔案）都已完整加密，則需要重新開機伺服器。

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>還原或建立已啟用資料加密之伺服器的複本

一旦適用於 PostgreSQL 的 Azure 資料庫單一伺服器以客戶的受控金鑰加密（儲存在 Key Vault 中），任何新建立的伺服器複本都會透過本機或異地還原作業或複本（本機/跨區域）作業來進行。 因此，如果是加密的于 postgresql 伺服器，您可以遵循下列步驟來建立加密的已還原伺服器。

1. 在您的伺服器上，選取 **[總覽**]，然後選取 [**還原**]。

   ![起始-還原](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   或若為已啟用複寫的伺服器，請在 [**設定**] 標題底下 **，選取 [** 複寫]

   ![起始-複本](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. 一旦還原作業完成，新建立的伺服器就會使用主伺服器的金鑰加密資料。 不過，伺服器上的功能和選項已停用，而伺服器則會標示為**無法存取**的狀態。 這是為了防止任何資料操作，因為新伺服器的身分識別仍然不會獲得存取 Key Vault 的許可權。

   ![標記伺服器無法存取](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)


3. 若要修正無法存取的狀態，您必須重新驗證還原之伺服器上的金鑰。

   ![重新驗證服務器](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   您必須將新伺服器的存取權授與 Key Vault。 

4. 一旦您重新驗證金鑰，伺服器就會繼續正常運作。

   ![一般伺服器已還原](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="next-steps"></a>後續步驟

 若要深入瞭解資料加密，請參閱[什麼是 Azure 資料加密](concepts-data-encryption-postgresql.md)。