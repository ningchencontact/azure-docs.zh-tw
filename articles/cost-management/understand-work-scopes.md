---
title: 瞭解及使用 Azure 成本管理範圍
description: 本文可協助您瞭解 Azure 中可用的計費和資源管理範圍，以及如何使用成本管理和 Api 中的範圍。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 1f02cf3abaae7d67ba3d204dc9419d9fbfa4a86d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597089"
---
# <a name="understand-and-work-with-scopes"></a>了解並使用範圍

本文可協助您瞭解 Azure 中可用的計費和資源管理範圍，以及如何使用成本管理和 Api 中的範圍。

## <a name="scopes"></a>範圍

「_範圍_」是 Azure 資源階層中的節點，Azure AD 使用者存取和管理服務。 大部分的 Azure 資源會建立並部署到資源群組中，這是訂用帳戶的一部分。 Microsoft 也在 Azure 訂用帳戶上提供兩個階層，其中具有專門用來管理帳單資料的角色：
- 帳單資料，例如付款和發票
- 雲端服務，例如成本和原則治理

範圍可供您管理帳單資料、擁有付款特有的角色、查看發票，以及進行一般帳戶管理。 計費和帳戶角色會與用於資源管理（使用[AZURE RBAC](../role-based-access-control/overview.md)）的人員分開管理。 為了清楚區分不同範圍的意圖（包括存取控制差異），這分別稱為_計費範圍_和_RBAC 範圍_。

## <a name="how-cost-management-uses-scopes"></a>成本管理如何使用範圍

成本管理適用于資源之上的所有範圍，讓組織能夠在他們擁有存取權的層級管理成本，無論是整個帳單帳戶還是單一資源群組。 雖然計費範圍會根據您的 Microsoft 合約（訂用帳戶類型）而有所不同，但 RBAC 範圍則不會。

## <a name="azure-rbac-scopes"></a>Azure RBAC 範圍

Azure 支援三個範圍來管理資源。 每個範圍都支援管理存取和治理，包括但不限於成本管理。

- [**管理群組**](../governance/management-groups/overview.md)-最多八個層級的階層式容器，用來組織 Azure 訂用帳戶。

    資源類型： [Microsoft. Management/managementGroups](/rest/api/resources/managementgroups)

- 訂用帳戶-適用于 Azure**資源的主要**容器。

    資源類型： [Microsoft .resources/](/rest/api/resources/subscriptions)訂用帳戶

