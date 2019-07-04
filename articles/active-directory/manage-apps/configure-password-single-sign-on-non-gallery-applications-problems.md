---
title: 設定密碼 SSO 為非資源庫應用程式時發生問題 |Microsoft Docs
description: 發生不存在於 Azure AD 應用程式資源庫中的自訂應用程式設定密碼單一登入 (SSO) 時的常見問題。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24330dc874173ba1c6f15abb7b4caf9f23e2e00c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440358"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>設定密碼單一登入非資源庫應用程式時發生問題

這篇文章描述當您設定時，可能會發生的常見問題*密碼單一登入*」 (SSO) 之非資源庫應用程式。

## <a name="capture-sign-in-fields-for-an-app"></a>擷取應用程式的登入的欄位

只有具備 HTML 功能登入頁面支援登入欄位擷取。 它具有不支援非標準登入頁面，例如使用 Adobe Flash 或其他非 HTML 功能技術。

有兩種方式可擷取自訂應用程式的登入欄位：

- **自動登入欄位擷取**適用於大部分具備 HTML 功能登入頁面，*如果他們使用已知的 DIV 識別碼*使用者名稱和密碼欄位。 在頁面的 HTML 會擷取來尋找符合特定準則的 DIV 識別碼。 該中繼資料會儲存，讓它可以重新執行應用程式更新版本。

- **手動登入欄位擷取**如果使用應用程式廠商*不加上標籤的登入輸入的欄位*。 如果手動擷取也會使用廠商*呈現多個無法自動偵測的欄位*。 Azure Active Directory (Azure AD) 可以儲存最多的欄位，因為有在登入頁面上，如果您告訴它這些欄位會位於頁面的資料。

一般情況下，如果自動登入欄位擷取無法運作，請嘗試手動選項。

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>自動擷取應用程式的登入欄位

若要使用自動登入欄位擷取設定密碼型 SSO，請遵循下列步驟：

