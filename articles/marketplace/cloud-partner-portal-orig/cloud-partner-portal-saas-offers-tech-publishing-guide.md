---
title: SaaS 應用程式技術發行指南 | Microsoft Docs
description: 說明將 SaaS 應用程式供應項目發行到適當的市集。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 7e648d6d3bd8d8ac14fb3f9723f4667c1cc33ac6
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620178"
---
<a name="saas-application-technical-publishing-guide"></a>SaaS 應用程式技術發行指南
===========================================

歡迎使用 SaaS 應用程式技術發行指南！ 此指南旨在協助候選發行者與現有發行者，讓其能夠使用 SaaS 應用程式供應項目來列出其在 AppSource 或 Azure Marketplace 中的應用程式與服務。

如需所有其他 Marketplace 供應項目的概觀，請參閱 [Marketplace 發行者指南](https://aka.ms/sellerguide)。


<a name="what-are-pre-requisites-for-publishing-a-saas-app"></a>發行 SaaS 應用程式的先決條件有哪些？
-------------------------------------------------

[Cloud Partner 入口網站](https://cloudpartner.azure.com)提供對入口網站的角色型存取，可讓多個人就發行供應項目共同作業。 如需詳細資訊，請參閱[管理使用者](./cloud-partner-portal-manage-users.md)。 

在代表發行者帳戶發行供應項目之前，必須有一個具有「擁有者」角色的人同意遵守[使用規定](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[Microsoft 隱私權聲明](https://www.microsoft.com/privacystatement/default.aspx)與 [Microsoft Azure 認證方案合約](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


<a name="creating-an-offer"></a>建立供應項目
-----------------

此節說明建立新 SaaS 應用程式供應項目的程序。

![SaaS 供應項目概觀](media/cpp-creating-saas-offers/saas-offer-overview.png)

SaaS 應用程式供應項目有五個區段組成，如下表所述：

| **[供應項目] 區段**  | **說明**                                                                                                     |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------|
| 供應項目設定     | 協助定義 SaaS 應用程式的唯一名稱與識別碼                                                                         |
| 技術資訊     | 協助設定 SaaS 解決方案類型，並提供您應用程式的連線詳細資料                            |
| 試用產品         | (選擇性區段) 可讓您定義將允許客戶在購買之前測試您供應項目的服務。 |
| 店面詳細資料 | 包含行銷、法律、潛在客戶管理與清單區段：   <br/> - [行銷] 區段可讓您輸入描述與標誌，讓供應項目能適當地在市集使用者入口網站中顯示。  <br/> - [潛在客戶管理] 可讓您定義一個位置，以將在 Azure Marketplace 使用者入口網站中產生的使用者潛在客戶重新導向到其中。  <br/> - [法律] 區段可讓您輸入隱私權原則與使用規定法律文件。  |
| 連絡人            | 可讓您輸入供應項目支援連絡人資訊。                                                                  |
|  |  |


### <a name="creating-a-new-offer"></a>建立新的供應項目

登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com)之後，請選取左側功能表列 (其中顯示可用供應項目功能表) 上的 [新增供應項目] 項目。 下圖顯示這些供應項目的範例：

![SaaS 新增供應項目](media/cpp-creating-saas-offers/saas-new-offer.png)

從清單選取已核准的供應項目，隨即開啟新供應項目表單。

![SaaS 新供應項目表單](media/cpp-creating-saas-offers/saas-new-offer-2.png)

下表描述供應項目欄位：

| **供應項目欄位** | **說明**                                                                                            |
|------------------|----------------------------------------------------------------------------------------------------------- |
| 供應項目識別碼         | 發行者設定檔內供應項目的唯一識別碼。 此識別碼會顯示在產品的 URL 與計費報告中。 此識別碼只能包含小寫英數字元或連字號 (-)。 此識別碼不能以連字號結尾，且不能超過 50 個字元。 請注意，供應項目上架後，此欄位便會鎖住。 例如，若發行者 Contoso 發行供應項目識別碼為 sample-vm 的供應項目，它將會在 Azure Marketplace 中顯示為：[https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview](https://azuremarketplace.microsoft.com/) |
| 發行者識別碼     | 發行者識別碼是您在 Marketplace 中的唯一識別碼。 您的所有供應項目都應該連結到您的發行者識別碼。 一旦儲存供應項目，即無法修改發行者識別碼。                                                                                       |
| 名稱             | 您供應項目的顯示名稱。 此名稱會顯示在 Azure Marketplace 與 Azure 入口網站中。 它最多不能超過 50 個字元。 建議在產品中包含明顯的品牌名稱。 請勿在這裡包含您的公司名稱，除非這是您的行銷方式。 如果您在自己的網站行銷這項供應項目，請確認名稱與您網站的名稱相同。             |
|  |  |

按一下 [儲存] 以儲存您的進度。 下一節說明新增方案到您的供應項目。


### <a name="technical-information"></a>技術資訊

[技術資訊] 區段可讓您輸入下列資訊：

![SaaS 供應項目技術資訊](media/cpp-creating-saas-offers/saas-new-offer-technical-info.png)

最重要的決定是您將會擁有 SaaS 清單或啟用商務功能。 若您是 SaaS 清單，您必須選取下列其中一個項目：

-   免費 - 提供可供客戶取得您應用程式的 SaaS 應用程式 URL。
-   免費試用 - 提供可供客戶在購買供應項目之前先進行試用的 SaaS 應用程式 URL。
-   連絡我 - 只有當您已連結潛在客戶管理系統時才有效，此選項允許詢問客戶是否可與其聯繫，而且將會與您分享潛在客戶資訊。

若您是 Azure Marketplace 上的 SaaS 應用程式且想要透過 Microsoft 交易啟用商務功能，請選取 [透過 Azure 銷售]。  
如需有關連結您 SaaS 應用程式的詳細資訊，請參閱 [SaaS - 透過 Azure 銷售](./cloud-partner-portal-saas-offer-subscriptions.md)。


### <a name="test-drive"></a>試用產品

為您的客戶建立試用體驗是確定他們能有信心購買的最佳做法。 在可用的試用選項中，試用產品是獲得高品質潛在客戶及提高那些潛在客戶轉換為付費客戶之轉換率最有效的方式。

它可讓客戶以自我引導方式實際操作您試用產品的主要功能與優點，而且這是以真實世界實作案例來示範。

![SaaS 供應項目試用產品](media/cpp-creating-saas-offers/saas-new-offer-test-drive.png)


#### <a name="how-a-test-drive-works"></a>試用產品如何運作

潛在客戶在 Marketplace 上搜尋並探索您的應用程式。 客戶登入並同意使用規定。 此時，客戶收到您預先設定的環境，並在固定的數小時內試用，而您也得到可進行後續追蹤的最適合潛在客戶。

![SaaS 供應項目試用產品 2](media/cpp-creating-saas-offers/saas-new-offer-test-drive-2.png)

無論您的應用程式多複雜，Microsoft 試用產品都能協助您將產品提供給客戶。 我們根據產品類型、案例和您所在的 Marketplace，提供三種不同類型的試用產品。

-   **Azure Resource Manager** - 「Azure ARM 試用產品」是部署範本，其中包含組成發行者所建置之解決方案的所有 Azure 資源。 適用於此類型試用產品的產品為僅使用 Azure 資源的產品。
-   **邏輯應用程式** -「邏輯應用程式試用產品」是部署範本，其目的是要包含所有複雜解決方案架構。 所有 Dynamics 應用程式或自訂產品，都應該使用此類型的試用產品。
-   **Power BI** -「Power BI 試用產品」由自訂建置儀表板的內嵌連結所組成。 任何要示範互動式 Power BI 視覺效果的產品，都應該使用此類型的試用產品。 您只需要上傳內嵌的 Power BI URL。

新增試用產品的主要發行步驟是：

1.  定義您的試用產品案例
2.  建置和/或修改您的 Resource Manager 範本
3.  建立您的試用產品逐步手冊
4.  重新發行您的供應項目

如需詳細資訊，請參閱[試用產品](./what-is-test-drive.md)。


### <a name="storefront-details"></a>店面詳細資料

SaaS 應用程式需要摘要的前兩節以及您為應用程式提供的相關描述。

![SaaS 供應項目店面詳細資料](media/cpp-creating-saas-offers/saas-new-offer-storefront-details.png)

下表說明供應項目的店面詳細資料：

| **供應項目欄位**        | **說明**                                                                                          |
|-------------------------| ---------------------------------------------------------------------------------------------------------|
| 供應項目摘要           | 供應項目的價值主張摘要。 它將會出現在您供應項目的搜尋頁面上。 且上限不可超過 100 個字元。   |
| 供應項目描述       | 將出現在您應用程式詳細資料頁面上的描述。 允許的上限是 1300 個字元*注意* 此欄位接受含標記的 HTML 內容，例如 &ltp\>, &lth1\>、 &lth2\>、&ltli\> 等，可讓您讓內容更美觀。 發行入口網站小組正在致力開發一個功能，讓開發人員能看到期店面詳細資料的預覽，讓內容更美觀，同時，您可以使用任何線上即時 HTML 工具 (例如 http://htmledit.squarefree.com) 來看看您的描述看起來的樣子。 |
| 產業              | 選取您供應項目的目標產業。 若您的應用程式與多個產業相關，您最多可以選取兩個產業。 |
| 建議的類別    | 選取您供應項目的目標類別。 您最多可以選取三個類別。     |
| 應用程式版本     | 輸入您應用程式的版本號碼                                                                |
| 搜尋關鍵字 (最多 3 個) | 輸入最多三個搜尋關鍵字，客戶可以使用這些關鍵字在 Marketplace 店面網站尋找您的應用程式。 |
|  |  |


### <a name="marketing-artifacts"></a>行銷成品

[行銷成品] 區段可讓您定義 Azure Marketplace 行銷資產，例如標誌、影片、螢幕擷取畫面與文件：

![SaaS 供應項目行銷成品](media/cpp-creating-saas-offers/saas-new-offer-marketing-artifacts.png)

下表說明行銷欄位：

| **供應項目欄位** | **說明**                                                                                                          |
|------------------| ------------------------------------------------------------------------------------------------------------------------ |
| 標誌            | 若您**透過 Azure 銷售** SaaS 應用程式，您應該提供所有標誌影像。 若您只是陳列，則只需要 2 個標誌。 在 [Cloud Partner 入口網站](https://cloudpartner.azure.com)中上傳的所有標誌都必須遵循下列指導方針：     <br/> - 保持標誌上具有最少的主要和次要色彩數目。 Azure 設計具有簡單的調色盤。     <br/> - 避免使用黑色或白色做為您標誌的背景色彩。 Azure 入口網站的佈景主題色彩是黑色與白色。 您應該改為使用會讓您的標誌在 Azure 入口網站顯得突出的某些色彩。 建議您使用簡單的主要色彩。 如果您使用透明背景，請確定標誌與文字不是黑色、白色或藍色。     <br/> - 不要在標誌上使用漸層背景。     <br/> - 避免在標誌上放置文字 (即使是公司或品牌名稱)。 您標誌的外觀與風格應該是「一般」，而且應該避免漸層。    <br/> - 標誌不應該延展。                   |
| 影片           | 可讓您新增供應項目的影片連結。 您可以使用 YouTube 和/或 Vimeo 影片的連結，這會隨著您的供應項目顯示給客戶。 您也需要輸入影片的縮圖影像，它必須是大小為 1280x720 個像素的 PNG 影像。 您最多可以為每個供應項目新增四個影片。 |
| 文件        | 可讓您新增行銷文件到您的供應項目。 所有文件都必須是 PDF 格式，而且每個供應項目最多可以有三份文件。                                                                                                                                                      |
| 螢幕擷取畫面      | 可讓您新增您供應項目的螢幕擷取畫面。 您可以為每個供應項目新增最多五個螢幕擷取畫面。 螢幕大小上限是 1280x720 個像素。                                                                                                                                             |
| 實用連結     | 可讓您新增您供應項目的外部 URL 以協助指向架構圖或客戶可能會使用的其他網站。                                                                                                                                                              |
|  |  |

下圖顯示該資訊如何顯示在 Marketplace 搜尋結果中：

![Saas 供應項目行銷發行者資訊](media/cpp-creating-saas-offers/saas-new-offer-marketing-publisher-info.png)

下圖顯示當客戶按一下較小的供應項目圖格之後，供應項目如何顯示在 Marketplace 中：

![SaaS 供應項目行銷發行者資訊 2](media/cpp-creating-saas-offers/saas-new-offer-marketing-publisher-info-2.png)


### <a name="lead-management-and-legal-information"></a>潛在客戶管理與法律資訊

[法律] 區段可讓您設定供應項目的法律文件。
每個 SaaS 應用程式供應項目都需要兩個法律文件：隱私權原則與使用規定。 如需詳細資訊，請參閱  
[設定潛在客戶](./cloud-partner-portal-get-customer-leads.md)。

![SaaS 供應項目行銷法律資訊](media/cpp-creating-saas-offers/saas-new-offer-legal-info.png)

下表說明 [法律] 區段屬性：

| **供應項目欄位**   | **說明**                                                                                 |
|--------------------| ----------------------------------------------------------------------------------------------- |
| 隱私權原則 URL | 您公司隱私權原則的 URL。                                                       |
| 使用條款       | 您供應項目的使用規定。 在這裡輸入或複製並貼上使用規定。 允許使用基本 HTML 語法，例如此欄位接受具有諸如 &ltp\>、&lth1\>、&lth2\> 與 &ltli\> 等之標籤的 html 內容。*重要* 強烈建議您先在瀏覽器中檢閱並預覽您建立的 HTML，再提交供應項目。 |
|  |  |


### <a name="contact-information"></a>連絡人資訊

在此節中，您將輸入您公司中將負責支援使用此供應項目之客戶的支援連絡人。
有三著主要區域：[連絡人]、[支援連絡人] 與 [支援 URL]：

![SaaS 供應項目行銷連絡資訊](media/cpp-creating-saas-offers/saas-new-offer-contact-info.png)


| **連絡人**         | **說明**                                                                                                                          |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 工程連絡人 | 提供 Microsoft 可以在遇到任何支援與商業問題時與其連絡之工程連絡人的姓名、電子郵件與電話號碼。 |
| 支援連絡人     | 提供您的客戶在有支援要求時可與其連絡之連絡人的姓名、電子郵件、電話號碼與支援 URL。                  |
|  |  |


一旦您的供應項目就緒而且您點選 [發行]，供應項目將進入認證程序。 我們會透過人工驗證程序來測試您的 SaaS 應用程式，如果您是要陳列在市集中，會測試您的 URL；如果您已選取 [透過 Azure 銷售]，則會測試端點。 在此人工核准期間，我們也會決定將陳列您應用程式的適當店面 (AppSource、Azure Marketplace 或兩者)。

<a name="updating-the-offer"></a>更新供應項目
------------------

在您的供應項目發行並上架之後，您可以對它進行多種更新。 您對供應項目新版本所做的任何變更都應該儲存並重新發行，以在 Marketplace 中反映您所做的變更。

<a name="deleting-an-existing-offer"></a>刪除現有的供應項目
--------------------------

您可能決定從 Marketplace 中移除您的供應項目。 「移除供應項目」可確保新的客戶無法再購買或部署您的供應項目，但是對現有客戶沒有任何影響。 「終止供應項目」是終止服務及/或您與現有客戶之間授權合約的程序。

＜Microsoft Marketplace 發行者合約＞(請參閱第 7 節) 與＜參與原則＞(請參閱第 6.2 節) 會管理移除和終止供應項目的相關指導方針與原則。 此節提供有關支援的不同刪除案例與您可以採取的動作之相關資訊。

### <a name="delete-the-live-offer"></a>刪除上線的供應項目

要求移除上線的供應項目時，有幾方面需要注意。 請依照下列步驟來取得支援小組的指導方針，以從 Azure Marketplace 移除上線的供應項目︰

1.  使用此連結提出支援票證
2.  在 [問題類型] 清單中，選取 [管理供應項目] ，並在 [類別] 清單中選取 [修改已在生產環境中的供應項目和/或 SKU]。
3.  提交要求

支援小組會引導您完成供應項目刪除程序。

> [!NOTE] 
> 刪除供應項目不會影響供應項目的目前購買。 那些客戶購買將如同一往一樣繼續運作。
不過，刪除完成之後，該供應項目將不再提供購買。