- [**資源群組**](../azure-resource-manager/resource-group-overview.md#resource-groups)-適用于共用相同生命週期的 Azure 解決方案相關資源的邏輯分組。 例如，一起部署和刪除的資源。

    資源類型： [Microsoft .resources/](/rest/api/resources/resourcegroups)訂用帳戶/resourceGroups

管理群組可讓您將訂用帳戶組織成階層。 例如，您可以使用管理群組建立邏輯組織階層。 然後，提供生產環境和開發/測試工作負載的小組訂閱。 然後在訂用帳戶中建立資源群組，以管理每個子系統或元件。

建立組織階層可進行成本和原則合規性匯總組織。 然後，每個領導者都可以查看和分析其目前成本。 然後，他們可以建立預算來與不正確的支出模式結合，並使用最低層級的 Advisor 建議來將成本優化。

使用 Azure RBAC 來授與存取權以查看成本，並選擇性地管理成本設定（例如預算和匯出）在治理範圍上執行。 您可以使用 Azure RBAC 來授與 Azure AD 使用者和群組存取權，以執行一組在特定範圍和以下角色中定義的預先定義動作。 例如，指派給管理群組範圍的角色也會授與相同的許可權給嵌套的訂用帳戶和資源群組。

成本管理針對下列每個範圍支援下列內建角色：

- [**擁有**](../role-based-access-control/built-in-roles.md#owner)者–可以查看成本及管理所有專案，包括成本設定。
- [**參與者**](../role-based-access-control/built-in-roles.md#contributor)–可以查看成本及管理所有專案，包括成本設定，但不包括存取控制。
- [**讀者**](../role-based-access-control/built-in-roles.md#reader)–可以查看所有專案，包括成本資料和設定，但無法進行任何變更。
- [**成本管理參與者**](../role-based-access-control/built-in-roles.md#cost-management-contributor)–可以查看成本、管理成本設定和查看建議。
- [**成本管理讀取器**](../role-based-access-control/built-in-roles.md#cost-management-reader)–可以查看成本資料、成本設定和 view 建議。

成本管理參與者是建議的最低許可權角色。 它允許人員存取來建立和管理預算和匯出，以更有效率地監視和報告成本。 成本管理參與者也可能需要額外的角色，以支援端對端成本管理案例。 請考慮下列情況：

- **超出預算時採取行動**–成本管理參與者也需要存取權來建立及/或管理動作群組，以自動回應超額部分。 請考慮將[監視參與者](../role-based-access-control/built-in-roles.md#monitoring-contributor)授與資源群組，其中包含超過預算臨界值時所要使用的動作群組。 自動化特定動作需要使用特定服務的其他角色，例如自動化和 Azure Functions。
- **排程成本資料匯出**–成本管理參與者也需要存取權來管理儲存體帳戶，以排程將資料複製到儲存體帳戶的匯出。 請考慮將[儲存體帳戶參與者](../role-based-access-control/built-in-roles.md#storage-account-contributor)授與資源群組，其中包含成本資料匯出所在的儲存體帳戶。
- **查看節省成本的建議**–成本管理的讀者和成本管理參與者預設都可以存取*查看*成本建議。 不過，對成本建議採取行動的存取需要個別資源的存取權。 如果您想要對以成本為基礎的建議採取行動，請考慮授與[服務特定角色](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)。

## <a name="enterprise-agreement-scopes"></a>Enterprise 合約範圍

Enterprise 合約（EA）帳單帳戶（也稱為註冊）具有下列範圍：

- [**帳單帳戶**](../billing/billing-view-all-accounts.md)-代表 EA 註冊。 此範圍會產生發票。 不以使用量為基礎（例如 Marketplace 和保留）的購買僅適用于此範圍。 它們不會在部門或註冊帳戶中代表。

    資源類型： `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **部門**-註冊帳戶的選擇性群組。

    資源類型： `Billing/billingAccounts/departments`

- **註冊帳戶**-代表單一帳戶擁有者。 不支援授與多人存取權。

    資源類型： `Microsoft.Billing/billingAccounts/enrollmentAccounts`

雖然治理範圍會系結至單一目錄，但 EA 計費範圍則不會。 EA 帳單帳戶可能會有任何數目的 Azure AD 目錄的訂閱。

EA 計費範圍支援下列角色：

- **企業系統管理員**–可以管理帳單帳戶設定和存取、可以查看所有成本，並可管理成本設定。 例如，預算和匯出。 在函數中，EA 計費範圍與[成本管理參與者 AZURE RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-contributor)相同。
- **企業唯讀使用者**–可以查看帳單帳戶設定、成本資料和成本設定。 例如，預算和匯出。 在函式中，EA 計費範圍與[成本管理讀取器 AZURE RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-reader)相同。
- **部門系統管理員**–可以管理部門設定（例如成本中心），而且可以存取、查看所有成本，以及管理成本設定。 例如，預算和匯出。  必須為部門系統管理員和唯讀使用者啟用**DA view**計費帳戶設定，才能看到成本。 如果已停用**DA view 費用**，部門使用者就不會看到任何層級的成本，即使他們是帳戶或訂用帳戶擁有者也一樣。
- **部門唯讀使用者**–可以查看部門設定、成本資料和成本設定。 例如，預算和匯出。 如果已停用**DA view 費用**，部門使用者就不會看到任何層級的成本，即使他們是帳戶或訂用帳戶擁有者也一樣。
- **帳戶擁有**者–可以管理註冊帳戶設定（例如成本中心）、查看所有成本，以及管理註冊帳戶的成本設定（例如預算和匯出）。 必須為帳戶擁有者和 RBAC 使用者啟用**AO view**計費帳戶設定，才能看到成本。

EA 帳單帳戶使用者沒有發票的直接存取權。 您可以從外部大量授權系統取得發票。

Azure 訂用帳戶會在 [註冊帳戶] 底下加以內嵌。 計費使用者可以存取其各自範圍下的訂用帳戶和資源群組的成本資料。 他們沒有存取權，無法查看或管理 Azure 入口網站中的資源。 計費使用者可以藉由流覽至 Azure 入口網站服務清單中的 [**成本管理 + 帳單**] 來查看成本。 然後，他們可以針對需要報告的特定訂用帳戶和資源群組，篩選成本。

計費使用者沒有管理群組的存取權，因為它們不會明確地在特定帳單帳戶之下。 必須明確授與管理群組的存取權。 管理群組會從所有的嵌套訂閱匯總成本。 不過，它們只包含以使用量為基礎的購買。 它們不包含購買專案，例如保留和協力廠商 Marketplace 供應專案。 若要查看這些成本，請使用 EA 帳單帳戶。

## <a name="individual-agreement-scopes"></a>個別合約範圍

從個別供應專案（例如免費試用版和開發/測試供應專案）所建立的 Azure 訂用帳戶，沒有明確的帳單帳戶範圍。 相反地，每個訂用帳戶都有帳戶擁有者或帳戶管理員，例如 EA 帳戶擁有者。

- [**帳單帳戶**](../billing/billing-view-all-accounts.md)-代表一或多個 Azure 訂用帳戶的單一帳戶擁有者。 目前不支援將存取權授與給多個人或匯總成本視圖的存取權。

    資源類型：不適用

個別的 Azure 訂用帳戶管理員可以從[Azure 帳戶中心](https://account.azure.com/subscriptions)查看和管理帳單資料，例如發票和付款。 不過，他們無法在 Azure 入口網站中查看成本資料或管理資源。 若要授與帳戶管理員的存取權，請使用先前所述的成本管理角色。

不同于 EA，個別的 Azure 訂用帳戶管理員可以在 Azure 入口網站中看到其發票。 請記住，成本管理讀者和成本管理參與者角色不會提供發票的存取權。 如需詳細資訊，請參閱[如何授與發票的存取權](../billing/billing-manage-access.md##give-read-only-access-to-billing)。

## <a name="microsoft-customer-agreement-scopes"></a>Microsoft 客戶合約範圍

Microsoft 客戶合約帳單帳戶具有下列範圍：

- **帳單帳戶**-代表適用于多個 Microsoft 產品和服務的客戶合約。 客戶合約帳單帳戶的功能與 EA 註冊不相同。 EA 註冊會與帳單設定檔更緊密配合。

    資源類型： `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **帳單設定檔**-定義發票中包含的訂閱。 計費設定檔是 EA 註冊的功能對等，因為這是發票產生的範圍。 同樣地，不以使用量為基礎（例如 Marketplace 和保留）的購買僅適用于此範圍。 它們不會包含在發票區段中。

    資源類型： `Microsoft.Billing/billingAccounts/billingProfiles`

- **Invoice 區段**-代表發票或帳單設定檔中的一組訂閱。 發票區段就像部門，多人可以存取發票區段。

    資源類型： `Microsoft.Billing/billingAccounts/invoiceSections`

- **客戶**代表一組訂用帳戶，這些訂用帳戶與由合作夥伴上架至 Microsoft 客戶合約的特定客戶相關聯。 此範圍是 CSP 特有的。

與 EA 計費範圍不同的是，客戶合約帳單帳戶_會_系結至單一目錄，而且不能有多個 Azure AD 目錄的訂閱。

客戶合約計費範圍不適用於合作夥伴。 夥伴角色和許可權記載于[指派使用者角色和許可權](/partner-center/permissions-overview)。

客戶合約計費範圍支援下列角色：

- **擁有**者–可以管理帳單設定和存取、查看所有成本，以及管理成本配置。 例如，預算和匯出。 在函式中，此客戶合約計費範圍與[成本管理參與者 AZURE RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-contributor)相同。
- **參與者**–可以管理存取權以外的帳單設定、查看所有成本，以及管理成本配置。 例如，預算和匯出。 在函式中，此客戶合約計費範圍與[成本管理參與者 AZURE RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-contributor)相同。
- **讀者**–可以查看帳單設定、成本資料和成本配置。 例如，預算和匯出。 在函式中，此客戶合約計費範圍與[成本管理讀取器 AZURE RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-reader)相同。
- **發票管理員**–可以查看和支付發票費用，並可查看成本資料和設定。 例如，預算和匯出。 在函式中，此客戶合約計費範圍與[成本管理讀取器 AZURE RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-reader)相同。
- **Azure 訂**用帳戶建立者–可以建立 azure 訂用帳戶、查看成本，以及管理成本設定。 例如，預算和匯出。 在函數中，此客戶合約計費範圍與 EA 註冊帳戶擁有者角色相同。

Azure 訂用帳戶會在 [發票] 區段下加以嵌套，如同其在 EA 註冊帳戶下的方式。 計費使用者可以存取其各自範圍下的訂用帳戶和資源群組的成本資料。 不過，他們沒有存取權來查看或管理 Azure 入口網站中的資源。 計費使用者可以藉由流覽至 Azure 入口網站服務清單中的 [**成本管理 + 帳單**] 來查看成本。 然後，針對需要報告的特定訂用帳戶和資源群組，篩選成本。

計費使用者沒有管理群組的存取權，因為他們並未明確地落在帳單帳戶之下。 不過，針對組織啟用管理群組時，會將所有訂用帳戶成本匯總到帳單帳戶和根管理群組，因為兩者都限制為單一目錄。 管理群組只包含以使用量為基礎的購買。 「保留」和協力廠商 Marketplace 供應專案等購買專案不會包含在管理群組中。 因此，帳單帳戶和根管理群組可能會報告不同的總計。 若要查看這些成本，請使用帳單帳戶或個別的帳單設定檔。

## <a name="aws-scopes"></a>AWS 範圍

完成 AWS 整合之後，請參閱[設定和設定 AWS 整合](aws-integration-set-up-configure.md)。 可用的範圍如下：

- **外部帳單帳戶**-代表與協力廠商廠商簽訂的客戶合約。 這類似于 EA 帳單帳戶。

    資源類型： `Microsoft.CostManagement/externalBillingAccounts`

- **外部訂**用帳戶-代表具有協力廠商廠商的客戶動作帳戶。 這類似于 Azure 訂用帳戶。

    資源類型： `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>雲端解決方案提供者（CSP）範圍

Microsoft 客戶合約上的客戶 Csp 支援下列範圍：

- **帳單帳戶**-代表適用于多個 Microsoft 產品和服務的客戶合約。 客戶合約帳單帳戶的功能與 EA 註冊不相同。 EA 註冊會與帳單設定檔更緊密配合。

    資源類型： `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **帳單設定檔**-定義發票中包含的訂閱。 計費設定檔是 EA 註冊的功能對等，因為這是發票產生的範圍。 同樣地，不以使用量為基礎（例如 Marketplace 和保留）的購買僅適用于此範圍。

    資源類型： `Microsoft.Billing/billingAccounts/billingProfiles`

- **客戶**代表一組訂用帳戶，這些訂用帳戶與由合作夥伴上架至 Microsoft 客戶合約的特定客戶相關聯。

只有具備*全域管理員*和系統*管理員代理程式*角色的使用者，才能夠直接在合作夥伴的 Azure 租使用者中管理和查看帳單帳戶、帳單設定檔和客戶的成本。 如需合作夥伴中心角色的詳細資訊，請參閱[指派使用者角色和許可權](/partner-center/permissions-overview)。

Azure 成本管理只有在客戶擁有 Microsoft 客戶合約時，才支援 CSP 合作夥伴客戶。 對於尚不是 Microsoft 客戶合約的 CSP 支援客戶，請參閱[合作夥伴中心](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)。

## <a name="switch-between-scopes-in-cost-management"></a>在成本管理的範圍之間切換

Azure 入口網站中的所有成本管理檢視都包含視圖左上方的**範圍**選取膠囊按鈕。 使用它來快速變更範圍。 按一下 [**領域**] 膠囊按鈕，以開啟 [領域選擇器]。 它會顯示帳單帳戶、根管理群組，以及未在根管理群組下的任何訂用帳戶。 若要選取範圍，請按一下背景以反白顯示，然後按一下底部的 [**選取**]。 若要向內切入到嵌套的範圍（例如訂用帳戶中的資源群組），請按一下 [範圍名稱] 連結。 若要選取任何嵌套層級的父範圍，請按一下 [範圍] 選擇器頂端的 [**選取此 &lt;scope] &gt;** 。

## <a name="identify-the-resource-id-for-a-scope"></a>識別範圍的資源識別碼

使用成本管理 Api 時，瞭解範圍是很重要的。 使用下列資訊來建立成本管理 Api 的適當範圍 URI。

### <a name="billing-accounts"></a>帳單帳戶

1. 開啟 Azure 入口網站，然後在服務清單中流覽至 [**成本管理 + 帳單**]。
2. 在 [帳單帳戶] 功能表中選取 [**屬性**]。
3. 複製帳單帳戶識別碼。
4. 您的範圍為： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>帳單設定檔

1. 開啟 Azure 入口網站，然後在服務清單中流覽至 [**成本管理 + 帳單**]。
2. 在 [帳單帳戶] 功能表中選取 [**帳單設定檔**]。
3. 按一下所需帳單設定檔的名稱。
4. 在 [帳單設定檔] 功能表中選取 [**屬性**]。
5. 複製帳單帳戶和帳單設定檔識別碼。
6. 您的範圍為： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>發票區段

1. 開啟 Azure 入口網站，然後在服務清單中流覽至 [**成本管理 + 帳單**]。
2. 在 [帳單帳戶] 功能表中選取 [**發票區段**]。
3. 按一下所需發票區段的名稱。
4. 選取 [發票] 區段功能表中的 [**屬性**]。
5. 複製帳單帳戶和發票區段識別碼。
6. 您的範圍為： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA 部門

1. 開啟 Azure 入口網站，然後在服務清單中流覽至 [**成本管理 + 帳單**]。
2. 選取 [帳單帳戶] 功能表中的 [**部門**]。
3. 按一下所需部門的名稱。
4. 選取 [部門] 功能表中的 [**屬性**]。
5. 複製帳單帳戶和部門識別碼。
6. 您的範圍為： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA 註冊帳戶

1. 開啟 Azure 入口網站，然後在服務清單中流覽至 [**成本管理 + 帳單**]。
2. 選取 [帳單帳戶] 功能表中的 [**註冊帳戶**]。
3. 按一下所需註冊帳戶的名稱。
4. 在 [註冊帳戶] 功能表中選取 [**屬性**]。
5. 複製帳單帳戶和註冊帳戶識別碼。
6. 您的範圍為： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>管理群組

1. 開啟 Azure 入口網站，然後流覽至服務清單中的 [**管理群組**]。
2. 流覽至所需的管理群組。
3. 複製資料表中的管理群組識別碼。
4. 您的範圍為： `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Subscription

1. 開啟 Azure 入口網站，然後流覽至服務清單中的 [**訂閱**]。
2. 複製資料表中的訂用帳戶識別碼。
3. 您的範圍為： `"/subscriptions/{id}"`

### <a name="resource-groups"></a>資源群組

1. 開啟 Azure 入口網站，然後流覽至服務清單中的 [**資源群組**]。
2. 按一下所需資源群組的名稱。
3. 選取 [資源群組] 功能表中的 [**屬性**]。
4. 複製 [資源識別碼] 域值。
5. 您的範圍為： `"/subscriptions/{id}/resourceGroups/{name}"`

[Azure Global](https://management.azure.com)和[Azure Government](https://management.usgovcloudapi.net)目前支援成本管理。 如需 Azure Government 的詳細資訊，請參閱[Azure 全域和政府 API 端點](../azure-government/documentation-government-developer-guide.md#endpoint-mapping) _。_

## <a name="next-steps"></a>後續步驟

- 如果您尚未完成成本管理的第一個快速入門，請在[開始分析成本](quick-acm-cost-analysis.md)閱讀它。
