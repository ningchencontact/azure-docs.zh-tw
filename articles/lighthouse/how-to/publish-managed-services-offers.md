---
title: 將受控服務供應項目發佈到 Azure Marketplace
description: 了解如何發佈將客戶上線至 Azure 委派資源管理的受控服務。
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: bb2f26a170bbd60eb927bd00f6def7d033fafee9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810832"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>將受控服務供應項目發佈到 Azure Marketplace

在此文章中，您將了解如何使用 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)，將公用或私人受控服務供應項目發佈至 [Azure Marketplace](https://azuremarketplace.microsoft.com)，讓購買供應項目的客戶能上線至 Azure 委派的資源管理。 

> [!NOTE]
> 您也必須有有效的[合作夥伴中心帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)，才能建立並發佈這些供應項目。 如果您還沒有帳戶，[註冊程序](https://aka.ms/joinmarketplace)會引導您完成在合作夥伴中心建立帳戶並註冊商業 Marketplace 計畫的步驟。 您的 Microsoft 合作夥伴網路 (MPN) 識別碼將會與您發佈的供應項目[自動關聯](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started) \(部分機器翻譯\)，以追蹤您對客戶參與整體的影響。
>
> 如果您不想將供應項目發佈至 Azure Marketplace，您可以使用 Azure Resource Manager 範本手動讓客戶上線。 如需詳細資訊，請參閱[讓客戶在 Azure 委派的資源管理中上線](onboard-customer.md)。

發佈受控服務供應項目，類似於將任何其他類型供應項目發佈至 Azure Marketplace。 若要了解該程序，請參閱 [Azure Marketplace 和 Azure Marketplace 和 AppSource 發行指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) \(部分機器翻譯\) 和[管理 Azure 與 AppSource Marketplace 供應項目](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers) \(部分機器翻譯\)。

> [!IMPORTANT]
> 受控服務供應項目中的每個方案都包含 [資訊清單詳細資料]  ，您會為購買方案的客戶，在此區段中定義租用戶中能存取委派資源群組和/或訂閱的 Azure Active Directory (Azure AD) 項目。 請務必注意，您在此處包含的任何群組 (或使用者或服務主體)，都與購買方案的客戶有相同權限。 若要指派不同群組來搭配每個客戶一起使用，您必須發佈每個客戶專屬的個別私人方案。

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>在 Cloud Partner 入口網站中建立供應項目

1. 登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。
2. 從左側導覽功能表中，選取 [新增供應項目]  ，然後選取 [受管理的服務]  。
3. 您會看到供應項目的 [編輯器]  區段，其中有四個部分要填寫：[供應項目設定]  、[方案]  、[Marketplace]  與 [支援]  。 請繼續閱讀以下內容，以了解如何完成這些區段。

## <a name="enter-offer-settings"></a>輸入供應項目設定

在 [供應項目設定]  區段中，提供下列項目：

|欄位  |說明  |
|---------|---------|
|**供應項目識別碼**     | 您供應項目的唯一識別碼 (您的發行者設定檔內)。 此識別碼只能包含小寫英數字元、連字號與底線，且最多 50 個字元。 請記住，客戶可能會在產品 URL 與帳單報表之類的地方看到供應項目識別碼。 一旦發佈供應項目，就無法變更此值。        |
|**發行者識別碼**     | 將會與供應項目相關聯的發行者識別碼。 如果您有多個發行者識別碼，您可以選取要用於此供應項目的識別碼。       |
|**名稱**     | 您的供應項目在 Azure Marketplace 和 Azure 入口網站中，客戶所看到的名稱 (最多 50 個字元)。 使用客戶能理解且容易識別的品牌名稱 - 如果要透過您自己的網站宣傳此供應項目，請務必在此使用完全相同的名稱。        |

完成之後，請選取 [儲存]  。 現在，您可以到 [方案]  區段繼續設定。

## <a name="create-plans"></a>建立方案

每個供應項目都必須有一或多個方案 (有時稱為 SKU)。 您可以新增多個方案來支援設定為不同價格的不同功能，或為特定客戶的限定對象自訂特定方案。 客戶可以檢視父代供應項目下可供他們使用的方案。

在 [方案] 區段中，針對您要建立的每個方案選取 [新增方案]  。 然後輸入 [方案識別碼]  。 此識別碼只能包含小寫英數字元、連字號與底線，且最多 50 個字元。 客戶可能會在產品 URL 和帳單報表之類的地方看到方案識別碼。 一旦發佈供應項目，就無法變更此值。

接下來，完成 [方案詳細資料]  區段中的下列區段：

|欄位  |說明  |
|---------|---------|
|**標題**     | 方案所顯示的易記名稱。 長度上限為 50 個字元。        |
|**總結**     | 顯示在標題下的方案簡短說明。 長度上限是 100 個字元。        |
|**說明**     | 描述文字，提供詳細的方案說明。         |
|**計費模型**     | 這裡顯示 2 個計費模型，但您必須為受控服務供應項目選擇 [自備授權]  。 這表示您會直接向您的客戶收取此供應項目的相關費用，而且 Microsoft 不會向您收取任何費用。   |
|**這是否為私人方案？**     | 表明 SKU 為私人或公用。 預設值為 [否]  (公用)。 如果您不變更此選項，您的方案不會限於特定客戶 (或特定數目的客戶)；發佈公用方案之後，您無法再將它變更為私人方案。 若要使此方案只有特定客戶才能取得，請選取 [是]  。 當您這麼做時，必須提供訂用帳戶識別碼來識別客戶。 您可以逐一輸入 (最多 10 個訂用帳戶)，或上傳 .csv 檔案 (最多 20,000 個訂用帳戶)。 請務必在此包含您自己的訂用帳戶，以便您可以測試及驗證供應項目。 如需詳細資訊，請參閱[私人 SKU 和方案](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus) \(部分機器翻譯\)。  |

最後，完成 [資訊清單詳細資料]  區段。 這會建立資訊清單，其中包含管理客戶資源的授權資訊。 您在這裡提供的資訊，是讓客戶上線至 Azure 委派的資源管理所需的資訊。 如上所述，這些權限會套用到購買方案的每個客戶，因此如果您想限制只有特定客戶才能存取，就必須發佈他們專用的私人方案。

- 首先，提供資訊清單的 [版本]  。 使用的格式為 *n.n.n* (例如，1.2.5)。
- 接下來，輸入您的 [租用戶識別碼]  。 此 GUID 與您組織的 Azure Active Directory 租用戶識別碼 (亦即您將會在其中管理客戶資源的租用戶) 相關聯。 如果您目前沒有此資訊，您可將滑鼠指標暫留在 Azure 入口網站右上角的帳戶名稱上，或選取 [切換目錄]  就能看到它。 
- 最後，將一或多個**授權**項目新增至您的方案。 授權會定義哪些實體能存取購買方案客戶才能存取的資源與訂用帳戶。 您必須提供此資訊，才能代替使用 Azure 委派的資源管理的客戶存取資源。
  請為每個授權提供下列項目。 然後，您要新增多少使用者/角色定義，就視需要選取多少次 [新增授權]  。
  - **Azure AD 物件識別碼**：使用者、使用者群組或應用程式的 Azure AD 識別碼，系統將會授與它們您客戶資源的特定權限 (按角色定義所描述)。
  - **Azure AD 物件顯示名稱**：協助客戶了解此授權用途的易記名稱。 客戶會在委派資源時看到此名稱。
  - **角色定義**：從清單中選取其中一個可用的 Azure AD 內建角色。 此角色將會決定 [Azure AD 物件識別碼]  欄位中的使用者，對於您的客戶資源會有那些權限。 如需這些角色的資訊，請參閱[內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) \(部分機器翻譯\)。
  - **可指派的角色**：如果您為此授權在 [角色定義]  中選取 [使用者存取系統管理員]，您可以在此新增一或多個可指派的角色。 [Azure AD 物件識別碼]  欄位中的使用者，可以將這些**可指派的角色**指派給[受控識別](https://docs.microsoft.com/azure/managed-applications/publish-managed-identity) \(部分機器翻譯\)。 請注意，不會有其他一般與「使用者存取系統管理員」角色相關聯的權限套用至此使用者。 (如果您沒有為此使用者的 [角色定義] 選取 [使用者存取系統管理員]，則此欄位無效。)

> [!TIP]
> 在多數情況下，建議您指派權限給 Azure AD 使用者群組或服務主體，而不是指派給一系列個別使用者帳戶。 如此一來，當您的存取需求變更時，就可以新增或移除個別使用者的存取權，而不需要更新並重新發佈方案。

當您完成新增方案時，請選取 [儲存]  ，然後到 [Marketplace]  區段繼續設定。

## <a name="provide-marketplace-text-and-images"></a>提供 Marketplace 文字與影像

[Marketplace]  區段可讓您提供客戶會在 Azure Marketplace 與 Azure 入口網站中看到的文字與影像。

在 [概觀]  區段中提供下列欄位的資訊：

|欄位  |說明  |
|---------|---------|
|**標題**     |  供應項目的標題，通常是較長的正式名稱。 這個標題會醒目顯示在 Marketplace 中。 長度上限為 50 個字元。 在多數情況下，這應該與您在 [供應項目設定]  區段中所提供的 [名稱]  相同。       |
|**總結**     | 您供應項目的用途或功能簡介。 此項目通常顯示在標題底下。 長度上限是 100 個字元。        |
|**完整摘要**     | 您供應項目用途或功能的較長摘要。 長度上限為 256 個字元。        |
|**說明**     | 您供應項目的詳細資訊。 此欄位的最大長度為 3000 個字元，且支援簡單 HTML 格式設定。        |
|**行銷識別碼**     | 適合 URL 的唯一識別碼。 將會用於此供應項目的 Marketplace URL。 例如，如果您的發行者識別碼為 *contoso*，而行銷識別碼是 *sampleApp*，則 Azure Marketplace 中供應項目的 URL 會是 *https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp* 。        |
|**預覽訂用帳戶識別碼**     | 可新增一到 100 個訂用帳戶識別碼。 與這些訂用帳戶相關聯的客戶，將能夠在供應項目於 Azure Marketplace 上線之前檢視它。 我們建議您在此包含自己的訂用帳戶，以便您能在供應項目可供客戶取得之前，預覽供應項目在 Azure Marketplace 中的顯示方式。  (Microsoft 支援和工程小組也能在此預覽期間檢視您的供應項目。)   |
|**實用連結**     | 與您供應項目相關的 URL，例如文件、版本資訊、常見問題集等等。        |
|**建議的類別 (最多 5 個)**     | 適用於您供應項目的一或多個類別 (最多五個)。 這些類別可協助客戶在 Azure Marketplace 與 Azure 入口網站中探索您的供應項目。        |

在 [行銷成品]  區段中，您可以上傳標誌和要與您供應項目一起顯示的其他資產。 您可以選擇性地上傳螢幕擷取畫面或影片連結，以協助客戶了解您的供應項目。

標誌需要四種大小：**小 (40x40)** 、**中 (90x90)** 、**大 (115x115)** 與**寬 (255x155)** 。 您的標誌應遵循這些指導方針：

- Azure 設計具有簡單的調色盤。 限制標誌上的主要和次要色彩數目。
- 入口網站的佈景主題色彩是白色與黑色。 請勿使用這些色彩作為標誌的背景色彩。 請使用可讓標誌在入口網站突顯出來的色彩。 建議您使用簡單的主要色彩。
- 如果您使用透明背景，請確定標誌與文字不是白色、黑色或藍色。
- 標誌的外觀與風格應該是「一般」，且避免使用漸層。 請不要在標誌上使用漸層背景。
- 請勿在標誌上放置文字 (甚至是公司或品牌名稱)。
- 確定標誌不會自動縮放。

**主圖 (815x290)** 標誌是選擇性的，但建議您使用。 如果您包含主圖標誌，請遵循這些指導方針：

- 請勿在主圖標誌中包含任何文字，並請在標誌右側保留 415 像素的空白空間。 這是將會以程式設計方式內嵌的文字元素所需的保留空間：您的發行者顯示名稱、方案標題、供應項目長摘要。
- 主圖標誌的背景不得為黑色、白色或透明。 請確保您的背景色彩不會太淺，因為內嵌文字會顯示為白色。
- 一旦您發佈包含主圖圖示的供應項目，就不能將它移除 (不過您可視需要將它更新為其他版本)。

在 [潛在客戶管理]  區段中，您可視需要選取將會儲存您潛在客戶的 CRM 系統。 

最後，在 [法律]  區段中，提供您的 [隱私權原則 URL]  與 [使用規定]  。 您也可以在這裡指定此供應項目是否要使用[標準合約](https://docs.microsoft.com/azure/marketplace/standard-contract) \(部分機器翻譯\)。

請務必先儲存您的變更，再到 [支援]  區段繼續設定。

## <a name="add-support-info"></a>新增支援資訊

在 [支援]  區段中，提供工程連絡人與客戶支援連絡人的姓名、電子郵件與電話號碼。 您也必須提供支援 URL。 當 Microsoft 有商務和支援相關問題需要連絡您時，我們會使用此資訊。

新增此資訊之後，選取 [儲存]  。

## <a name="publish-your-offer"></a>發佈您的供應項目

您對所有提供的資訊都滿意之後，下一步是將供應項目發佈至 Azure Marketplace。 選取 [發行]  按鈕來起始供應項目上線程序。 如需此程序的詳細資訊，請參閱[發行 Azure Marketplace 和 AppSource 供應項目](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer) \(部分機器翻譯\)。

## <a name="next-steps"></a>後續步驟

- 了解[跨租用戶管理體驗](../concepts/cross-tenant-management-experience.md)。
- 前往 Azure 入口網站中的 [我的客戶]  ，以[檢視及管理客戶](view-manage-customers.md)。
