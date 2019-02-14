---
title: 什麼是 Azure 金鑰保存庫？ - Azure Key Vault | Microsoft Docs
description: Azure 金鑰保存庫可保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 客戶可以透過使用受硬體安全模組 (HSM) 保護的金鑰，將驗證金鑰、儲存體帳戶金鑰、資料加密金鑰、.PFX 檔案和密碼加密。
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: db2e2e80ecc98eba0f1bba18abad8b561b16036c
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745855"
---
# <a name="what-is-azure-key-vault"></a>什麼是 Azure 金鑰保存庫？

Azure Key Vault 可協助您解決下列問題：

- **祕密管理** - Azure Key Vault 可用來安全地儲存權杖、密碼、憑證、API 金鑰和其他祕密，並嚴密控制其存取。
- **金鑰管理** - Azure Key Vault 也可作為金鑰管理解決方案。 Azure Key Vault 可讓您輕鬆地建立和控制用來加密資料的加密金鑰。 
- **憑證管理** - Azure Key Vault 也是一項服務，可讓您輕鬆地佈建、管理及部署 Azure 和您內部連線的資源所使用的公用和私人安全通訊端層/傳輸層安全性 (SSL/TLS) 憑證。 
- **儲存受到硬體安全性模型支援的祕密** - 祕密和金鑰可受到軟體的保護，或由通過 FIPS 140-2 Level 2 驗證的 HSM 保護。

## <a name="basic-concepts"></a>基本概念

Azure Key Vault 是可安全儲存及存取祕密的工具。 祕密是指任何需受到嚴密存取控制的項目，例如 API 金鑰、密碼或憑證。 **保存庫**是祕密的邏輯群組。 現在若要對 Key Vault 執行任何作業，您必須先向它進行驗證。 

基本上有三種向 Key Vault 驗證的方法：

1. **使用 [Azure 資源的受控識別驗證](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)** (**建議和最佳做法**)：當您在 Azure 中的虛擬機器上部署應用程式時，您可以將身分識別指派給具有 Key Vault 存取權的虛擬機器。 您也可以將身分識別指派給列在[這裡](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)的其他 Azure 資源。 使用此方法的優點是應用程式/服務並未管理第一個祕密的輪替。 Azure 會自動輪換身分識別。 
2. **使用服務主體和憑證：** 第二個選項是使用具有 Key Vault 存取權的服務主體和相關聯的憑證。 輪換憑證的責任在於應用程式擁有者或開發人員，因此不建議這麼做。
3. **使用服務主體和祕密：** 第三個選項 (非偏好選項) 是使用服務主體和祕密來驗證 Key Vault。

> [!NOTE]
> 因為難以自動輪替用來向 Key Vault 進行驗證的啟動程序祕密，所以請勿使用上述的第 3 個選項。

以下提供部分主要詞彙：

- **Tenant**：租用戶是擁有並且可管理特定 Microsoft 雲端服務執行個體的組織。 租用戶最常以明確的方式用來參照組織的 Azure 和 Office 365 服務集合。
- **保存庫擁有者**：保存庫擁有者可建立金鑰保存庫，並取得其完整存取權和控制權。 保存庫擁有者也可以設定稽核，以記錄存取祕密和金鑰的人員。 系統管理員可控制金鑰的生命週期。 他們可將金鑰變換為新版本、備份金鑰，以及執行相關工作。
- **保存庫取用者**：保存庫取用者可在保存庫擁有者為取用者授與存取權時，對金鑰保存庫內的資產執行動作。 可用的動作取決於授與的權限。
- **資源**：資源是透過 Azure 提供的可管理項目。 部分常見資源有虛擬機器、儲存體帳戶、Web 應用程式、資料庫和虛擬網路，但這只是其中一小部分。
- **資源群組**：資源群組是存放 Azure 方案相關資源的容器。 資源群組可以包含方案的所有資源，或只包含您要以群組方式管理的資源。 您可決定如何根據對組織最有利的方式，將資源配置到資源群組。
- **服務主體** - Azure 服務主體是一項安全性身分識別，可供使用者所建立的應用程式、服務和自動化工具用來存取特定 Azure 資源。 您可以把它想成是具有特定角色及嚴格控制權限的「使用者身分識別」(使用者名稱和密碼或憑證)。 不同於一般的使用者身分識別，服務主體只需要執行特定動作。 如果您只對服務主體授與它為了執行管理工作所需要的最低權限等級，它就能提高安全性。
- **[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md)**：Azure AD 是租用戶的 Active Directory 服務。 每個目錄都有一或多個網域。 一個目錄可以有多個相關聯的訂用帳戶，但只能有一個租用戶。 
- **Azure 租用戶識別碼**：租用戶識別碼是對 Azure 訂用帳戶內的 Azure AD 執行個體進行識別的唯一方法。
- **適用於 Azure 資源的受控識別**：Azure Key Vault 可安全地儲存認證和其他金鑰及密碼，但是您的程式碼必須向 Key Vault 進行驗證，才可取得這些項目。 使用受控識別可以輕易地解決此問題，因為它可在 Azure AD 中將自動受控識別提供給 Azure 服務。 您可以使用此身分識別向 Key Vault 或任何支援 Azure AD 驗證的服務進行驗證，而無須在程式碼中使用任何認證。 如需詳細資訊，請參閱下圖和 [Auzre 資源的受控識別概觀](../active-directory/managed-identities-azure-resources/overview.md)。

    ![Azure 資源的受控識別如何運作的圖表](./media/key-vault-whatis/msi.png)

