---
title: 如何透過 Cloud Partner 入口網站建立 Dynamics 365 for Operations 供應項目 | Microsoft Docs
description: 如何透過 Cloud Partner 入口網站建立 Dynamics 365 for Operations 供應項目
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: f2e0c7b335b09a0d2b8b8399e807a354ec8e4282
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805574"
---
# <a name="how-to-create-dynamics-365-for-operations-offer-via-cloud-partner-portal"></a>如何透過 Cloud Partner 入口網站建立 Dynamics 365 for Operations 供應項目

發行入口網站能提供對入口網站的角色型存取，可讓多個人員就發行供應項目共同作業。 請參閱 [Cloud 入口網站管理使用者](./cloud-partner-portal-manage-users.md)以取得更多資訊。

在代表發行者帳戶發行供應項目之前，必須有一個具有「擁有者」\"\"角色的人員同意遵守[使用條款](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[Microsoft 隱私權聲明](http://www.microsoft.com/privacystatement/default.aspx)與 [Microsoft Azure 認證方案合約](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)。

## <a name="how-to-create-a-new-dynamics-365-for-operations-offer"></a>如何建立新的 Dynamics 365 for Operations 供應項目

一旦符合所有先決條件後，您便已準備好開始製作您的 Dynamics 365 for Operations 供應項目。

