---
title: 關於 Azure Active Directory B2C 中的使用者介面自訂 | Microsoft Docs
description: 了解如何為使用 Azure Active Directory B2C 的應用程式自訂使用者介面。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6c9109cf4d6d67d3d8001a9de1d54e24622a9286
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511183"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>關於 Azure Active Directory B2C 中的使用者介面自訂

有能力自訂 Azure Active Directory (Azure AD) B2C 為您的應用程式提供服務的使用者介面 (UI)，並設定其品牌，深切關係到您能否為客戶提供順暢的體驗。 這些體驗包括註冊、登入、設定檔編輯和密碼重設。 本文將提供有助於您自訂應用程式 UI 的資訊。

根據您在這些體驗方面的需求，您可以透過不同的方式自訂應用程式的 UI。 例如:

- 如果您要使用[使用者流程](active-directory-b2c-reference-policies.md)來提供應用程式中的註冊或登入、密碼重設或設定檔編輯的體驗，您可以使用 [Azure 入口網站來自訂 UI](tutorial-customize-ui.md)。
- 如果您使用的是 v2 使用者流程，則可以使用[頁面配置範本](#page-layout-templates)變更使用者流程頁面的外觀，而不需要進一步的自訂。 例如，您可以將「海藍」或「岩灰」佈景主題套用至使用者流程中的所有頁面。
- 如果您只要提供登入、及其伴隨的密碼重設頁面和驗證電子郵件方面的體驗，您可以使用在 [Azure AD 登入頁面](../active-directory/fundamentals/customize-branding.md)中使用的相同自訂步驟。
- 如果客戶嘗試在登入前編輯其設定檔，他們將會重新導向至您使用在自訂 Azure AD 登入頁面時所使用的相同步驟自訂的頁面。
- 如果您要使用[自訂原則](active-directory-b2c-overview-custom.md)來提供應用程式中的註冊或登入、密碼重設或設定檔編輯的體驗，您可以使用[原則檔案來自訂 UI](active-directory-b2c-ui-customization-custom.md)。
- 如果您需要根據客戶的決策提供動態內容，您可以根據在查詢字串中傳送的參數使用[可變更頁面內容的自訂原則](active-directory-b2c-ui-customization-custom-dynamic.md)。 例如，Azure AD B2C 註冊或登入頁面的背景影像可根據您從 Web 或行動裝置應用程式傳遞的參數而變更。
- 您可以在 Azure AD B2C [使用者流程](user-flow-javascript-overview.md)或[自訂原則](page-contract.md)中啟用 JavaScript 用戶端程式碼。

Azure AD B2C 會在客戶的瀏覽器中執行程式碼，並使用名為[跨原始資源共用 (CORS)](https://www.w3.org/TR/cors/) 的新式方法。 在執行階段中，會從您在使用者流程中指定的 URL 載入內容。 您可以對不同的頁面指定不同的 URL。 從您的 URL 載入的內容後，該內容就會與從 Azure AD B2C 插入的 HTML 片段合併，然後向客戶顯示。

當使用您自己的 HTML 和 CSS 檔案自訂 UI 時，請在開始之前檢閱下列指導方針：

- Azure AD B2C 會將 HTML 內容合併到您的頁面中。 請勿複製及嘗試變更 Azure AD B2C 所提供的預設內容。 最好是從頭建置您的 HTML 內容，將預設範本當作參考即可。
- JavaScript 現在可以包含在您的自訂內容中。
- 支援的瀏覽器版本包括︰ 
    - Internet Explorer 11、10 和 Microsoft Edge
    - 對 Internet Explorer 9 和 8 僅提供有限支援
    - Google Chrome 42.0 和更新版本
    - Mozilla Firefox 38.0 和更新版本
- 確定您的 HTML 中未包含表單標記，因為這會干擾從 Azure AD B2C 插入的 HTML 所產生的 POST 作業。

## <a name="page-layout-templates"></a>頁面配置範本

針對 v2 使用者流程，您可以選擇預先設計的範本，，可讓您的預設頁面外觀更好，並為您的自訂提供良好的基礎。

在左側功能表的 [自訂]  下方，選取 [頁面配置]  。 然後選取 [範本 (預覽)]  。

![選擇頁面配置範本](media/customize-ui-overview/template.png)

從清單中選取範本。 例如，**海藍**範本將下列配置套用至您的使用者流程頁面：

![海藍範本](media/customize-ui-overview/ocean-blue.png)

選擇範本時，選取的配置將套用到使用者流程中的所有頁面，並且每一頁的 URI 會顯示在 [自訂頁面 URI]  欄位中。

## <a name="where-do-i-store-ui-content"></a>UI 內容應儲存於何處？

當使用您自己的 HTML 和 CSS 檔案自訂 UI 時，您可以將 UI 內容裝載於任何地方，例如 [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)、Web 伺服器、CDN、AWS S3 或檔案共用系統。 重點是內容應裝載在已啟用 CORS 的公用 HTTPS 端點上。 在您的內容中指定 URL 時，必須使用絕對 URL。

## <a name="how-do-i-get-started"></a>如何開始使用？

自訂 UI 時須執行下列步驟：

- 建立格式正確的 HTML 內容，其中的空白 `<div id="api"></div>` 元素位於 `<body>` 中的某處。 這個元素會標記插入 Azure AD B2C 內容的地方。 下列範例顯示最小頁面：

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- 將您的內容裝載於 HTTPS 端點 (允許 CORS)。 設定 CORS 時，必須同時啟用 GET 和 OPTIONS 要求方法。
- 使用 CSS 為 Azure AD B2C 在您的頁面中插入的 UI 元素設定樣式。 下列範例顯示簡單的 CSS 檔案，其中也包含註冊插入 HTML 元素的設定：

    ```css 
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center; 
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center; 
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

- 建立或編輯原則以使用您所建立的內容。

下表列出 Azure AD B2C 合併至 `<div id="api"></div>` 元素 (位於您的內容中) 的 HTML 片段範例。

| 插入的頁面 | HTML 的描述 |
| ------------- | ------------------- |
| 識別提供者選取項目 | 包含客戶在註冊或登入期間可選擇的識別提供者按鈕清單。 這些按鈕包括社交識別提供者 (例如 Facebook、Google) 或本機帳戶 (以電子郵件地址或使用者名稱作為基礎)。 |
| 本機帳戶註冊 | 包含可供使用者根據電子郵件地址或使用者名稱進行本機帳戶註冊的表單。 此表單可以包含不同的輸入控制項，例如文字輸入方塊、密碼輸入方塊、選項按鈕、單選下拉式清單方塊和多選核取方塊。 |
| 社交帳戶註冊 | 可在使用社交識別提供者 (例如 Facebook 或 Google) 的現有帳戶註冊時顯示。 必須向使用註冊表單的客戶收集其他資訊時，則需要使用。 |
| 統一的註冊或登入 | 可處理客戶的註冊和登入，這些客戶可使用社交識別提供者 (例如 Facebook、Google) 或本機帳戶。 |
| Multi-Factor Authentication | 客戶可以在註冊或登入期間驗證其電話號碼 (使用文字或語音)。 |
| Error | 提供錯誤資訊給客戶。 |


## <a name="how-do-i-localize-content"></a>如何將內容當地語系化？

您可以在 Azure AD B2C 租用戶上啟用[語言自訂](active-directory-b2c-reference-language-customization.md)，以將 HTML 內容當地語系化。 啟用此功能會讓 Azure AD B2C 將 Open ID Connect 參數 `ui-locales` 轉送給端點。 內容伺服器可使用此參數來提供語言特定的 HTML 頁面。

您可以根據所使用的地區設定，從不同的地方提取內容。 在已啟用 CORS 的端點中，您可以針對特定語言設定主機內容的資料夾結構。 如果您使用萬用字元值 {Culture:RFC5646}，則會呼叫正確的語言。 例如，您的自訂頁面可能會顯示為 `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html`。 您可以從 `https://contoso.blob.core.windows.net/fr/myHTML/unified.html` 提取內容，以載入以法文顯示的頁面

## <a name="examples"></a>範例

如需自訂範例，請下載並檢閱這些[範例範本檔案](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip)。

## <a name="next-steps"></a>後續步驟

- 如果您使用使用者流程，您可以透過以下教學課程開始自訂您的 UI：[在 Azure Active Directory B2C 中自訂應用程式的使用者介面](tutorial-customize-ui.md)。
- 如果您使用自訂原則，您可以透過以下文章開始自訂 UI：[在 Azure Active Directory B2C 中使用自訂原則來自訂應用程式的使用者介面](active-directory-b2c-ui-customization-custom.md)。

