---
title: 保護 Azure Key Vault | Microsoft Docs
description: 針對 Azure Key Vault、金鑰和祕密進行存取權限管理。 包含 Key Vault 的驗證和授權模型以及如何保護金鑰保存庫。
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: ambapat
ms.openlocfilehash: 67f24bbccdd2dcf5cca09e09557d7ebebd0a5c2d
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891073"
---
# <a name="secure-your-key-vault"></a>保護您的金鑰保存庫
Azure Key Vault 是用來保護加密金鑰和祕密 (例如憑證、連接字串和密碼) 的雲端服務。 由於這項資料相當敏感且攸關業務，所以您必須保護金鑰保存庫的存取權，只讓獲得授權的應用程式和使用者取得存取權。 本文提供 Key Vault 存取模型的概觀。 文中會說明驗證和授權，並說明如何保護存取權。

## <a name="overview"></a>概觀
金鑰保存庫的存取權可透過兩個不同介面來控制︰管理平面和資料平面。 
**管理平面**會用來管理保存庫，例如 - 建立保存庫、更新保存庫、刪除保存庫。 
**資料平面**會處理保存庫內的祕密，也就是建立、更新、刪除及讀取保存庫內的祕密。 這兩個平面都需要先經過適當的驗證和授權，呼叫者 (使用者或應用程式) 才能取得金鑰保存庫的存取權。 驗證會建立呼叫者的身分識別，授權則會判斷呼叫者可以執行的作業。

管理平面和資料平面都會使用 Azure Active Directory 來進行驗證。 至於授權，管理平面使用角色型存取控制 (RBAC)，資料平面則使用金鑰保存庫存取原則。

以下是所涵蓋主題的簡短概觀︰