1. 開啟 [Azure 入口網站](https://portal.azure.com/)。 登入的全域管理員或共同管理員。

2. 在左側導覽窗格中選取**所有服務**若要開啟 Azure AD 延伸模組。

3. 型別**Azure Active Directory**篩選搜尋方塊中，然後選取**Azure Active Directory**。

4. 選取 **企業應用程式**Azure AD 瀏覽窗格中。

5. 選取 **所有應用程式**若要檢視您的應用程式清單。

   > [!NOTE]
   > 如果您沒有看到您想要的應用程式，使用**篩選條件**控制項頂端**所有應用程式**清單。 設定**顯示**選項，所有應用程式。 」

6. 選取您想要為 SSO 設定的應用程式。

7. 應用程式載入之後，請選取**單一登入**在左側導覽窗格中。

8. 選取 **密碼型登入**模式。

9. 請輸入**登入 URL**，這是使用者輸入其使用者名稱和密碼來登入頁面的 URL。 *請確定登入欄位會顯示您所提供的 URL 的頁面上*。

10. 選取 [ **儲存**]。

    頁面會自動擷取使用者名稱和密碼輸入方塊。 您現在可以使用 Azure AD 密碼安全地傳輸到該應用程式使用存取面板瀏覽器延伸模組。

### <a name="manually-capture-sign-in-fields-for-an-app"></a>手動擷取應用程式的登入欄位

若要手動擷取登入欄位，您必須安裝存取面板瀏覽器延伸模組。 此外，無法在中執行您的瀏覽器*inPrivate*， *incognito*，或*私人*模式。

若要安裝擴充功能，請參閱[安裝存取面板的瀏覽器延伸模組](#install-the-access-panel-browser-extension)一節。

若要使用手動登入欄位擷取設定密碼型 SSO 應用程式，請遵循下列步驟：

1. 開啟 [Azure 入口網站](https://portal.azure.com/)。 登入的全域管理員或共同管理員。

2. 在左側導覽窗格中選取**所有服務**若要開啟 Azure AD 延伸模組。

3. 型別**Azure Active Directory**篩選搜尋方塊中，然後選取**Azure Active Directory**。

4. 選取 **企業應用程式**Azure AD 瀏覽窗格中。

5. 選取 **所有應用程式**若要檢視您的應用程式清單。

   > [!NOTE] 
   > 如果您沒有看到您想要的應用程式，使用**篩選條件**控制項頂端**所有應用程式**清單。 設定**顯示**選項，所有應用程式。 」

6. 選取您想要為 SSO 設定的應用程式。

7. 應用程式載入之後，請選取**單一登入**在左側導覽窗格中。

8. 選取 **密碼型登入**模式。

9. 請輸入**登入 URL**，這是使用者輸入其使用者名稱和密碼來登入頁面。 *請確定登入欄位會顯示您所提供的 URL 的頁面上*。

10. 選取 **設定 *&lt;appname&gt;* 密碼單一登入設定**。

11. 選取 **手動偵測登入欄位**。

14. 選取 [確定]  。

15. 選取 [ **儲存**]。

16. 請依照下列指示來使用存取面板。

## <a name="troubleshoot-problems"></a>針對問題進行疑難排解

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>我收到 「 找不到任何登入欄位，在該 URL 」 的錯誤

自動偵測登入欄位失敗時，您就會收到此錯誤訊息。 若要解決此問題，請嘗試手動登入欄位偵測。 請參閱[手動擷取應用程式的登入欄位](#manually-capture-sign-in-fields-for-an-app)一節。

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>我收到 「 無法儲存單一登入設定 」 錯誤

很少更新的 SSO 組態將會失敗。 若要解決此問題，請嘗試儲存組態。

如果您一直收到錯誤，請開啟支援案例。 包含資訊中所述[檢視入口網站通知詳細資料](#view-portal-notification-details)並[將通知詳細資料傳送給支援工程師以取得協助](#send-notification-details-to-a-support-engineer-to-get-help)本文的各節。

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>我無法手動偵測登入欄位的 我的應用程式

當手動偵測無法運作時，可能會看到下列行為：

- 手動擷取程序看似正常運作，但擷取的欄位不正確。

- 當擷取處理序執行時，並未反白顯示正確的欄位。

- 擷取程序會帶您前往應用程式的登入頁面如預期般運作，但會有任何作用。

- 手動擷取看似正常運作，但是當使用者瀏覽至應用程式從存取面板，SSO 不會發生。

如果您遇到這些問題，請執行下列動作：

- 請確定您有最新版的存取面板瀏覽器延伸模組*安裝並啟用*。 請參閱[安裝存取面板瀏覽器延伸模組](#install-the-access-panel-browser-extension)一節。

- 請確定您的瀏覽器不在*incognito*， *inPrivate*，或*私人*擷取程序的模式。 存取面板延伸模組不支援這些模式。

- 請確定您的使用者不嘗試從存取面板中登入應用程式*incognito*， *inPrivate*，或*私用模式*。

- 再試一次手動擷取程序。 請確定，正確的欄位上都有紅色標記。

- 如果手動擷取程序似乎停止回應或沒有回應的登入頁面，手動擷取程序再試一次。 但這次，完成此程序之後, 按下 F12 鍵以開啟您的瀏覽器開發人員主控台。 選取 [**主控台**] 索引標籤。型別**window.location =" *&lt;設定應用程式時，您所指定的登入 URL&gt;* "** ，然後按 Enter 鍵。 這會強制頁面重新導向，結束擷取程序，並將所擷取的欄位。

### <a name="contact-support"></a>請連絡支援人員

如果您仍有問題，請向 Microsoft 支援服務開啟案例。 描述您所嘗試動作。 包含詳細資料中所述[檢視入口網站通知詳細資料](#view-portal-notification-details)並[將通知詳細資料傳送給支援工程師以取得協助](#send-notification-details-to-a-support-engineer-to-get-help)本文 （如果適用） 的各節。

## <a name="install-the-access-panel-browser-extension"></a>安裝存取面板瀏覽器延伸模組

請遵循下列步驟：

1. 開啟[存取面板](https://myapps.microsoft.com)中支援的瀏覽器。 登入 Azure AD*使用者*。

2. 選取 **密碼-SSO 應用程式**存取面板 」 中。

3. 當系統提示您安裝軟體時，選取**立即安裝**。

4. 您會為您的瀏覽器導向至下載頁面。 若要選擇**新增**延伸模組。

5. 如果系統提示您，請選取**啟用**或是**允許**。

6. 安裝之後，重新啟動您的瀏覽器。

7. 登入存取面板。 如果您可以開啟您的密碼-已啟用 SSO 的應用程式，請參閱。

您也可以直接可以下載適用於 Chrome 和 Firefox 的瀏覽器延伸模組，透過這些連結：

-   [Chrome 存取面板延伸模組](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 存取面板延伸模組](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>檢視入口網站通知詳細資料

若要查看任何入口網站通知的詳細資訊，請遵循下列步驟：

1. 選取 **通知**Azure 入口網站右上角的圖示 （鐘）。

2. 選取顯示任何通知*錯誤*狀態。 (它們有一個紅色的"！"。)

   > [!NOTE]
   > 您無法選取中的通知*成功*或是*In Progress*狀態。

3. [通知詳細資料]  窗格隨即開啟。 閱讀以了解問題的資訊。

5. 如果您仍然需要協助時，這些資訊分享給支援工程師或產品群組。 選取 **複製**右邊的圖示**複製錯誤**方塊，以複製共用通知詳細資料。

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>將通知詳細資料傳送給支援工程師以取得協助

很重要，您會共用*所有*，讓他們可以快速協助您使用支援這一節所列的詳細資料。 若要錄製它，您可以擷取螢幕擷取畫面，或選取**複製錯誤**。

下列資訊說明什麼每個通知項目表示，並提供範例。

### <a name="essential-notification-items"></a>必要通知項目

- **標題**： 通知的描述性標題。

   範例：*應用程式 proxy 設定*

- **描述**: 作業所產生結果。

   範例：*輸入的內部 URL 已正由另一個應用程式。*

- **通知識別碼**： 通知的唯一識別碼。

    範例： *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **用戶端要求識別碼**： 您的瀏覽器所做的特定要求識別碼。

    範例：*302fd775-3329-4670-a9f3-bea37004f0bc*

- **時間戳記 UTC**： 當發生通知，以 utc 表示的時間戳記。

    範例：*2017-03-23T19:50:43.7583681Z*

- **內部交易識別碼**： 用來在系統中查閱錯誤的內部識別碼。

    範例：**71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**:執行此作業的使用者。

    範例： *tperkins\@f128.info*

- **租用戶識別碼**： 屬於執行作業之使用者的租用戶的唯一識別碼。

    範例：*7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **使用者物件識別碼**:執行作業之使用者的唯一識別碼。

    範例：*17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>詳細的通知項目

- **顯示名稱**: （可以是空的） 錯誤的更詳細的顯示名稱。

    範例：*應用程式 proxy 設定*

- **狀態**： 特定通知的狀態。

    範例：*已失敗*

- **物件識別碼**: （可以是空的） 執行作業的物件識別碼。

   範例：*8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **詳細資料**: 作業所產生結果的詳細的描述。

    範例：*內部 url '<https://bing.com/>' 無效，因為它已在使用。*

- **複製錯誤**:可讓您選取**複製圖示**右邊**複製錯誤**文字方塊將通知詳細資料，以協助支援複製。

    範例：   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>後續步驟
[使用應用程式 Proxy 提供單一登入應用程式](application-proxy-configure-single-sign-on-with-kcd.md)
