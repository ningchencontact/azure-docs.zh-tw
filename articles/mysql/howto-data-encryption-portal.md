---
title: 使用入口網站適用於 MySQL 的 Azure 資料庫的資料加密
description: 瞭解如何使用 Azure 入口網站設定和管理適用於 MySQL 的 Azure 資料庫的資料加密
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: 01c64a6880d671289d02dd36f9e4a9dda2f91131
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922816"
---
# <a name="data-encryption-for-azure-database-for-mysql-server-using-azure-portal"></a>使用 Azure 入口網站適用於 MySQL 的 Azure 資料庫伺服器的資料加密

在本文中，您將瞭解如何設定和管理，以使用 Azure 入口網站來設定適用於 MySQL 的 Azure 資料庫的資料加密。

## <a name="prerequisites-for-cli"></a>CLI 的必要條件

* 您必須具有 Azure 訂用帳戶，並且是該訂用帳戶的系統管理員。
* 建立要用於客戶管理金鑰的 Azure Key Vault 和金鑰。
* Key Vault 必須具有下列屬性，才能做為客戶管理的金鑰使用
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

2. 選取**金鑰許可權**選取 [**取得**]、[包裝]、[解除**封裝** **] 和 [** **主體**]，這是 MySQL 伺服器的名稱。 如果在現有主體的清單中找不到您的伺服器主體，您將需要嘗試第一次設定資料加密（這會失敗）來註冊它。

   ![存取原則總覽](media/concepts-data-access-and-security-data-encryption/access-policy-warp-unwrap.png)

3. **儲存**設定。

## <a name="setting-data-encryption-for-azure-database-for-mysql"></a>設定適用於 MySQL 的 Azure 資料庫的資料加密

1. 在 **適用於 MySQL 的 Azure 資料庫**上，選取 **資料加密** 來設定客戶管理的金鑰設定。

   ![設定資料加密](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. 您可以選取**Key Vault**和**金鑰**組，或傳遞**金鑰識別碼**。

   ![設定 Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **儲存**設定。

4. 若要確保所有檔案（包括**暫存檔案**）都已完整加密，則**需要** **重新開機**伺服器。

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>還原或建立已啟用資料加密之伺服器的複本

適用於 MySQL 的 Azure 資料庫使用儲存在 Key Vault 中的客戶管理金鑰加密之後，任何新建立的伺服器複本都會透過本機或異地還原作業或複本（本機/跨區域）作業進行。 因此，對於已加密的 MySQL 伺服器，您可以遵循下列步驟來建立加密的已還原伺服器。

1. 在您的伺服器上，選取 **[總覽**]，然後選取 [**還原**]。

   ![起始-還原](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   或若為已啟用複寫的伺服器，請在 [**設定**] 標題底下 **，選取 [** 複寫]

   ![起始-複本](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. 一旦還原作業完成，新建立的伺服器就會使用用來加密主伺服器的金鑰進行加密。 不過，伺服器上的功能和選項已停用，而伺服器則會標示為**無法存取**的狀態。 這是為了防止任何資料操作，因為新伺服器的身分識別仍然不會獲得存取 Key Vault 的許可權。

   ![標記伺服器無法存取](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. 若要修正無法存取的狀態，您必須重新驗證還原之伺服器上的金鑰。 按一下 [**資料加密**] 分頁，然後按 [重新**驗證金鑰**] 按鈕。

   > [!NOTE]
   > 第一次重新驗證的嘗試將會失敗，因為新伺服器的服務主體必須獲得金鑰保存庫的存取權。 若要產生服務主體，請按一下 [重新**驗證金鑰**]，這將會產生錯誤，但會產生服務主體。 之後，請參閱上述第[2 節中的](https://docs.microsoft.com/azure/mysql/howto-data-encryption-portal#setting-the-right-permissions-for-key-operations)步驟。

   ![重新驗證服務器](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   您必須將新伺服器的存取權授與 Key Vault。 

4. 註冊服務主體之後，您必須再次重新驗證金鑰，伺服器才會繼續正常運作。

   ![一般伺服器已還原](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>後續步驟

 若要深入瞭解資料加密，請參閱[什麼是 Azure 資料加密](concepts-data-encryption-mysql.md)。

