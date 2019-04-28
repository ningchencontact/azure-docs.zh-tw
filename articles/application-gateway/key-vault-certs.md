---
title: 使用 Key Vault 憑證的 SSL 終止
description: 了解如何整合 Azure 應用程式閘道使用 Key Vault 之伺服器憑證附加到啟用 HTTPS 接聽程式。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 9ab1a42578081d4e6537f221e3f3a8f4c7babde0
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759603"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>使用 Key Vault 憑證的 SSL 終止

[Azure Key Vault](../key-vault/key-vault-whatis.md)是平台管理祕密存放區，您可以使用它來保護祕密、 金鑰和 SSL 憑證。 應用程式閘道支援連接到啟用 HTTPS 接聽程式的伺服器憑證 （公開預覽） 中的使用金鑰保存庫整合。 這項支援僅限於應用程式閘道 v2 SKU。

> [!IMPORTANT]
> 應用程式閘道的金鑰保存庫整合目前處於公開預覽狀態。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

有兩種 SSL 終止，利用此公開預覽版本的模型：

- 您可以明確地提供附加至接聽程式的 SSL 憑證。 這是傳統的模型傳遞至應用程式閘道的 SSL 憑證，SSL 終止。
- 您可以選擇性地提供現有的 Key Vault 憑證的參考，或在 HTTPS 的祕密已啟用接聽程式建立。

有許多好處，有了 Key Vault 整合，包括：

- 因為 SSL 憑證不會直接處理應用程式開發小組更強的安全性。 使用金鑰保存庫的整合可讓個別的安全性小組佈建、 控制的生命週期，並讓選取的應用程式閘道，以存取憑證儲存在金鑰保存庫中的適當權限。
- 支援將現有的憑證匯入至 Key Vault，或使用金鑰保存庫 Api 來建立和管理新的憑證與任何受信任的 Key Vault 合作夥伴。
- 自動更新的金鑰保存庫中儲存之憑證的支援。

應用程式閘道目前支援僅限於驗證的軟體憑證。 不支援的硬體安全性模組 (HSM) 驗證憑證。 一旦應用程式閘道設定為使用 Key Vault 憑證，其執行個體就會從 Key Vault 擷取憑證，並在本機上安裝 SSL 終止。 若有的話，執行個體會將金鑰保存庫也會定期輪詢 24 小時制的間隔來擷取憑證的更新的版本。 如果找到更新的憑證，則會自動旋轉目前與 HTTPS 接聽程式相關聯的 SSL 憑證。

## <a name="how-it-works"></a>運作方式

使用金鑰保存庫的整合需要三個步驟設定程序：

1. **建立使用者指派給受控身分識別**

   您必須建立或重複使用現有的使用者指派的受控應用程式閘道用來擷取 Key Vault 中的憑證，代表您的身分識別。 如需詳細資訊，請參閱[什麼是適用於 Azure 資源管理的身分識別？](../active-directory/managed-identities-azure-resources/overview.md) 此步驟會建立新的身分識別，在 Azure AD 租用戶，用來建立身分識別的訂用帳戶所信任。
1. **設定金鑰保存庫**

   您接著必須匯入，或透過應用程式閘道執行的應用程式所使用的金鑰保存庫中建立新的憑證。 Key Vault 祕密儲存為無密碼的 base 64 編碼的 PFX 的檔案也可用在此步驟。 使用憑證型別是慣用因為自動更新功能適用於金鑰保存庫中的憑證類型物件。 一旦建立憑證或密碼時，必須在金鑰保存庫，以允許被授與身分識別定義存取原則*取得*擷取祕密的存取。

1. **設定應用程式閘道**

   前兩個步驟都完成後，您可以佈建，或修改現有的應用程式閘道，以使用指派給受控身分識別的使用者。 您也可以設定 HTTP 接聽程式的 SSL 憑證，以指向完整 URI 的金鑰保存庫的憑證或密碼的識別碼。

![Key Vault 憑證](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>後續步驟

[使用 Azure PowerShell 的 Key Vault 憑證與設定 SSL 終止](configure-keyvault-ps.md)。