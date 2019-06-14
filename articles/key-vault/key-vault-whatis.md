---
title: 什麼是 Azure 金鑰保存庫？ | Microsoft Docs
description: 了解 Azure Key Vault 會保護密碼編譯金鑰和祕密，雲端應用程式和服務使用。
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 43794b8ef4e0272362c7695eda75f5af36a77d1e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683467"
---
# <a name="what-is-azure-key-vault"></a>什麼是 Azure 金鑰保存庫？

雲端應用程式和服務使用密碼編譯金鑰和祕密，以協助保護資訊安全。 Azure Key Vault 會保護這些金鑰和祕密。 當您使用金鑰保存庫時，您可以使用硬體安全模組 (Hsm) 保護的金鑰來加密驗證金鑰、 儲存體帳戶金鑰、 資料加密金鑰、.pfx 檔案和密碼。

Key Vault 可協助您解決下列問題：

- **祕密管理**:安全地儲存和嚴密控制存取權杖、 密碼、 憑證、 API 金鑰和其他祕密。
- **金鑰管理**:建立並控制加密資料的加密金鑰。 
- **憑證管理**:佈建、 管理及部署適用於 Azure 和內部連接的資源的公用和私用安全通訊端層/傳輸層安全性 (SSL/TLS) 憑證。 
- **儲存機密資料受到 Hsm**:使用軟體或 FIPS 140-2 Level 2 驗證的 Hsm 來保護祕密和金鑰。

## <a name="basic-concepts"></a>基本概念

Azure Key Vault 是可安全儲存及存取祕密的工具。 祕密是指任何需受到嚴密存取控制的項目，例如 API 金鑰、密碼或憑證。 保存庫是祕密的邏輯群組。

以下是重要的其他詞彙：

- **Tenant**：租用戶是擁有並且可管理特定 Microsoft 雲端服務執行個體的組織。 它是最常用來參考 Azure 和 Office 365 組織的服務集。

- **保存庫擁有者**：保存庫擁有者可建立金鑰保存庫，並取得其完整存取權和控制權。 保存庫擁有者也可以設定稽核，以記錄存取祕密和金鑰的人員。 系統管理員可控制金鑰的生命週期。 他們可將金鑰變換為新版本、備份金鑰，以及執行相關工作。

- **保存庫取用者**：保存庫取用者可在保存庫擁有者為取用者授與存取權時，對金鑰保存庫內的資產執行動作。 可用的動作取決於授與的權限。

- **資源**：資源是透過 Azure 提供的可管理項目。 常見的範例包括虛擬機器、 儲存體帳戶、 web 應用程式、 資料庫和虛擬網路。 有更多。

- **资源组**：資源群組是存放 Azure 方案相關資源的容器。 資源群組可以包含方案的所有資源，或只包含您要以群組方式管理的資源。 您可決定如何根據對組織最有利的方式，將資源配置到資源群組。

- **服務主體**：Azure 服務主體是安全性識別使用者建立的應用程式、 服務和自動化工具用來存取特定 Azure 資源。 把它想成 「 使用者身分識別 」 (使用者名稱和密碼或憑證) 與特定的角色，並具有受到嚴格控制的權限。 不同於一般的使用者身分識別，服務主體只需要執行特定動作。 如果您授與它只需要執行管理工作的最小權限層級，它就會改善安全性。

- [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md)：Azure AD 是租用戶的 Active Directory 服務。 每個目錄都有一或多個網域。 一個目錄可以有多個相關聯的訂用帳戶，但只能有一個租用戶。

- **Azure 租用戶識別碼**：租用戶識別碼是對 Azure 訂用帳戶內的 Azure AD 執行個體進行識別的唯一方法。

