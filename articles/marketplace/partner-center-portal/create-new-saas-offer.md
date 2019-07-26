---
title: 在商業 Marketplace 中建立新的 SaaS 供應專案
description: 如何使用 Microsoft 合作夥伴中心的商業 Marketplace 入口網站, 建立新的軟體即服務 (SaaS) 供應專案, 以便在 Azure Marketplace、AppSource 或透過雲端解決方案提供者 (CSP) 方案中進行列出或銷售。
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: a4947349e64d5f9bf95a9213701dc62a0e018b8f
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501312"
---
# <a name="create-a-new-saas-offer"></a>建立新的 SaaS 供應專案

若要開始建立軟體即服務 (SaaS) 供應專案, 請確定您先[建立合作夥伴中心帳戶](./create-account.md), 並開啟 [[商業 Marketplace] 儀表板](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), 並選取 [**總覽**] 索引標籤。

![合作夥伴中心的商業 Marketplace 儀表板](./media/new-offer-overview.png)

選取 [+**新增供應**專案]。 按鈕, 然後選取 [**軟體即服務**] 功能表項目。 

如果您選取其中一個供應專案類型, 系統會將您重新導向至較舊的[Cloud Partner 入口網站](https://cloudpartner.azure.com/)。  目前只有在合作夥伴中心的商用 Marketplace 入口網站提供 SaaS 供應專案。 

![在合作夥伴中心建立供應專案視窗](./media/new-offer-click.png)

[**新增供應**專案] 對話方塊隨即顯示。 

![[新增供應專案] 對話方塊](./media/new-offer-popup.png)


## <a name="offer-id-and-alias"></a>供應專案識別碼和別名

- **供應項目識別碼**：您帳戶中每個供應專案的唯一識別碼。 在 marketplace 供應專案的 URL 位址中, 客戶會看到此識別碼, 而 Azure Resource Manager 範本 (如果適用)。 供應專案識別碼必須是小寫、英數位元 (包括連字號和底線, 但不能有空格)。 這限制為50個字元, 且在您選取 [*建立*] 之後無法變更。  
範例: 測試-供應專案-1
<br>產生 URL:`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **供應專案別名**:用來在合作夥伴中心入口網站中參考供應專案的名稱。 此名稱不會在 marketplace 中使用, 而且與供應專案*名稱*和其他會向客戶顯示的值不同。 在您選取 [*建立*] 之後, 就無法變更這個值。

<br>範例:測試供應專案1&#8482;

選取 [建立]。  系統會為此供應專案建立供應專案的 **[總覽**] 頁面。  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>供應項目概觀

[**供應專案總覽**] 頁面包含: 

- **發行狀態**會顯示發佈此供應專案所需步驟的視覺標記法, 以及完成每個步驟所需的時間。 不完整的發行步驟圖示將會呈現灰色。 

- [**供應專案總覽**] 功能表包含在此供應專案上執行作業的連結清單。 這份動作清單會根據您對供應專案所做的選擇而變更。  
    - 如果供應專案為草稿–刪除草稿 
    - 如果供應專案上線–停止銷售供應專案 
    - 如果供應專案處於預覽狀態–上線 
    - 如果您尚未完成發行者登出–取消發行

## <a name="offer-setup"></a>供應專案設定

[**供應專案設定**] 索引標籤會要求您提供下列資訊。 完成這些欄位之後, 請選取 [**儲存**]。

- **您想要透過 Microsoft 銷售嗎？** (是/否)
    - **是**, 您想要透過 microsoft 銷售您的供應專案, microsoft 會代表您託管 marketplace 交易;或 
    - **不**可以, 您只想要透過 marketplace 來列出您的供應專案, 並獨立處理任何與 Microsoft 無關的貨幣交易。    

### <a name="sell-through-microsoft"></a>透過 Microsoft 銷售

透過 Microsoft 銷售可提供更好的客戶探索和收購, 讓 Microsoft 代表您裝載 marketplace 交易, 並利用 Microsoft 全球可用的商務功能。

#### <a name="saas-offer-requirements"></a>SaaS 供應專案需求

若要在合作夥伴中心上以商業 Marketplace 列出軟體即服務 (SaaS) 供應專案, 必須符合下列準則:

- 您的供應專案必須使用[Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/)來進行身分識別管理和驗證。
- 您的供應專案必須使用[SaaS 履行 api](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)來與 Azure Marketplace 整合。
- 如需更廣泛的需求, 請參閱[SaaS 供應專案發佈指南](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)。

#### <a name="saas-on-azure-billing-infrastructure-costs"></a>Azure 計費基礎結構成本上的 SaaS
如果 SaaS 供應專案裝載于 Azure 中, 則身為發行者的您必須考慮使用 Azure 基礎結構的費用, 並將軟體授權費用視為單一成本專案。 這種成本會以固定的每月費用表示給客戶。 Azure 基礎結構的使用方式是由合作夥伴直接管理和計費。 客戶不會看到實際的基礎結構使用量費用。 發行者通常選擇將 Azure 基礎結構使用量費用算在他們的軟體授權定價中。 

軟體授權費用會以每月定期以網站為基礎的訂用帳戶, 以隨用隨付的方式呈現, 且不會以計量付費或使用。

|**您的授權成本**|**每月 $100**|
|:---|:---|
|Azure 使用量成本 (D1/1 核心)|直接向發行者收費，不是向客戶收費|
|客戶是由 Microsoft 計費|每月 $100.00 (發行者必須考慮授權費用中任何產生或通過的基礎結構成本)|

- 在此案例中，Microsoft 針對您的軟體授權收費 $100.00，並支付 $80.00 給發行者。
- 已符合**Marketplace 服務費用**的合作夥伴, 將會在2019年 6 2020 月30日前的 SaaS 供應專案上看到較少的交易費用。 在此案例中, Microsoft 會為您的軟體授權帳單 $100.00, 並向發行者收取 $90.00。

> [!NOTE]
> **減少 Marketplace 服務費用**:對於您在我們的商業 Marketplace 上發佈的特定 SaaS 供應專案, Microsoft 會將其 Marketplace 服務費用從 20% (如 Microsoft 發行者合約中所述) 降到 10%。 為了讓您的供應專案符合資格, 至少必須將您的其中一個供應專案指定為 IP 共同銷售就緒或 IP 共同銷售優先。  至少必須符合每個行事曆月份結束前五 (5) 個工作天的資格, 才能收到該月份的縮減 Marketplace 服務費用。  降低的 Marketplace 服務費用不適用於 Vm、受管理的應用程式, 或透過我們的商業 Marketplace 提供的任何其他產品。  優惠的 Marketplace 服務費用僅適用于 Microsoft 在2019年5月1日到2020日之間所收集之授權費用的合格供應專案。  在這段時間之後, Marketplace 服務費用會回到其一般金額。 

|**Microsoft 收取的費用**|**每月 $100**|
|:---|:---|
|Microsoft 向您支付授權成本的 80% <br>**針對合格的 SaaS 應用程式, Microsoft 會支付您的授權成本 90%*|每月 $80.00 <br>*$* 每月 90.00 *|


#### <a name="csp-program-opt-in"></a>CSP 方案加入宣告
[雲端解決方案提供者 (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers)方案可讓軟體供應專案透過最少的行銷和銷售投資, 觸及數百萬位合格的 Microsoft 客戶。

- **通道將供應專案提供給 CSP 方案** (核取方塊)

選取在 CSP 方案中提供您的供應專案, 可讓雲端解決方案提供者銷售您的產品, 做為其客戶配套解決方案的一部分。 

### <a name="list-through-microsoft"></a>透過 Microsoft 列出

藉由建立 marketplace 清單, 向 Microsoft 推廣您的業務。 選擇只列出您的供應專案, 而不是透過 Microsoft, 表示 Microsoft 不會直接參與軟體授權交易。 沒有相關聯的交易費用, 發行者會保留從客戶收集之任何軟體授權費用的 100%。 不過, 發行者負責支援軟體授權交易的所有層面, 包括但不限於: 訂單履行、計量、帳單、發票、付款和集合。 

- **您要如何讓潛在客戶與此清單供應專案互動？**

##### <a name="get-it-now-free"></a>立即取得 (免費)
提供可存取您應用程式的有效 URL (從*HTTP*或*HTTPs*開始), 免費向客戶列出您的供應專案。  例如：`https://contoso.com/saas-app`

##### <a name="free-trial-listing"></a>免費試用 (清單)
藉由提供有效的 URL (從*HTTP*或*HTTPs*開始), 向客戶列出您的供應專案, 其可透過[使用 Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)以單鍵驗證的方式來取得試用版。  例如： `https://contoso.com/trial/saas-app` 。 供應專案列出免費試用版是由您的服務所建立、管理及設定, 而且沒有由 Microsoft 管理的訂用帳戶。

> [!NOTE]
> 您的應用程式將透過試用連結接收的權杖, 只能用來透過 Azure AD 取得使用者資訊, 以在您的應用程式中自動建立帳戶。 Microsoft 帳戶 (MSA) 不支援使用此權杖進行驗證。

##### <a name="contact-me"></a>與我連絡
藉由連接客戶關係管理 (CRM) 系統來收集客戶連絡人資訊。 系統會要求客戶提供共用其資訊的許可權。 這些客戶詳細資料以及他們找到您供應專案的供應專案名稱、識別碼和 marketplace 來源, 都會傳送至您所設定的 CRM 系統。 如需設定 CRM 的詳細資訊, 請參閱[連接潛在客戶管理](#connect-lead-management)。 

## <a name="example-marketplace-offer-listing"></a>範例 marketplace 供應專案清單

![範例 marketplace 供應專案清單與附注](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>啟用試用產品

試用產品是向潛在客戶展示您的供應專案的絕佳方式, 方法是讓他們選擇「在購買前試用」, 進而提升轉換並產生高度合格的潛在客戶。 [深入瞭解試用產品。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **啟用試用**產品checkbox 

藉由啟用試用產品, 系統會要求您設定示範環境, 讓客戶可以在一段固定時間內試用您的供應專案。 

### <a name="type-of-test-drive"></a>試用產品的類型

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** :部署範本, 其中包含組成您解決方案的所有 Azure 資源。 符合此案例的產品只會使用 Azure 資源。
- **[Dynamics 365 For Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** :Microsoft 會裝載並維護商業中央企業資源規劃系統 (財務、營運、供應鏈、CRM 等) 的試用產品服務 (包括布建和部署)。  
- **[Dynamics 365 For Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** :Microsoft 會裝載並維護客戶參與系統 (銷售、服務、專案服務、現場服務等) 的試用產品服務 (包括布建和部署)。  
- **[適用于作業的 Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** :Microsoft 會託管並維護財務和營運企業資源規劃系統 (財務、營運、製造、供應鏈等) 的試用產品服務 (包括布建和部署)。 
- **[邏輯應用程式](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** :包含所有複雜解決方案架構的部署範本。 任何自訂產品都應該使用這種類型的試用產品。
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** :自訂建立儀表板的內嵌連結。 想要示範互動式 Power BI 視覺效果的產品應該使用這種類型的試用產品。 您只需要上傳內嵌的 Power BI URL。

#### <a name="additional-test-drive-resources"></a>其他試用產品資源
- [試用技術最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [試用產品行銷最佳做法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [試用產品總覽一頁](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>連接潛在客戶管理

藉由在 marketplace 中列出您的供應專案並連結客戶關係管理 (CRM) 系統, 直接與客戶連線, 讓您可以在客戶有興趣或部署您的之後, 立即收到客戶連絡人資訊。基礎.

- **選擇潛在客戶目的地**(下拉式功能表):提供連線詳細資料給您希望我們傳送客戶潛在客戶的 CRM 系統。 

合作夥伴中心支援下列適用于潛在客戶管理的 CRM 系統。 選取設定指示的連結。

- Azure Blob –提供連絡人電子郵件、容器名稱和儲存體帳戶連接字串。 
- [Azure 資料表](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table)–提供連絡人電子郵件和儲存體帳戶連接字串。 
- [DYNAMICS CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) –提供連絡人電子郵件、URL 和驗證模式 (Office 365 或 Azure Active Directory)。
- [Https 端點](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https)–提供連絡人電子郵件和 HTTPS 端點 URL。 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) –提供連絡人電子郵件、表單識別碼、MUNCHKIN 帳戶識別碼和伺服器識別碼。
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) -提供連絡人電子郵件和組織識別碼。 

#### <a name="additional-lead-management-resources"></a>其他潛在客戶管理資源
- [潛在客戶管理常見問題](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常見的潛在客戶設定錯誤](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潛在客戶管理總覽一頁](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

繼續進行下一節之前, 請記得先**儲存**!

## <a name="properties"></a>Properties
[**屬性**] 索引標籤會要求您定義用來將您的供應專案分組在 marketplace 上的類別和產業、支援您供應專案的法律合約, 以及您的應用程式版本。 

完成這些欄位之後, 請選取 [**儲存**]。 

### <a name="category"></a>Category
選取最少一個 (1) 和最多三個 (3) 分類, 用來將您的供應專案分組至適當的 marketplace 搜尋區域。 請在供應專案描述中, 打電話給您的供應專案如何支援這些類別。 

### <a name="industry"></a>產業
選取最多兩個 (2) 用來將您的供應專案分組至適當 marketplace 搜尋區域的產業。 如果您的供應專案並非產業專屬, 請不要選取其中一個。 請在供應專案描述中, 打電話給您的供應專案如何支援選取的產業。 

### <a name="app-version"></a>應用程式版本
這是在 AppSource marketplace 中用來識別供應專案版本號碼的選擇性欄位。 

### <a name="standard-contract"></a>標準合約

- **使用標準合約嗎？**

為了簡化客戶的採購程式, 並降低軟體廠商的法律複雜度, Microsoft 提供了標準合約範本, 以協助在 marketplace 中進行交易。 

Azure Marketplace 發行者可以選擇在標準合約下提供其軟體, 而不是製作自訂的條款及條件, 而是客戶只需要審查並接受一次。 

您可以在以下位置找到標準合約 https://go.microsoft.com/fwlink/?linkid=2041178:。

#### <a name="terms-of-use"></a>使用規定

如果您的授權條款與標準合約不同, 您可以選擇在此輸入您自己的法律使用規定。 您也可以在此欄位中輸入最多10000個字元的文字。 如果您的使用規定需要較長的描述, 請在此欄位中輸入單一 URL 連結, 您可以在其中找到額外的授權條款。 它會向客戶顯示為作用中連結。

客戶必須先接受這些條款，才能試用您的應用程式。 

繼續進行下一節之前, 請記得先**儲存**!

## <a name="offer-listing"></a>供應專案清單

[供應專案清單] 索引標籤會顯示您的供應專案可供使用的語言 (和市場), 目前的英文 (美國) 是唯一可用的位置。 此外, 此頁面會顯示特定語言清單的狀態, 以及它所加入的日期/時間。 您將需要針對每個語言/市場定義 marketplace 詳細資料 (供應專案名稱、描述、搜尋詞彙等)。

> [!NOTE]
> 提供清單內容 (例如供應專案描述、檔、螢幕擷取畫面、使用規定和隱私權原則) 不一定要使用英文, 前提是該供應專案的開頭必須是片語「此應用程式僅適用于 [非英文語言]。」 您也可以提供*有用的連結 URL*來提供內容, 而不是供應專案清單內容所使用的語言。

### <a name="offer-listings"></a>供應項目清單

提供要顯示在 marketplace 中的詳細資料, 包括供應專案和行銷資產的說明。

- **名稱**(必要):此處定義的名稱會顯示為您所選 marketplace 上的供應專案清單標題。 系統會根據您先前的**新供應**專案, 預先填入名稱。  這可能是商標的。  這不能包含空白字元、emoji (除非它們是商標和著作權符號), 而且必須限制為50個字元。
- **摘要**(必要):提供供應專案的簡短描述, 以用於 marketplace 清單搜尋結果。 最多可以在此欄位中輸入100個字元的文字。
- **描述**(必要):提供您供應專案的描述, 以顯示在 marketplace 清單總覽中。 請考慮包含價值主張、主要優點、任何類別或產業關聯、應用程式內購買機會、任何必要的公開, 以及深入瞭解的連結。
最多可以在此欄位中輸入3000個字元的文字。 如需其他秘訣, 請參閱[撰寫絕佳的應用程式描述](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description)一文。
- **搜尋關鍵字**:輸入最多三個搜尋關鍵字, 客戶可以用來在 marketplace 中尋找您的供應專案。
- **快速入門指示**(必要):說明如何設定並開始使用您的應用程式來提供潛在客戶。  本快速入門可以包含更詳細的線上檔連結。 最多可以在此欄位中輸入3000個字元的文字。 

#### <a name="description"></a>**描述**

此為必填欄位。 要包含在描述中的專案: 

* 在您的描述中的前幾個句子清楚描述您供應項目的價值主張。  
* 請記住，前幾個句子可能會顯示在搜尋引擎結果中。  
* 請勿依賴特色與功能來銷售您的產品。 反之，要鎖定在您所提供的價值。  
* 請盡可能使用業界特定詞彙或凸顯優點的字眼。 

您的價值主張核心部分應該包含下列資訊： 

* 產品的描述。 
* 可從產品獲益的使用者類型。 
* 客戶需要或痛苦的產品位址。 

若要讓您的供應專案描述更吸引人, 您可以使用 HTML 標籤來設定描述的格式。 

1. 如果您想要建立段落, 請`<p>`在文字的乞求新增, 並在`</p>`結尾加入。

    **範例**： 

    `<p>`這是我的第一個段落。 `</p>` <br>
    `<p>`這是我的第二段。 `</p>` <br>

    上述內容看起來像這樣:

    <p> 這是我的第一個段落。 </p>
    <p> 這是我的第二段。 </p>

1. 如果您想要新增專案的點**符清單**, 請將您的`<li>`文字放在下列標記內。 您可以複製和貼上更多點符專案 ( `<li>`和`</li>`標記之間的專案`<ul>` ) `</ul>`和標記內。 請確定您已新增`<ul></ul>`。 

    **範例**：

    ```
    <ul> 
        <li>add text here</li> 
        <li> add text here </li> 
        <li> add text here </li> 
    </ul> 
    ```

    上述內容看起來像這樣:
    <ul> 
        <li>在此加入文字</li> 
        <li> 在此加入文字 </li> 
        <li> 在此加入文字 </li> 
    </ul> 

1. 若要以**粗體顯示**內容, 請在您想要以粗體顯示的文字`</b>`開頭新增`<b>` , 並在您想要以粗體顯示的文字結尾處新增。 

    **範例**：`<b>`免費試用`</b>`
    
    上述專案會導致店面中的供應專案描述中的「免費試用」字樣為粗體。 

    **免費試用**

1. 若要在您的內容之間加上`<br>`分行符號, 請在您想要在新行上啟動的內容之前新增。 如果您想要保留一個空格, 並確定內容是從新的一行開始, `<br><br>`請在內容之前新增。 

    **範例**：

    這是文字行。 `<br>`這是將在新行中啟動的文字行。 `<br><br>`這是將在下面兩行開頭的一行。 

    上述內容看起來像這樣:

    這是文字行。 <br> 這是將在新行中啟動的文字行。 <br><br> 這是將在下面兩行開頭的一行。 

1. 如果您想要**增加文字的大小**, 請先選擇文字要多大。 使用下列範例。 選取文字大小之後, 請將對應`<H*></H*>`的標記加入至文字的開頭和結尾。 

    **範例**：

    `<h1>`這是標題1`</h1>` <br>
    `<h2>`這是標題2`</h2>` <br>
    `<h3>`這是標題3`</h3>` <br>
    `<h4>`這是標題4`</h4>` <br>
    `<h5>`這是標題5`</h5>` <br>
    `<h6>`這是標題6`</h6>` 

    上述內容看起來像這樣:

    <h1>這是標題1</h1> 
    <h2>這是標題2</h2> 
    <h3>這是標題3</h3> 
    <h4>這是標題4</h4> 
    <h5>這是標題5</h5> 
    <h6>這是標題6</h6> 

#### <a name="links"></a>連結

- **隱私權原則**(必要):連結至貴組織的隱私權原則。 貴使用者必須負責確保您的應用程式符合隱私權法律和法規, 以及提供有效的隱私權原則
- **CSP 計畫行銷材料**(選擇性):如果您選擇將您的供應專案延伸至[雲端解決方案提供者 (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers)方案, 則必須提供行銷資料的連結。 CSP 藉由讓 CSP 合作夥伴組合、行銷及轉售您的供應專案, 來將您的供應專案延伸至更廣泛的合格客戶。 這些轉銷商將需要存取行銷您供應專案的素材。 如需詳細資訊, 請參閱[進入市場服務](https://partner.microsoft.com/reach-customers/gtm)。
- **有用的連結**(選擇性):有關您的應用程式的選擇性補充線上檔, 或提供**標題**和**URL**所列出的相關服務。 按一下 [ **+ 新增 URL**] 來新增其他有用的連結。

#### <a name="contact-information"></a>連絡資訊

- **連絡人**:針對每個客戶連絡人, 提供員工**姓名**、**電話號碼**和**電子郵件**位址。  (這些*不會*公開顯示)。 **支援連絡人**群組也需要**支援 URL** 。  (此資訊*將*會公開顯示)。

**支援連絡人**(必要):以取得一般支援問題。

**工程連絡人**(必要):技術問題。

**頻道管理員連絡人**(必要):適用于與 CSP 計畫相關的轉售商問題。

#### <a name="files-and-images"></a>檔案和影像

- **檔**(必要):為您的供應專案新增相關的行銷檔 (PDF 格式), 每個供應專案最少可提供一 (1) 個或最多三 (3) 份檔。
- **影像**(選擇性):您的供應專案標誌影像可能會出現在 marketplace 的多個位置, 需要下列大小--小型:48 x 48 圖元 _(必要)、_ 中:90 x 90 圖元, 大型:216 x 216 圖元 _(必要),_ 寬:255 x 115 圖元和主圖:815 x 290 圖元。 所有映射都必須在中。PNG 格式。
- **螢幕擷取畫面**(必要):新增示範您供應專案的螢幕擷取畫面。 最多可新增五個 (5) 個螢幕擷取畫面, 且應以 1280 x 720 圖元大小。 所有映射都必須在中。PNG 格式。
- 影片(選擇性):新增示範您供應專案之影片的連結。 您可以使用 YouTube 和/或 Vimeo 影片的連結，這會隨著您的供應項目顯示給客戶。 您也需要輸入影片的縮圖影像, 並將其大小調整為 1280 x 720 圖元 (PNG 格式)。 每個供應專案最多可以顯示四個影片。

繼續進行下一節之前, 請記得先**儲存**!

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 清單資源

- [Marketplace 供應專案清單的最佳做法](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>預覽

[**預覽**] 索引標籤可讓您定義有限的**預覽物件**, 以便在將您的供應專案即時發佈到更廣泛的 marketplace 物件之前發行供應專案。

> [!IMPORTANT]
> 您必須先選取 [上線], 供應專案才會在預覽中核取您的供應專案之後, 即時發佈給 marketplace 公用物件。

- **定義預覽物件:每行新增一個 AAD/MSA 帳戶電子郵件, 以及選擇性的描述。**

手動新增最多十 (10) 個電子郵件地址, 或在上傳 CSV 檔案時, 針對現有的 Microsoft 帳戶 (MSA) 或 Azure Active Directory 帳戶進行二十 (20), 以協助驗證您的供應專案, 然後再即時發佈。 藉由新增這些帳戶, 您就可以在將供應專案發佈至 marketplace 之前, 定義允許預覽存取權的物件。 如果您的供應專案已上線, 您仍然可以定義預覽物件, 以測試您的供應專案是否有任何變更或更新。

> [!NOTE]
> 預覽物件與私用物件不同。 預覽物件可以在 marketplace 中即時發佈_之前, 先_允許其存取您的供應專案。 您也可以選擇建立方案, 使其僅供私用物件使用。 在 [**方案清單**] 索引標籤中, 您可以使用 [**這是私用方案**] 核取方塊來定義私用物件。 然後, 您可以使用 Azure 租使用者識別碼, 為多達20000的客戶定義私用物件。

## <a name="technical-configuration"></a>技術設定

[**技術**設定] 索引標籤會定義用來連接到您的供應專案的技術詳細資料 (URL 路徑、webhook、租使用者識別碼和應用程式識別碼)。 此連線可讓我們為終端客戶布建您的供應專案 (如果他們選擇取得)。 在[SaaS 履行 api](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)的檔中可取得說明所收集欄位使用方式的圖表。

- **登陸頁面 URL**(必要):定義客戶從 marketplace 取得您的供應專案後將居住的網站 URL。 此 URL 會是當客戶路由傳送至頁面時, 接收權杖的端點。 您可以在履行 Api 中使用 resolve, 交換該權杖以提供布建詳細資料。 您所收集的這些詳細資料和任何其他專案, 都可以用來作為您的經驗, 以完成註冊並啟用其購買的客戶互動網頁的一部分。

- **連接 webhook**(必要):針對 Microsoft 代表客戶傳送給您所需的所有非同步事件 (範例：SaaS 訂用帳戶已失效), 我們要求您提供連接 webhook。 如果您還沒有 webhook 系統, 最簡單的設定就是讓 HTTP 端點邏輯應用程式接聽張貼的任何事件, 然後適當地處理 (例如 HTTPs:\//prod-1westus.logic.azure.com:443/work)。 如需詳細資訊，請參閱[在邏輯應用程式中透過 HTTP 端點呼叫、觸發或巢狀處理工作流程](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint)。

- **Azure AD 租使用者識別碼**(必要):在 Azure 入口網站內, 我們會要求您[建立 Azure Active Directory (AD) 應用程式](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal), 以便驗證這兩個服務之間的連線是在驗證通訊之後。 若要尋找[租使用者識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in), 請移至您的 Azure Active Directory 並選取 [**屬性**], 然後尋找列出的**目錄識別碼**(例如 50c464d3-4930-494c-963c-1e951d15360e)。

- **Azure AD 應用程式識別碼**(必要):您也需要[應用程式識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)和驗證金鑰。 若要取得這些值, 請移至您的 Azure Active Directory 並選取 [**應用程式註冊**], 然後尋找所列的**應用程式識別碼**編號 (例如 50c464d3-4930-494c-963c-1e951d15360e)。 若要尋找驗證金鑰, 請移至 [**設定**], 然後選取 [**金鑰**]。 您必須提供 [描述] 和 [持續時間], 然後才會提供數值。

 請注意, Azure 應用程式識別碼會與您的發行者識別碼相關聯, 因此請確定您的所有供應專案都使用相同的應用程式識別碼。

## <a name="plan-overview"></a>計畫總覽

[**計畫總覽**] 索引標籤可讓您在相同的供應專案中提供各種方案選項。 這些方案 (有時稱為 Sku) 在版本、營收或服務層級方面可能有所不同。 您必須至少設定一個方案, 才能在 marketplace 中銷售您的供應專案。

建立之後, 您將會看到您的方案名稱、識別碼、定價模型、可用性 (公用或私人)、目前發行狀態, 以及任何可用的動作。

**計畫總覽**中的可用**動作**會因方案的目前狀態而有所不同, 可能包括:

- 如果方案狀態為**草稿**–刪除草稿
- 如果方案狀態是**Live** –停止銷售方案或同步私用物件

**建立新方案**(針對選擇透過 Microsoft 銷售的使用者, 至少須規劃一項方案)

- **方案識別碼:** 為此供應專案中的每個方案建立唯一的方案識別碼。 [產品 URL] 和 [Azure Resource Manager 範本] (如果適用) 中的客戶會看到此識別碼。 僅使用小寫、英數位元、虛線或底線。 此方案識別碼最多可以有50個字元。 請注意, 在選取 [建立] 之後, 就無法修改識別碼。
- **方案名稱:** 當決定要在您的供應專案內選取哪一個方案時, 客戶會看到此名稱。 為此供應專案中的每個方案建立唯一的供應專案名稱。 方案名稱是用來區分可能屬於相同供應專案一部分的軟體方案 (例如 供應項目名稱:Windows Server;計畫Windows Server 2016、Windows Server 2019)。

### <a name="plan-listing"></a>計畫清單

[**方案清單**] 索引標籤會顯示您的方案可用的語言 (和市場), 目前的英文 (美國) 是唯一可用的位置。 此外, 此頁面會顯示特定語言清單的狀態, 以及它所加入的日期/時間。 您將需要針對每個語言/市場定義 marketplace 詳細資料 (供應專案名稱、描述、搜尋詞彙等)。

#### <a name="plan-listing-details"></a>方案清單詳細資料

選取其中一個方案語言會顯示**計畫清單**資訊, 包括**名稱**和**描述。**

- **名稱**：會根據您的 [預覽]**新方案**專案預先填入, 並顯示為您供應專案「軟體方案」的標題, 並顯示在 marketplace 中。
- **說明：** 此描述可讓您瞭解此軟體方案的獨特之處, 以及與您的供應專案內其他軟體方案有何差異。 最多可包含500個字元。

完成這些欄位之後, 請選取 [**儲存**]。

#### <a name="plan-pricing-and-availability"></a>規劃定價和可用性

[**定價與可用性**] 索引標籤可讓您設定此方案將在其中使用的市場、所需的營收模型、價格和計費期限。 此外, 您還可以指出是否要讓所有人都能看到此計畫, 或僅針對特定客戶 (私用物件)。

##### <a name="enabling-free-trials"></a>啟用免費試用

透過商業 marketplace 提供的 SaaS 供應專案, 可讓您在透過 Microsoft 銷售時, 提供一個月的免費試用版。 對於計量付費方案以外的所有帳單模型和條款, 支援免費試用版。 此選項可讓客戶透過一個月的免費存取, 取得低屏障的專案。  如果您選擇在供應專案內啟用方案的免費試用版, 客戶將無法在初始一個月期間結束前轉換為付費訂用帳戶。  在這段期間, 購買供應專案的客戶可以試用已啟用免費試用的任何支援方案, 並在其間進行轉換。  付費訂閱的轉換會在期限結束時自動完成。

>[!Note]
>如果客戶選擇在沒有免費試用的情況下轉換成方案, 將會進行轉換, 但免費試用將會立即遺失。  此外, 一旦客戶開始支付方案費用, 他們就無法再于相同的訂用帳戶上再次取得免費試用版, 即使他們轉換為支援免費試用的 SKU 也一樣。

您供應專案中的每個方案都可以使用設定免費試用的功能。 只要流覽至每個供應專案的定價和可用性, 並核取該方塊即可允許一個月的試用。

![一個月免費試用核取方塊](./media/free-trial-enable.png)

若要取得目前參與免費試用之客戶訂用帳戶的相關資訊, 請使用`isFreeTrial`新的 API 屬性, 它會標示為 true 或 false。 如需詳細資訊, 請參閱[SaaS 取得訂](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription)用帳戶 API。

>[!Note]
>運用 marketplace 計量服務的方案不支援免費試用。

#### <a name="markets"></a>市場

- **編輯市場**選擇性

必須至少有一個市場提供每個方案。 選取您想要讓此方案可供使用的任何市場位置的核取方塊。 包含一個搜尋方塊和按鈕, 用於選取「稅金已匯款」的國家/地區, 其中 Microsoft 免除銷售並代表您使用稅額, 以協助。 


如果您已經在美國美元 (USD) 中設定方案的價格, 並新增另一個市場位置, 新市場的價格將會根據目前的匯率計算。 在發佈之前, 您應該一律檢查每個市場的價格。 儲存變更之後, 您可以使用 [匯出價格 (.xlsx)] 連結來檢查定價。

#### <a name="pricing"></a>定價

- **定價模式**：以一般費率或基座為基礎

**一般費率:** 以單一每月或全年價格的原價價格, 來存取您的供應專案。 這有時稱為以網站為基礎的定價。 使用此計價模式時, 您可以選擇性地定義使用 marketplace 計量服務 API 的計量付費方案, 以根據非標準單位向客戶收費。  如需計量付費計費的詳細資訊, 請參閱[使用 marketplace 計量服務的計量付費](./saas-metered-billing.md)。

**以基座為基礎:** 依據存取供應專案或佔用基座的使用者人數, 使用價格來存取您的供應專案。 這個以基座為基礎的模型可讓您根據價格來設定允許的基座數目下限和上限。 如此一來, 您可以設定多個方案, 根據使用者數目來設定不同的價格點。  這些欄位為選擇性。 如果保留空白, 基座數目將會被視為沒有限制 (最小值為 1, 最多可支援系統)。 這些欄位可能會在您的方案更新中編輯。

發佈之後, 就無法變更計費計價模式選項。 此外, 相同供應專案的所有方案都必須共用相同的計價模式。

- **計費期限**:每月或每年

選取客戶必須支付所列價格的頻率。 至少必須提供一個月或年度費用, 或這兩個選項都可供客戶使用。

- **價格**:每月美元或每年美元

以當地貨幣 (美元 = 美國貨幣) 設定的價格, 會使用安裝期間目前可用的匯率, 轉換為所有選定市場的本地貨幣。 在發佈之前, 請先匯出定價試算表, 並查看每個市場的價格, 以驗證這些價格。 如果您想要在個別市場中設定自訂價格, 請修改並匯入定價試算表。 您必須負責驗證此定價並擁有這些設定。
**您必須先儲存定價變更, 以啟用匯出定價資料。*

發行之前, 請先仔細檢查您的價格, 因為在計畫發行後可能會變更的部分有一些限制:

- 方案發佈之後, 就無法變更定價模式。
- 為方案發佈計費期限之後, 稍後就無法移除。
- 一旦您的方案中的市場價格發佈之後, 就無法再變更。

### <a name="plan-audience"></a>規劃物件

您可以選擇將每個方案設定為每個人都可看見, 或僅供您選擇的特定物件使用。 您可以使用 Azure AD 的租使用者識別碼, 為此限制的物件指派成員資格。

#### <a name="privacy"></a>隱私權聲明

- **這是私用計畫**(選擇性核取方塊)

核取此方塊, 將您的方案設為私用, 而且只對您選擇的限制物件是可見的。 發行為私用方案之後, 您可以更新物件, 或選擇將方案提供給所有人。 一旦方案發行為每個人都可以看到, 每個人都必須保持可見。 (方案無法再次設定為私用計畫)。

- **限制的物件 (租使用者識別碼)**

指派可存取此私人方案的物件。 使用租使用者識別碼來指派存取權, 並使用選項來包含指派給每個租使用者識別碼的描述。 若要匯入 .csv 試算表檔案, 最多可以新增10個租使用者識別碼, 或20000客戶租使用者識別碼。

租使用者是組織的標記法, 其識別碼表示為 GUID (全域唯一識別碼, 用來識別資源的128位整數編號)。 它是組織或應用程式開發人員在與 Microsoft 建立關係 (例如註冊 Azure、Microsoft Intune 或 Microsoft 365) 時收到的 Azure AD 專屬執行個體。 每個 Azure AD 租用戶都不同，並與其他 Azure AD 租用戶分開。 若要檢查租使用者, 請使用您要用來管理應用程式的帳戶登入 Azure 入口網站。 如果您有租用戶，系統會自動將您登入，而且您會在您帳戶名稱正下方看到租用戶名稱。 將滑鼠停留在 Azure 入口網站右上方的帳戶名稱上，以查看您的姓名、電子郵件、目錄/租用戶識別碼 (一個 GUID) 與您的網域。 如果帳戶與多個租用戶相關聯，您可以選取帳戶名稱開啟功能表，以切換租用戶。 每個租用戶都有自己的租用戶識別碼。 您也可以在下列位置使用功能變數名稱 URL 來查詢組織的租使用者識別碼: [https://www.whatismytenantid.com](https://www.whatismytenantid.com)。

雖然 SaaS 提供使用租使用者識別碼來定義私人物件, 但其他供應專案類型可能會使用 Azure 訂用帳戶識別碼 (也會以 Guid 表示)。

> [!NOTE]
> 私用物件 (或受限制的物件) 與預覽物件不同。 在 [ **[預覽](#preview)** ] 索引標籤中, 您可以定義預覽物件。 在 marketplace 中發佈供應專案*之前*, 允許預覽物件存取您的供應專案。 雖然私用物件指定僅適用于特定方案, 但預覽物件可以查看所有方案 (私用或非), 但僅限在測試和驗證計畫時的有限預覽期間。

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Marketplace 供應專案中的方案清單範例

![包含附注的範例 marketplace 方案清單](./media/marketplace-plan.svg)

## <a name="test-drive"></a>試用產品

[**試用**產品] 索引標籤可讓您設定示範 (或「試用產品」), 讓客戶可以在承諾購買之前先試用您的供應專案。 若要深入瞭解, 請參閱[什麼是試用產品？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)一文。 如果您不想再為供應專案提供試用產品, 請返回 [供應專案 **[設定](#offer-setup)** ] 頁面, 然後取消核取 [**啟用試用**產品]。

### <a name="technical-configuration"></a>技術設定
下列是可用的試用產品類型, 每個都有自己的技術設定需求。

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [邏輯應用程式](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives)(不需要技術設定)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager 試用產品的技術設定

部署範本, 其中包含組成您解決方案的所有 Azure 資源。 符合此案例的產品只會使用 Azure 資源。 深入瞭解如何設定 Azure Resource Manager 的[試用](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)產品。

- **區域**(必要):目前有26個 Azure 支援的區域, 可讓您試用產品。 一般來說, 您會想要讓試用產品在您預期最大客戶數目的區域中使用, 以便他們可以選取最接近的區域, 以獲得最佳效能。 您必須確定您的訂用帳戶可以在您所選取的每個區域中部署所需的所有資源。

- **Instances**：選取類型 (經常性存取或非經常性存取) 和可用的實例數目, 這會乘以供應專案可用的區域數目。

經常性:這種類型的實例已部署, 並會在每個選取的區域等待存取。 客戶可以立即存取試用產品的*熱*實例, 而不必等待部署。 缺點是，這些執行個體一直在您的 Azure 訂用帳戶中執行，因此將會產生可觀的運作成本。 強烈建議至少有一個*熱*實例, 因為大部分的客戶不想等候完整部署, 因此如果沒有可用的*熱*實例, 客戶使用量就會有下降。

**冷**:這種類型的實例代表每個區域可能部署的實例總數。 冷實例需要整個試用產品 Resource Manager 範本, 以便在客戶要求試用產品時進行部署, 因此*冷*實例的載入速度會比*熱*實例慢很多。 取捨是您只需要支付試用產品的持續時間, 而*不*一定會在您的 Azure 訂用帳戶上執行, 就像使用*熱*實例一樣。

- **試用產品 Azure Resource Manager 範本**:上傳包含您 Azure Resource Manager 範本的 .zip。  若要深入瞭解如何建立 Azure Resource Manager 範本, 請參閱[使用 Azure 入口網站建立和部署 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)的快速入門文章。

- **試用產品持續時間**(必要):輸入試用產品將維持作用中狀態的時間長度 (以小時為單位)。 在此持續時間過後，試用產品將會自動終止。 這個持續時間可能只會以整數小時數來設定 (例如"2" 小時, "1.5" 無效)。

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 試用產品的技術設定

Microsoft 可以藉由使用這種類型的試用產品來裝載和維護服務布建和部署, 藉此降低設定試用產品的複雜性。 無論試用產品是以商業中心、客戶參與或營運物件為目標, 此類型的託管試用產品的設定都相同。

- **並行試用**產品數上限(必要):設定一次可以使用試用產品的客戶數目上限。 當試用產品在使用中時, 每個並行使用者都會取用 Dynamics 365 授權, 因此您必須確保您有足夠的授權可支援所設定的上限。 建議值為 3-5 個。

- **試用產品持續時間**(必要):藉由定義時數, 輸入試用產品將維持作用中狀態的時間長度。 過了數小時之後, 會話就會結束, 而且不再使用您的其中一個授權。 我們建議的值為2-24 小時, 視您的供應專案複雜度而定。 這個持續時間可能只會以整數小時數來設定 (例如"2" 小時, "1.5" 無效)。  使用者可以要求新的會話 (如果工作時間已用盡), 並想要再次存取試用產品。

- **實例 URL**(必要):客戶將開始其試用產品的 URL。 一般來說, 您的 Dynamics 365 實例 URL 會執行您的應用程式, 並安裝範例 https://testdrive.crm.dynamics.com) 資料 (例如)。

- **實例 WEB API URL**(必要):藉由登入您的 Microsoft 365 帳戶並流覽至 [**設定** \&] >, 抓取 Dynamics 365 實例的 Web API URL。**自訂**\&gt;**開發人員資源**\&gt;**實例 WEB API (服務根 URL)** , 複製此處找到的 URL (例如 https://testdrive.crm.dynamics.com/api/data/v9.0) )。

- **角色名稱**(必要):提供您在自訂 Dynamics 365 試用產品中定義的安全性角色名稱。 這會在其試用產品 (例如, 測試磁片磁碟機-角色) 期間指派給使用者。

#### <a name="technical-configuration-for-logic-app-test-drive"></a>邏輯應用程式試用產品的技術設定

任何自訂產品都應該使用此類型的試用產品部署範本, 其中包含各種複雜的解決方案架構。 如需設定邏輯應用程式試用產品的詳細資訊, 請造訪 GitHub 上的[作業](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md)和[客戶參與](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)。

- **區域**([必要]、[單一選取] 下拉式清單):目前有26個 Azure 支援的區域, 可讓您試用產品。 邏輯應用程式的資源將會部署在您選取的區域中。 如果您的邏輯應用程式有任何自訂資源儲存在特定區域中, 請確定已在此處選取該區域。 若要這麼做, 最佳方式是在入口網站中的 Azure 訂用帳戶本機上完整部署邏輯應用程式, 並確認其正常運作, 然後再進行此選取。

- **並行試用**產品數上限(必要):設定一次可以使用試用產品的客戶數目上限。 這些試用產品已部署, 讓客戶可以立即存取它們, 而不需要等候部署。

- **試用產品持續時間**(必要):輸入試用產品將維持作用中狀態的時間長度 (以小時為單位)。 試用產品會在此時間週期結束後自動終止。

- **Azure 資源組名**(必要):輸入您的邏輯應用程式試用產品儲存所在的[Azure 資源組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)名。

- **Azure 邏輯應用程式名稱**(必要):輸入將試用產品指派給使用者的邏輯應用程式名稱。 此邏輯應用程式必須儲存在上述的 Azure 資源群組中。

- 取消布建**邏輯應用程式名稱**(必要):輸入客戶完成後, 取消布建試用產品的邏輯應用程式名稱。 此邏輯應用程式必須儲存在上述的 Azure 資源群組中。

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>不需要 Power BI 試用產品的技術設定

想要示範互動式 Power BI 視覺效果的產品可以使用內嵌連結來共用自訂的儀表板作為其試用產品, 而不需要進一步的技術設定。 深入瞭解如何設定[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)範本應用程式。

### <a name="deployment-subscription-details"></a>部署訂用帳戶詳細資料

若要代表您部署試用產品, 請建立並提供另一個唯一的 Azure 訂用帳戶。 (不需要用於 Power BI 試用產品)。

- **Azure 訂**用帳戶識別碼(Azure Resource Manager 和邏輯應用程式所需):輸入訂用帳戶識別碼, 以授與存取權給您的 Azure 帳戶服務, 以進行資源使用量報告和計費。 我們建議您考慮[建立個別的 Azure 訂](https://docs.microsoft.com/azure/billing/billing-create-subscription)用帳戶, 以用於試用產品 (如果您還沒有的話)。 您可以藉由登入[Azure 入口網站](https://portal.azure.com/)並流覽至左側功能表的 [**訂閱**] 索引標籤, 來尋找您的 AZURE 訂用帳戶識別碼。 選取此索引標籤會顯示您的訂用帳戶識別碼 (例如 "a83645ac-1234-5ab6-6789-1h234g764ghty")。

- **Azure AD 租使用者識別碼**(必要):輸入您的 Azure Active Directory (AD)[租使用者識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要尋找此識別碼, 請登入[Azure 入口網站](https://portal.azure.com/), 選取左側功能表中的 [Active Directory] 索引標籤, 選取 [**屬性**], 然後尋找列出的**目錄識別碼**(例如 50c464d3-4930-494c-963c-1e951d15360e)。 您也可以在下列位置使用您的功能變數名稱 URL 來查詢組織的租使用者[https://www.whatismytenantid.com](https://www.whatismytenantid.com)識別碼:。

- **Azure AD 租使用者名稱**(動態365所需):輸入您的 Azure Active Directory (AD) 名稱。 若要尋找此名稱, 請登入[Azure 入口網站](https://portal.azure.com/), 在右上角, 您的租使用者名稱會列在 [您的帳戶名稱] 底下。

- **Azure AD 應用程式識別碼**(必要):輸入您的 Azure Active Directory (AD)[應用程式識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要尋找此識別碼, 請登入[Azure 入口網站](https://portal.azure.com/), 選取左側功能表中的 [Active Directory] 索引標籤, 選取 [**應用程式註冊**], 然後尋找所列的**應用程式識別碼**編號 (例如 50c464d3-4930-494c-963c-1e951d15360e)。

- **Azure AD 應用程式用戶端密碼**(必要):輸入您的 Azure AD 應用程式[用戶端密碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 若要尋找此值, 請登入[Azure 入口網站](https://portal.azure.com/)。 選取左側功能表中的 [ **Azure Active Directory** ] 索引標籤, 選取 [**應用程式註冊**], 然後選取您的試用產品應用程式。 接下來, 依序選取 [**憑證和秘密**]、[**新增用戶端密碼**]、[描述]、[**永不** **過期**], 然後選擇 [**新增**]。 請務必向下複製值。 (在執行此動作之前, 請不要離開頁面, 否則您將無法存取此值。)

繼續進行下一節之前, 請記得先**儲存**!

### <a name="test-drive-listings-optional"></a>試用產品清單 (選擇性)

在 [**試用**產品] 索引標籤下找到的 [**試用產品清單**] 選項會顯示您的試用產品可供使用的語言 (和市場), 目前的英文 (美國) 是唯一可用的位置。 此外, 此頁面會顯示特定語言清單的狀態, 以及它所加入的日期/時間。 您必須為每個語言/市場定義試用產品的詳細資料 (描述、使用者手冊、影片等等)。

- **描述**(必要):描述您的試用產品、要示範的專案、要進行實驗的目標、探索的功能, 以及可協助使用者決定是否要取得您供應專案的任何相關資訊。 最多可以在此欄位中輸入3000個字元的文字。 

- **存取訊號**(Azure Resource Manager 和邏輯試用產品所需):說明客戶必須知道才能存取和使用此試用產品的內容。 逐步解說使用您的供應專案的案例, 以及客戶在整個試用產品中存取功能的確切方式。 最多可以在此欄位中輸入10000個字元的文字。

- **使用者手冊**(必要):試用產品體驗的深入逐步解說。 使用者手冊應涵蓋您想要讓客戶從試用產品中取得的確切內容, 並做為參考, 以取得他們可能會遇到的任何問題。 檔案必須是 PDF 格式, 並在上傳後命名為 (最多255個字元)。

- **視頻新增影片** (選擇性):您可以將影片上傳至 YouTube 或 Vimeo, 並在這裡參考連結和縮圖影像 (533 x 324 圖元), 讓客戶可以查看資訊的逐步解說, 以協助他們更瞭解試用產品, 包括如何成功使用您的提供並瞭解強調其優點的案例。
  - **名稱**具備
  - **URL (僅限 YouTube 或 Vimeo)** 具備
  - **縮圖 (533 x 324px)** :影像檔案必須是 PNG 格式。

完成這些欄位之後, 請選取 [**儲存**]。

## <a name="publish"></a>發行

#### <a name="submit-offer-to-preview"></a>提交供應專案以供預覽

當您完成供應專案的所有必要區段之後, 請選取入口網站右上角的 [**發佈**]。 系統會將您重新導向至 [**審查及發佈**] 頁面。 

如果這是您第一次發佈此供應專案, 您可以:

- 請參閱供應專案每個區段的完成狀態。
    - *未啟動*–表示區段尚未觸及, 需要完成。
    - *不完整*–表示區段中有需要修正的錯誤, 或需要提供更多資訊的詳細資訊。 請返回一節並加以更新。
    - *Complete* –表示區段已完成, 所有必要的資料都已提供, 而且沒有任何錯誤。 供應專案的所有區段都必須處於「完整」狀態, 您才能提交供應專案。
- 將測試指示提供給認證小組, 以確保您的應用程式已正確測試, 以及有助於瞭解應用程式的任何補充注意事項。
- 選取 [**提交**] 以提交供應專案進行發佈。 我們會傳送一封電子郵件給您, 讓您知道供應專案的預覽版本何時可供您審查和核准。 您必須回到合作夥伴中心, 並為供應專案選取 [**上線**], 以將您的供應專案發佈至公用 (或私用供應專案給私人物件)。

## <a name="next-steps"></a>後續步驟

- [更新商用 Marketplace 中的現有供應專案](./update-existing-offer.md)
