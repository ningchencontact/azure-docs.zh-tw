---
title: 了解及使用 Azure 成本管理範圍 |Microsoft Docs
description: 這篇文章可協助您了解適用於 Azure 和如何使用成本管理和 Api 範圍計費和資源管理範圍。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 4e7956e8873b552fcd73c51a51f51d99f21af324
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002945"
---
# <a name="understand-and-work-with-scopes"></a>了解並使用範圍

這篇文章可協助您了解適用於 Azure 和如何使用成本管理和 Api 範圍計費和資源管理範圍。

## <a name="scopes"></a>範圍

A_範圍_是 Azure 資源階層，其中 Azure AD 使用者存取和管理服務中的節點。 大多數 Azure 資源建立並部署到資源群組，也就是訂用帳戶的一部分。 Microsoft 還另外提供上述有專用的角色來管理計費資料的 Azure 訂用帳戶的兩個階層：
- 計費資料，例如付款和發票
- 雲端服務，例如成本和原則控管

範圍可讓您管理計費的資料、 已付款的角色特定、 檢視發票，與進行一般的帳戶管理。 帳單和帳戶的角色會分開管理與使用用於資源管理[Azure RBAC](../role-based-access-control/overview.md)。 清楚區分的目的不同的範圍，包括存取控制的差異，這些指_計費領域_並_RBAC 範圍_分別。

## <a name="how-cost-management-uses-scopes"></a>成本管理如何使用範圍

成本的管理的運作完全範圍上方，可讓組織管理能存取層級的成本，不論是整個的帳單帳戶或單一資源群組的資源。 雖然計費範圍根據您的 Microsoft 合約 （訂用帳戶類型） 而有所不同，RBAC 範圍則否。

## <a name="azure-rbac-scopes"></a>Azure RBAC 範圍

Azure 支援資源管理的三個的範圍。 每個範圍支援管理存取和控管，包括但不是限於，成本管理。

