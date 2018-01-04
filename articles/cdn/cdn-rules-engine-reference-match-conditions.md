---
title: "符合條件的內容傳遞網路規則引擎 |Microsoft 文件"
description: "Azure 內容傳遞網路的參考文件規則引擎比對條件和功能。"
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: 944675f4b055c3d0c2559db53e4807a5c00bda64
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="match-conditions-for-the-content-delivery-network-rules-engine"></a>內容傳遞網路規則引擎的比對條件
本文列出 Azure 內容傳遞網路的可用符合條件的詳細的描述[規則引擎](cdn-rules-engine.md)。

規則的第二個部分是比對條件。 比對條件會識別特定類型的要求，系統將針對這類要求執行一組功能。

例如，您可以使用的比對條件：
- 篩選要求的特定位置的內容。
- 從特定的 IP 位址或國家/地區產生篩選要求。
- 篩選要求的標頭資訊。

## <a name="always-match-condition"></a>一律符合條件

永遠比對條件適用於所有的要求一組預設的功能。

名稱 | 目的
-----|--------
[一律](#always) | 適用於所有的要求一組預設的功能。

## <a name="device-match-condition"></a>裝置符合條件

「裝置」比對條件可識別從行動裝置根據其屬性所提出的要求。  

名稱 | 目的
-----|--------
[裝置](#device) | 識別從行動裝置根據其內容所提出的要求。

## <a name="location-match-conditions"></a>位置比對條件

位置比對條件識別要求的要求者的位置。

名稱 | 目的
-----|--------
[數字](#as-number) | 識別源自特定網路的要求。
[國家/地區](#country) | 識別源自特定國家/地區的要求。

## <a name="origin-match-conditions"></a>原始比對條件

原始比對條件識別指向內容傳遞網路儲存裝置或客戶原始伺服器的要求。

名稱 | 目的
-----|--------
[CDN 來源](#cdn-origin) | 識別要求內容傳遞網路儲存體中儲存的內容。
[客戶來源](#customer-origin) | 識別對儲存於特定客戶原始伺服器上之內容的要求。

## <a name="request-match-conditions"></a>要求比對條件

要求比對條件會識別根據屬性的要求。

名稱 | 目的
-----|--------
[用戶端 IP 位址](#client-ip-address) | 識別源自特定 IP 位址的要求。
[Cookie 參數](#cookie-parameter) | 檢查與每個適用於指定值之要求相關聯的 Cookie。
[Cookie 參數 Regex](#cookie-parameter-regex) | 檢查與每個適用於規則運算式之要求相關聯的 Cookie。
[邊緣 Cname](#edge-cname) | 識別指向特定邊緣 CNAME 的要求。
[轉介網域](#referring-domain) | 識別所參考從指定的主機名稱的要求。
[要求標頭的常值](#request-header-literal) | 識別包含指定的標頭設定為指定值的要求。
[要求標頭 Regex](#request-header-regex) | 識別包含指定標頭的要求，該標頭已設定為符合特定規則運算式的值。
[要求標頭萬用字元](#request-header-wildcard) | 識別包含指定標頭的要求，該標頭已設定為符合特定模式的值。
[要求方法](#request-method) | 依其 HTTP 方法來識別要求。
[要求配置](#request-scheme) | 依其 HTTP 通訊協定來識別要求。

## <a name="url-match-conditions"></a>URL 比對條件

URL 比對條件，找出其 Url 為基礎的要求。

名稱 | 目的
-----|--------
URL 路徑目錄 | 依其相對路徑來識別要求。
URL 路徑的副檔名 | 依副檔名來識別要求。
URL 路徑的檔案名稱 | 依其檔案名稱來識別要求。
URL 路徑常值 | 比較要求的相對路徑與指定的值。
URL 路徑 Regex | 比較要求的相對路徑與指定的規則運算式。
URL 路徑萬用字元 | 比較要求的相對路徑與指定的模式。
URL 查詢常值 | 比較要求的查詢字串與指定的值。
URL 查詢參數 | 識別包含指定查詢字串參數的要求，該參數已設定為符合特定模式的值。
URL 查詢 Regex | 識別包含指定查詢字串參數的要求，該參數已設定為符合特定規則運算式的值。
URL 查詢萬用字元 | 比較指定的值，針對要求的查詢字串。


## <a name="reference-for-rules-engine-match-conditions"></a>規則引擎比對條件的參考

---
### <a name="always"></a>一律

永遠比對條件適用於所有的要求一組預設的功能。

[回到頁首](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="as-number"></a>AS 號碼 
網路的 AS 號碼是由其自發系統編號 (ASN) 定義。 表示當用戶端的網路"符合"並不相符項目 」 指定的 ASN 是否將符合此條件被提供的選項。

重要資訊：
- 分隔每個以單一空格，以指定多個的 Asn。 例如，64514 64515 符合要求，從 64514 或 64515 到達。
- 特定的要求可能不會傳回有效的 ASN。 問號 （？） 會比對其有效的 ASN 無法判斷的要求。
- 您必須指定整個 ASN 的所需的網路。 將不會比對部分值。
- 追蹤哪一個快取設定的方式，因為此比對條件與不相容的下列功能：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="cdn-origin"></a>CDN 原點
CDN 原點相符項目符合條件，就這兩個下列條件符合時：
- 從內容傳遞網路存放裝置的內容要求。
- 要求 URI 會使用這個比對條件中所定義的內容的存取點 (例如，/000001)。
  - 內容傳遞網路 URL： 要求 URI 必須包含所選內容的存取點。
  - 邊緣 CNAME 的 URL： 對應的邊緣 CNAME 組態必須指向所選內容的存取點。
  
重要資訊：
 - 內容的存取點會識別應該做的要求的內容的服務。
 - 不使用和 IF 陳述式結合特定比對條件。 例如，結合 CDN 原點比對的條件的客戶原點符合條件會建立可能永遠不會符合比對模式。 基於這個原因，無法透過和 IF 陳述式結合兩個 CDN 原點比對條件。

[回到頁首](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="client-ip-address"></a>用戶端 IP 位址
表示指定的 IP 位址的用戶端的 IP 位址"符合"並不相符項目 」 系統是否符合用戶端 IP 位址條件被提供的選項。

重要資訊：
- 請務必使用 CIDR 表示法。
- 分隔每個以單一空格來指定多個 IP 位址和/或 IP 位址區塊。
  - **IPv4 範例**: 1.2.3.4 10.20.30.40 符合來自 1.2.3.4 或 10.20.30.40 任何要求。
  - **IPv6 範例**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 符合來自 1:2:3:4:5:6:7:8 或 10:20:30:40:50:60:70:80 任何要求。
- IP 位址區塊的語法是基底的 IP 位址，後面接著斜線和首碼大小。
  - **IPv4 範例**: 5.5.5.64/26 符合來自 5.5.5.64 5.5.5.127 透過任何要求。
  - **IPv6 範例**: 1:2:3: / 48 符合來自 1:2:3:0:0:0:0:0 1:2:3:ffff:ffff:ffff:ffff:ffff 透過任何要求。
- 追蹤哪一個快取設定的方式，因為此比對條件與不相容的下列功能：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="cookie-parameter"></a>Cookie 參數
**相符項目**/**不符**選項會決定將符合的 Cookie 參數符合條件的條件。
- **符合**： 需要包含符合至少其中一個的值，這個比對條件中所定義的值來指定的 cookie 的要求。
- **不符**： 需要要求滿足下列條件之一：
  - 它不包含指定的 cookie。
  - 它包含指定的 cookie，但其值不符合此比對條件中所定義的值。
  
重要資訊：
- Cookie 名稱： 
  - 特殊字元，包括星號，不支援時，您會指定 cookie 名稱。 這表示只有精確的 cookie 名稱相符項目有資格進行比較。
  - 只能在單一的 cookie 名稱可以指定每個執行個體，這個比對條件。
  - Cookie 的名稱比較不區分大小寫。
- Cookie 值： 
  - 分隔每個以單一空格，以指定多個 cookie 值。
  - Cookie 值可以利用特殊字元。 
  - 如果未指定萬用字元，只有完全符合將符合此比對條件。 例如，指定 「 值 」 會比對"Value，"但不是"Value1"或"Value2"。
  - **忽略大小寫**選項會決定區分大小寫的比較都會針對要求的 cookie 值。
- 追蹤哪一個快取設定的方式，因為此比對條件與不相容的下列功能：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="cookie-parameter-regex"></a>Cookie 參數 Regex
Cookie 參數 Regex 比對條件定義的 cookie 名稱和值。 您可以使用規則運算式來定義所需的 cookie 值。 

**相符項目**/**不符**選項會決定將符合此符合條件的條件。
- **符合**： 需要包含符合指定的規則運算式的值與指定的 cookie 的要求。
- **不符**： 需要要求滿足下列條件之一：
  - 它不包含指定的 cookie。
  - 它包含指定的 cookie，但其值不符合指定的規則運算式。
  
重要資訊：
- Cookie 名稱： 
  - 規則運算式和特殊字元，包括星號，不支援時，您會指定 cookie 名稱。 這表示只有精確的 cookie 名稱相符項目有資格進行比較。
  - 只能在單一的 cookie 名稱可以指定每個執行個體，這個比對條件。
  - Cookie 的名稱比較不區分大小寫。
- Cookie 值： 
  - Cookie 值可以利用規則運算式。
  - **忽略大小寫**選項會決定區分大小寫的比較都會針對要求的 cookie 值。
- 追蹤哪一個快取設定的方式，因為此比對條件與不相容的下列功能：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#azure-cdn-rules-engine-match-conditions)

</br>

--- 
### <a name="country"></a>國家 (地區)
您可以指定透過其國家/地區碼國家/地區。 提供的選項，表示這個條件將會符合當要求源自"比"並不相符項目 」 的指定的值的國家/地區。

重要資訊：
- 分隔每個以單一空格，以指定多個國家/地區碼。
- 您會指定國家/地區的程式碼時，不支援萬用字元。
- "EU"和"AP 」 的國家/地區代碼不會包含這些區域中的所有 IP 位址。
- 特定的要求可能不會傳回有效的國碼。 問號 （？） 會比對其有效的國家/地區的程式碼無法判斷的要求。
- 國家/地區碼是區分大小寫。
- 追蹤哪一個快取設定的方式，因為此比對條件與不相容的下列功能：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="customer-origin"></a>客戶原點

重要資訊： 
- 將滿足客戶原點符合條件，不論內容透過內容傳遞網路 URL 或邊緣 CNAME 的 URL 指向選取的客戶原始要求。
- 客戶原點組態參考由規則無法刪除從客戶原始頁面。 您嘗試刪除客戶原點組態之前，請確定下列組態請勿參考它：
  - 客戶原點符合條件
  - 邊緣 CNAME 組態
- 不使用和 IF 陳述式結合特定比對條件。 例如，結合客戶原點比對的條件的 CDN 原點符合條件會建立可能永遠不會符合比對模式。 基於這個原因，無法透過和 IF 陳述式結合兩個客戶原點比對條件。

[回到頁首](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="device"></a>裝置

「裝置」比對條件可識別從行動裝置根據其屬性所提出的要求。 行動裝置偵測可以透過 [WURFL](http://wurfl.sourceforge.net/) 達成。 下表列出 WURFL 功能和其內容傳遞網路規則引擎的變數。
<br>
> [!NOTE] 
> 支援下列變數**修改用戶端要求標頭**和**修改用戶端的回應標頭**功能。

功能 | 變數 | 說明 | 範例值
-----------|----------|-------------|----------------
品牌名稱 | %{wurfl_cap_brand_name} | 字串，表示裝置的品牌名稱。 | Samsung
裝置作業系統 | %{wurfl_cap_device_os} | 字串，表示裝置上安裝的作業系統。 | iOS
裝置作業系統版本 | %{wurfl_cap_device_os_version} | 表示在裝置上安裝作業系統的版本號碼的字串。 | 1.0.1
雙向 | %{wurfl_cap_dual_orientation} | 布林值，表示裝置是否支援雙向。 | true
HTML 慣用 DTD | %{wurfl_cap_html_preferred_dtd} | 字串，表示 HTML 內容的行動裝置慣用文件類型定義 (DTD)。 | None<br/>xhtml_basic<br/>html5
內嵌影像 | %{wurfl_cap_image_inlining} | 布林值，表示裝置是否支援 Base64 編碼映像。 | false
是 Android | %{wurfl_vcap_is_android} | 布林值，表示裝置是否使用 Android 作業系統。 | true
是 iOS | %{wurfl_vcap_is_ios} | 布林值，表示裝置是否使用 iOS。 | false
是智慧型電視 | %{wurfl_cap_is_smarttv} | 布林值，表示裝置是否為智慧型電視。 | false
是智慧型手機 | %{wurfl_vcap_is_smartphone} | 布林值，表示裝置是否為智慧型手機。 | true
是平板電腦 | %{wurfl_cap_is_tablet} | 布林值，表示裝置是否為平板電腦。 這是獨立於作業系統的描述。 | true
是無線裝置 | %{wurfl_cap_is_wireless_device} | 布林值，表示裝置是否視為無線裝置。 | true
行銷名稱 | %{wurfl_cap_marketing_name} | 字串，表示裝置的行銷名稱。 | BlackBerry 8100 Pearl
行動瀏覽器 | %{wurfl_cap_mobile_browser} | 字串，指出瀏覽器用來從裝置要求內容。 | Chrome
行動瀏覽器版本 | %{wurfl_cap_mobile_browser_version} | 字串，表示用來從裝置要求內容的瀏覽器版本。 | 31
模型名稱 | %{wurfl_cap_model_name} | 字串，表示裝置的模型名稱。 | s3
漸進式下載 | %{wurfl_cap_progressive_download} | 布林值，指出裝置是否仍在下載時支援播放音訊與視訊。 | true
發行日期 | %{wurfl_cap_release_date} | 字串，表示裝置新增至 WURFL 資料庫的年和月。<br/><br/>格式： `yyyy_mm` | 2013_december
解析高度 | %{wurfl_cap_resolution_height} | 整數，表示裝置的高度，以像素為單位。 | 768
解析寬度 | %{wurfl_cap_resolution_width} | 整數，表示裝置的寬度，以像素為單位。 | 1024

[回到頁首](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="edge-cname"></a>邊緣 Cname
重要資訊： 
- 使用邊緣 Cname 清單僅限於邊緣 Cname 頁面對應至 HTTP 規則引擎正在設定所在的平台上已設定的。
- 您嘗試刪除的邊緣 CNAME 組態之前，請確定邊緣 Cname 相符的狀況不會參考它。 無法從邊緣 Cname 頁面中刪除邊緣 CNAME 組態規則中所定義。 
- 不使用和 IF 陳述式結合特定比對條件。 例如，結合邊緣 Cname 比對的條件的客戶原點符合條件會建立可能永遠不會符合比對模式。 基於這個原因，無法透過和 IF 陳述式結合兩個邊緣 Cname 比對條件。
- 追蹤哪一個快取設定的方式，因為此比對條件與不相容的下列功能：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="referring-domain"></a>轉介網域
查閱者透過其內容要求，判斷是否符合參考網域條件相關聯的主機名稱。 指出參考的主機名稱"符合"並不相符項目 」 指定的值時是否將符合此條件被提供的選項。

重要資訊：
- 分隔每個以單一空格，以指定多個主機名稱。
- 此比對條件支援的特殊字元。
- 如果指定的值不包含星號，必須完全相符的轉介者的主機名稱。 例如，指定 「 mydomain.com"將不會相符"www.mydomain.com。 」
- **忽略大小寫**選項會決定是否執行區分大小寫的比較。
- 追蹤哪一個快取設定的方式，因為此比對條件與不相容的下列功能：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#azure-cdn-rules-engine-match-conditions)

</br>

---  
### <a name="request-header-literal"></a>要求標頭常值
**相符項目**/**不符**選項會決定將符合此符合條件的條件。
- **相符項目**： 需要包含指定的標頭的要求。 其值必須符合此比對條件中所定義。
- **不符**： 需要要求滿足下列條件之一：
  - 它不包含指定的標頭。
  - 它包含指定的標頭，但其值不符合此比對條件中所定義的一個。
  
重要資訊：
- 標頭的名稱比較一定不區分大小寫。 **忽略大小寫**選項會決定的標頭值比較區分大小寫。
- 追蹤哪一個快取設定的方式，因為此比對條件與不相容的下列功能：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#azure-cdn-rules-engine-match-conditions)

</br>

---  
### <a name="request-header-regex"></a>要求標頭 Regex
**相符項目**/**不符**選項會決定將符合的要求標頭 Regex 符合條件的條件。
- **相符項目**： 需要包含指定的標頭的要求。 其值必須符合定義中指定的規則運算式模式。
- **不符**： 需要要求滿足下列條件之一：
  - 它不包含指定的標頭。
  - 它包含指定的標頭，但其值不符合指定的規則運算式。

重要資訊：
- 標頭名稱： 
  - 標頭的名稱比較不區分大小寫。
  - 標頭名稱中的空格應該取代成"%20"。 
- 標頭值： 
  - 標頭值，可以利用規則運算式。
  - **忽略大小寫**選項會決定的標頭值比較區分大小寫。
  - 只有確切的標頭值符合至少其中一個指定的模式將會符合此條件。
- 追蹤哪一個快取設定的方式，因為此比對條件與不相容的下列功能：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時 

[回到頁首](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="request-header-wildcard"></a>要求標頭萬用字元
**相符項目**/**不符**選項會決定將符合的要求標頭萬用字元比對條件的條件。
- **相符項目**： 需要包含指定的標頭的要求。 其值必須符合至少其中一個這種狀況比對中所定義的值。
- **不符**： 需要要求滿足下列條件之一：
  - 它不包含指定的標頭。
  - 它包含指定的標頭，但其值不符合任何一個指定的值。
  
重要資訊：
- 標頭名稱： 
  - 標頭的名稱比較不區分大小寫。
  - 標頭名稱中的空格應該取代成"%20"。 您也可以使用此值來指定空間中的標頭值。
- 標頭值： 
  - 標頭值，可以利用特殊字元。
  - **忽略大小寫**選項會決定的標頭值比較區分大小寫。
  - 只有確切的標頭值符合至少其中一個指定的模式將會符合此條件。
  - 分隔每個以單一空格，以指定多個值。
- 追蹤哪一個快取設定的方式，因為此比對條件與不相容的下列功能：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="request-method"></a>要求方法
透過所選的要求方法要求的資產會滿足要求方法條件。 可用的要求方法為：
- GET
- HEAD 
- POST 
- OPTIONS 
- PUT 
- 刪除 
- 追蹤 
- 連接 

重要資訊：
- 根據預設，只能將 GET 要求方法可以產生網路上的快取的內容。 所有其他要求方法是透過網路 proxy。
- 追蹤哪一個快取設定的方式，因為此比對條件與不相容的下列功能：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="request-scheme"></a>要求配置
透過所選通訊協定會要求的資產會滿足要求配置條件。 可用的通訊協定為 HTTP 及 HTTPS。

重要資訊：
- 追蹤哪一個快取設定的方式，因為此比對條件與不相容的下列功能：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#azure-cdn-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>後續步驟
* [Azure 內容傳遞網路概觀](cdn-overview.md)
* [規則引擎的參考](cdn-rules-engine-reference.md)
* [規則引擎的條件運算式](cdn-rules-engine-reference-conditional-expressions.md)
* [規則引擎功能](cdn-rules-engine-reference-features.md)
* [使用規則引擎覆寫預設的 HTTP 行為](cdn-rules-engine.md)

