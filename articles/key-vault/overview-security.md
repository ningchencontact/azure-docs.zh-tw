---
title: Azure Key Vault 安全性 |Microsoft Docs
description: 針對 Azure Key Vault、金鑰和祕密進行存取權限管理。 包含 Key Vault 的驗證和授權模型以及如何保護金鑰保存庫。
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: barclayn
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 43847b53fbf84fe42be3efdbd647767904a05fb8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013777"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault 的安全性

您需要保護加密金鑰和祕密，例如憑證、 連接字串和雲端中的密碼，讓您使用 Azure 金鑰保存庫。 因為您要儲存機密和業務關鍵資料，您需要採取的步驟來最大化您的保存庫和其中儲存資料的安全性。 本文將介紹一些設計您的 Azure Key Vault 安全性時，您應考量的概念。

## <a name="identity-and-access-management"></a>身分識別和存取管理

當您在 Azure 訂用帳戶中建立金鑰保存庫時，它會自動與該訂用帳戶的 Azure AD 租用戶建立關聯。 嘗試管理，或從保存庫擷取內容的任何人都必須由 Azure AD 進行驗證。

- 驗證會建立呼叫者的身分識別。
- 授權則會判斷呼叫端可以執行哪些作業。 金鑰保存庫中的授權使用的組合[角色型存取控制](../role-based-access-control/overview.md)(RBAC) 和 Azure 金鑰保存庫存取原則。

### <a name="access-model-overview"></a>存取模型概觀

保存庫的存取都會透過兩個介面或平面。 這些平面是管理平面和資料平面。

- *管理平面*是其中您管理金鑰保存庫本身，而它是用來建立和刪除保存庫的介面。 您也可以讀取金鑰保存庫內容，並管理存取原則。
- *資料平面*可讓您能夠使用金鑰保存庫中儲存的資料。 您可以新增、刪除和修改金鑰、祕密和憑證。

若要存取金鑰保存庫中任一個平面，必須進行驗證與授權 （使用者或應用程式） 的所有呼叫端。 兩個平面都會使用 Azure Active Directory (Azure AD) 來進行驗證。 至於授權，管理平面會使用角色型存取控制 (RBAC)，資料平面則會使用 Key Vault 存取原則。

這兩個平面的單一驗證機制模型有幾個優點：

- 組織可以集中控制其組織內所有金鑰保存庫的存取權。
- 使用者如果離職，就會立即失去組織中所有金鑰保存庫的存取權。
- 組織可以使用來自訂驗證選項在 Azure AD 中，例如若要啟用 multi-factor authentication，以提升安全性

### <a name="managing-administrative-access-to-key-vault"></a>管理系統管理存取金鑰保存庫

當您在資源群組中建立金鑰保存庫時，您會管理使用 Azure AD 的存取。 您可以對使用者或群組授與在資源群組中管理金鑰保存庫的能力。 您可以藉由指派適當的 RBAC 角色，授與特定範圍層級的存取。 若要對使用者授與管理金鑰保存庫的權限，您可以在特定範圍對使用者指派預先定義的 `key vault Contributor` 角色。 您可以對 RBAC 角色指派下列範圍層級：

- 訂用帳戶：在訂用帳戶層級指派的 RBAC 角色，會套用至該訂用帳戶內的所有資源群組和資源。
- **资源组**：在資源群組層級指派的 RBAC 角色，會套用至該資源群組內的所有資源。
- **特定資源**：針對特定資源指派的 RBAC 角色，則會套用至該資源。 在此情況下，資源會是特定的金鑰保存庫。

有數個預先定義的角色。 如果預先定義的角色不符合您的需求，您可以定義您自己的角色。 如需詳細資訊，請參閱[RBAC：內建角色](../role-based-access-control/built-in-roles.md)。

> [!IMPORTANT]
> 如果使用者具有金鑰保存庫管理平面的 `Contributor` 權限，就可以透過設定 Key Vault 存取原則，對自己授與資料平面的存取權。 因此，請嚴格控制誰可以有金鑰保存庫的 `Contributor` 角色存取權。 請確定只有獲得授權的人員可以存取和管理您的金鑰保存庫、金鑰、祕密和憑證。

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>控制對金鑰保存庫資料的存取

