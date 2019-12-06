---
title: 設定您自己的金鑰來加密待用 Azure 服務匯流排資料
description: 本文提供有關如何設定您自己的金鑰來加密 Azure 服務匯流排資料其餘部分的資訊。
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: aschhab
ms.openlocfilehash: 356f825524192c3b6cf7df7f0460975f23ea4f7c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852285"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal-preview"></a>使用 Azure 入口網站（預覽），設定客戶管理的金鑰來加密待用 Azure 服務匯流排資料
Azure 服務匯流排 Premium 使用 Azure 儲存體服務加密（Azure SSE）提供待用資料的加密。 服務匯流排 Premium 依賴 Azure 儲存體來儲存資料，而且根據預設，所有以 Azure 儲存體儲存的資料都會使用 Microsoft 管理的金鑰進行加密。 

## <a name="overview"></a>概觀
Azure 服務匯流排現在支援使用 Microsoft 管理的金鑰或客戶管理的金鑰（攜帶您自己的金鑰-BYOK）來加密待用資料的選項。 這項功能可讓您建立、輪替、停用及撤銷用來加密待用 Azure 服務匯流排之客戶管理金鑰的存取權。

啟用 BYOK 功能是在命名空間上進行的一次性設定程式。

> [!NOTE]
> 針對服務端加密，客戶管理的金鑰會有一些注意事項。 
>   * [Azure 服務匯流排](service-bus-premium-messaging.md)進階層支援這項功能。 無法為標準層服務匯流排命名空間啟用此功能。
>   * 只能為新的或空的命名空間啟用加密。 如果命名空間包含資料，加密作業將會失敗。
>   * 如果您服務匯流排命名空間的 Azure Key Vault 上設定[虛擬網路（VNet）服務端點](service-bus-service-endpoints.md)，將不會支援 BYOK。 

您可以使用 Azure Key Vault 來管理金鑰，並審核金鑰使用方式。 您可以建立自己的金鑰，並將其儲存在金鑰保存庫中，或者您可以使用 Azure Key Vault Api 來產生金鑰。 如需 Azure 金鑰保存庫的詳細資訊，請參閱 [什麼是 Azure 金鑰保存庫？](../key-vault/key-vault-overview.md)

本文說明如何使用 Azure 入口網站，以客戶管理的金鑰來設定金鑰保存庫。 若要瞭解如何使用 Azure 入口網站建立金鑰保存庫，請參閱[快速入門：使用 Azure 入口網站從 Azure Key Vault 設定和取出秘密](../key-vault/quick-create-portal.md)。

> [!IMPORTANT]
> 將客戶管理的金鑰與 Azure 服務匯流排搭配使用時，金鑰保存庫必須設定兩個必要的屬性。 它們是：虛**刪除**且**不會清除**。 當您在 Azure 入口網站中建立新的金鑰保存庫時，預設會啟用這些屬性。 不過，如果您需要在現有的金鑰保存庫上啟用這些屬性，則必須使用 PowerShell 或 Azure CLI。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰
若要在 Azure 入口網站中啟用客戶管理的金鑰，請遵循下列步驟：

