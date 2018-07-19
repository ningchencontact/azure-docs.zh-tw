---
title: Azure 中的治理 | Microsoft Docs
description: 了解可以相應增加和減少的雲端式運算服務，以符合應用程式或企業的需求。
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 579e900ee6616af8fd197e501364acd8e18d3e37
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970518"
---
# <a name="governance-in-azure"></a>Azure 中的治理

Azure 為您提供許多安全性選項並讓您控制這些選項，讓您能夠符合組織部署的特殊需求。

Azure 雲端治理指的是雲端運算的規劃、架構、擷取、部署、操作及管理上所涉及的決策制訂程序、條件及原則。 Azure 雲端治理會以整合的稽核與諮詢方式，針對其 Azure 平台使用方式，為組織提供檢閱及建議。 

若要建立 Azure 雲端治理的計劃，您需要立即深入了解現有的人員、程序與技術。 接著，您可以建置可讓 IT 持續支援商務需求的架構，同時為使用者提供使用 Azure 功能的彈性。

## <a name="implementation-of-policies-processes-and-standards"></a>實作原則、程序與標準 

管理階層已建立角色和職責，以監督實作 Azure 中的資訊安全性原則及作業持續性。 Azure 管理階層負責監督其個別小組 (包括第三方) 內的安全性和持續性作法。 它還有助於符合安全性原則、程序與標準的規範。

### <a name="account-provisioning"></a>帳戶佈建

定義帳戶階層是在公司內使用並建構 Azure 服務的重要步驟， 它也是核心治理結構。 擁有 Enterprise 合約 (EA) 的客戶能夠進一步將環境細分成部門、帳戶和訂用帳戶。

![帳戶佈建](./media/governance-in-azure/security-governance-in-azure-fig1.png)

如果您沒有 Enterprise 合約，請考慮使用訂用帳戶層級的 [Azure 標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)來定義階層。 Azure 訂用帳戶是包含所有資源的基本單位。 它也可以在 Azure 中定義數個限制，例如核心和資源的數目。 訂用帳戶可以包含[資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)，而資源群組可以包含資源。 那三個層級需要套用[角色型存取控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) 準則。

每個企業都不同。 對於非企業的公司而言，使用 Azure 標記的階層可在 Azure 的組織方式上擁有更大的彈性。 在 Azure 中部署資源之前，您應該建構階層的模型並了解這會對計費、資源存取及複雜性所帶來的影響。

### <a name="subscription-controls"></a>訂用帳戶控制

訂用帳戶可決定資源使用量的報告與計費方式。 您可以針對個別的計費與付款，設定訂用帳戶。 一個 Azure 帳戶可以有多個訂用帳戶。 訂用帳戶可以用來判斷公司內多個部門的 Azure 資源使用量。

例如，如果某個公司有 IT、HR 及行銷部門，而且這些部門都在執行不同的專案。 該公司可以利用每個部門對 Azure 資源 (例如虛擬機器) 的使用量為基礎來計費。 該公司之後便能控制每個部門的財務狀況。

Azure 訂用帳戶會建立三個參數：

- 唯一的訂閱者識別碼

- 計費位置

- 可用資源集合

針對個人，那些參數包括一個 Microsoft 帳戶識別碼、一個信用卡號碼，以及一套完整的 Azure 服務。 Microsoft 會根據訂用帳戶類型，強制套用取用限制。

Azure 註冊階層會定義服務在企業合約內的結構方式。 Enterprise 合約入口網站可讓客戶根據能夠針對組織需求自訂的彈性階層，將存取權分給與 Enterprise 合約相關聯的 Azure 資源。 階層模式應該符合組織的管理和地理結構，以準確記錄相關聯的計費和資源存取。

三個高層級的階層模式包括功能、業務單位及地理。 部門是帳戶群組的系統管理建構。 在每個部門中，可以將訂用帳戶指派給帳戶，如此能夠在 Azure 中建立計費和數個重要限制 (例如 VM 和儲存體帳戶數目) 的隔閡。

![訂用帳戶控制](./media/governance-in-azure/security-governance-in-azure-fig2.png)


針對具有企業合約的組織，Azure 訂用帳戶會遵循四個層級的階層：

1. 企業註冊系統管理員

2. 部門系統管理員

3. 帳戶擁有者

4. 服務管理員

此階層可控制下列項目：

- 計費關聯性。

- 帳戶管理。

- 透過 RBAC 存取資源。