- [**管理群組**](../governance/management-groups/index.md) -最多八個層級，來組織 Azure 訂用帳戶的階層式容器。

    資源類型：[Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **訂用帳戶**-適用於 Azure 資源的主要容器。

    資源類型：[Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**資源群組**](../azure-resource-manager/resource-group-overview.md#resource-groups) -Azure 方案相同的生命週期的相關資源的邏輯群組。 適用於部署並進行一次刪除範例資源。

    資源類型：[Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

管理群組可讓您組織成階層的訂用帳戶。 例如，您可以建立使用管理群組的邏輯組織階層。 然後，在適用於生產和開發/測試工作負載提供小組訂用帳戶。 然後再建立來管理每個子系統或元件的訂用帳戶中的 資源群組。

建立組織階層可讓成本和原則合規性彙總組織。 然後，每個領導者可以檢視和分析其目前的成本。 然後他們可以在其中建立停止不正確的消費模式及最佳化成本，以及在最低層級的 Advisor 建議的預算。

檢視成本，並選擇性地管理成本的設定，例如預算和匯出的存取權授與使用 Azure RBAC 的控管範圍上執行。 您使用 Azure RBAC 授與 Azure AD 使用者和群組存取權執行一組預先定義的特定範圍和下方的角色中所定義的動作。 比方說，指派給管理群組範圍的角色也會授予巢狀的訂用帳戶和資源群組相同的權限。

成本的管理下列範圍的每個支援下列的內建角色：

- [**擁有者**](../role-based-access-control/built-in-roles.md#owner) -可以檢視成本和管理所有項目，包括成本的組態。
- [**參與者**](../role-based-access-control/built-in-roles.md#contributor) -可以檢視成本和管理一切，包括成本組態，但不包括存取控制。
- [**讀取器**](../role-based-access-control/built-in-roles.md#reader) -可以檢視所有項目，包括成本資料和組態，但無法進行任何變更。
- [**成本管理參與者**](../role-based-access-control/built-in-roles.md#cost-management-contributor) -可以檢視成本和管理成本的組態。
- [**成本管理讀者**](../role-based-access-control/built-in-roles.md#cost-management-reader) -可以檢視成本資料和組態。

成本的管理參與者是建議的最低權限角色。 它可以讓使用者建立及管理預算的存取，並將匯出更有效地監視和報告的成本。 成本管理參與者，也可能需要額外的角色，以支援端對端成本管理案例。 請考慮下列情況：

- **處理超過預算時**– 成本管理參與者也需要建立及/或管理自動回應超額部分的動作群組的存取。 請考慮授與[監視參與者](../role-based-access-control/built-in-roles.md#monitoring-contributor)到資源群組，其中包含動作群組時要使用超過預算臨界值。 自動執行特定動作需要其他角色使用，例如自動化和 Azure Functions 的特定服務。
- **成本匯出資料的排程**– 成本管理參與者也需要管理排程的匯出將資料複製到儲存體帳戶的儲存體帳戶存取權。 請考慮授與[儲存體帳戶參與者](../role-based-access-control/built-in-roles.md#storage-account-contributor)匯出其中成本資料的帳戶的資源群組，其中包含儲存體。
- **檢視成本節約建議**– 成本管理 Readers 和 Contributors 沒有存取權的建議預設。 建議存取需要個別資源的 「 讀取 」 權限。 請考慮授與[讀者](../role-based-access-control/built-in-roles.md#reader)或是[服務專屬角色](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)。

## <a name="enterprise-agreement-scopes"></a>Enterprise 合約範圍

Enterprise 合約 (EA) 計費帳戶，也稱為註冊，有下列範圍：

- [**計費帳戶**](../billing/billing-view-all-accounts.md) -代表 EA 註冊。 在這個範圍內，會產生發票。 在這個範圍內，才可購買項目不是使用量為基礎，Marketplace 和保留項目，例如。 它們不在部門或註冊帳戶代表。

    資源類型： `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **部門**-選擇性註冊帳戶的群組。

    資源類型： `Billing/billingAccounts/departments`

- **註冊帳戶**-代表單一帳戶擁有者。 不支援存取權授與給多人。

    資源類型： `Microsoft.Billing/billingAccounts/enrollmentAccounts`

雖然控管範圍限定為單一目錄，並不 EA 計費的範圍。 EA 計費帳戶可能會有跨任意數目的 Azure AD 目錄的訂用帳戶。

EA 計費範圍可支援下列角色：

- **企業系統管理員**– 可計費的帳戶設定和存取管理，可以檢視所有成本，而且可以管理成本組態。 比方說，是低的預算，並將匯出。 在函式、 EA 計費範圍是相同[成本管理參與者 Azure RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-contributor)。
- **企業唯讀使用者**-可以檢視帳單帳戶設定、 成本資料和成本的組態。 比方說，是低的預算，並將匯出。 在函式、 EA 計費範圍是相同[成本管理讀取器 Azure RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-reader)。
- **部門系統管理員**-可以管理部門的設定，例如成本中心和可以存取、 檢視所有的成本和管理成本設定。 比方說，是低的預算，並將匯出。  **DA 檢視費用**帳單帳戶設定必須啟用部門系統管理員和唯讀使用者来查看的成本。 如果**DA 檢視費用**是停用，部門的使用者無法看到任何層級的成本，即使的帳戶或訂用帳戶的擁有者。
- **唯讀使用者的部門**-可以檢視部門設定、 成本資料和成本的組態。 比方說，是低的預算，並將匯出。 如果**DA 檢視費用**是停用，部門的使用者無法看到任何層級的成本，即使的帳戶或訂用帳戶的擁有者。
- **帳戶擁有者**-可以管理 （例如成本中心） 的註冊帳戶設定、 檢視所有成本，以及管理註冊帳戶的成本設定 （例如預算和匯出）。 **AO 檢視費用**帳單帳戶設定必須啟用帳戶擁有者和 RBAC 使用者来查看的成本。

EA 計費帳戶的使用者不需要直接存取發票。 發票都是從外部的大量授權系統。

Azure 訂用帳戶註冊帳戶之下巢狀。 計費的使用者有訂用帳戶和資源群組，也就是在其各自範圍的存取權的成本資料。 它們不需要查看，或在 Azure 入口網站中管理資源的存取權。 計費的使用者可以瀏覽至檢視成本**成本管理 + 計費**在 Azure 入口網站的服務清單中。 然後，他們可以篩選特定的訂用帳戶和報告所需的資源群組的成本。

計費的使用者沒有存取權管理群組，因為它們不明確落在特定的帳單帳戶。 存取必須明確授與給管理群組。 管理群組中所有的巢狀的訂用帳戶中的彙總成本。 不過，它們只包含使用狀況為基礎的購買項目。 它們不包含購買項目，例如保留項目和第三方 Marketplace 供應項目。 若要檢視這些成本，請使用 EA 計費帳戶。

## <a name="individual-agreement-pay-as-you-go-scopes"></a>個別的合約 （隨用隨付） 範圍

隨用隨付 (PAYG) 訂用帳戶，包括相關的型別喜歡免費/試用和開發/測試優惠，沒有明確的計費帳戶範圍。 相反地，每個訂用帳戶有帳戶擁有者或帳戶管理員，例如 EA 帳戶擁有者。

- [**計費帳戶**](../billing/billing-view-all-accounts.md) -表示一個或多個 Azure 訂用帳戶的單一帳戶擁有者。 它目前不支援多位人員或彙總的成本檢視存取權授與存取。

    資源類型：不適用

PAYG 訂用帳戶的帳戶管理員可以檢視和管理計費的資料，例如發票與付款，從[Azure 帳戶中心](https://account.azure.com/subscriptions)。 不過，它們無法檢視成本資料，或在 Azure 入口網站中管理資源。 若要授與存取權的帳戶管理員，使用先前所述的成本管理角色。

不同於 EA，PAYG 訂用帳戶的帳戶管理員可以看到他們在 Azure 入口網站中的發票。 請記住，成本管理讀取器 」 和 「 成本管理參與者 」 角色不提供發票存取權。 如需詳細資訊，請參閱 <<c0> [ 如何授與存取權 PAYG 發票](../billing/billing-manage-access.md#give-access-to-billing)。

## <a name="customer-agreement-scopes"></a>客戶合約範圍

Microsoft 客戶合約計費帳戶有下列範圍：

- **帳單帳戶**-代表多個 Microsoft 產品和服務之客戶合約。 客戶合約計費帳戶功能上與聊天室不同 — EA 註冊。 EA 註冊更密切計費的設定檔。

    資源類型： `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **計費的設定檔**-定義包含在發票的訂用帳戶。 因為這是在產生的發票的範圍會在功能上等同的 EA 註冊，帳單設定檔。 同樣地，未使用量為基礎 （例如服務商場和保留項目） 的購買項目才可用在這個範圍內。 它們不包含在發票區段。

    資源類型： `Microsoft.Billing/billingAccounts/billingProfiles`

- **發票區段**-代表發票或帳單的設定檔中的訂用帳戶的群組。 例如部門的發票區段 — 多人可以存取發票一節。

    資源類型： `Microsoft.Billing/billingAccounts/invoiceSections`

不同於 EA 計費範圍計費帳戶的客戶合約_是_繫結至單一目錄，而且不能有多個 Azure AD 目錄的訂用帳戶。

客戶合約計費範圍可支援下列角色：

- **擁有者**-可以管理計費的設定和存取、 檢視所有成本，以及管理成本的組態。 比方說，是低的預算，並將匯出。 在函數中，此帳單範圍的客戶合約等同[成本管理參與者 Azure RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-contributor)。
- **參與者**-可以管理存取以外的計費設定、 檢視所有成本，以及管理成本組態。 比方說，是低的預算，並將匯出。 在函數中，此帳單範圍的客戶合約等同[成本管理參與者 Azure RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-contributor)。
- **讀取器**-可以檢視計費的設定、 成本資料和成本的組態。 比方說，是低的預算，並將匯出。 在函數中，此帳單範圍的客戶合約等同[成本管理讀取器 Azure RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-reader)。
- **發票 manager** -可以檢視和發票用多少付多少，以及檢視成本資料和設定。 比方說，是低的預算，並將匯出。 在函數中，此帳單範圍的客戶合約等同[成本管理讀取器 Azure RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-reader)。
- **Azure 訂用帳戶的建立者**– 可以建立 Azure 訂用帳戶、 檢視成本，以及管理成本的組態。 比方說，是低的預算，並將匯出。 在函數中，此客戶合約計費的範圍是 EA 註冊的帳戶 「 擁有者 」 角色相同。

Azure 訂用帳戶發票等區段，它們的 EA 註冊帳戶的方式之下巢狀。 計費的使用者有訂用帳戶和其各自範圍下的資源群組的存取權的成本資料。 不過，它們不需要查看，或在 Azure 入口網站中管理資源的存取權。 計費的使用者可以瀏覽至檢視成本**成本管理 + 計費**在 Azure 入口網站的服務清單中。 然後，篩選到特定的訂用帳戶和報告所需的資源群組的成本。

計費的使用者沒有存取權管理群組，因為其不明確會落在計費的帳戶下。 不過，當組織啟用的管理群組，所有訂用帳戶費用會彙總帳單帳戶，根管理群組因為他們兩者都受限於單一目錄。 管理群組只包含以使用量為基礎的購買項目。 購買保留項目等第三方 Marketplace 供應項目不包含在管理群組中。 因此，計費的帳戶和根管理群組可能會報告不同的總計。 若要檢視這些成本，請使用帳單帳戶或個別計費的設定檔。

## <a name="cloud-solution-provider-csp-scopes"></a>雲端解決方案提供者 (CSP) 的範圍

雲端解決方案提供者 (CSP) 合作夥伴不是目前支援的成本管理。 相反地，您可以使用[合作夥伴中心](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)。

## <a name="switch-between-scopes-in-cost-management"></a>在成本管理中的範圍之間切換

在 Azure 入口網站中的所有成本管理檢視都包含**範圍**丸在左上方的檢視。 您可以使用它來快速變更範圍。 按一下 **範圍**丸開啟範圍選擇器。 它會顯示計費的帳戶、 根管理群組中，並不巢狀根管理群組下的任何訂用帳戶。 若要選取的範圍內，按一下 反白顯示，然後按一下 背景**選取**底部。 若要向下切入以巢狀範圍，例如資源群組中的訂用帳戶中，按一下 範圍名稱連結。 若要選取之任何巢狀層級的父範圍，請按一下**選取此選項&lt;範圍&gt;** 頂端的範圍選擇器。

## <a name="identify-the-resource-id-for-a-scope"></a>識別範圍的資源識別碼

當使用成本管理 Api，了解範圍很重要。 您可以使用下列資訊，成本管理 api 建置適當的範圍 URI。

### <a name="billing-accounts"></a>計費帳戶

1. 開啟 Azure 入口網站，然後瀏覽**成本管理 + 計費**在服務清單中。
2. 選取 **屬性**帳單的 帳戶 功能表中。
3. 複製計費的帳戶識別碼。
4. 您的範圍是： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>帳單設定檔

1. 開啟 Azure 入口網站，然後瀏覽**成本管理 + 計費**在服務清單中。
2. 選取 **帳單設定檔**帳單的 帳戶 功能表中。
3. 按一下所需計費的設定檔的名稱。
4. 選取 **屬性**帳單的 設定檔 功能表中。
5. 複製的計費帳戶和計費的設定檔識別碼。
6. 您的範圍是： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>發票區段

1. 開啟 Azure 入口網站，然後瀏覽**成本管理 + 計費**在服務清單中。
2. 選取 **發票區段**帳單的 帳戶 功能表中。
3. 按一下所需的發票區段的名稱。
4. 選取 **屬性**發票區段功能表中。
5. 複製的計費帳戶，然後開立發票區段識別碼。
6. 您的範圍是： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA 部門

1. 開啟 Azure 入口網站，然後瀏覽**成本管理 + 計費**在服務清單中。
2. 選取 **部門**帳單的 帳戶 功能表中。
3. 按一下所需的部門名稱。
4. 選取 **屬性**部門 功能表中。
5. 將複製的計費的帳戶和部門識別碼。
6. 您的範圍是： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA 註冊帳戶

1. 開啟 Azure 入口網站並瀏覽至**成本管理 + 計費**在服務清單中。
2. 選取 **註冊帳戶**帳單的 帳戶 功能表中。
3. 按一下想要的註冊帳戶的名稱。
4. 選取 **屬性**註冊帳戶功能表中。
5. 複製的帳單帳戶與註冊帳戶識別碼。
6. 您的範圍是： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>管理群組

1. 開啟 Azure 入口網站並瀏覽至**管理群組**在服務清單中。
2. 瀏覽至所需的管理群組。
3. 複製資料表中的管理群組識別碼。
4. 您的範圍是： `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>訂用帳戶

1. 開啟 Azure 入口網站並瀏覽至**訂用帳戶**在服務清單中。
2. 從資料表中複製訂用帳戶識別碼。
3. 您的範圍是： `"/subscriptions/{id}"`

### <a name="resource-groups"></a>資源群組

1. 開啟 Azure 入口網站並瀏覽至**資源群組**在服務清單中。
2. 按一下所需的資源群組的名稱。
3. 選取 **屬性**在資源群組功能表。
4. 將複製的資源識別碼 欄位值。
5. 您的範圍是： `"/subscriptions/{id}/resourceGroups/{name}"`

目前支援成本的管理[Azure 全球](https://management.azure.com)並[Azure Government](https://management.usgovcloudapi.net)。 如需有關 Azure Government 的詳細資訊，請參閱 <<c0> [ 全域 Azure 和政府 API 端點](../azure-government/documentation-government-developer-guide.md#endpoint-mapping)_。_

## <a name="next-steps"></a>後續步驟

- 如果您尚未完成成本管理的第一個快速入門，請在[開始分析成本](quick-acm-cost-analysis.md)閱讀它。