[使用 Azure Active Directory 進行驗證](#authentication-using-azure-active-directory) - 本節說明呼叫者如何使用 Azure Active Directory 進行驗證，以透過管理平面和資料平面存取金鑰保存庫。 

針對驗證，兩個平台都使用 Azure Active Directory (Azure AD)。 至於授權，管理平面使用角色型存取控制 (RBAC)，資料平面則使用 Key Vault 存取原則。

## <a name="authenticate-by-using-azure-active-directory"></a>使用 Azure Active Directory 進行驗證
當您在 Azure 訂用帳戶中建立金鑰保存庫時，它會自動與該訂用帳戶的 Azure AD 租用戶建立關聯。 所有呼叫者都必須在此租用戶中註冊，而且必須經過驗證才能存取金鑰保存庫。 管理平面和資料平面的存取皆適用此需求。 在這兩種情況中，應用程式可透過兩種方式存取 Key Vault︰

* **使用者 + 應用程式存取**：用於代表登入使用者存取 Key Vault 的應用程式。 舉例來說，Azure PowerShell 和 Azure 入口網站便是這類存取。 有兩種方式可以將存取權授與使用者： 
  - 從任何應用程式存取金鑰保存庫。
  - 只在使用者使用特定應用程式時，才能存取 Key Vault (這稱為複合身分識別)。

* **僅限應用程式存取**：用於以精靈服務或背景作業形式執行的應用程式。 該應用程式的身分識別會被授與金鑰保存庫的存取權。

在這兩種應用程式中，應用程式會使用任何[支援的驗證方法](../active-directory/develop/authentication-scenarios.md)向 Azure AD 進行驗證，並取得權杖。 使用何種驗證方法取決於應用程式類型。 然後，應用程式會使用此權杖，對 Key Vault 傳送 REST API 要求。 管理平面要求會透過 Azure Resource Manager 端點路由傳送。 在存取資料平面時，應用程式則會直接與 Key Vault 端點通訊。 如需詳細資訊，請參閱[完整驗證流程](../active-directory/develop/v1-protocols-oauth-code.md)。 

應用程式用來要求權杖的資源名稱取決於應用程式存取哪一個平面。 資源名稱會是管理平面端點或資料平面端點，皆取決於 Azure 環境。 如需詳細資訊，請參閱本文稍後的資料表。

對兩個平面使用一個單一驗證機制會有一些優點：

* 組織可以集中控制其組織內所有金鑰保存庫的存取權。
* 使用者如果離職，就會立即失去組織中所有金鑰保存庫的存取權。
* 組織可以透過 Azure AD 中的選項 (例如，啟用多重要素驗證以提升安全性) 自訂驗證。

## <a name="the-management-plane-and-the-data-plane"></a>管理平面和資料平面
使用管理平面可用來管理 Key Vault 本身。 這包括管理屬性以及設定資料平面存取原則等作業。 使用資料平面來新增、刪除、修改和使用 Key Vault 中儲存的金鑰、祕密和憑證。

透過下表所列的不同端點來存取管理平面和資料平面介面。 資料表中的第二個資料行描述不同 Azure 環境中這些端點的 DNS 名稱。 第三個資料行描述可從每個存取平面執行的作業。 每個存取平面也有它自己的存取控制機制。 您可以使用 Azure Resource Manager 角色型存取控制 (RBAC) 來設定管理平面存取控制。 您可以使用 Key Vault 存取原則來設定資料平面存取控制。

| 存取平面 | 存取端點 | 作業 | 存取控制機制 |
| --- | --- | --- | --- |
| 管理平面 |**全域：**<br> management.azure.com:443<br><br> **Azure China 21Vianet：**<br> management.chinacloudapi.cn:443<br><br> **Azure 美國政府︰**<br> management.usgovcloudapi.net:443<br><br> **Azure 德國︰**<br> management.microsoftazure.de:443 |建立/讀取/更新/刪除 Key Vault <br> 設定 Key Vault 的存取原則<br>設定 Key Vault 的標籤 |Azure Resource Manager RBAC |
| 資料平面 |**全域：**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet：**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure 美國政府︰**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure 德國︰**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |針對金鑰︰解密、加密、解除包裝金鑰、包裝金鑰、驗證、簽署、取得、列出、更新、建立、匯入、刪除、備份、還原<br><br> 針對密碼︰取得、列出、設定、刪除 |Key Vault 存取原則 |

管理平面和資料平面的存取控制在運作上互不相關。 比方說，如果您想要對應用程式授與金鑰保存庫中金鑰的使用權限，您只需要對資料平面授與存取權。 您可透過 Key Vault 存取原則來授與存取權。 相反地，需要讀取 Key Vault 屬性和標記、但不需要存取資料 (金鑰、祕密或憑證) 的使用者，則只需要管理平面存取權。 您可以透過使用 RBAC 將讀取權限指派給使用者，來授與存取權。

## <a name="management-plane-access-control"></a>管理平面存取控制
管理平面是由會影響金鑰保存庫本身的作業所組成，例如：

- 建立或刪除金鑰保存庫。
- 取得訂用帳戶中的保存庫清單。
- 擷取 Key Vault 屬性 (例如 SKU 和標記)。
- 設定 Key Vault 存取原則，以控制使用者和應用程式對於金鑰和祕密的存取權。

管理平面存取控制會使用 RBAC。  

### <a name="role-based-access-control-rbac"></a>角色型存取控制 (RBAC)
每一個 Azure 訂用帳戶都具有 Azure AD 執行個體。 您可以為來自該目錄的使用者、群組和應用程式授與存取權，以便在使用 Azure Resource Manager 部署模型的 Azure 訂用帳戶中管理資源。 此類型的存取控制稱為 RBAC。 若要管理此存取權，您可以使用 [Azure 入口網站](https://portal.azure.com/)、[Azure CLI 工具](../cli-install-nodejs.md)、[PowerShell](/powershell/azureps-cmdlets-docs) 或 [Azure Resource Manager REST API](https://msdn.microsoft.com/library/azure/dn906885.aspx)。

您可以使用 Azure AD 在資源群組中建立金鑰保存庫，並控制對管理平面的存取權。 例如，您可以對使用者或群組授與在資源群組中管理金鑰保存庫的能力。

您可以藉由指派適當的 RBAC 角色，在特定範圍對使用者、群組和應用程式授與存取權。 例如，若要對使用者授與管理金鑰保存庫的權限，您可以在特定範圍對此使用者指派預先定義的「金鑰保存庫參與者」角色。 在此案例中，範圍會是訂用帳戶、資源群組或特定金鑰保存庫。 在訂用帳戶層級指派的角色，會套用至該訂用帳戶內的所有資源群組和資源。 在資源群組層級指派的角色，會套用至該資源群組內的所有資源。 針對特定資源指派的角色，則會套用至該資源。 有數個預先定義的角色 (請參閱 [RBAC：內建角色](../role-based-access-control/built-in-roles.md))。 如果預先定義的角色不符合您的需求，您可以定義您自己的角色。

> [!IMPORTANT]
> 請注意，如果使用者具有 Key Vault 管理平面的參與者權限 (RBAC)，則可以透過設定 Key Vault 存取原則，對自己授與資料平面的存取權。 因此，您應該嚴格控制誰有金鑰保存庫的「參與者」存取權。 請確定只有獲得授權的人員可以存取和管理您的金鑰保存庫、金鑰、祕密和憑證。
> 
> 

## <a name="data-plane-access-control"></a>資料平面存取控制
Key Vault 資料平面作業會套用至預存物件，例如金鑰、祕密和憑證。 金鑰作業包括建立、匯入、更新、列出、備份和還原金鑰。 密碼編譯作業包括簽署、驗證、加密、解密、包裝、解除包裝、設定標記以及設定其他金鑰屬性。 同樣地，祕密的作業包括取得、設定、列出和刪除。

您可以藉由設定金鑰保存庫的存取原則來授與資料平面的存取權。 使用者、群組或應用程式必須具有管理平面的參與者權限，金鑰保存庫才能設定該金鑰保存庫的存取原則。 您可以對使用者、群組或應用程式授與權限，來為金鑰保存庫中的金鑰或密碼執行特定作業。 Key Vault 最多可支援 1024 個存取原則項目。 若要對數名使用者授與資料平面存取權，請建立 Azure AD 安全性群組並在該群組中新增使用者。

### <a name="key-vault-access-policies"></a>Key Vault 存取原則
Key Vault 存取原則可分別授與金鑰、祕密和憑證的權限。 例如，您可以只提供金鑰存取權給使用者，而不提供祕密的權限。 金鑰、祕密或憑證的存取權限是在保存庫層級。 Key Vault 存取原則不支援細微物件層級的權限，例如特定金鑰、祕密或憑證。 您可以使用 [Azure 入口網站](https://portal.azure.com/)、[Azure CLI 工具](../cli-install-nodejs.md)、[PowerShell](/powershell/azureps-cmdlets-docs) 或 [Key Vault 管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx) 來設定金鑰保存庫的存取原則。

> [!IMPORTANT]
> Key Vault 存取原則會套用在保存庫層級。 例如，對使用者授與金鑰的建立和刪除權限時，她就可以對該金鑰保存庫的所有金鑰執行這些作業。

除了使用存取原則，您也可以使用 [Azure Key Vault 的虛擬網路服務端點](key-vault-overview-vnet-service-endpoints.md)來限制資料平面的存取。 您可以設定[防火牆和虛擬網路規則](key-vault-network-security.md)來額外加上一層安全性。

## <a name="example"></a>範例
假設您正在開發的應用程式會針對 SSL 使用憑證、使用 Azure 儲存體來儲存資料，並使用 RSA 2048 位元金鑰來進行簽署作業。 假設此應用程式會在 Azure 虛擬機器 (或虛擬機器擴展集) 中執行。 您可以使用金鑰保存庫來儲存所有應用程式祕密，並儲存應用程式用來向 Azure AD 進行驗證的啟動程序憑證。

以下是所儲存金鑰和祕密的類型摘要：

* **SSL 憑證**：用於 SSL。
* **儲存體金鑰**：用於取得儲存體帳戶的存取權。
* **RSA 2048 位元金鑰**：用於簽署作業。
* **啟動程序憑證**：用於向 Azure AD 進行驗證。 獲得存取權後，您便可以擷取儲存體金鑰，以及使用 RSA 金鑰進行簽署。

現在讓我們看看負責管理、部署和稽核此應用程式的人員。 在此範例中，我們會使用三種角色。

* **安全性小組**：通常是 CSO (首席安全官) 辦公室的 IT 人員或具有同等職權的人員。 這個小組負責妥善保管祕密。 例如，SSL 憑證、用於簽署作業的 RSA 金鑰、連接字串和儲存體帳戶金鑰。
* **開發人員/操作員**：這些人負責開發應用程式，然後將它部署在 Azure 中。 一般而言，他們不屬於安全性小組，因此不應具備敏感性資料 (例如 SSL 憑證與 RSA 金鑰) 的存取權。 其所部署的應用程式才應具備這些物件的存取權。
* **稽核員**：通常是一組不同人員，與開發人員和一般 IT 人員有所區隔。 他們的責任是檢閱憑證、金鑰和祕密的使用與維護情形，以確保各項作業符合安全性標準。 

還有一個不在此應用程式範圍內，但因為有所關聯而在此提及的角色。 該角色便是訂用帳戶 (或資源群組) 管理員。 訂用帳戶管理員會設定安全性小組的初始存取權限。 訂用帳戶管理員會使用資源群組 (內含此應用程式所需的資源)，將存取權授與安全性小組。

現在我們來看看，每個角色會在此應用程式內容中執行的動作。

* **安全性小組**
  * 建立金鑰保存庫。
  * 開啟 Key Vault 記錄。
  * 新增金鑰/祕密。
  * 建立金鑰備份以用於災害復原。
  * 設定 Key Vault 存取原則，對使用者和應用程式授與執行特定作業的權限。
  * 定期輪替金鑰/祕密。
* **開發人員/操作員**
  * 從安全性小組取得啟動程序和 SSL 憑證 (指紋)、儲存體金鑰 (密碼 URI) 和簽署金鑰 (金鑰 URI) 的參考。
  * 開發和部署以程式設計方式存取金鑰和密碼的應用程式。
* **稽核員**
  * 檢閱使用記錄，確認金鑰/密碼的使用是否適當，以及是否符合資料安全性標準。

現在讓我們看看，每個角色和應用程式需要哪些存取權限才能執行對其指派的工作。 

| 使用者角色 | 管理平面權限 | 資料平面權限 |
| --- | --- | --- |
| 安全性小組 |Key Vault 參與者 |金鑰︰備份、建立、刪除、取得、匯入、列出、還原 <br> 密碼︰全部 |
| 開發人員/操作員 |Key Vault 部署權限，以便其所部署的虛擬機器可以擷取金鑰保存庫中的密碼。 |None |
| 稽核員 |None |金鑰︰列出<br>密碼︰列出 |
| 應用程式 |None |金鑰︰簽署<br>密碼︰取得 |

> [!NOTE]
> 稽核員需要金鑰和祕密的列出權限，以便可以檢查記錄檔中不會顯現的金鑰和祕密屬性。 這些屬性包括標籤、啟用日和到期日。
> 
> 

除了 Key Vault 的權限外，這三個角色還需要其他資源的存取權。 例如，若要能夠部署 VM (或 Azure App Service 的 Web Apps 功能)，開發人員和操作員也需要這些資源類型的「參與者」存取權。 稽核員需要儲存 Key Vault 記錄所在儲存體帳戶的「讀取」權限。

因為本文的重點是保護金鑰保存庫的存取權，所以我們只會示範與此主題相關的概念。 與部署憑證和以程式設計方式存取金鑰和祕密等作業有關的詳細資料，則會另外討論。 例如：

- 將儲存於 Key Vault 中的憑證部署至 VM，涵蓋於[從客戶管理的金鑰保存庫將憑證部署至 VM](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (部落格文章)。
- [Azure Key Vault 用戶端範例下載](https://www.microsoft.com/download/details.aspx?id=45343)會示範如何使用啟動程序憑證，向 Azure AD 驗證以存取金鑰保存庫。

您可以使用 Azure 入口網站授與大部分的存取權限。 若要授與細微權限，則請使用 Azure PowerShell 或 CLI 來達成所要的結果。 

下列 PowerShell 程式碼片段假設︰

* Azure AD 系統管理員已建立安全性群組來代表三種角色 (Contoso Security Team、Contoso App Devops 和 Contoso App Auditors)。 系統管理員也已將使用者新增至所屬群組。
* **ContosoAppRG** 是所有資源所在的資源群組。 **contosologstorage** 是記錄檔的儲存位置。 
* 金鑰保存庫 **ContosoKeyVault** 和用於 Key Vault 記錄的儲存體帳戶 **contosologstorage** 必須位在相同的 Azure 位置。

首先，訂用帳戶管理員會指派 `key vault Contributor` 和 `User Access Administrator` 角色給安全性小組。 這些角色可讓安全性小組管理其他資源的存取權，以及管理資源群組 ContosoAppRG 中的金鑰保存庫。

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

下列指令碼示範安全性小組如何建立金鑰保存庫，並設定記錄和存取權限。 如需 Key Vault 存取原則權限的詳細資訊，請參閱[關於 Azure Key Vault 金鑰、祕密和憑證](about-keys-secrets-and-certificates.md)。

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

所定義的自訂角色只能指派給 `ContosoAppRG` 資源群組建立所在的訂用帳戶。 如果相同的自訂角色會用於其他訂用帳戶中的其他專案，則其範圍可以新增更多訂用帳戶。

可為開發人員和操作員指派「部署/動作」權限的自訂角色侷限於資源群組。 如此一來，只有在資源群組 `ContosoAppRG` 中建立的 VM 才會具有祕密 (SSL 憑證和啟動程序憑證) 的存取權。 由開發人員和操作員小組成員在其他資源群組中建立的 VM 即使有祕密的 URI，也不會有這些祕密的存取權。

此範例示範簡單案例。 真實案例可能會更複雜，您可以根據需求調整金鑰保存庫的權限。 在我們的範例中，我們假設該安全性小組會提供開發人員和操作員需要在其應用程式中參考的金鑰和密碼參考 (URI 和指紋)。 開發人員和操作員不需要任何資料平面存取權。 此範例的內容著重在保護金鑰保存庫。 若要保護[您的 VM](https://azure.microsoft.com/services/virtual-machines/security/)、[儲存體帳戶](../storage/common/storage-security-guide.md)和其他 Azure 資源，您應提供類似考量。

> [!NOTE]
> 此範例示範生產環境中會如何鎖定 Key Vault 的存取權。 開發人員應該有自己的訂用帳戶或資源群組，並擁有完整權限以便管理他們用來開發應用程式的保存庫、VM 和儲存體帳戶。

強烈建議[設定 Key Vault 防火牆和虛擬網路](key-vault-network-security.md)，以進一步保護金鑰保存庫的存取。

## <a name="resources"></a>資源
* [Azure Active Directory 角色型存取控制](../role-based-access-control/role-assignments-portal.md)
  
* [RBAC：內建角色](../role-based-access-control/built-in-roles.md)
  
* [了解資源管理員部署和傳統部署](../azure-resource-manager/resource-manager-deployment-model.md)
  
* [使用 Azure PowerShell 管理角色型存取控制](../role-based-access-control/role-assignments-powershell.md)
  
* [使用 REST API 管理角色型存取控制](../role-based-access-control/role-assignments-rest.md)
  
* [來自Ignite 且適用於 Microsoft Azure 的角色型存取控制](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  這個 2015 Microsoft Ignite 大會影片討論 Azure 中的存取管理和報告功能。 此外，也會探討使用 Azure AD 保護 Azure 訂用帳戶存取權的最佳做法。
* [使用 OAuth 2.0 和 Azure AD 授權存取 Web 應用程式](../active-directory/develop/v1-protocols-oauth-code.md)
  
* [Key Vault 管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  本文件是可供以程式設計方式管理金鑰保存庫 (包括設定 Key Vault 存取原則) 的 REST API 參考。
* [Key Vault REST API](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
* [金鑰存取控制](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [密碼存取控制](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* 使用 PowerShell [設定](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy)和[移除](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Remove-AzureRmKeyVaultAccessPolicy) Key Vault 存取原則
  
## <a name="next-steps"></a>後續步驟
[設定 Key Vault 防火牆和虛擬網路](key-vault-network-security.md)

如需適用於系統管理員的開始使用教學課程，請參閱 [開始使用 Azure 金鑰保存庫](key-vault-get-started.md)。

如需 Key Vault 使用記錄的詳細資訊，請參閱 [Azure Key Vault 記錄](key-vault-logging.md)。

如需搭配 Azure Key Vault 使用金鑰和密碼的詳細資訊，請參閱[關於金鑰和祕密](https://msdn.microsoft.com/library/azure/dn903623.aspx)。

如果您有關於 Key Vault 的問題，請造訪[論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)。