- 界限：

  - 使用量和計費 (費率卡片會以供應項目數目為基礎)

  - 限制

  - 虛擬網路

- 附加至 Azure Active Directory (Azure AD)。 一個 Azure AD 執行個體可以與多個訂用帳戶產生關聯。

- 與企業註冊帳戶的關聯。

### <a name="role-based-access-control"></a>角色型存取控制

[RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) 可以讓您在 Azure 中對資源進行詳細的存取權管理。 您可以使用 RBAC，僅授與使用者執行其作業所需的存取權。 公司應該將焦點放在提供員工所需的確切權限上。 權限太多會讓帳戶暴露在攻擊者的威脅下。 權限太少則會讓員工無法有效率地完成工作。 

您可以不授與每個人 Azure 訂用帳戶或資源中無限制的權限，而是只允許執行特定的動作。 例如，您可以使用 RBAC 讓一位員工管理某個訂用帳戶中的虛擬機器，而讓另一位員工管理相同訂用帳戶中的 SQL 資料庫。

若要授與存取權，您要將角色指派給使用者、群組或特定範圍的應用程式。 角色指派的範圍可以是訂用帳戶、資源群組或單一資源。 在父範圍指派的角色也會授與其內含子系的存取權。 例如，具有資源群組存取權的使用者可以管理其內含的所有資源，例如網站、虛擬機器和子網路。 在每個訂用帳戶內，您最多可以建立 2,000 個角色指派。

角色是權限的集合，而 RBAC 有數個內建角色。 下列內建角色適用於所有資源類型：

- **擁有者**：具有所有資源的完整存取權，包括將存取權委派給其他人的權限。

- **參與者**：可以建立和管理所有類型的 Azure 資源，但是不能將存取權授與其他人。

- **讀者**：可以檢視所有 Azure 資源。

