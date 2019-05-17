---
title: 使用 Azure 金鑰保存庫憑證的 SSL 終止
description: 了解如何整合 Azure 應用程式閘道使用 Key Vault 之伺服器憑證附加到啟用 HTTPS 接聽程式。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 18af315c58c838a7237acfbcc32f622a0edbd3b3
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827634"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>使用 Key Vault 憑證的 SSL 終止

[Azure Key Vault](../key-vault/key-vault-whatis.md)與平台管理祕密存放區，您可以使用來保護祕密、 金鑰和 SSL 憑證。 Azure 應用程式閘道支援與整合 Key Vault （公開預覽） 中的之伺服器憑證附加到啟用 HTTPS 接聽程式。 這項支援僅限於應用程式閘道 v2 SKU。

> [!IMPORTANT]
> 使用金鑰保存庫的應用程式閘道整合目前處於公開預覽狀態。 此預覽版的服務等級協定 (SLA) 不提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

此公開預覽版本提供 SSL 終止的兩個模型：

- 您可以明確地提供附加至接聽程式的 SSL 憑證。 此模型是傳統的方式傳遞至應用程式閘道的 SSL 憑證，SSL 終止。
- 當您建立的已啟用 HTTPS 的接聽程式時，您可以選擇性地提供現有的 Key Vault 憑證或祕密的參考。

使用金鑰保存庫的應用程式閘道整合可提供許多優點，包括：

- 更強的安全性，因為 SSL 憑證不直接由應用程式開發小組。 整合可讓個別的安全性小組：
  * 設定應用程式閘道。
  * 控制應用程式閘道的生命週期。
  * 授與存取權限給選取的應用程式閘道會儲存在金鑰保存庫的憑證。
- 現有的憑證匯入金鑰保存庫的支援。 或使用金鑰保存庫 Api 來建立和管理新的憑證與任何受信任的 Key Vault 合作夥伴。
- 支援的憑證儲存在金鑰保存庫中的自動更新。

應用程式閘道目前支援僅限於軟體驗證憑證。 硬體安全性模組 (HSM)-不支援已驗證的憑證。 應用程式閘道設定為使用 Key Vault 憑證之後，其執行個體就會從 Key Vault 擷取憑證，並在本機上安裝 SSL 終止。 執行個體也會輪詢金鑰保存庫擷取更新的版本的憑證，每隔 24 小時制若有的話。 如果找到更新的憑證，則會自動旋轉目前與 HTTPS 接聽程式相關聯的 SSL 憑證。

## <a name="how-integration-works"></a>整合的運作方式

使用金鑰保存庫的應用程式閘道整合需要三個步驟設定程序：

1. **建立使用者指派的受控身分識別**

   建立、 重複使用現有使用者指派給受控身分識別，用來擷取 Key Vault 中的憑證，代表您的應用程式閘道。 如需詳細資訊，請參閱[什麼是適用於 Azure 資源的受控識別？](../active-directory/managed-identities-azure-resources/overview.md)。 這個步驟會在 Azure Active Directory 租用戶中建立新的身分識別。 用來建立身分識別的訂用帳戶信任的身分識別。

1. **設定金鑰保存庫**

   您然後匯入現有憑證或建立新的金鑰保存庫。 憑證將用於透過應用程式閘道執行的應用程式。 在此步驟中，您也可以使用金鑰保存庫祕密儲存為無密碼的基本 64 編碼的 PFX 檔案。 我們建議使用憑證類型，因為使用金鑰保存庫中的憑證類型物件的自動更新功能。 您已建立的憑證或密碼之後，您會定義存取原則可讓被授與身分識別金鑰的保存庫中*取得*密碼的存取權。

1. **設定應用程式閘道**

   完成的兩個上述步驟之後，您可以設定或修改現有的應用程式閘道使用的使用者指派的受控身分識別。 您也可以設定為指向金鑰保存庫憑證或祕密識別碼的完整 URI 的 HTTP 接聽程式的 SSL 憑證

   ![金鑰保存庫憑證](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>後續步驟

[使用 Azure PowerShell，使用金鑰保存庫的憑證設定 SSL 終止](configure-keyvault-ps.md)