金鑰保存庫存取原則權限授與個別金鑰、 密碼或憑證。 您只能對使用者授與存取金鑰 (而非祕密) 的權限。 在保存庫層級管理金鑰、 祕密和憑證的存取權限。

> [!IMPORTANT]
> Key Vault 存取原則不支援細微物件層級的權限，例如特定金鑰、祕密或憑證。 對使用者授與金鑰的建立和刪除權限時，其便可對該金鑰保存庫的所有金鑰執行這些作業。

若要設定金鑰保存庫的存取原則，請使用 [Azure 入口網站](https://portal.azure.com/)、[Azure CLI](../cli-install-nodejs.md)、[Azure PowerShell](/powershell/azureps-cmdlets-docs) 或 [Key Vault 管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx)。

您可以使用 [Azure Key Vault 的虛擬網路服務端點](key-vault-overview-vnet-service-endpoints.md)來限制資料平面的存取。 您可以設定[防火牆和虛擬網路規則](key-vault-network-security.md)來額外加上一層安全性。

## <a name="network-access"></a>網路存取

您可以減少您的保存庫公開藉由指定哪些 IP 位址才能存取它們。 Azure Key Vault 的虛擬網路服務端點可讓您將存取權限制為指定的虛擬網路。 這些端點也可讓您將存取權限制為 IPv4 (網際網路通訊協定第 4 版) 位址範圍的清單。 任何從這些來源之外連線到金鑰保存庫的使用者都會被拒絕存取。

在防火牆後規則才會生效，使用者只能讀取的資料從 Key Vault 時其要求源自允許的虛擬網路或 IPv4 位址範圍。 這也適用於從 Azure 入口網站存取 Key Vault。 雖然使用者可以從 Azure 入口網站瀏覽金鑰保存庫，但是如果其用戶端電腦不在允許的清單中，他們就無法列出金鑰、祕密或憑證。 這也會影響其他 Azure 服務的 [金鑰保存庫選擇器]。 使用者可以看到金鑰保存庫清單，但是如果防火牆規則阻止其用戶端電腦，則不會列出金鑰。

如需有關 Azure 金鑰保存庫的網路位址檢閱[Azure 金鑰保存庫的虛擬網路服務端點](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>監視

金鑰保存庫記錄會儲存在您的保存庫上執行之活動的相關資訊。 金鑰保存庫記錄：

- 所有經過驗證的 REST API 要求，包括失敗的要求
  - 金鑰的作業保存庫本身。 這些作業包括建立、 刪除、 設定存取原則，及更新等標記的金鑰保存庫屬性。
  - 金鑰保存庫中金鑰和密碼的作業包括：
    - 建立、 修改或刪除這些金鑰或祕密。
    - 簽署、 驗證、 加密、 解密、 包裝和解除包裝金鑰，讓祕密及清單的金鑰和祕密 （和其版本）。
- 產生 401 回應的未經驗證要求。 範例包括沒有的要求的持有人權杖，格式不正確或已過期，或具有無效的語彙基元。

金鑰保存庫作業後 10 分鐘內，可以存取記錄資訊。 它由您管理您的記錄檔，在儲存體帳戶。 

- 請使用標準的 Azure 存取控制方法限制可存取記錄的人員，藉此來保護記錄。
- 刪除不想繼續保留在儲存體帳戶中的記錄。

如需安全地管理儲存體的建議檢閱帳戶[Azure 儲存體安全性指南](../storage/common/storage-security-guide.md)

## <a name="next-steps"></a>後續步驟

- [Azure 金鑰保存庫的虛擬網路服務端點](key-vault-overview-vnet-service-endpoints.md)
- [RBAC：內建角色](../role-based-access-control/built-in-roles.md)
- [Azure 金鑰保存庫的虛擬網路服務端點](key-vault-overview-vnet-service-endpoints.md)
