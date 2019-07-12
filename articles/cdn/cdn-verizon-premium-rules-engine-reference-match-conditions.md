---
title: 來自 Verizon Premium 的 azure CDN 規則引擎比對條件 |Microsoft Docs
description: 來自 Verizon Premium Azure 內容傳遞網路的參考文件規則引擎比對條件。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 1660dca34b2f128ef5889145fcdeed0d2523b9bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593208"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure 的 CDN from Verizon Premium 規則引擎比對條件

這篇文章列出可用比對條件的 Azure 內容傳遞網路 (CDN) 來自 Verizon Premium 的詳細的說明[規則引擎](cdn-verizon-premium-rules-engine.md)。

規則的第二個部分是比對條件。 比對條件會識別特定類型的要求，系統將針對這類要求執行一組功能。

例如，您可以使用比對條件：

- 篩選特定位置的內容要求。
- 從特定的 IP 位址或國家/地區產生的篩選要求。
- 依標頭資訊篩選要求。

## <a name="always-match-condition"></a>一律比對條件

「一律」比對條件可將一組預設功能套用至所有要求。

名稱 | 用途
-----|--------
[一律](#always) | 將一組預設功能套用至所有要求。

## <a name="device-match-condition"></a>裝置比對條件

「裝置」比對條件可識別從行動裝置根據其屬性所提出的要求。  

名稱 | 用途
-----|--------
[裝置](#device) | 識別從行動裝置根據其屬性所提出的要求。

## <a name="location-match-conditions"></a>位置比對條件

「位置」比對條件可根據要求者的位置識別要求。

名稱 | 用途
-----|--------
[AS 號碼](#as-number) | 識別源自特定網路的要求。
[國家/地區](#country) | 識別來自指定國家/地區的要求。

## <a name="origin-match-conditions"></a>原點比對條件

「原點」比對條件可識別指向內容傳遞網路儲存體或客戶原始伺服器的要求。

名稱 | 用途
-----|--------
[CDN 原點](#cdn-origin) | 識別儲存在內容傳遞網路儲存體中的內容要求。
[客戶原點](#customer-origin) | 識別對儲存於特定客戶原始伺服器上之內容的要求。

## <a name="request-match-conditions"></a>要求比對條件

「要求」比對條件可根據要求的屬性識別它們。

名稱 | 用途
-----|--------
[用戶端 IP 位址](#client-ip-address) | 識別源自特定 IP 位址的要求。
[Cookie 參數](#cookie-parameter) | 檢查與每個適用於指定值之要求相關聯的 Cookie。
[Cookie 參數 Regex](#cookie-parameter-regex) | 檢查與每個適用於規則運算式之要求相關聯的 Cookie。
[邊緣 Cname](#edge-cname) | 識別指向特定邊緣 CNAME 的要求。
[參考網域](#referring-domain) | 識別從指定主機名稱轉介的要求。
[要求標頭常值](#request-header-literal) | 識別包含設為指定值之指定標頭的要求。
[要求標頭 Regex](#request-header-regex) | 識別包含指定標頭的要求，該標頭已設定為符合特定規則運算式的值。
[要求標頭萬用字元](#request-header-wildcard) | 識別包含指定標頭的要求，該標頭已設定為符合特定模式的值。
[要求方法](#request-method) | 依其 HTTP 方法來識別要求。
[要求配置](#request-scheme) | 依其 HTTP 通訊協定來識別要求。

## <a name="url-match-conditions"></a>URL 比對條件

「URL」比對條件可根據要求的 URL 識別它們。

名稱 | 用途
-----|--------
[URL 路徑目錄](#url-path-directory) | 依其相對路徑來識別要求。
[URL 路徑副檔名](#url-path-extension) | 依其副檔名來識別要求。
[URL 路徑檔案名稱](#url-path-filename) | 依其檔案名稱來識別要求。
[URL 路徑常值](#url-path-literal) | 比較要求的相對路徑與指定的值。
[URL 路徑 Regex](#url-path-regex) | 比較要求的相對路徑與指定的規則運算式。
[URL 路徑萬用字元](#url-path-wildcard) | 比較要求的相對路徑與指定的模式。
[URL 查詢常值](#url-query-literal) | 比較要求的查詢字串與指定的值。
[URL 查詢參數](#url-query-parameter) | 識別包含指定查詢字串參數的要求，該參數已設定為符合特定模式的值。
[URL 查詢 Regex](#url-query-regex) | 識別包含指定查詢字串參數的要求，該參數已設定為符合特定規則運算式的值。
[URL 查詢萬用字元](#url-query-wildcard) | 根據要求的查詢字串來比較指定的值。

## <a name="reference-for-rules-engine-match-conditions"></a>規則引擎比對條件的參考

---

### <a name="always"></a>一律

「一律」比對條件可將一組預設功能套用至所有要求。

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>AS 號碼

AS 號碼網路是由其自發系統編號 (ASN) 所定義。 

[符合]  /[不符合]  選項決定符合「AS 號碼」比對條件所依據的條件：

- **符合**：用戶端網路的 ASN 必須符合其中一個指定的 ASN。 
- **不符合**：用戶端網路的 ASN 不得符合任何一個指定的 ASN。

重要資訊：

- 使用單一空格來分隔每個 ASN，藉以指定多個 ASN。 例如，64514 64515 會比對來自 64514 或 64515 的要求。
- 某些要求可能不會傳回有效的 ASN。 問號 (?) 會比對無法判斷有效 ASN 的要求。
- 指定所需網路的整個 ASN。 部分值無法進行比對。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>CDN 原點

當符合下列兩個條件時，即符合「CDN 原點」比對條件：

- 已要求 CDN 儲存體的內容。
- 要求 URI 使用此比對條件中所定義之內容存取點 (例如 /000001) 的類型：
  - CDN URL：要求 URI 必須包含選取的內容存取點。
  - 邊緣 CNAME URL：對應的邊緣 CNAME 設定必須指向選取的內容存取點。
  
重要資訊：

- 內容存取點可識別應該提供所要求內容的服務。
- 請勿使用 AND IF 陳述式來結合特定比對條件。 例如，結合「CDN 原點」比對條件與「客戶原點」比對條件會建立可能永遠無法比對的比對模式。 因此，不可透過 AND IF 陳述式結合兩個「CDN 原點」比對條件。

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>用戶端 IP 位址

[符合]  /[不符合]  選項決定符合「用戶端 IP 位址」比對條件所依據的條件：

- **符合**：用戶端的 IP 位址必須符合其中一個指定的 IP 位址。 
- **不符合**：用戶端的 IP 位址不得符合任何一個指定的 IP 位址。 

重要資訊：

- 使用 CIDR 標記法。
- 使用單一空格來分隔每個 IP 位址及/或 IP 位址區塊，藉以指定多個 IP 位址及/或 IP 位址區塊。 例如:
  - **IPv4 範例**：1.2.3.4 10.20.30.40 會比對來自位址 1.2.3.4 或 10.20.30.40 的任何要求。
  - **IPv6 範例**：1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 會比對來自位址 1:2:3:4:5:6:7:8 或 10:20:30:40:50:60:70:80 的任何要求。
- IP 位址區塊的語法是基底 IP 位址，後面接著正斜線和首碼大小。 例如:
  - **IPv4 範例**：5.5.5.64/26 會比對來自 5.5.5.64 到 5.5.5.127 的任何要求。
  - **IPv6 範例**：1:2:3:/48 會比對來自位址 1:2:3:0:0:0:0:0 到 1:2:3:ffff:ffff:ffff:ffff:ffff 的任何要求。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Cookie 參數

[符合]/[不符合]   選項決定符合「Cookie 參數」比對條件所依據的條件。

- **符合**：要求必須包含指定的 Cookie，且其值至少符合此比對條件中所定義的其中一個值。
- **不符合**：要求必須符合下列任一準則：
  - 它不包含指定的 Cookie。
  - 它包含指定的 Cookie，但其值不符合此比對條件中所定義的任何值。
  
重要資訊：

- Cookie 名稱：
  - 您在指定 Cookie 名稱時，並不支援包括星號 (*) 等萬用字元值，因此只有精確 Cookie 名稱相符項目才符合比較的資格。
  - 此比對條件的每個執行個體只能指定一個 Cookie 名稱。
  - Cookie 名稱比較不區分大小寫。
- Cookie 值：
  - 使用單一空格來分隔每個 Cookie 值，藉以指定多個 Cookie 值。
  - Cookie 值可以使用[萬用字元值](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)。
  - 如果未指定萬用字元值，則只有精確相符項目才符合此比對條件。 例如，指定 "Value" 會比對 "Value"，但不會比對 "Value1" 或 "Value2"。
  - 使用 [忽略大小寫]  選項來控制是否對要求的 Cookie 值執行區分大小寫比較。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Cookie 參數 Regex

「Cookie 參數 Regex」比對條件會定義 Cookie 名稱和值。 您可以使用[規則運算式](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)來定義所需的 Cookie 值。

[符合]/[不符合]   選項決定符合「Cookie 參數 RegEx」比對條件所依據的條件。

- **符合**：要求必須包含指定的 Cookie，且其值符合指定的規則運算式。
- **不符合**：要求必須符合下列任一準則：
  - 它不包含指定的 Cookie。
  - 它包含指定的 Cookie，但其值不符合指定的規則運算式。
  
重要資訊：

- Cookie 名稱：
  - 您在指定 Cookie 名稱時，並不支援規則運算式和包括星號 (*) 等萬用字元值，因此只有精確 Cookie 名稱相符項目才符合比較的資格。
  - 此比對條件的每個執行個體只能指定一個 Cookie 名稱。
  - Cookie 名稱比較不區分大小寫。
- Cookie 值：
  - Cookie 值可以使用規則運算式。
  - 使用 [忽略大小寫]  選項來控制是否對要求的 Cookie 值執行區分大小寫比較。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Country

您可以透過國碼 (地區碼) 來指定國家/地區。 

[符合]  /[不符合]  選項決定符合「國家/地區」比對條件所依據的條件：

- **符合**：要求必須包含指定的國碼 (地區碼) 值。 
- **不符合**：要求不得包含指定的國碼 (地區碼) 值。

重要資訊：

- 使用單一空格來分隔每個國碼 (地區碼)，藉以指定多個國碼 (地區碼)。
- 當您指定國碼 (地區碼) 時，不支援萬用字元。
- 國碼 (地區碼) "EU" 和 "AP" 不包含這些區域中的所有 IP 位址。
- 某些要求可能不會傳回有效的國碼 (地區碼)。 問號 (?) 會比對無法判斷有效國碼 (地區碼) 的要求。
- 國碼 (地區碼) 會區分大小寫。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>使用規則引擎來實作「國家/地區篩選條件」

此比對條件可讓您以要求的來源位置為基礎，執行許多自訂項目。 例如，您可以透過下列設定來複寫「國家/地區篩選條件」功能的行為：

- URL 路徑萬用字元比對：將 [URL 路徑萬用字元比對條件](#url-path-wildcard)設定為要保護的目錄。 
    將星號附加至相對路徑的結尾，以確保此規則會限制對其所有子系的存取。

- 國家/地區比對：將「國家/地區」比對條件設為一組所需的國家/地區。
  - 允許：將「國家/地區」比對條件設為 [不符合]  ，僅允許指定的國家/地區才能存取儲存在 URL 路徑萬用字元比對條件所定義之位置中的內容。
  - 封鎖：將「國家/地區」比對條件設為 [符合]  ，以封鎖指定的國家/地區存取儲存在 URL 路徑萬用字元比對條件所定義之位置中的內容。

- 拒絕存取 (403) 功能：啟用[拒絕存取 (403) 功能](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403)以複寫「國家/地區篩選條件」功能的允許或封鎖部分。

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>客戶原點

重要資訊：

- 不論內容的要求方式是透過 CDN URL 或指向所選客戶原點的邊緣 CNAME URL，都符合「客戶原點」比對條件。
- 您無法從 [客戶原點] 頁面刪除規則所參考的客戶原點設定。 嘗試刪除客戶原點設定之前，請確定下列設定未參考它：
  - 客戶原點比對條件
  - 邊緣 CNAME 設定
- 請勿使用 AND IF 陳述式來結合特定比對條件。 例如，結合「客戶原點」比對條件與「CDN 原點」比對條件會建立可能永遠無法比對的比對模式。 因此，不可透過 AND IF 陳述式結合兩個「客戶原點」比對條件。

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>裝置

「裝置」比對條件可識別從行動裝置根據其屬性所提出的要求。 行動裝置偵測可以透過 [WURFL](http://wurfl.sourceforge.net/) 達成。 

[符合]  /[不符合]  選項決定符合「裝置」比對條件所依據的條件：

- **符合**：要求者的裝置必須符合指定的值。 
- **不符合**：要求者的裝置不得符合指定的值。

重要資訊：

- 使用 [忽略大小寫]  選項來指定所指定的值是否區分大小寫。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

#### <a name="string-type"></a>字串類型

WURFL 功能通常會接受數字、字母和符號的任何組合。 由於這項功能具有彈性的本質，您必須選擇如何解譯與此比對條件相關聯的值。 下表描述可用選項組：

type     | 描述
---------|------------
常值  | 選取此選項可防止大部分字元使用其[常值](cdn-verizon-premium-rules-engine-reference.md#literal-values)來採用特殊的意義。
萬用字元 | 選取此選項以使用所有 [萬用字元] [萬用字元值](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)。
Regex    | 選取此選項可使用[規則運算式](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)。 規則運算式適用於定義字元的模式。

#### <a name="wurfl-capabilities"></a>WURFL 功能

WURFL 功能是指說明行動裝置的類別。 選取的功能會決定要用來識別要求的行動裝置描述之類型。

下表列出規則引擎的 WURFL 功能及其變數。

> [!NOTE]
> 下列變數在 [修改用戶端要求標頭]  和 [修改用戶端回應標頭]  功能中受到支援。

功能 | 變數 | 描述 | 範例值
-----------|----------|-------------|----------------
品牌名稱 | %{wurfl_cap_brand_name} | 字串，表示裝置的品牌名稱。 | Samsung
裝置作業系統 | %{wurfl_cap_device_os} | 字串，表示裝置上安裝的作業系統。 | iOS
裝置作業系統版本 | %{wurfl_cap_device_os_version} | 字串，表示裝置上安裝的作業系統版本號碼。 | 1.0.1
雙向 | %{wurfl_cap_dual_orientation} | 布林值，表示裝置是否支援雙向。 | true
HTML 慣用 DTD | %{wurfl_cap_html_preferred_dtd} | 字串，表示 HTML 內容的行動裝置慣用文件類型定義 (DTD)。 | None<br/>xhtml_basic<br/>html5
內嵌影像 | %{wurfl_cap_image_inlining} | 布林值，表示裝置是否支援 Base64 編碼映像。 | false
是 Android | %{wurfl_vcap_is_android} | 布林值，表示裝置是否使用 Android 作業系統。 | true
是 iOS | %{wurfl_vcap_is_ios} | 布林值，表示裝置是否使用 iOS。 | false
是智慧型電視 | %{wurfl_cap_is_smarttv} | 布林值，表示裝置是否為智慧型電視。 | false
是智慧型手機 | %{wurfl_vcap_is_smartphone} | 布林值，表示裝置是否為智慧型手機。 | true
是平板電腦 | %{wurfl_cap_is_tablet} | 布林值，表示裝置是否為平板電腦。 這項描述與作業系統無關。 | true
是無線裝置 | %{wurfl_cap_is_wireless_device} | 布林值，表示裝置是否視為無線裝置。 | true
行銷名稱 | %{wurfl_cap_marketing_name} | 字串，表示裝置的行銷名稱。 | BlackBerry 8100 Pearl
行動瀏覽器 | %{wurfl_cap_mobile_browser} | 字串，表示用來向裝置要求內容的瀏覽器。 | Chrome
行動瀏覽器版本 | %{wurfl_cap_mobile_browser_version} | 字串，表示用來向裝置要求內容的瀏覽器版本。 | 31
模型名稱 | %{wurfl_cap_model_name} | 字串，表示裝置的模型名稱。 | s3
漸進式下載 | %{wurfl_cap_progressive_download} | 布林值，指出裝置是否支援音訊和視訊仍在下載時播放。 | true
發行日期 | %{wurfl_cap_release_date} | 字串，表示裝置新增至 WURFL 資料庫的年和月。<br/><br/>格式： `yyyy_mm` | 2013_december
解析高度 | %{wurfl_cap_resolution_height} | 整數，表示裝置的高度，以像素為單位。 | 768
解析寬度 | %{wurfl_cap_resolution_width} | 整數，表示裝置的寬度，以像素為單位。 | 1024

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>邊緣 Cname

重要資訊：

- 可用的邊緣 CNAME 清單僅限於 [邊緣 CNAME] 頁面上已針對將要設定規則引擎之平台上設定的邊緣 CNAME。
- 嘗試刪除邊緣 CNAME 設定之前，請確定「邊緣 Cname」比對條件未參考它。 您無法從 [邊緣 CNAME] 頁面刪除規則中已定義的邊緣 CNAME 設定。
- 請勿使用 AND IF 陳述式來結合特定比對條件。 例如，結合「邊緣 Cname」比對條件與「客戶原點」比對條件會建立可能永遠無法比對的比對模式。 因此，不可透過 AND IF 陳述式結合兩個「邊緣 Cname」比對條件。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>轉介網域

與提出內容要求之查閱者建立關聯的主機名稱決定是否符合「參考網域」比對條件。

[符合]  /[不符合]  選項決定符合「參考網域」比對條件所依據的條件：

- **符合**：參考的主機名稱必須符合指定的值。 
- **不符合**：參考的主機名稱不得符合指定的值。

重要資訊：

- 使用單一空格來分隔每個主機名稱，藉以指定多個主機名稱。
- 此比對條件支援[萬用字元值](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)。
- 如果指定的值不包含星號，則必須完全符合查閱者的主機名稱。 例如，指定 "mydomain.com" 不會比對 "www.mydomain.com"。
- 使用 [忽略大小寫]  選項來控制是否要執行區分大小寫比較。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>要求標頭常值

[符合]/[不符合]   選項決定符合「要求標頭常值」比對條件所依據的條件。

- **符合**：要求必須包含指定的標頭。 其值必須符合此比對條件中所定義的值。
- **不符合**：要求必須符合下列任一準則：
  - 它不包含指定的標頭。
  - 它包含指定的標頭，但其值不符合此比對條件中所定義的標頭。
  
重要資訊：

- 標頭名稱比較一律不區分大小寫。 使用 [忽略大小寫]  選項來控制標頭值比較是否區分大小寫。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>要求標頭 Regex

[符合]/[不符合]   選項決定符合「要求標頭 RegEx」比對條件所依據的條件。

- **符合**：要求必須包含指定的標頭。 其值必須符合指定的[規則運算式](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)中所定義的模式。
- **不符合**：要求必須符合下列任一準則：
  - 它不包含指定的標頭。
  - 它包含指定的標頭，但其值不符合指定的規則運算式。

重要資訊：

- 標頭名稱：
  - 標頭名稱比較不區分大小寫。
  - 將標頭名稱中的空格取代為 "%20"。
- 標頭值：
  - 標頭值可以使用規則運算式。
  - 使用 [忽略大小寫]  選項來控制標頭值比較是否區分大小寫。
  - 僅當標頭值精確符合至少其中一個指定的模式時，才符合比對條件。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>要求標頭萬用字元

[符合]/[不符合]   選項決定符合「要求標頭萬用字元」比對條件所依據的條件。

- **符合**：要求必須包含指定的標頭。 其值必須至少符合此比對條件中所定義的其中一個值。
- **不符合**：要求必須符合下列任一準則：
  - 它不包含指定的標頭。
  - 它包含指定的標頭，但其值不符合任何一個指定的值。
  
重要資訊：

- 標頭名稱：
  - 標頭名稱比較不區分大小寫。
  - 標頭名稱中的空格應該以 "%20" 取代。 您也可以使用此值來指定標頭值中的空格。
- 標頭值：
  - 標頭值可以使用[萬用字元值](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)。
  - 使用 [忽略大小寫]  選項來控制標頭值比較是否區分大小寫。
  - 當標頭值完精確合至少其中一個指定的模式時，才符合比對條件。
  - 使用單一空格來分隔每個值，藉以指定多個值。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>要求方法

僅當透過所選要求方法要求資產時，才符合「要求方法」比對條件。 可用的要求方法包括：

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

重要資訊：

- 根據預設，只有 GET 要求方法可以產生網路上的快取內容。 所有其他要求方法則是透過網路進行 Proxy 處理。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>要求配置

僅當透過所選通訊協定要求資產時，才符合「要求配置」比對條件。 可用的通訊協定為：

- HTTP
- HTTPS

重要資訊：

- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>URL 路徑目錄

依要求的相對路徑來加以識別，但不包括所要求資產的檔案名稱。

[符合]  /[不符合]  選項決定符合「URL 路徑目錄」比對條件所依據的條件。

- **符合**：要求必須包含相對的 URL 路徑，但不包括符合指定 URL 模式的檔案名稱。
- **不符合**：要求必須包含相對的 URL 路徑，但不包括不符合指定 URL 模式的檔案名稱。

重要資訊：

- 使用 [相對於]  選項以指定 URL 比較要在內容存取點之前還是之後開始。 內容存取點是路徑的一部分，會出現在 Verizon CDN 主機名稱和所要求資產的相對路徑 (例如，/800001/CustomerOrigin) 之間。 它會依伺服器類型 (例如，CDN 或客戶原點) 和您的客戶帳戶編號來識別位置。

   下列的值可供 [相對於]  選項使用：
  - **根**：表示 URL 比較點會緊接在 CDN 主機名稱之後。 

  例如：http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **原點**：表示 URL 比較點會在內容存取點 (例如，/000001 或 /800001/myorigin) 之後。 相對於原點目錄，\*.azureedge.net CNAME 依預設會建立在 Verizon CDN 主機名稱上，因此 Azure CDN 使用者應該使用**原點**值。 

  例如：https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  此 URL 會指向下列 Verizon CDN 主機名稱：http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- 在 URL 比較之前，邊緣 CNAME URL 會重寫為 CDN URL。

    例如，以下這兩個 URL 都指向相同的資產，因此具有相同的 URL 路徑。
  - CDN URL：http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - 邊緣 CNAME URL：http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    其他資訊：
  - 自訂網域：https:\//my.domain.com/path/asset.htm
    
    - URL 路徑 (相對於根)：/800001/CustomerOrigin/path/
    
    - URL 路徑 (相對於原點)：/path/

- 用於 URL 比較的 URL 部分剛好結束於所要求資產的檔案名稱之前。 這種類型路徑中的最後一個字元是句尾斜線。

- 將 URL 路徑模式中的任何空格取代為 "%20"。

- 每個 URL 路徑模式可包含一個或多個星號 (*)，其中每個星號都符合一個或多個字元序列。

- 使用單一空格來分隔模式中的每個 URL 路徑，藉以指定模式中的多個 URL 路徑。

    例如：*/sales/ */marketing/

- URL 路徑規格可以使用[萬用字元值](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)。

- 使用 [忽略大小寫]  選項來控制是否要執行區分大小寫比較。

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>URL 路徑的副檔名

依所要求資產的副檔名來識別要求。

[符合]  /[不符合]  選項決定符合「URL 路徑副檔名」比對條件所依據的條件。

- **符合**：要求的 URL 必須包含精確符合指定模式的副檔名。

   例如，如果您指定 "htm"，就會比對 "htm" 資產，但不會比對 "html" 資產。  

- **不符合**：要求的 URL 必須包含不符合指定模式的副檔名。

重要資訊：

- 指定要在 [值]  方塊中比對的副檔名。 請勿包含前置句號；例如，使用 htm 而不是 .htm。

- 使用 [忽略大小寫]  選項來控制是否要執行區分大小寫比較。

- 使用單一空格來分隔每個副檔名，藉以指定多個副檔名。 

    例如：htm html

- 例如，指定 "htm" 會比對 "htm" 資產，但不會比對 "html" 資產。

#### <a name="sample-scenario"></a>範例案例

下列範例設定會假設當要求符合其中一個指定的副檔名時，就符合比對條件。

值規格： asp aspx php html

當此比對條件找到以下列副檔名結尾的 URL 時，就符合此比對條件：

- .asp
- .aspx
- .php
- .html

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>URL 路徑的檔案名稱

依所要求資產的檔案名稱來識別要求。 基於這個比對條件的目的，檔案名稱會包含所要求資產的名稱、句號和副檔名 (例如，index.html)。

[符合]  /[不符合]  選項決定符合「URL 路徑檔名」比對條件所依據的條件。

- **符合**：要求必須在其 URL 路徑中包含符合指定模式的檔案名稱。
- **不符合**：要求必須在其 URL 路徑中包含不符合指定模式的檔案名稱。

重要資訊：

- 使用 [忽略大小寫]  選項來控制是否要執行區分大小寫比較。

- 若要指定多個副檔名，請使用單一空格來分隔每個副檔名。

    例如：index.htm index.html

- 將檔案名稱值中的空格取代為 "%20"。

- 檔案名稱值可以使用[萬用字元值](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)。 例如，每個檔案名稱模式可包含一個或多個星號 (*)，其中每個星號都符合一個或多個字元序列。

- 如果未指定萬用字元，則只有精確相符項目才符合此比對條件。

    例如，指定 "presentation.ppt" 會比對名為 "presentation.ppt" 的資產，但不會比對名為 "presentation.pptx" 的資產。

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>URL 路徑常值

比較要求的 URL 路徑 (包括檔案名稱) 與指定的值。

[符合]  /[不符合]  選項決定符合「URL 路徑常值」比對條件所依據的條件。

- **符合**：要求必須包含符合指定模式的 URL 路徑。
- **不符合**：要求必須包含不符合指定模式的 URL 路徑。

重要資訊：

- 使用 [相對於]  選項以指定 URL 比較點要在內容存取點之前還是之後開始。 

    下列的值可供 [相對於]  選項使用：
  - **根**：表示 URL 比較點會緊接在 CDN 主機名稱之後。

    例如：http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **原點**：表示 URL 比較點會在內容存取點 (例如，/000001 或 /800001/myorigin) 之後。 相對於原點目錄，\*.azureedge.net CNAME 依預設會建立在 Verizon CDN 主機名稱上，因此 Azure CDN 使用者應該使用**原點**值。 

    例如：https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  此 URL 會指向下列 Verizon CDN 主機名稱：http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- 在 URL 比較之前，邊緣 CNAME URL 會重寫為 CDN URL。

例如，以下這兩個 URL 都指向相同的資產，因此具有相同的 URL 路徑：

- CDN URL：http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- 邊緣 CNAME URL：http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    其他資訊：
    
    - URL 路徑 (相對於根)：/800001/CustomerOrigin/path/asset.htm
   
    - URL 路徑 (相對於原點)：/path/asset.htm

- 會忽略 URL 中的查詢字串。
- 使用 [忽略大小寫]  選項來控制是否要執行區分大小寫比較。
- 系統會以此比對條件所指定的值，與用戶端所提出的精確要求之相對路徑進行比較。

- 若要將對特定目錄所做的所有要求進行比對，請使用 [URL 路徑目錄](#url-path-directory)或 [URL 路徑萬用字元](#url-path-wildcard)比對條件。

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>URL 路徑 Regex

比較要求的 URL 路徑與指定的[規則運算式](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)。

[符合]  /[不符合]  選項決定符合「URL 路徑 RegEx」比對條件所依據的條件。

- **符合**：要求必須包含符合指定規則運算式的 URL 路徑。
- **不符合**：要求必須包含不符合指定規則運算式的 URL 路徑。

重要資訊：

- 在 URL 比較之前，邊緣 CNAME URL 會重寫為 CDN URL。

    例如，這兩個 URL 都指向相同的資產，因此具有相同的 URL 路徑。

     - CDN URL：http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - 邊緣 CNAME URL：http:\//my.domain.com/path/asset.htm

    其他資訊：
    
     - URL 路徑：/800001/CustomerOrigin/path/asset.htm

- 會忽略 URL 中的查詢字串。
    
- 使用 [忽略大小寫]  選項來控制是否要執行區分大小寫比較。
    
- URL 路徑中的空格應該以 "%20" 取代。

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>URL 路徑萬用字元

比較要求的相對 URL 路徑與指定的萬用字元模式。

[符合]  /[不符合]  選項決定符合「URL 路徑萬用字元」比對條件所依據的條件。

- **符合**：要求必須包含符合指定萬用字元模式的 URL 路徑。
- **不符合**：要求必須包含不符合指定萬用字元模式的 URL 路徑。

重要資訊：

- [相對於]  選項：此選項決定 URL 比較點要在內容存取點之前還是之後開始。

   這個選項具有下列值：
     - **根**：表示 URL 比較點會緊接在 CDN 主機名稱之後。

       例如：http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **原點**：表示 URL 比較點會在內容存取點 (例如，/000001 或 /800001/myorigin) 之後。 相對於原點目錄，\*.azureedge.net CNAME 依預設會建立在 Verizon CDN 主機名稱上，因此 Azure CDN 使用者應該使用**原點**值。 

       例如：https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     此 URL 會指向下列 Verizon CDN 主機名稱：http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- 在 URL 比較之前，邊緣 CNAME URL 會重寫為 CDN URL。

    例如，以下這兩個 URL 都指向相同的資產，因此具有相同的 URL 路徑：
     - CDN URL： http://wpc.0001.&lt ;domain&gt; /800001/CustomerOrigin/path/asset.htm
     - 邊緣 CNAME URL：http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    其他資訊：
    
     - URL 路徑 (相對於根)：/800001/CustomerOrigin/path/asset.htm
    
     - URL 路徑 (相對於原點)：/path/asset.htm
    
- 使用單一空格來分隔每個 URL 路徑，藉以指定多個 URL 路徑。

   例如：/marketing/asset.* /sales/\*.htm

- 會忽略 URL 中的查詢字串。
    
- 使用 [忽略大小寫]  選項來控制是否要執行區分大小寫比較。
    
- 將 URL 路徑中的空格取代為 "%20"。
    
- 針對 URL 路徑指定的值可以使用[萬用字元值](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)。 每個 URL 路徑模式可包含一個或多個星號 (*)，其中每個星號都可能符合一個或多個字元序列。

#### <a name="sample-scenarios"></a>範例案例

下表中的範例設定會假設當要求符合指定的 URL 模式時，就符合比對條件：

值                   | 相對於    | 結果 
------------------------|----------------|-------
\*/test.html \*/test.php  | 根或原點 | 要求此模式比對任何資料夾中名為 "test.html" 或 "test.php" 的資產。
/80ABCD/origin/text/*   | 根           | 要求的資產符合下列準則時，會比對此模式： <br />- 必須位於名為 "origin" 的客戶原點。 <br />- 相對路徑必須以名為 "text" 的資料夾為開頭。 也就是所要求的資產可以位於 "text" 資料夾中，或是位於該資料夾的其中一個遞迴子資料夾中。
/css/  /js/           | 根或原點 | 此模式會依所有包含 css 或 js 資料夾的 CDN 或邊緣 CNAME URL 進行比對。
*.jpg *.gif *.png       | 根或原點 | 此模式會依所有以 .jpg、.gif 或 .png 結尾的 CDN 或邊緣 CNAME URL 進行比對。 指定此模式的替代方式是使用 [URL 路徑副檔名比對條件](#url-path-extension)。
/images/\* /media/\*      | 來源         | 此模式會依相對路徑開頭為「映像」或「媒體」資料夾的 CDN 或邊緣 CNAME URL 進行比對。 <br />- CDN URL：http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- 範例邊緣 CNAME URL：http:\//cdn.mydomain.com/images/sales/event1.png

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>URL 查詢常值

比較要求的查詢字串與指定的值。

[符合]  /[不符合]  選項決定符合「URL 查詢常值」比對條件所依據的條件。

- **符合**：要求必須包含符合指定查詢字串的 URL 查詢字串。
- **不符合**：要求必須包含不符合指定查詢字串的 URL 查詢字串。

重要資訊：

- 只有精確查詢字串相符項目符合這項比對條件。
    
- 使用 [忽略大小寫]  選項來控制查詢字串比較是否區分大小寫。
    
- 請勿在查詢字串值文字中包含前置問號 (?)。
    
- 某些字元需要 URL 編碼。 使用百分比符號以 URL 編碼下列字元：

   Character | URL 編碼
   ----------|---------
   空白字元     | %20
   &         | %25

- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
   - 完成快取填滿
   - 預設的內部最大壽命
   - 強制執行內部最大壽命
   - 忽略原始的 No-Cache
   - 內部最大過時

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>URL 查詢參數

識別包含指定之查詢字串參數的要求。 這個參數會設定為符合指定之模式的值。 要求 URL 中的查詢字串參數 (例如，parameter=value) 會決定是否符合此條件。 此比對條件會依查詢字串參數的名稱來識別查詢字串參數，並接受一個或多個參數值的值。 

[符合]  /[不符合]  選項決定符合「URL 查詢參數」比對條件所依據的條件。

- **符合**：要求必須包含指定的參數，且其值至少符合此比對條件中所定義的其中一個值。
- **不符合**：要求必須符合下列任一準則：
  - 它不包含指定的參數。
  - 它包含指定的參數，但其值不符合此比對條件中所定義的任何值。

此比對條件提供簡易的方式來指定參數名稱/值的組合。 如果您要比對查詢字串參數，為取得更大的彈性，請考量使用 [URL 查詢萬用字元](#url-query-wildcard)比對條件。

重要資訊：

- 此比對條件的每個執行個體只能指定一個 URL 查詢參數名稱。
    
- 指定參數名稱時，並不支援萬用字元值，因此只有精確參數名稱相符項目才符合比較的資格。
- 參數值可以包含[萬用字元值](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)。
   - 每個參數值模式可包含一個或多個星號 (*)，其中每個星號都可能符合一個或多個字元序列。
   - 某些字元需要 URL 編碼。 使用百分比符號以 URL 編碼下列字元：

       Character | URL 編碼
       ----------|---------
       空白字元     | %20
       &         | %25

- 使用單一空格來分隔每個查詢字串參數值，藉以指定多個查詢字串參數值。 當要求包含其中一個指定的名稱/值組合時，才符合此比對條件。

   - 範例 1：

     - 組態:

       ValueA ValueB

     - 此設定符合下列查詢字串參數：

       Parameter1=ValueA
    
       Parameter1=ValueB

   - 範例 2：

     - 組態: 

        Value%20A Value%20B

     - 此設定符合下列查詢字串參數：

       Parameter1=Value%20A

       Parameter1=Value%20B

- 僅當至少其中一個指定的查詢字串名稱/值組合有精確相符項目時，才符合此比對條件。

   如果您使用先前範例中的設定，就不會將名稱/值組合 "Parameter1=ValueAdd" 視為相符。 不過，如果您指定下列任何一個值，它就會比對該名稱/值組合：

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- 使用 [忽略大小寫]  選項來控制查詢字串比較是否區分大小寫。
    
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
   - 完成快取填滿
   - 預設的內部最大壽命
   - 強制執行內部最大壽命
   - 忽略原始的 No-Cache
   - 內部最大過時

#### <a name="sample-scenarios"></a>範例案例

下列範例會示範這個選項在特定情況中的運作方式：

名稱  | 值 |  結果
------|-------|--------
使用者  | Joe   | 當所要求 URL 的查詢字串為 "?user=joe" 時，會比對此模式。
使用者  | *     | 當所要求 URL 的查詢字串包含 User 參數時，會比對此模式。
Email | Joe\* | 當所要求 URL 的查詢字串包含以 "Joe" 為開頭的 Email 參數時，會比對此模式。

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>URL 查詢 Regex

識別包含指定之查詢字串參數的要求。 這個參數會設定為符合指定之[規則運算式](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)的值。

[符合]  /[不符合]  選項決定符合「URL 查詢 RegEx」比對條件所依據的條件。

- **符合**：要求必須包含符合指定規則運算式的 URL 查詢字串。
- **不符合**：要求必須包含不符合指定規則運算式的 URL 查詢字串。

重要資訊：

- 只有精確符合指定的規則運算式才符合此比對條件。
    
- 使用 [忽略大小寫]  選項來控制查詢字串比較是否區分大小寫。
    
- 基於此選項的目的，查詢字串是從查詢字串的問號 (?) 分隔符號之後的第一個字元為開頭。
    
- 某些字元需要 URL 編碼。 使用百分比符號以 URL 編碼下列字元：

   Character | URL 編碼 | 值
   ----------|--------------|------
   空白字元     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`20
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`25
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   請注意必須逸出百分比符號。

- 要在規則運算式中包含一個反斜線的規則運算式雙逸出特殊字元 (例如，\^$.+)。

   例如:

   值 | 解譯為 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
   - 完成快取填滿
   - 預設的內部最大壽命
   - 強制執行內部最大壽命
   - 忽略原始的 No-Cache
   - 內部最大過時

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>URL 查詢萬用字元

根據要求的查詢字串來比較指定的值。

[符合]  /[不符合]  選項決定符合「URL 查詢萬用字元」比對條件所依據的條件。

- **符合**：要求必須包含符合指定萬用字元值的 URL 查詢字串。
- **不符合**：要求必須包含不符合指定萬用字元值的 URL 查詢字串。

重要資訊：

- 基於此選項的目的，查詢字串是從查詢字串的問號 (?) 分隔符號之後的第一個字元為開頭。
- 參數值可以包含[萬用字元值](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)：
   - 每個參數值模式可包含一個或多個星號 (*)，其中每個星號都可能符合一個或多個字元序列。
   - 某些字元需要 URL 編碼。 使用百分比符號以 URL 編碼下列字元：

     Character | URL 編碼
     ----------|---------
     空白字元     | %20
     &         | %25

- 使用單一空格來分隔每個值，藉以指定多個值。

   例如: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- 只有精確符合至少其中一個指定的查詢字串模式才符合此比對條件。
    
- 使用 [忽略大小寫]  選項來控制查詢字串比較是否區分大小寫。
    
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
   - 完成快取填滿
   - 預設的內部最大壽命
   - 強制執行內部最大壽命
   - 忽略原始的 No-Cache
   - 內部最大過時

#### <a name="sample-scenarios"></a>範例案例

下列範例會示範這個選項在特定情況中的運作方式：

 名稱                 | 描述
 ---------------------|------------
user=joe              | 當所要求 URL 的查詢字串為 "?user=joe" 時，會比對此模式。
\*user=\* \*optout=\* | 當 CDN URL 查詢包含 user 或 optout 參數時，會比對此模式。

[回到頁首](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>後續步驟

- [Azure 內容傳遞網路概觀](cdn-overview.md)
- [規則引擎參考](cdn-verizon-premium-rules-engine-reference.md)
- [規則引擎條件運算式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [規則引擎功能](cdn-verizon-premium-rules-engine-reference-features.md)
- [使用規則引擎覆寫預設的 HTTP 行為](cdn-verizon-premium-rules-engine.md)
