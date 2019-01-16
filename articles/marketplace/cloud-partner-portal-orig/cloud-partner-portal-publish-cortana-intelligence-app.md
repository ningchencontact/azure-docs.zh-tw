---
title: Cortana Intelligence | Microsoft Docs
description: 發行 Cortana Intelligence 供應項目。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: a6357640ceeb7c5f1bde4eea85aa5265990935f4
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157151"
---
# <a name="publish-a-cortana-intelligence-offer-using-the-cloud-partner-portal"></a>使用 Cloud Partner 入口網站發行 Cortana Intelligence 供應項目

此文章將描述如何使用 Cloud Partner 入口網站來發行 Cortana Intelligence 供應項目。

## <a name="prerequisites"></a>必要條件

Cloud Partner 入口網站支援對該入口網站進行角色型存取，讓參與者能夠在發行供應項目上進行共同作業。 如需詳細資訊，請參閱 [Cloud 入口網站管理使用者](./cloud-partner-portal-manage-users.md)。

在代表發行者帳戶發行供應項目之前，必須有一個具有「擁有者」\"\"角色的人同意遵守[使用條款](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[Microsoft 隱私權聲明](https://www.microsoft.com/privacystatement/default.aspx)與 [Microsoft Azure 認證方案合約](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)。

## <a name="to-start-creating-a-cortana-inteligence-offer"></a>開始建立 Cortana Inteligence 供應項目

一旦符合所有先決條件後，您便已準備好開始製作您的 Cortana Inteligence 供應項目。