Azure 中其餘的內建角色可以管理特定的 Azure 資源。 例如，「虛擬機器參與者」角色可讓使用者建立和管理虛擬機器。 如需所有內建角色及其作業的清單，請參閱 [RBAC 內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

RBAC 支援在 Azure 入口網站和 Azure Resource Manager API 中的 Azure 資源管理作業。 在多數情況下，RBAC 無法授權 Azure 資源的資料層級作業。 如需如何將 RBAC 延伸至資料作業的相關資訊，請參閱[了解角色定義](https://docs.microsoft.com/azure/role-based-access-control/role-definitions)。

如果內建角色不符合您的特定存取需求，可以[建立自訂角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)。 就像內建角色一樣，您可以將自訂角色指派給訂用帳戶、資源群組和資源範圍的使用者、群組和應用程式。 您可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 和 [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest) 建立自訂角色。

### <a name="resource-management"></a>資源管理

Azure 提供兩種部署模型：[傳統](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)和 Azure Resource Manger。

在傳統模型中，每個資源都獨立存在。 沒有任何方式可以將相關的資源組成群組。 您必須手動追蹤組成方案或應用程式的資源，並記得要以協調的方法進行管理。 管理的基本單位是訂用帳戶。 訂用帳戶內的資源難以細分，以致建立大量的訂用帳戶。

Resource Manager 部署模型包括[資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的概念。 資源群組是共用共同生命週期的資源容器。 它可以包含方案的所有資源，或只包含您要以群組方式管理的資源。 您可以決定如何根據對組織最有利的方式，將資源配置到資源群組。

Resource Manager 部署模型提供數個優點：

- 您可以以群組形式部署、管理及監視方案的所有服務，而不是個別處理這些服務。

- 您可以在整個生命週期中重複部署方案，並確信您的資源會以一致的狀態部署。

- 您可以將存取控制套用至資源群組中的所有資源。 將新的資源加入至資源群組時，系統會自動套用這些原則。

- 您可以將標籤套用至資源，以便以邏輯方式組織訂用帳戶中的所有資源。

- 您可以使用 JavaScript 物件標記法 (JSON) 來定義您的解決方案的基礎結構。 JSON 檔案也稱為 Resource Manager 範本。

- 您可以定義資源之間的相依性，使其以正確的順序部署。

![Resource Manager](./media/governance-in-azure/security-governance-in-azure-fig4.png)

如需範本的建議，請參閱[建立 Azure Resource Manager 範本的最佳做法](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)。

Azure Resource Manager 會分析相依性，協助確保以正確的順序建立資源。 如果某個資源依賴來自另一個資源的值 (例如需要儲存體帳戶以供磁碟使用的虛擬機器)，您必須在範本中[設定相依性](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies)。

您也可以使用範本進行基礎結構的更新。 例如，您可以將資源新增至您的方案，並將組態規則新增至已部署的資源。 如果此範本指定建立資源，但該資源已經存在，Resource Manager 會執行更新，而不是建立新資產。 Resource Manager 會將現有的資產更新為和新資產相同的狀態。

當您需要進行其他作業 (例如安裝不包含在安裝程式中的軟體) 時，Resource Manager 能針對這些案例提供擴充功能。

### <a name="resource-tracking"></a>資源追蹤

隨著組織中的使用者將資源新增至訂用帳戶，讓資源與適當的部門、客戶和環境建立關聯變得越來越重要。 您可以透過[標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)將中繼資料附加到資源。 您可以使用標籤來提供資源或擁有者的相關資訊。 標籤不僅可讓您以數種方式對資源進行彙總及分組，還可將該資料用於退款目的。

當您有複雜的資源群組和資源集合，而且必須以您最有利的方式視覺化資產時，請使用標籤。 例如，您可以將組織中具有類似角色，或屬於相同部門的資源加上標籤。

如果不使用標籤，組織中的使用者就可能會建立多個資源，如此對於日後的識別及管理來說可能很困難。 例如，您可能想要刪除某個專案的所有資源。 如果未對此專案標記那些資源，您必須手動尋找它們。 標記是降低訂用帳戶不必要成本的重要方法。

資源不需要位於相同的資源群組即可共用標籤。 您可以建立自己的標籤分類，以確保組織中的所有使用者都使用常見的標籤，而不是意外套用稍有不同的標籤 (例如 "dept" 而不是 "department")。

資源原則可讓您建立組織適用的標準規則。 您可以建立原則，以確保會以適當的值為資源加上標籤。

您也可以透過 Azure 入口網站檢視已加上標籤的資源。 訂用帳戶的[使用報告](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)包含標籤名稱和值，可讓您依標籤細分成本。

如需標籤的詳資訊，請參閱[計費標籤原則計畫](../azure-policy/scripts/billing-tags-policy-init.md)。

標籤具有下列限制：

- 每個資源或資源群組最多可以有 15 個標記索引鍵/值組。 此限制只適用於直接套用至資源群組或資源的標記。 資源群組可以包含許多資源，其各自有 15 個標記索引鍵/值組。

- 標籤名稱的上限為 512 個字元。

- 標籤值的上限為 256 個字元。

- 資源群組中的資源不會繼承套用至該資源群組的標籤。

如果您有超過 15 個要與資源產生關聯的值，請使用 JSON 字串作為標記值。 JSON 字串可以包含許多套用至單一標記索引鍵的值。

#### <a name="tags-for-billing"></a>為計費加上標籤

標籤可讓您將計費資料分組。 例如，如果您執行不同組織的多個 VM，請使用標籤根據成本中心將使用量分組。 您也可以使用標籤來根據執行階段環境將成本分類。例如，在生產環境中執行之 VM 的計費使用量。

您可以透過 [Azure 資源使用狀況和 RateCard API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) 或使用狀況逗號分隔值 (CSV) 檔案，擷取關於標記的資訊。 您可以從 [Azure 帳戶入口網站](https://account.windowsazure.com/)或 [EA 入口網站](https://ea.azure.com/)下載使用量檔案。

如需以程式設計方式存取帳單資訊的詳細資訊，請參閱[深入了解 Microsoft Azure 資源耗用量](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview)。 若為 REST API 作業，請參閱 [Azure 計費 REST API 參考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)。

當您針對支援具計費之標記的服務下載使用情況 CSV 時，標記會出現在 [標記] 資料行。

### <a name="critical-resource-controls"></a>重要資源控制

隨著您的組織將核心服務新增至訂用帳戶，確保這些服務的可用性以避免業務中斷變得越來越重要。 [資源鎖定](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)可讓您限制對珍貴資源執行的作業，而修改或刪除這類資源會嚴重影響您的應用程式或雲端基礎結構。 您可以將鎖定套用至訂用帳戶、資源群組或資源。 一般而言，您會將鎖定套用至基礎資源，例如虛擬網路、閘道和儲存體帳戶。

資源鎖定目前支援兩個值︰**CanNotDelete** 和 **ReadOnly**。 **CanNotDelete** 表示使用者 (具有適當權限) 仍可讀取或修改資源，但無法將它刪除。 **ReadOnly** 表示經過授權的使用者無法刪除或修改資源。

資源鎖定只會套用到管理平面發生的作業，亦即要傳送至 <https://management.azure.com> 的作業。 鎖定並不會限制資源執行自己函式的方式。 限制資源的變更，但沒有限制資源的作業。 例如，SQL 資料庫的 **ReadOnly** 鎖定會防止您刪除或修改資料庫，但它不會防止您建立、更新或刪除資料庫中的資料。

套用 **ReadOnly** 會導致無法預期的結果，因為有些看似讀取作業的作業會需要進行其他動作。 例如，將 **ReadOnly** 鎖定放置在儲存體帳戶上，會防止所有使用者列出金鑰。 列出金鑰的作業是透過 POST 要求進行處理，因為傳回的金鑰可用於寫入作業。

![重要資源控制](./media/governance-in-azure/security-governance-in-azure-fig5.png)

舉另一個例子來說，將 **ReadOnly** 鎖定放置在 Azure App Service 資源上可防止 Visual Studio 伺服器總管顯示該資源的檔案，因為該互動需要寫入存取權。

不同於角色型存取控制，您可以使用管理鎖定來對所有使用者和角色套用限制。 若要了解如何設定權限，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

當您在父範圍套用鎖定時，該範圍內的所有資源都會都繼承相同的鎖定。 甚至您稍後新增的資源都會繼承父項的鎖定。 繼承中限制最嚴格的鎖定優先順序最高。

若要建立或刪除管理鎖定，您必須擁有 Microsoft.Authorization/ 或 Microsoft.Authorization/locks/ 動作的存取權。 在內建角色中，只有「擁有者」和「使用者存取管理員」被授與這些動作的存取權。

### <a name="api-access-to-billing-information"></a>針對帳單資訊的 API 存取

使用 Azure 計費 API 將使用情況和資源資料提取到您慣用的資料分析工具。 Azure 資源使用情況和 RateCard API 可協助您準確地預測並管理成本。 這些 API 會實作為資源提供者，並成為 Azure Resource Manager 所公開 API 系列的一部分。

#### <a name="resource-usage-api-preview"></a>資源使用情況 API (預覽)

使用 Azure [資源使用情況 API](https://msdn.microsoft.com/library/azure/mt219003) 來取得預估的 Azure 耗用量資料。 此 API 包含︰

- **RBAC**：在 [Azure 入口網站](https://portal.azure.com/)上或透過 [Azure PowerShell Cmdlet](https://docs.microsoft.com/powershell/azure/overview) 設定存取原則，以指定哪些使用者或應用程式可以存取訂用帳戶的使用情況資料。 呼叫端必須使用標準的 Azure Active Directory 權杖進行驗證。 將呼叫端新增到「發票讀者」、「讀者」、「擁有者」或「參與者」角色，以存取特定 Azure 訂用帳戶的使用情況資料。

- **每小時或每日彙總**：呼叫端可以指定要取得每小時增量或每日增量的 Azure 使用情況資料。 預設值為每日。

- **執行個體中繼資料 (包括資源標籤)**：取得執行個體層級詳細資料，例如完整的資源 URI (/subscriptions/{subscription-id} /..)、資源群組資訊與資源標籤。 此中繼資料可協助您以決定性及程式設計方式，根據標籤為跨領域收費之類的使用案例配置使用量。

- **資源中繼資料**：資源詳細資料 (例如計量名稱、計量類別、計量子類別、單位和區域) 可讓呼叫端深入了解取用的內容。 我們也致力於跨 Azure 入口網站、Azure 使用情況 CSV、EA 計費 CSV 和其他向外公開的體驗統一資源中繼資料術語，以協助您跨體驗將資料相互關聯。

- **所有供應項目類型的使用情況**：所有供應項目類型 (包括預付型方案、MSDN、承諾用量金額、信用額度金額和 EA) 都提供使用情況資料。

#### <a name="resource-ratecard-api"></a>資源 RateCard API

使用 Azure 資源 RateCard API 來取得可用 Azure 資源的清單，以及每個資源的預估價格資訊。 此 API 包含︰

- **RBAC**：在 Azure 入口網站上或透過 Azure PowerShell Cmdlet 設定存取原則，以指定哪些使用者或應用程式可以存取 RateCard 資料。 呼叫端必須使用標準的 Azure Active Directory 權杖進行驗證。 將呼叫端新增至讀者、擁有者或參與者角色，以存取特定 Azure 訂用帳戶的使用情況資料。

- **支援預付型方案、MSDN、承諾用量金額、信用額度金額供應項目 (但不支援 EA)**：此 API 提供 Azure 供應項目層級費率資訊。 此 API 的呼叫端必須輸入供應項目資訊以取得資源詳細資料和費率。 由於 EA 供應項目已自訂每個註冊的費率，因此目前不支援 EA。 

#### <a name="scenarios"></a>案例

使用情況和 RateCard API 的組合可讓這些案例變成可能：

- **了解 Azure 月份花費**：使用使用情況和 RateCard API 的組合，取得當月份雲端花費的更佳見解。 您可以分析每小時和每天的使用情況及收費評估。

- **設定警示**：使用使用情況和 RateCard API 取得預估的雲端取用量和收費，並設定以資源為基礎或以貨幣為基礎的警示。

- **預測計費**：取得預估的取用量及雲端花費，並應用機器學習演算法來預測在計費週期結束時的計費情形。

- **執行取用前成本分析**：使用 RateCard API 來預測當您將工作負載移至 Azure 時，所預期的使用量需要多少費用。 如果您有其他雲端或私用雲端中的現有工作負載，您也可以對應您的使用情況和 Azure 費率，以取得 Azure 花費的較佳評估。 此預估可讓您以供應項目為依據來進行選擇，並比較預付型方案以外的不同供應項目類型，例如承諾用量金額和信用額度金額。

- **執行模擬分析**：您可以判斷在另一個區域執行工作負載，或是在 Azure 資源的另一個設定上執行工作負載是否會比較符合成本效益。 Azure 資源成本可能會因為您所使用的 Azure 區域而有所不同。 您也可以判斷另一個 Azure 供應項目類型是否會在 Azure 資源上提供更好的費率。

### <a name="networking-controls"></a>網路控制

資源的存取可以是內部的 (在公司網路內) 或外部的 (透過網際網路)。 您組織中的使用者很容易不小心將資源放在錯誤的位置，並可能開啟資源而遭到惡意存取。 針對內部部署裝置，企業必須加上適度的控制，以確保 Azure 使用者能做出適當的決策。

![網路控制](./media/governance-in-azure/security-governance-in-azure-fig6.png)

針對訂用帳戶治理，下列核心資源可提供基本存取控制。

#### <a name="network-connectivity"></a>網路連線

[虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)是子網路的容器物件。 雖然不是絕對必要，但虛擬網路通常用於將應用程式連線到內部公司資源。 Azure 虛擬網路服務可讓 Azure 資源與虛擬網路安全地彼此連線。

虛擬網路是您的網路在雲端中的身分。 虛擬網路是專屬於您訂用帳戶的 Azure 雲端邏輯隔離。 您也可以將虛擬網路連線到您的內部部署網路。

以下為 Azure 虛擬網路的功能：

- **隔離**︰虛擬網路會彼此隔離。 您可以為使用相同 CIDR 位址區塊的開發、測試和生產環境建立個別的虛擬網路。 相反地，您可以建立多個使用不同 CIDR 位址區塊的虛擬網路並將這些網路連線在一起。 您可以將虛擬網路分成多個子網路。 Azure 為連線至虛擬網路中的 VM 和 Azure 雲端服務角色執行個體，提供內部名稱解析。 您可以選擇將虛擬機器設定成使用自己的 DNS 伺服器，而不使用 Azure 內部名稱解析。

- **網際網路連線能力**︰根據預設，連線至虛擬網路的所有 Azure 虛擬機器和雲端服務角色執行個體都可以存取網際網路。 您也可以視需要啟用特定資源的輸入存取。

- **Azure 資源連線能力**︰您可以將 Azure 資源 (例如雲端服務和 VM) 連線至相同的虛擬網路。 即使資源位於不同的子網路，還是可以透過私人 IP 位址彼此連線。 Azure 會在子網路、虛擬網路及內部部署網路之間提供預設的路由，因此您無須設定及管理路由。

- **虛擬網路連線能力**：您可以讓虛擬網路彼此連線。 接著，連線至任何虛擬網路的資源可與位於任何其他虛擬網路上的任何資源進行通訊。

- **內部部署連線能力**︰您可以透過您的網路與 Azure 之間的私人網路連線，或透過經由網際網路的站對站虛擬私人網路 (VPN) 連線，將虛擬網路連線到內部部署網路。

- **流量篩選**︰您可以依照來源 IP 位址和連接埠、目的地 IP 位址和連接埠以及通訊協定，篩選虛擬機器和雲端服務的網路流量 (連入和連出)。

- **路由**︰您可以設定自己的路由，或透過網路閘道使用 BGP 路由，以選擇性地覆寫 Azure 的預設路由。

#### <a name="network-access-controls"></a>網路存取控制

[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) 類似防火牆，而且提供資源透過網路進行「交談」的規則。 這類群組可控制子網路 (或虛擬機器) 如何連線至網際網路或相同虛擬網路中的其他子網路。

網路安全性群組包含安全性規則的清單，可允許或拒絕已連線至 Azure 虛擬網路之資源的網路流量。 NSG 可以與子網路、個別 VM (傳統) 或已連結至 VM (Resource Manager) 的個別網路介面 (NIC) 建立關聯。

當 NSG 與子網路相關聯時，系統會將規則套用至已連線至子網路的所有資源。 您可以建立 NSG 與 VM 或 NIC 的關聯，以進一步限制流量。

## <a name="security-and-compliance-with-organizational-standards"></a>符合組織標準的安全性及合規性

每個企業都有不同的需求，而且都能透過雲端解決方案取得不同的好處。 不過，無論客戶的背景為何，他們都會對移至雲端抱持著一些基本的疑慮。 客戶想要從雲端提供者取得的是：

- **資料安全性**：IT 領導者確認雲端可以提供提升的資料安全性和系統管理控制。 但是，相較於目前的內部解決方案，他們仍然擔心移轉至雲端會讓資料更容易受到駭客攻擊。

- **資料私密性**：雲端服務帶來了獨特的隱私問題。 在眾多公司考慮採納雲端以節省基礎結構成本並提升其彈性的同時，他們也擔心失去對資料的儲存位置、存取人員及使用方式的控制。

- **資料可控性**：雖然有許多公司利用雲端部署更加創新的解決方案，但是他們還是非常擔心會失去對資料的控制。 於近日浮上檯面，有關政府機關透過合法及不受法律支配的方式存取客戶資料的消息，使得許多資訊長 (CIO) 對於將資料儲存在雲端的方式抱持疑慮。

- **提升透明度**：業務決策者了解安全性、隱私權和控制的重要性。 但是他們也希望能夠自行確認其資料儲存、存取及保護的方式。

- **維持合規性**：隨著公司持續擴展對雲端技術的運用，各種標準和法規的複雜度及範圍也持續進化。 公司需要知道是否符合其合規性標準。

## <a name="security-configuration-for-monitoring-logging-and-auditing"></a>用於監視、記錄和稽核的安全性設定

Azure 訂用帳戶可以從多個裝置管理其雲端環境。 這些裝置可能包括管理工作站、開發人員電腦，甚至是擁有工作專屬權限的特殊權限使用者裝置。 

在某些情況下，管理功能是透過 Web 式主控台 (例如 Azure 入口網站) 來執行。 在其他案例中，則可能會從內部部署系統，透過 VPN、終端機服務、用戶端應用程式通訊協定或 Azure 服務管理 API (SMAPI) (以程式設計方式) 直接連線至 Azure。 此外，用戶端端點也可以加入網域或是隔離且非受控，例如平板電腦或智慧型手機。

這種差異性可能會大幅增加雲端部署的風險。 您將難以管理、追蹤和稽核系統管理動作。 由於用來管理雲端服務的用戶端端點，其存取不受管制，因此這種差異性也可能招致安全性威脅。 使用一般工作站或私人工作站來開發和管理基礎結構將會打開無法預期的威脅媒介，例如網頁瀏覽 (例如水坑攻擊) 或電子郵件 (例如社交工程和網路釣魚)。

監視、記錄及稽核提供追蹤和了解系統管理活動的基礎。 稽核完整詳細資料中的所有動作可能會因為產生的資料量而不一定都可行。 但是，稽核管理原則的效果是最佳作法。

來自 Azure Active Directory Domain Services (AD DS) GPO 的 Azure 安全性治理可以協助您控制所有系統管理員的 Windows 介面，例如檔案共用。 將管理工作站納入監視、記錄及稽核流程內。 追蹤所有系統管理員和開發人員的存取和使用活動。

### <a name="azure-security-center"></a>Azure 資訊安全中心

[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)可提供訂用帳戶中資源安全性狀態的集中檢視。 它提供的建議有助於防止資源遭到入侵。 它可以啟用更詳細的原則，例如，將原則套用至特定資源群組，讓企業能針對他們所面臨的風險來調整其應對方式。

![Azure 資訊安全中心](./media/governance-in-azure/security-governance-in-azure-fig7.png)

資訊安全中心能提供您 Azure 訂用帳戶之間的整合式安全性監視和原則管理，協助偵測原先可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。 在您為訂用帳戶的資源啟用[安全性原則](https://docs.microsoft.com/azure/security-center/security-center-policies)之後，資訊安全中心會分析資源的安全性狀態，以找出潛在的弱點。 您可以立即取得網路組態的相關資訊。

Azure 資訊安全中心是針對 Azure 訂用帳戶內所有資源的最佳做法分析及安全性原則管理組合。 它可以自動收集並分析來自您 Azure 資源、網路及合作夥伴解決方案 (例如反惡意程式碼程式及防火牆) 的安全性資料，使安全性小組及風險控管人員能夠防止、偵測及回應安全性威脅。

此外，Azure 資訊安全中心可以套用進階分析，包括機器學習和行為分析。 它會使用來自 Microsoft 產品與服務、Microsoft 數位犯罪防治中心 (DCU)、Microsoft 安全性回應中心 (MSRC) 以及外部摘要的全球威脅情報。 您可以在訂用帳戶層級廣泛地套用[安全性治理](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/)。 或者，您可以將其縮小到特定需求，並透過原則定義，將這些需求套用至個別的資源。

最後，Azure 資訊安全中心會根據那些原則分析資源安全性健康情況，並使用此資訊提供含深入見解的儀表板，以及針對惡意程式碼偵測或惡意 IP 連線嘗試等事件的警示功能。 如需有關如何套用建議的詳細資訊，請參閱[實作 Azure 資訊安全中心的安全性建議](https://docs.microsoft.com/azure/security-center/security-center-recommendations)。

Azure 資訊安全中心會監視下列 Azure 資源：

- 虛擬機器 (VM) (包括雲端服務)

- 虛擬網路

- SQL 資料庫

- 與您的 Azure 訂用帳戶整合的合作夥伴解決方案，例如 VM 和 [App Service 環境](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)上的 Web 應用程式防火牆

當您第一次存取資訊安全中心時，訂用帳戶中的所有虛擬機器都會啟用資料收集。 建議您將資料收集保持在啟用狀態，但是您也可以在資訊安全中心原則中[將它停用](https://docs.microsoft.com/azure/security-center/security-center-faq)。

### <a name="log-analytics"></a>Log Analytics

Azure Log Analytics 軟體開發和服務小組的資訊安全性及[治理方案](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md)支援其商務需求。 它會遵守法律與法規，如 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)與 [Microsoft 信任中心合規性](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)中所述。 上述位置也會描述 Log Analytics 建立安全性需求、識別安全性控制，以及管理和監視風險的方式。 小組每年都會檢閱原則、標準、程序和指導方針。

每個 Log Analytics 開發小組成員都會獲得正式的應用程式安全性訓練。 版本控制系統有助於保護開發中的每個軟體專案。

Microsoft 備有會監看及評估 Microsoft 中所有服務的安全性和法規遵循小組。 資訊安全人員會組成小組，且他們與開發 Log Analytics 的工程部門並無關聯。 安全人員有他們自己的管理鏈。 他們進行產品和服務的獨立評定，以協助確保安全性與合規性。

Log Analytics 的核心功能是由在 Azure 中執行的一組服務所提供。 每個服務會提供特定的管理功能，您可以合併服務以達成不同的管理案例。

![用於管理的 Azure 服務](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

這些管理服務是在雲端中設計的。 它們完全裝載於 Azure 中，因此不涉及部署及管理內部部署資源。 需要進行的設定很少，因此您可以在幾分鐘內完成啟動並執行。

將代理程式放在您資料中心的任何 Windows 或 Linux 電腦上，它就會將資料傳送至 Log Analytics。 在那裡，該資料可以與從雲端或內部部署服務收集的其他所有資料一起進行分析。 使用 Azure 備份和 Azure Site Recovery，以運用雲端針對內部部署資源取得備份及高可用性的優勢。

![Azure 儀表板上的管理服務](./media/governance-in-azure/security-governance-in-azure-fig8.png)

雲端中的 Runbook 通常無法存取內部部署資源，但您可以在一或多部電腦上安裝將在資料中心裝載 Runbook 的代理程式。 當您啟動 Runbook 時，會指定希望它在雲端或在本機背景工作角色上執行。

Microsoft 和合作夥伴會提供不同的解決方案，您可以將其新增至 Azure 訂用帳戶，以提升 Log Analytics 投資價值。 例如，Azure 提供的[管理解決方案](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions)是預先封裝的邏輯集合，它使用一個或多個管理服務來實作管理案例。

![Azure 中的管理解決方案資源庫](./media/governance-in-azure/security-governance-in-azure-fig10.png)

您身為合作夥伴，可以建立自己的解決方案，以支援應用程式與服務，並透過 Azure Marketplace 或快速入門範本將其提供給使用者。

## <a name="performance-alerting-and-monitoring"></a>效能警示和監視

### <a name="alerting"></a>警示

警示是監視 Azure 資源計量、事件或記錄檔的方法。 符合您所指定的條件時，就會向您發送警示。

警示可跨服務使用，包括：

- **Azure Application Insights**：允許 Web 測試及計量警示。 如需詳細資訊，請參閱[在 Application Insights 中設定警示](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)和[監視任何網站的可用性和回應性](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)。

- **Log Analytics**：允許將活動和診斷記錄路由傳送至 Log Analytics。 它允許計量、記錄和其他警示類型。 如需詳細資訊，請參閱 [Log Analytics 中的警示](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)。

- **Azure 監視器**：允許根據度量值和活動記錄檔事件的警示。 您可以使用 [Azure 監視器 REST API](https://msdn.microsoft.com/library/dn931943.aspx) 來管理警示。 如需詳細資訊，請參閱[使用 Azure 入口網站、PowerShell 或命令列介面來建立警示](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal)。

### <a name="monitoring"></a>監視

您雲端應用程式中的效能問題可能會對企業產生影響。 透過多個互連的元件和頻繁的發行，隨時都可能導致效能降低。 此外，如果您正在開發應用程式，使用者通常會探索到您在測試時未發現的問題。 您應該立即知道這些問題，並擁有可用以診斷和修復問題的工具。

有多種工具可用來監視 Azure 應用程式與服務。 其中有些功能重疊。 這其中有部分是因為應用程式開發與操作之間的界限模糊不清而導致。

以下是主要工具：

- **Azure 監視器**是一個用於監視 Azure 上所執行服務的基本工具。 它會提供關於服務輸送量及周遭環境之基礎結構等級的資料。 如果您在 Azure 中管理所有應用程式，並決定要相應增加或減少資源，Azure 監視器可以協助您開始。

- **Application Insights** 可用來開發，並作為監視生產環境的解決方案。 它的運作方式是將套件安裝在您的應用程式中，因此可讓您更深入檢視內部情形。 它的資料包括相依性的回應時間、例外狀況追蹤、偵錯快照集，以及執行設定檔。 它所提供的工具可完整分析此遙測，以協助您針對應用程式進行偵錯，並協助您了解使用者在應用程式上的動作。 您可以判斷回應時間突然增加是因為應用程式中的某些項目所引起，還是由一些外部資源問題所引起。 如果您使用 Visual Studio 且應用程式發生問題，則可以直接前往發生問題的程式碼行，讓您能夠修正問題。

- **Log Analytics** 適用於需要微調效能並計劃維護在生產環境中執行之應用程式的使用者。 它會從許多來源收集並彙總資料，但會有 10 到 15 分鐘的延遲。 它會針對 Azure、內部部署及協力廠商的雲端式基礎結構 (例如 Amazon Web 服務) 提供整體 IT 管理解決方案。 它提供的工具可跨來源分析資料、允許跨所有記錄進行複雜的查詢，而且可以依據指定的條件主動發出警示。 您甚至可以將自訂資料收集到其中央存放庫，然後查詢該資料並將它視覺化。

- **System Center Operations Manager** 可用來管理及監視大型雲端安裝。 您可能已經熟悉它作為適用於內部部署 Windows Sever 和 Hyper-V 型雲端的管理工具，但它也可以與 Azure 應用程式整合並加以管理。 在其他方面，它可以在現有的即時應用程式上安裝 Application Insights。 如果應用程式當掉，Operations Manager 會在短時間內通知您。


## <a name="next-steps"></a>後續步驟

- [建立 Azure Resource Manager 範本的最佳做法](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)

- [實作 Azure 訂用帳戶治理的範例](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples)

- [Microsoft Azure Government](https://docs.microsoft.com/azure/azure-government/)