- **受控身分識別**:Azure Key Vault 可安全地儲存認證和其他金鑰及密碼，但是您的程式碼必須向 Key Vault 進行驗證，才可取得這些項目。 使用受控識別可以輕易地解決此問題，因為它可在 Azure AD 中將自動受控識別提供給 Azure 服務。 您可以使用此身分識別向 Key Vault 或任何支援 Azure AD 驗證的服務進行驗證，而無須在程式碼中使用任何認證。 如需詳細資訊，請參閱下圖中，[適用於 Azure 資源管理的身分識別概觀](../active-directory/managed-identities-azure-resources/overview.md)。

    ![如何管理的身分識別，用於 Azure 資源的工時的圖表](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Authentication
若要執行任何作業，使用金鑰保存庫，您需要向它。 有三種方式可向 Key Vault:

- [適用於 Azure 資源的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)：當您部署 Azure 中的虛擬機器上的應用程式時，您可以指派到可存取金鑰保存庫的虛擬機器的身分識別。 您也可以指派的身分識別[其他 Azure 資源](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。 此方法的優點是應用程式或服務不管理第一個祕密的旋轉角度。 Azure 會自動輪換身分識別。 我們建議最佳做法是使用此方法。 
- **服務主體與憑證**:您可以使用服務主體和相關聯的憑證具有金鑰保存庫的存取權。 我們不建議這種方法，因為此應用程式擁有者或開發人員必須輪替憑證。
- **服務主體和祕密**:雖然您可以使用服務主體和密碼來向 Key Vault 中，我們不建議。 很難自動旋轉啟動程序用來驗證 Key Vault 的密碼。


## <a name="key-vault-roles"></a>Key Vault 角色

使用下表將有助於瞭解金鑰保存庫可以如何協助達到開發人員和安全性系統管理員的需求。

| 角色 | 問題陳述 | Azure 金鑰保存庫已解決問題 |
| --- | --- | --- |
| Azure 應用程式的開發人員 |「 我想要撰寫使用金鑰進行簽署和加密的 Azure 應用程式。 但我希望這些金鑰會與我的應用程式分開的因此，此解決方案便適用於地理區域分散的應用程式。 <br/><br/>我希望這些金鑰和密碼會受到保護，但我可以不用自己撰寫程式碼。 我也希望從我的應用程式就可以輕鬆使用這些金鑰和密碼，並得到最佳效能。」 |√ 金鑰會儲存在保存庫中，並且視需要由 URI 叫用。<br/><br/> √ Azure 使用會業界標準演算法、金鑰長度和硬體安全性模組保護金鑰。<br/><br/> √ 金鑰會在與應用程式位於相同 Azure 資料中心的 HSM 中處理。 此方法會比金鑰位於不同位置 (例如內部部署) 提供更高的可靠性並減少延遲。 |
| 軟體即服務 (SaaS) 的開發人員 |「對於客戶之租用戶的金鑰和密碼，我不想承擔任何實際或潛在法律責任。 <br/><br/>我希望客戶擁有並自行管理金鑰，這樣我就可以將全部精力集中在我的專長上，也就是提供核心軟體功能。」 |√ 客戶可以將他們自己的金鑰匯入 Azure 並加以管理。 當 SaaS 應用程式需要使用客戶的金鑰執行加密編譯作業時，Key Vault 會代表應用程式的這些作業。 應用程式不會看到客戶的金鑰。 |
| 資訊安全長 (CSO) |「我想要知道我們的應用程式是否遵守 FIPS 140-2 Level 2 HSM 的安全金鑰管理規定。 <br/><br/>我想要確定我的組織妥善掌控金鑰生命週期，並可監視金鑰使用情形。 <br/><br/>而且即使我們使用多個 Azure 服務和資源，我仍想要從 Azure 中的單一位置管理金鑰。」 |√ HSM 已通過 FIPS 140-2 Level 2 驗證。<br/><br/>√ 金鑰保存庫經過設計，所以 Microsoft 不會看見或擷取您的金鑰。<br/><br/>√ 金鑰使用情形會近乎即時地進行記錄。<br/><br/>√ 不論您在 Azure 中有幾個保存庫、保存庫支援哪些區域，以及哪些應用程式使用這些保存庫，保存庫均提供單一介面讓您清楚了解。 |

只要擁有 Azure 訂用帳戶，任何人都可以建立和使用金鑰保存庫。 雖然金鑰保存庫有益於開發人員和安全性系統管理員，它可以實作及管理組織的系統管理員負責管理其他 Azure 服務。 例如，此系統管理員可以使用 Azure 訂用帳戶，登入建立組織要用來儲存索引鍵，然後負責這類的作業工作的保存庫：

- 建立或匯入金鑰或密碼
- 撤銷或刪除金鑰或密碼
- 授權使用者或應用程式存取金鑰保存庫，以便他們管理或使用其金鑰和密碼
- 設定金鑰使用方法 (例如，簽署或加密)
- 監視金鑰使用方法

此系統管理員，然後提供開發人員若要從他們的應用程式呼叫的 Uri。 此系統管理員也可讓安全性系統管理員金鑰使用方法記錄資訊。 

![Azure 金鑰保存庫運作方式的概觀][1]

開發人員也可透過使用 API 直接管理金鑰。 如需詳細資訊，請參閱 [金鑰保存庫開發人員指南](key-vault-developers-guide.md)。

## <a name="next-steps"></a>後續步驟

了解如何[保護您的保存庫](key-vault-secure-your-key-vault.md)。

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
大部分地區均提供 Azure 金鑰保存庫。 如需詳細資訊，請參閱 [金鑰保存庫價格頁面](https://azure.microsoft.com/pricing/details/key-vault/)。