1. 登入 [Cloud Partner 入口網站](http://cloudpartner.azure.com/)。
2. 從左側導覽列，按一下 [新增供應項目] 並選取 [Dynamics 365 for Operations]。
3. 接著會開啟新供應項目的 [編輯器] 檢視，我們便準備好開始進行製作。
4. 在左側 [編輯器] 檢視中，可以看到需要填寫的「表單」。 每個「表單」都包含需要填寫的欄位。必填欄位會標示紅色星號 (\*)。

針對製作 Dynamics 365 for Operations 供應項目，有四個主要的表單：

- 供應項目設定
- 技術資訊
- 店面詳細資料
- 連絡人

## <a name="how-to-fill-out-the-offer-settings-form"></a>如何填寫供應項目設定表單

供應項目設定表單是指定供應項目設定的基本表單。 下列說明各個不同欄位。

### <a name="offer-id"></a>供應項目識別碼

這是在發行者設定檔內供應項目的唯一識別碼。 此識別碼在產品 URL 中將是可見的。 此識別碼只能包含小寫英數字元或連字號 (-)。 此識別碼不能以連字號結尾，且不能超過 50 個字元。 供應項目上架後，此欄位便會鎖住。

例如，如果發行者 contoso 建立具有供應項目識別碼 *sample-dynamics365 for operations* 的供應項目，它將會在 AppSource 中顯示為 \"https://appsource.microsoft.com/marketplace/apps/**contoso**.*sample-dynamics 365 for operations*?tab=Overview\"

### <a name="publisher-id"></a>發行者識別碼

此下拉式表單，可讓您選擇您想要在什麼發行者設定檔之下發佈此供應項目。 供應項目上架後，此欄位便會鎖住。

名稱

您供應項目的顯示名稱。 這是會顯示在 [AppSource](https://appsource.microsoft.com) 中的名稱。 它最多不能超過 50 個字元。

按一下 [儲存] 以儲存您的進度。 下一個步驟是填寫供應項目的技術資訊。

## <a name="how-to-fill-out-the-technical-info-form"></a>如何填寫技術資訊表單

技術資訊表單包含將顯示於供應項目頁面中的資訊。 下面會說明不同欄位的指示。

![[新增供應項目] 畫面](./media/publish_d365_new_offer/Technical_info.png)

### <a name="solution-identifier"></a>解決方案識別碼

首先是您的解決方案識別碼。

1. 若要找到這個識別碼，請移至 [生命週期服務] 並選取 [解決方案管理]。
2. 選擇適當的解決方案之後，您將能在 [套件] 概觀中看見 [解決方案識別碼]。 \*\*如果識別碼為空白，請選取 [編輯] 並重新發佈您的套件，[解決方案識別碼] 便會出現。

### <a name="validation-assets"></a>驗證資產

請在此上傳您的 CAR (自訂分析報告)。

### <a name="does-solution-enable-translations"></a>解決方案是否啟用翻譯功能？

選取 [是]\'\' 或 [否]\'\'。

### <a name="does-solution-include-localizations"></a>解決方案是否包含當地語系化？

選取 [是]\'\' 或 [否]\'\'。

### <a name="product-version"></a>產品版本

選取 [新增 AX]。 最後，按一下 [儲存]。

## <a name="how-to-fill-out-storefront-details-form"></a>如何填寫 [電子店面詳細資料] 表單

首先是您的 [供應項目詳細資料]。

1. **供應項目摘要**

    \- 為解決方案輸入簡短的摘要 (最多 100 個字元)。

2. **供應項目描述**

    \- 為解決方案輸入簡短的描述。 您的描述應該具備解決方案的功能使用量，並直接配合您的 BPM 文件庫。 例如，如果您的行銷內容中有功能 X、Y 和 Z，我們將會在最後的檢閱期間確保這些功能會記錄在 LCS 內的 BPM 文件庫中。

![[店面詳細資料] 畫面](./media/publish_d365_new_offer/offer_details.png)

### <a name="listing-details"></a>清單詳細資料

![[店面詳細資料] 畫面](./media/publish_d365_new_offer/storefront_details.png)

1. **產業**：從所給予的選項中選取最多兩個產業。
2. **類別**：從所給予的選項中選取最多兩個類別。
3. **應用程式類型**：從所給予的選項中選取。
4. **應用程式的說明連結**：輸入解決方案的說明連結。
5. **支援的國家/地區**：從所給予的選項中選取。
6. **支援的語言**：從所給予的選項中選取。
7. **應用程式版本**：輸入要發行之解決方案的版本。 (例如 1.0.0.0)
8. **應用程式發行日期**：輸入解決方案的發行日期 (mm/dd/yyyy)。
9. **與您的應用程式一起運作的產品**：列出可搭配您應用程式運作的特定產品。 您最多可以列出三個產品。 若要列出產品，請按一下 (位於 [新增] 旁邊的) 加號，系統將會建立新的開放文字欄位，以供您輸入可與您的應用程式一起運作的產品名稱。
10. **搜尋關鍵字**：輸入使用者可以用來在搜尋時找到您應用程式的常用詞彙。 \*\*這些關鍵字將不會顯示在 Marketplace 中。
11. **隱藏金鑰**：這是會與供應項目預覽 URL 結合，以從公用檢視中隱藏的金鑰。 它不是密碼。 您可以在此輸入任何字串。

### <a name="marketing-artifacts"></a>行銷成品

1. 下一步是上傳您的 [標誌] 與 [螢幕擷取畫面]。 \*\*請留意上傳的檔案大小，且所有影像都應該為 PNG 格式。
2. **示範影片**：按一下 [+新增]\"\"。 上傳解決方案的示範影片 (僅限 YouTube 或 Vimeo 連結)。\*\* 請注意，您必須上傳指定大小的縮圖，您的影片才會出現在預備環境中。
3. **文件**：上傳任何與解決方案相關的文件，並記得輸入文件名稱。

### <a name="legal"></a>法律

此資訊將會連結至您的隱私權原則和使用條款。 請輸入解決方案的隱私權原則及使用條款 URL。 \*\*客戶將能在入口網站上看見這些原則。

### <a name="customer-support"></a>客戶支援

只有您的使用者才能在入口網站中看見 [支援 URL]。

### <a name="leads-management"></a>潛在客戶管理

選取將儲存您潛在客戶的 CRM 系統。 如果您使用下列其中一個 CRM 系統，請在此選取 [Azure 資料表]\"\"： Salesforce、Marketo、Microsoft Dynamics CRM。 針對在 AppSource 上嘗試您應用程式的使用者 (潛在客戶)，我們會將其詳細資料寫入至您在此所選取的 CRM 系統。 請根據您所選取的 CRM 系統，按一下下面相對應的 URL 來取得完成下一組欄位的方式。

![[潛在客戶管理] 詳細資料](./media/publish_d365_new_offer/leads.png)

1. 針對 Azure 資料表，請參考[這裡](https://aka.ms/leadsettingforazuretable)
2. 針對 Dynamics CRM Online，請參考[這裡](https://aka.ms/leadsettingfordynamicscrm)
3. 針對 Marketo，請參考[這裡](https://aka.ms/leadsettingformarketo)
4. 針對 Salesforce，請參考[這裡](https://aka.ms/leadsettingforsalesforce)

## <a name="how-to-fill-out-the-contacts-form"></a>如何填寫 [連絡人] 表單

此資訊將會用於 Microsoft 和客戶支援。 輸入您公司的 [工程連絡人] 和 [客戶支援]，以及解決方案的 [支援 URL]。 如果 Microsoft 對於您的解決方案有疑問，將會使用此資訊作為連絡的單一窗口。此資訊同時也會用於客戶支援。

![供應項目 [連絡人] 畫面](./media/publish_d365_new_offer/Contacts.png)
