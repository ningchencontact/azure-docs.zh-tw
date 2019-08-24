---
title: Azure 澳大利亞中以角色為基礎的存取控制和 Privileged Identity Management
description: 根據澳大利亞政府原則、法規及法規的特定需求, 在澳大利亞地區內執行角色型存取控制和 Privileged Identity Management 的指導方針。
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: e2a94f82e4830bd1e9c96039f5ef8fe6546b0d0b
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982668"
---
# <a name="role-based-access-control-rbac-and-privileged-identity-management-pim"></a>以角色為基礎的存取控制 (RBAC) 和 Privileged Identity Management (PIM)

管理系統管理許可權是確保任何 IT 環境中安全性的重要步驟。 藉由使用最低許可權安全性來限制系統管理許可權, 是[ACSC ISM](https://acsc.gov.au/infosec/ism/index.htm)的需求, 並構成 ACSC 第[8](https://www.acsc.gov.au/infosec/mitigationstrategies.htm)項安全性建議清單的一部分。

Microsoft 提供一套控制措施, 可在 Microsoft Azure 內即時執行並僅限存取。 瞭解這些控制項對於雲端中的有效安全性狀態而言, 是不可或缺的。 本指南將提供控制項本身的總覽, 以及執行它們時的重要設計考慮。

## <a name="role-based-access-control-rbac"></a>角色型存取控制 (RBAC)

以角色為基礎的存取控制是管理 Microsoft Azure 內所有資源的存取權的核心, 以及 Azure Active Directory (Azure AD) 的管理。 RBAC 可以與 Azure 中提供的一些互補功能一起執行。 本文著重于使用 Azure 管理群組、Azure Active Directory 群組和 Azure Privileged Identity Management (PIM) 來執行有效的 RBAC。

概括而言, 執行 RBAC 需要三個元件:

![RBAC-總覽](media/rbac-overview.png)

* **安全性主體**:安全性主體可以是下列任何一項:使用者、群組、[服務主體](https://docs.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals)或[受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。 安全性主體應使用 Azure Active Directory 群組指派許可權。

* **角色定義**:角色定義 (也稱為「角色」 (Role)) 是許可權的集合。 這些許可權會定義指派給角色定義的安全性主體可以執行的作業。 這項功能是由 Azure 資源角色和 Azure Active Directory 系統管理員角色所提供。 Azure 隨附一組內建角色 (連結), 可以使用自訂角色來增強。

* **範圍**：範圍是角色定義適用的一組 Azure 資源。 您可以使用 Azure 管理群組將 azure 角色指派給 Azure 資源。

這三個元件結合, 以將角色定義中定義的存取權授與所有落在 Azure 管理群組範圍下的資源, 這稱為「角色指派」。 可以將多個角色定義指派給安全性主體, 而且可以將多個安全性主體指派給單一範圍。

### <a name="azure-active-directory-groups"></a>Azure Active Directory 群組

指派許可權給個人或小組時, 應該盡可能將指派連結至 Azure Active Directory 群組, 而不是直接指派給有問題的使用者。 這是繼承自內部部署 Active Directory 的建議做法。 可能的話, 請為每個小組建立 Azure Active Directory 群組, 這是您所建立的 Azure 管理群組邏輯結構互補的關係。

在混合式雲端案例中, 內部部署 Windows Server Active Directory 安全性群組可以同步處理至您的 Azure Active Directory 實例。 如果您已經使用這些 Windows Server Active Directory 安全性群組來執行 RBAC 內部部署, 則這些群組一旦同步處理之後, 就可以用來為您的 Azure 資源執行 RBAC。 否則, 可以將您的雲端環境視為全新的平板電腦, 以設計和實施以您的 Azure Active Directory 執行為基礎的健全的版權管理計畫。

### <a name="azure-resource-roles-versus-azure-active-directory-administrator-roles"></a>Azure 資源角色與 Azure Active Directory 系統管理員角色的比較

Microsoft Azure 提供適用于[Azure 資源](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)和[Azure Active Directory 系統管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的各種內建角色。 這兩種類型的角色都會提供對 Azure 資源或 Azure AD 系統管理員的特定細微存取權。 請務必注意, Azure 資源角色無法用來提供 Azure AD 的系統管理存取權, 而且 Azure AD 角色不會提供對 Azure 資源的特定存取權。

可以使用內建角色指派給 Azure 資源的一些存取類型範例如下:

* 允許一位使用者管理訂用帳戶中的虛擬機器，而另一位使用者管理虛擬網路
* 允許 DBA 群組管理訂用帳戶中的 SQL 資料庫
* 允許使用者管理資源群組中的所有資源，例如虛擬機器、網站和子網路
* 允許應用程式存取資源群組中的所有資源

可以為 Azure AD 管理指派的存取類型範例包括:

* 允許技術服務人員重設使用者密碼
* 允許員工邀請外部使用者使用 Azure AD 實例進行 B2B 共同作業
* 允許系統管理人員讀取登入和審核報告的許可權
* 允許員工管理所有使用者和群組, 包括重設密碼。

請務必花點時間瞭解內建角色所提供的允許動作完整清單, 以確保不會授與非過度的存取權。 內建角色及其提供的存取權清單不斷演進, 您可以藉由查看上方連結的檔, 或使用 Azure PowerShell Cmdlet, 來查看角色及其定義的完整清單:

```PowerShell
PS C:\> Get-AzRoleDefinition

Name             : AcrDelete
Id               : <<RoleID>>
IsCustom         : False
Description      : acr delete
Actions          : {Microsoft.ContainerRegistry/registries/artifacts/delete}
NotActions       : {}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
...
```

或 Azure CLI 命令:

```bash
PS C:\> az role definition list
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "acr delete",
    "id": "/subscriptions/49b12d1b-4030-431c-8448-39056021c4ab/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "permissions": [
      {
        "actions": [
          "Microsoft.ContainerRegistry/registries/artifacts/delete"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "AcrDelete",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  },
...
```

您也可以視需要建立自訂的 Azure 資源角色。 您可以在 Azure 入口網站、透過 PowerShell 或 Azure CLI 建立這些自訂角色。 建立自訂角色時, 請務必確保角色的用途是唯一的, 而且它的函式尚未由現有的 Azure 資源角色提供。 這可減少持續進行的管理複雜性, 並降低接收不必要許可權之安全性主體的風險。 其中一個範例會建立位於內建 Azure 資源角色「虛擬機器參與者」和「虛擬機器系統管理員登入」之間的自訂 Azure 資源角色。

自訂角色可以根據現有的「參與者」角色, 授與下列存取權:

| Azure 資源 | 存取層級 |
| --- | --- |
| 虛擬機器 | 可以管理但無法存取 |
| 連接至 VM 的虛擬網路 | 無法存取 |
| 連接至 VM 的儲存體 | 無法存取 |
|

自訂角色可以保留這項基本存取權, 但允許指定的使用者有一些基本的額外許可權, 以修改虛擬機器的網路設定。

Azure 資源角色也具有能夠透過 Azure 管理群組指派給資源的優點。

### <a name="azure-management-groups"></a>Azure 管理群組

組織可以使用 azure 管理群組來管理 Azure 租用內所有訂用帳戶及其資源的角色指派。 Azure 管理群組的設計可讓您建立管理階層, 包括在 Azure 中以階層方式對應組織結構的能力。 以個別邏輯實體的形式建立組織業務單位, 可讓您根據每個小組的特定需求, 在組織內套用許可權。 Azure 管理群組可用來定義一層深度最多六個層級的管理階層。

![管理群組](media/management-groups.png)

Azure 管理群組會對應至 azure 租使用者內的 Azure 訂用帳戶。 這可讓組織隔離屬於特定業務單位的 Azure 資源, 並提供對成本管理和許可權指派的細微控制層級。

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Microsoft 已透過 Azure Privileged Identity Management 實作為即時 (JIT) 和足夠的存取權 (JEA)。 此服務可讓系統管理人員控制、管理及監視 Azure 資源的特殊許可權存取。 PIM 可讓系統管理人員將安全性主體設為「合格」角色, 讓使用者透過 Azure 入口網站或 PowerShell Cmdlet 來要求啟用角色。 根據預設, 角色指派可在1到72小時的期間內啟用。 如有需要, 使用者可以要求其角色指派的擴充功能, 以及允許角色指派永久存在的選項。 (選擇性) 當使用者要求啟用其符合資格的角色時, 可以強制執行多重要素驗證的需求。 一旦角色啟用的配置期間過期, 安全性主體就不再具有該角色所授與的特殊許可權存取權。

使用 PIM 可防止在不使用即時存取或不會執行許可權指派之例行審核的環境中可能發生的一般許可權指派問題。 其中一個常見的問題是, 在需要更高許可權的工作完成之後, 將會忘記並保留較高的許可權。 另一個問題是在設定其他類似的安全性主體時, 透過複製指派給安全性主體的存取權, 在環境內產生較高的許可權。

## <a name="key-design-considerations"></a>主要設計考量

使用強制最低許可權安全性來設計 RBAC 策略時, 應考慮下列安全性需求:

* 已驗證特殊許可權存取的要求
* 系統管理許可權限制為執行特定職責所需的最低存取權
* 系統管理許可權限制在執行特定職責所需的最短時間內
* 已授與系統管理許可權的定期審查

設計 RBAC 策略的程式需要詳細審查商務功能, 以瞭解不同商務角色之間存取的差異, 以及需要更高許可權的工作類型和頻率。 「備份操作員」、「安全性系統管理員」和「審核者」之間的功能差異, 將會在不同的時間點, 需要不同的存取層級, 並有不同的持續性審核層級。

## <a name="validate-requests-for-access"></a>驗證存取要求

必須明確核准較高的許可權。 若要支援這種情況, 必須開發核准程式, 以及負責驗證是否所有額外許可權要求都合法的適當人員。 Privileged Identity Management 提供了核准角色指派的多個選項。 角色啟用要求可以設定為允許自我核准或閘道, 並要求提名核准者手動檢查和核准所有角色啟用要求。 啟用要求也可以設定為要求包含額外的支援資訊, 例如票證號碼。

### <a name="restrict-privilege-based-on-duties"></a>根據職責限制許可權

限制授與安全性主體的許可權層級是很重要的, 因為過度指派許可權是常見的 IT 安全性攻擊媒介。 要管理的資源類型和負責的小組必須經過評估, 才能指派每日責任所需的最低許可權層級。 超過每日責任所需的其他許可權, 應該只會被授與執行特定工作所需的一段時間。 其中一個範例是提供客戶系統管理員的「參與者」存取權, 但允許他們針對需要暫時高階存取權的特定工作, 要求 Azure 資源的「擁有者」許可權。

這可確保每個個別的系統管理員在最短的時間內, 只能有更高的存取權。 遵循這些實務可減少任何組織 IT 基礎結構的整體攻擊面。

### <a name="regular-evaluation-of-administrative-privilege"></a>定期評估系統管理許可權

請務必定期審核環境中的安全性主體, 以確保目前已指派正確的許可權層級。 Microsoft Azure 提供一些方法來審查和評估指派給 Azure 安全性主體的許可權。 Privileged Identity Management 可讓系統管理人員定期執行授與安全性主體之角色的「存取權審查」。 可以進行存取權審查, 以同時審核 Azure 資源角色指派和 Azure Active Directory 系統管理角色指派。 您可以使用下列屬性來設定存取權審查:

* **審查名稱並審查開始和結束日期**:審查應設定為夠久, 供提名使用者完成。

* **要檢查的角色**:每個存取權審查著重于單一 Azure 角色。

* **提名審核者**:執行檢閱的選項有三個。 您可以將檢閱指派給其他人來完成、可以自行進行檢閱，或者可以讓每個使用者檢閱自己的存取權。

* **要求使用者提供存取權的原因**:完成存取權審查時, 使用者可能必須輸入維護其許可權層級的原因。

您可以透過 Azure 入口網站中的儀表板, 隨時監視暫止的存取評論進度。 在存取權審查完成之前, 存取所要檢查的角色將保持不變。 您也可以在指定的時段內, 對所有 PIM 使用者指派和啟動進行[審核](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-use-audit-log)。

## <a name="next-steps"></a>後續步驟

請參閱[Azure 澳大利亞的系統監視](system-monitor.md)文章。