## <a name="key-vault-roles"></a>Key Vault 角色

使用下表將有助於瞭解金鑰保存庫可以如何協助達到開發人員和安全性系統管理員的需求。

| 角色 | 問題陳述 | Azure 金鑰保存庫已解決問題 |
| --- | --- | --- |
| Azure 應用程式的開發人員 |「我想要撰寫使用金鑰進行簽署和加密的 Azure 應用程式，但我希望這些金鑰會與我的應用程式分開，如此一來此解決方案便適用於位於不同地點的應用程式。 <br/><br/>我希望這些金鑰和密碼會受到保護，但我可以不用自己撰寫程式碼。 我也希望從我的應用程式就可以輕鬆使用這些金鑰和密碼，並得到最佳效能。」 |√ 金鑰會儲存在保存庫中，並且視需要由 URI 叫用。<br/><br/> √ Azure 使用會業界標準演算法、金鑰長度和硬體安全性模組保護金鑰。<br/><br/> √ 金鑰會在與應用程式位於相同 Azure 資料中心的 HSM 中處理。 此方法會比金鑰位於不同位置 (例如內部部署) 提供更高的可靠性並減少延遲。 |
| 軟體即服務 (SaaS) 的開發人員 |「對於客戶之租用戶的金鑰和密碼，我不想承擔任何實際或潛在法律責任。 <br/><br/>我希望客戶擁有並自行管理金鑰，這樣我就可以將全部精力集中在我的專長上，也就是提供核心軟體功能。」 |√ 客戶可以將他們自己的金鑰匯入 Azure 並加以管理。 當 SaaS 應用程式必須使用其客戶金鑰來執行加密編譯密碼作業時，金鑰保存庫會代表應用程式執行這些作業。 應用程式不會看到客戶的金鑰。 |
| 資訊安全長 (CSO) |「我想要知道我們的應用程式是否遵守 FIPS 140-2 Level 2 HSM 的安全金鑰管理規定。 <br/><br/>我想要確定我的組織妥善掌控金鑰生命週期，並可監視金鑰使用情形。 <br/><br/>而且即使我們使用多個 Azure 服務和資源，我仍想要從 Azure 中的單一位置管理金鑰。」 |√ HSM 已通過 FIPS 140-2 Level 2 驗證。<br/><br/>√ 金鑰保存庫經過設計，所以 Microsoft 不會看見或擷取您的金鑰。<br/><br/>√ 金鑰使用情形會近乎即時地進行記錄。<br/><br/>√ 不論您在 Azure 中有幾個保存庫、保存庫支援哪些區域，以及哪些應用程式使用這些保存庫，保存庫均提供單一介面讓您清楚了解。 |

只要擁有 Azure 訂用帳戶，任何人都可以建立和使用金鑰保存庫。 雖然金鑰保存庫有益於開發人員和安全性系統管理員，但管理組織其他 Azure 服務的組織系統管理員也可以實作並管理金鑰保存庫。 例如，此系統管理員可以使用 Azure 訂用帳戶登入、建立組織要用來儲存金鑰的保存庫，然後負責執行營運工作，例如：

- 建立或匯入金鑰或密碼
- 撤銷或刪除金鑰或密碼
- 授權使用者或應用程式存取金鑰保存庫，以便他們管理或使用其金鑰和密碼
- 設定金鑰使用方法 (例如，簽署或加密)
- 監視金鑰使用方法

此系統管理員接著可以為開發人員提供可從其應用程式進行呼叫的 URI，並為其安全性系統管理員提供金鑰使用方法記錄資訊。 

![Azure 金鑰保存庫運作方式的概觀][1]

開發人員也可透過使用 API 直接管理金鑰。 如需詳細資訊，請參閱 [金鑰保存庫開發人員指南](key-vault-developers-guide.md)。

## <a name="next-steps"></a>後續步驟

了解如何[保護您的保存庫](key-vault-secure-your-key-vault.md)

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
大部分地區均提供 Azure 金鑰保存庫。 如需詳細資訊，請參閱 [金鑰保存庫價格頁面](https://azure.microsoft.com/pricing/details/key-vault/)。