1.  登入 [Cloud Partner 入口網站](http://cloudpartner.azure.com/)。
2.  在左側導覽列上，選取 [+ 新增供應項目]。
3. 選取 [Cortana Intelligence]。
4. 選取 [新增供應項目] 檢視中的 [編輯器] 索引標籤來設定下列選項：
    -   供應項目設定
    -   技術資訊
    -   店面詳細資料
    -   連絡人

    上述每個選項都會顯示一個您要填寫的表單。每個表單的必填欄位都會標示紅色星號 (\*)。

## <a name="to-configure-offer-settings"></a>設定供應項目設定

[供應項目設定] 會提供有關供應項目的基本資訊，例如供應項目識別碼、發行者識別碼和供應項目名稱。

### <a name="offer-id"></a>供應項目識別碼

這是在發行者設定檔內供應項目的唯一識別碼。
此識別碼會顯示於產品 URL 中。 此識別碼只能包含小寫英數字元或連字號 (-)。 此識別碼不能以連字號結尾，且不能超過 50 個字元。 
>[!Note]
>供應項目上架後，此欄位便會鎖定。

**範例：**

如果發行者 **contoso** 使用供應項目識別碼 *sample-Cortana Intelligence* 來建立供應項目，它將會在 AppSource 中顯示為 "https://appsource.microsoft.com/marketplace/apps/**contoso**.*sample-Cortana Intelligence*?tab=Overview"。

### <a name="publisher-id"></a>發行者識別碼

發行者設定檔的下拉式清單。 用來選擇您想要在哪個發行者設定檔之下發行此供應項目。

>[!Note]
>供應項目上架後，此欄位便會鎖定。

### <a name="name"></a>Name

您供應項目的顯示名稱。 此名稱會顯示在 [AppSource](https://appsource.microsoft.com) 中。 它最多不能超過 50 個字元。

當您完成提供 [供應項目設定] 中所需的資訊之後，選取 [儲存] 以繼續前進到供應項目的 [技術資訊] 部分。 選項。

## <a name="to-configure-technical-info"></a>設定技術資訊

使用此檢視來提供將顯示於您供應項目頁面中的技術資訊。 下列欄位會套用到此檢視。

### <a name="partner-mpn-id"></a>合作夥伴 MPN 識別碼

如果您是已註冊的 Microsoft 合作夥伴，請移至[合作夥伴網站](https://partners.microsoft.com/)並登入，以取得您的組織合作夥伴識別碼。針對**合作夥伴 MPN 識別碼**輸入該識別碼。

### <a name="analytics-components-used"></a>使用的分析元件

選取您供應項目要使用的所有元件。 至少選取 1 個元件。 只有當您使用 SQL 2016 R 服務的任一個 MRS 授權、HDInsight Premium 或在 VM 上執行的 MRS 時，才需要選取 [Microsoft R]。

### <a name="additional-components-used"></a>使用的其他元件

選取您解決方案要使用的所有其他元件。

### <a name="customer-name-using-solution"></a>使用解決方案的客戶名稱

提供在生產環境中使用此解決方案的客戶名稱。 

>[!Note]
>此資訊不會在 AppSource 上發行。 僅用於解決方案評估。

### <a name="azure-consumption-requirement-met"></a>符合 Azure 使用量需求？

指出解決方案是否會針對 Azure 套件元件的每位客戶每月至少產生 $1 K，或者解決方案是否會使用 Microsoft R。

>[!Note]
>此資訊不會在 AppSource 上發行。 僅用於解決方案評估。

### <a name="demo-video-url"></a>示範影片 URL

提供預售小組可向客戶顯示之解決方案/應用程式示範影片的 URL。 

>[!Note]
>此資訊不會在 AppSource 上發行。 僅用於解決方案評估。

#### <a name="demo-video-guidelines"></a>示範影片指導方針

- 影片長度應該小於 15 分鐘。
- 此影片應該以最低限度的編輯方式來錄製解決方案的功能。 
- 此影片會重點摘要使用者互動功能的重要層面，並將焦點放在進階分析的整合上。 
- 示範影片不會公開提供給客戶，但要能具體呈現解決方案給潛在客戶。 因此，這些影片應備有腳本，並且能重複播放。
- 使用任一種畫面錄製工具，匯出標準影片格式 (例如 MPEG) 來建立您的影片。 

下列指示會示範如何使用商務用 Skype 來建立影片。 

1. [開始會議](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
2. [共用您的桌面](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
3. [開始錄製](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
4. 在您停止錄製後，[使用錄製管理員來發佈您的錄製影片](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)

將錄製的影片上傳至服務，讓您能夠產生共用的 URL。 例如，[OneDrive 或 Sharepoint 中的來賓連結](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)。

### <a name="supported-regions"></a>支援區域

選取您解決方案支援的所有區域。 至少選取 1 個區域。

### <a name="power-bi-desktop-file-pbix"></a>Power BI Desktop 檔案 (.pbix)

如果適當，請上傳 .pbix 檔案。 確定已將該資料匯入到檔案，而不是以外部方式進行參考。 我們將為您建立內嵌報表。

### <a name="solution-architecture"></a>方案架構

上傳可詳細說明方案架構的文件。 如需上傳方案架構圖表的方針，請參閱[進階分析解決方案工作流程範本](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx)。

>[!Note]
>此資訊不會在 AppSource 上發行。 僅用於解決方案評估。

### <a name="select-segments"></a>選取區段

選取所有相關的產業區段。 如果您的應用程式適用於任何未列出的區段，請在 [其他] 欄位中輸入區段名稱。 您只能針對此區段使用三個字組。

### <a name="select-business-processes"></a>選取商務程序

選取最能說明解決方案的商務程序。 如果您的應用程式適用於任何未列出的程序，請在 [其他] 欄位中輸入程序名稱。 您只能針對此程序使用三個字組。

### <a name="trial-info"></a>試用資訊

-   **SaaS 試用 URL：** 輸入適用於您應用程式試用體驗的 URL。
-   **試用產品試用 URL：** 輸入適用於您應用程式試用產品體驗的 URL。

如需試用的詳細資訊，請參閱此文章下一節中的**應用程式類型**。

當您完成提供 [技術資訊] 中所需的資訊之後，選取 [儲存] 以繼續前進到供應項目的 [電子店面詳細資料] 部分。 

## <a name="to-configure-storefront-details"></a>設定電子店面詳細資料

### <a name="offer-summary"></a>供應項目摘要

這是供應項目價值主張的摘要。 它將出現在供應項目的搜尋頁面上。 此摘要的長度上限為 100 個字元。

### <a name="offer-description"></a>供應項目描述

這是將出現在應用程式詳細資料頁面上的描述。
描述的長度上限為 1300 個字元。

請注意，此欄位所採用的 html 內容含有 \<p\>、\<h1\>、\<h2\>、\<li\> 等標記，可讓您的內容更美觀。 發行入口網站小組正在致力開發一個功能，使開發人員能看到其電子店面詳細資料的預覽，讓內容更美觀，同時，您可以使用任何線上即時 HTML 工具 (例如 [http://htmledit.squarefree.com](http://htmledit.squarefree.com/)) 來看看您的描述看起來的樣子。

建議的描述格式是根據價值主張將文字細分為小節，而每個小節都有醒目提示的子標題。 訪客通常會快速瀏覽 [供應項目摘要] 欄位和子標題，以快速了解該應用程式能處理之內容的重點，以及為何他們應考慮使用該應用程式。 因此，請務必抓住使用者的注意力，以使他們願意繼續閱讀來了解細節。\'

#### <a name="partner-examples"></a>合作夥伴範例

- [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview) \(英文\)
- [Plexure Retail Personalization](https://appsource.microsoft.com/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview) \(英文\)
- [AvePoint Citizen Services](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview) \(英文\)

### <a name="industries"></a>產業

請選取您應用程式最適用的產業。 如果您的應用程式與多個產業有關，請將此欄位保留空白。

### <a name="categories"></a>類別

選取與您應用程式相相關的類別。 最多選取兩個類別。

### <a name="app-type"></a>應用程式類型

選取您的應用程式將在 AppSource 上支援的試用類型。 選擇下列其中一個試用：
- **免費**表示您的應用程式免費。
- **試用**表示客戶可以在指定期間內試用您的應用程式。
- **試用要求**表示客戶可以要求試用應用程式。

合作夥伴可以在 AppSource 上提供兩種類型的試用體驗。

- **試用**選項 (也稱為**客戶導向的試用 (CLT)**) 可以是下列其中一個類型： 
    - SaaS 試用
        - 客戶可以瀏覽至您或您的合作夥伴所提供的 URL。 客戶會通過 AAD 同盟的 SSO 來獲得有時間限制的試用體驗。
        - 這是具有多租用戶的 SaaS 應用程式，其會將使用者的設定/資料與其他使用者隔離。 或者，此體驗只提供使用唯讀作業的子集。

        **範例：**

        - [AFS POP Retail Execution](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview) \(英文\)
        - [AvePoint Citizen Services](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview) \(英文\) (此應用程式會針對一組選取的使用者角色提供為您打造的體驗及清晰的路徑)。

     - 試用產品
        - 您 (或您合作夥伴) 的解決方案或其子集可以使用 AppSource 可具現化的 Azure Resource Manager 範本來封裝。 AppSource 可以透過有時間限制並維護執行個體的熱/冷集區等，來管理合作夥伴訂用帳戶中的應用程式。
        - 如果您選擇此選項，我們可以提供範本和範例程式碼來協助您。

        **範例：**

        - [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome) \(英文\)

- **要求試用** (**合作夥伴導向的試用 / PLT**) 選項會要求客戶填寫連絡資訊表單，讓合作夥伴可進一步採取動作。 合作夥伴會進行後續動作，並提供應用程式的示範或試用。 如需詳細資訊，請參閱 [AppSource 試用體驗逐步解說](https://aka.ms/trialexperienceforwebapps) \(英文\) 影片以取得高階概述。

>[!Note]
>資料顯示**客戶導向的試用**的潛在客戶開發可能性高於**合作夥伴導向的試用**。


### <a name="help-link-for-your-app"></a>您應用程式的說明連結

提供包含應用程式說明資訊的頁面 URL。

### <a name="supported-countriesregions"></a>支援的國家/地區

此欄位會決定您供應項目將可供試用的國家/地區。

![支援的國家/區域](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot3.png)

### <a name="supported-languages"></a>支援的語言

選取您應用程式支援的語言。 如果應用程式支援的語言不在此清單上，請發行您的供應項目，並寄送電子郵件給我們：<appsource@microsoft.com>，讓我們能夠了解對其他語言的支援。

### <a name="app-version"></a>應用程式版本

輸入應用程式的版本號碼。

### <a name="products-your-app-works-with"></a>與您的應用程式一起運作的產品

列出與您的應用程式一起運作的特定產品。 您最多可以列出 3 個產品。 若要列出產品，請選取**加號** (新增旁邊)，即會為您建立開放式文字欄位。 輸入與您的應用程式一起運作的產品名稱。

### <a name="hide-key"></a>隱藏金鑰

這是與供應項目預覽 URL 相結合的金鑰，可從公用檢視中隱藏該供應項目。 它不是密碼。 您可以輸入任何英數字元的字串。

### <a name="offer-logo-small"></a>供應項目標誌 (小型)

此標誌會出現在應用程式的搜尋頁面上。 Png 影像格式是唯一允許的格式。 映像大小是 48 像素 x 48 像素。

### <a name="offer-logo-large"></a>供應項目標誌 (大型)

此標誌會出現在應用程式的詳細資料頁面上。 Png 影像格式是唯一允許的格式。 映像大小是 48 像素 x 48 像素。

### <a name="video"></a>影片

最多可上傳四段影片。 針對每段您想要上傳的影片：
- 提供影片名稱
- 提供 URL (僅限 YouTube 或 Vimeo)
- 提供要與影片產生關聯的縮圖。 縮圖必須使用 png 影像格式，而其大小必須是 1280 像素 X 720 像素。 

若要新增新影片，請選取 [+ 新增]，如下一個螢幕擷取畫面所示。

![新增新影片](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot4.png)

### <a name="document"></a>文件

您最多可以上傳三份文件。 每份文件都必須使用 PDF 檔案格式。 新增新文件：

- 選取 [+ 新增]
- 輸入文件名稱
- 選取 [上傳] 以上傳文件。

![新增新文件](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot5.png)

### <a name="privacy-policy"></a>隱私權原則

輸入應用程式隱私權原則的 URL

### <a name="terms-of-use"></a>使用規定

輸入您應用程式的使用條款。 AppSource 客戶必須先接受這些條款，才能試用您的應用程式。

>[!Note]
>此欄位會使用 HTML 標記來接受內容，例如 html 內容 <p\>、<h1\> 及 <li\>。 您可以使用這些標記來將內容格式化。 

### <a name="lead-destination"></a>潛在客戶目的地

選取將儲存您潛在客戶的 CRM 系統。 

如果您使用下列其中一個 CRM 系統，請選取 [Azure 資料表]：Salesforce、Marketo 或 Microsoft Dynamics CRM。 

若要取得您想要使用的 CRM 系統詳細資訊，請選取下列其中一個支援系統的連結。

-   [Azure 資料表](./cloud-partner-portal-lead-management-instructions-azure-table.md)
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
-   [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
-   [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)
