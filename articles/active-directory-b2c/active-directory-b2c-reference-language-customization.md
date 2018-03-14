---
title: "使用語言自訂 - Azure AD B2C | Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 02/26/2018
ms.author: sama
ms.openlocfilehash: 332c6b4ffd841a2c7aef9db5c8ba9e843790f4d6
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2018
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C︰使用語言自訂

>[!NOTE]
>這項功能處於公開預覽狀態。
>

語言自訂可讓您的原則適應不同的語言，以符合您客戶的需求。  Microsoft 提供 36 種語言的翻譯 (請參閱[其他資訊](#additional-information))，但您也可以針對任何語言提供自己的的翻譯。  即使您的體驗僅提供單一語言，您也可以自訂頁面上的任何文字。  

## <a name="how-does-language-customization-work"></a>語言自訂的運作方式為何？
語言自訂可讓您選取使用者旅程所適用的語言。  此功能啟用後，您就可以從應用程式提供查詢字串參數 ui_locales。  當您呼叫 Azure AD B2C 時，我們會將您的頁面轉譯為您指定的地區設定。  此類型的組態可讓您完整控制使用者旅程的語言，並忽略客戶瀏覽器的語言設定。 或者，您也可能不需要對客戶會看見哪種語言擁有這種程度的控制力。  如果您沒有提供 ui_locales 參數，則客戶的使用體驗會由其瀏覽器的設定來決定。  您仍可將各種語言新增為支援的語言，以控制要將使用者旅程轉譯為何種語言。  如果客戶瀏覽器所設定要顯示的語言不是您想要支援的語言，則系統會改為顯示您選取作為支援之文化特性預設值的語言。

1. **ui_locales 所指定的語言** - 在啟用語言自訂後，您的使用者旅程會轉譯為此處指定的語言
2. **瀏覽器所要求的語言** - 如果未指定 ui_locales，則語言會轉譯為瀏覽器所要求的語言 (**如果支援的語言中有包括此語言**)
3. **原則的預設語言** - 如果瀏覽器未指定語言，或指定不支援的語言，語言就會轉譯為原則的預設語言

>[!NOTE]
>如果您使用自訂使用者屬性，則必須提供自己的翻譯。 如需詳細資訊，請參閱[自訂您的字串](#customize-your-strings)。
>

## <a name="support-uilocales-requested-languages"></a>支援 ui_locales 所要求的語言 
在語言自訂的公開發行之前建立的原則必須先啟用此功能。  之後建立的原則預設會啟用語言自訂。  藉由對原則啟用「語言自訂」，您現在可以藉由新增 ui_locales 參數來控制使用者旅程的語言。
1. [遵循下列步驟以瀏覽至 Azure 入口網站上的 B2C 功能頁面。](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. 瀏覽至您想要啟用轉譯的原則。
3. 按一下 [語言自訂]。  
4. 按一下頂端的 [啟用語言自訂]。
5. 閱讀對話方塊，然後按一下 [是]。

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>選取您的使用者旅程中啟用的語言 
針對您的使用者旅程啟用一組語言，以便在未提供 ui_locales 參數時進行翻譯。
1. 透過前面的指示來確定您的原則已啟用「語言自訂」。
2. 從 [編輯原則] 頁面，選取 [語言自訂]。
3. 選取您想要支援的語言。
4. 在 [屬性] 窗格中，將 [已啟用] 切換為 [是]。  
5. 按一下 [屬性] 窗格頂端的 [儲存]。

>[!NOTE]
>如果您未提供 ui_locales 參數，則頁面會翻譯為客戶的瀏覽器語言 (但前提是已啟用該語言)
>

## <a name="customize-your-strings"></a>自訂您的字串
「語言自訂」可讓您自訂使用者旅程中的任何字串。
1. 透過前面的指示來確定您的原則已啟用「語言自訂」。
2. 從 [編輯原則] 頁面，選取 [語言自訂]。
3. 選取您想要自訂的語言。
4. 選取您想要編輯的頁面。
5. 按一下 [下載預設值] (如果您先前已編輯這個語言，請按一下 [下載覆寫])。 

上述步驟會向您提供 JSON 檔案，以供您用來開始編輯字串。

### <a name="changing-any-string-on-the-page"></a>變更頁面上的任何字串
1. 在 JSON 編輯器中開啟透過前面的指示所下載的 JSON 檔案。
2. 尋找您想要變更的元素。  您可以尋找您要尋找之字串的 `StringId`，或尋找您要變更的 `Value`。
3. 以您想要顯示的內容來更新 `Value` 屬性。
4. 對於您想要變更每個字串，請記得將 `Override` 切換為 [True]。
5. 儲存檔案並上傳您所做的變更 (您可以在您下載 JSON 檔案的相同位置找到上傳控制項)。 

>[!IMPORTANT]
>如果您需要覆寫字串，請務必將 `Override` 值設為 `true`。  如果該值未變更，則系統會忽略您的輸入。 
>

### <a name="changing-extension-attributes"></a>變更擴充屬性
如果您想要變更自訂使用者屬性的字串，或想要將字串新增到 JSON 中，它的格式如下︰
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

以自訂使用者屬性的名稱來取代 `<ExtensionAttribute>`。  

以要顯示的新字串來取代 `<ExtensionAttributeValue>`。

### <a name="using-localizedcollections"></a>使用 LocalizedCollections
如果您想要提供設定好的回應值清單，您需要建立 `LocalizedCollections`。  `LocalizedCollections` 是 `Name` 和 `Value` 的配對陣列。  `Name` 是所顯示的內容，`Value` 則是宣告中所傳回的內容。  若要新增 `LocalizedCollections`，其格式如下︰

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` 是使用者屬性，而其回應則是這個 `LocalizedCollections`
* `Name` 是向使用者顯示的值
* `Value` 是選取此選項時在宣告中所傳回的內容

### <a name="upload-your-changes"></a>上傳您的變更
1. 對 JSON 檔案變更完成後，請瀏覽回到 B2C 租用戶。
2. 從 [編輯原則] 頁面，選取 [語言自訂]。
3. 選取您想要提供翻譯的語言。
4. 選取您想要提供翻譯的頁面。
5. 按一下資料夾圖示，然後選取要上傳的 JSON 檔案。
6. 此變更會自動儲存到您的原則。

## <a name="using-page-ui-customization-with-language-customization"></a>搭配使用頁面 UI 自訂與語言自訂

有兩種方法可將您的 HTML 內容當地語系化。  開啟 [[語言自訂]](active-directory-b2c-reference-language-customization.md)。  啟用此功能會讓 Azure AD B2C 將 Open ID Connect 參數 `ui-locales` 轉送給端點。  內容伺服器可以使用這個參數來提供語言特定的自訂 HTML 頁面。

或者，我們可以根據所使用的地區設定，從不同的地方提取內容。  在已啟用 CORS 的端點中，您可以針對特定語言設定主機內容的資料夾結構，而如果您放置萬用字元值 `{Culture:RFC5646}`，我們將會呼叫正確的語言。  例如，如果我的自訂頁面 URI 如下：

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
我可以用 `fr` 載入我的網頁，而當它提取 html 和 css 內容時，它會從以下 URI 提取：
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="custom-locales"></a>自訂地區設定

您也可以新增 Microsoft 目前不提供翻譯的語言。  您必須提供原則中所有字串的翻譯。

1. 從 [編輯原則] 頁面，選取 [語言自訂]。
2. 從頁面頂端選取 [新增自訂語言]。
3. 在開啟的內容窗格中，輸入有效的地區設定代碼，以識別您提供哪種語言的翻譯。
4. 針對每一頁，您可以下載適用於英文的一組覆寫並處理翻譯。
5. 處理好 JSON 檔案後，您可以為每一頁上傳它們。
6. 選取 [啟用]，而您的原則現在可以為您的使用者顯示此語言。
7. 啟用您的語言後，請記得加以儲存。

## <a name="additional-information"></a>其他資訊

### <a name="page-ui-customization-labels-are-persisted-as-your-first-set-of-overrides-once-language-customization-is-enabled"></a>啟用 [語言自訂] 後，系統會保存頁面 UI 自訂標籤作為您的第一組覆寫。
當您啟用 [語言自訂] 時，您先前使用頁面 UI 自訂對標籤所進行的編輯會保存在英文 (en) 的 JSON 檔案中。  您可以上傳「語言自訂」中的語言資源，來繼續變更您的標籤和其他字串。
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Microsoft 致力於提供最新的翻譯來供您使用
我們會持續改善翻譯，並讓它們符合您的需求。  我們會找出全域術語中的錯誤和變更，並製作可在使用者旅程中順暢運作的更新。
### <a name="support-for-right-to-left-languages"></a>支援從右至左的語言
我們目前不支援由右至左的語言，如果您需要此功能，請在 [Azure 意見反應](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)投票給這項功能。
### <a name="social-identity-provider-translations"></a>社交識別提供者的翻譯
我們為社交登入提供了 ui_locales OIDC 參數，但某些社交識別提供者 (包括 Facebook 和 Google) 並未加以採用。 
### <a name="browser-behavior"></a>瀏覽器行為
Chrome 和 Firefox 皆會要求自設的語言，如果該語言受支援，則會顯示該語言而不會顯示預設語言。  Edge 目前未要求語言，而是會直接使用預設語言。

### <a name="what-languages-are-supported"></a>支援哪些語言？

| 語言              | 語言代碼 |
|-----------------------|---------------|
| 孟加拉文                | bn            |
| 捷克文                 | cs            |
| 丹麥文                | da            |
| 德文                | de            |
| 希臘文                 | el            |
| English               | en            |
| 西班牙文               | es            |
| 芬蘭文               | fi            |
| 法文                | fr            |
| 古吉拉特文              | gu            |
| 北印度文                 | hi            |
| 克羅埃西亞文              | hr            |
| 匈牙利文             | hu            |
| 義大利文               | it            |
| 日文              | ja            |
| 坎那達文               | kn            |
| 韓文                | ko            |
| 馬來亞拉姆文             | ml            |
| 馬拉提文               | mr            |
| 馬來文                 | ms            |
| 挪威文 (巴克摩)      | nb            |
| 荷蘭文                 | nl            |
| 旁遮普文               | pa            |
| 波蘭文                | pl            |
| 葡萄牙文 - 巴西   | pt-br         |
| 葡萄牙文 - 葡萄牙 | pt-pt         |
| 羅馬尼亞文              | ro            |
| 俄文               | ru            |
| 斯洛伐克文                | sk            |
| 瑞典文               | sv            |
| 坦米爾文                 | ta            |
| 特拉古文                | te            |
| 泰文                  | th            |
| 土耳其文               | tr            |
| 中文 - 簡體  | zh-hans       |
| 中文 - 繁體 | zh-hant       |