1. 流覽至您的服務匯流排 Premium 命名空間。
2. 在服務匯流排命名空間的 [**設定**] 頁面上，選取 [**加密（預覽）** ]。
3. 選取 [待用**客戶管理的金鑰加密**]，如下圖所示。

    ![啟用客戶管理的金鑰](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>使用金鑰設定金鑰保存庫

啟用客戶管理的金鑰之後，您必須將客戶管理的金鑰與您的 Azure 服務匯流排命名空間產生關聯。 服務匯流排只支援 Azure Key Vault。 如果您在上一節中啟用 [**使用客戶管理的金鑰進行加密**] 選項，則必須將金鑰匯入 Azure Key Vault。 此外，索引鍵必須具有虛**刪除**，而且**不會**針對金鑰進行清除設定。 這些設定可以使用[PowerShell](../key-vault/key-vault-soft-delete-powershell.md)或[CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)來設定。

1. 若要建立新的金鑰保存庫，請遵循 Azure Key Vault[快速入門](../key-vault/key-vault-overview.md)。 如需匯入現有金鑰的詳細資訊，請參閱[關於金鑰、秘密和憑證](../key-vault/about-keys-secrets-and-certificates.md)。
1. 若要在建立保存庫時開啟虛刪除和清除保護，請使用[az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)命令。

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 若要將清除保護新增至現有的保存庫（已啟用虛刪除），請使用[az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)命令。

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 依照下列步驟建立金鑰：
    1. 若要建立新的金鑰，從 [設定] 下方的 [金鑰] 功能表中選取 [產生/匯入]。
        
        ![選取 [產生/匯入] 按鈕](./media/configure-customer-managed-key/select-generate-import.png)

    1. 將 [選項] 設定為 [產生]，並為金鑰提供名稱。

        ![建立金鑰](./media/configure-customer-managed-key/create-key.png) 

    1. 您現在可以從下拉式清單中選取此金鑰，使其與要加密的服務匯流排命名空間產生關聯。 

        ![從 key vault 選取金鑰](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > 針對 [冗余]，您可以新增最多3個金鑰。 如果其中一個金鑰已過期，或無法存取，則會使用其他金鑰進行加密。
        
    1. 填入金鑰的詳細資料，然後按一下 [**選取**]。 這可讓您使用客戶管理的金鑰，對命名空間中的待用資料進行加密。 


> [!IMPORTANT]
> 如果您想要使用客戶管理的金鑰以及異地嚴重損壞修復，請參閱下列- 
>
> 若要使用客戶管理的金鑰啟用待用加密，請在指定的 Azure KeyVault 上設定服務匯流排「受控識別」的[存取原則](../key-vault/key-vault-secure-your-key-vault.md)。 這可確保從 Azure 服務匯流排命名空間控制對 Azure KeyVault 的存取。
>
> 原因如下：
> 
>   * 如果已啟用服務匯流排命名空間的[地理災難](service-bus-geo-dr.md)復原，而且您想要啟用客戶管理的金鑰，則 
>     * 中斷配對
>     * 將主要和次要命名空間的受控識別[存取原則設定](../key-vault/managed-identity.md)為金鑰保存庫。
>     * 在主要命名空間上設定加密。
>     * 重新配對主要和次要命名空間。
> 
>   * 如果您想要在已設定客戶管理金鑰的服務匯流排命名空間上啟用異地 DR，則-
>     * 將次要命名空間之受控識別的[存取原則設定](../key-vault/managed-identity.md)為金鑰保存庫。
>     * 配對主要和次要命名空間。


## <a name="rotate-your-encryption-keys"></a>輪替加密金鑰

您可以使用 Azure 金鑰保存庫迴圈機制來輪替金鑰保存庫中的金鑰。 如需詳細資訊，請參閱[設定金鑰輪替和審核](../key-vault/key-vault-key-rotation-log-monitoring.md)。 啟用和到期日期也可以設定為自動執行金鑰輪替。 服務匯流排服務會偵測新的金鑰版本，並開始自動使用它們。

## <a name="revoke-access-to-keys"></a>撤銷金鑰的存取權

撤銷加密金鑰的存取權並不會從服務匯流排清除資料。 不過，資料無法從服務匯流排命名空間存取。 您可以透過存取原則或藉由刪除金鑰來撤銷加密金鑰。 深入瞭解存取原則和保護您的金鑰保存庫，[以安全地存取金鑰保存庫](../key-vault/key-vault-secure-your-key-vault.md)。

撤銷加密金鑰之後，加密命名空間上的服務匯流排服務就會變成無法運作。 如果已啟用金鑰的存取權，或還原已刪除的金鑰，服務匯流排服務會挑選金鑰，讓您可以從加密的服務匯流排命名空間存取資料。

> [!NOTE]
> 如果您從金鑰保存庫中刪除現有的加密金鑰，並將它取代為服務匯流排命名空間上的新金鑰，由於刪除金鑰仍然有效（因為它已快取）長達一小時，您仍然可以存取 alon 的舊資料（以舊金鑰加密）以新的資料為 g，現在只能使用新的金鑰來存取。 此行為是在功能的預覽版本中設計的。 

## <a name="next-steps"></a>後續步驟
請參閱下列文章：
- [服務匯流排概觀](service-bus-messaging-overview.md)
- [Key Vault 總覽](../key-vault/key-vault-overview.md)


