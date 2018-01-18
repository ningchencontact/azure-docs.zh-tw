---
title: "Azure CDN 規則引擎的比對條件 | Microsoft Docs"
description: "Azure 內容傳遞網路規則引擎比對條件的參考文件。"
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
ms.openlocfilehash: 9986e654b076df099e3912f9da628728723b5c3d
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2018
---
# <a name="match-conditions-for-the-azure-cdn-rules-engine"></a>Azure CDN 規則引擎的比對條件
本文會針對 Azure 內容傳遞網路 (CDN) [規則引擎](cdn-rules-engine.md)列出可用比對條件的詳細描述。

規則的第二個部分是比對條件。 比對條件會識別特定類型的要求，系統將針對這類要求執行一組功能。

例如，您可以使用比對條件：
- 篩選特定位置的內容要求。
- 篩選從特定 IP 位址或國家/地區產生的要求。
- 依標頭資訊篩選要求。

## <a name="always-match-condition"></a>一律比對條件

「一律」比對條件可將一組預設功能套用至所有要求。

名稱 | 目的
-----|--------
[一律](#always) | 將一組預設功能套用至所有要求。

## <a name="device-match-condition"></a>裝置比對條件

「裝置」比對條件可識別從行動裝置根據其屬性所提出的要求。  

名稱 | 目的
-----|--------
[裝置](#device) | 識別從行動裝置根據其屬性所提出的要求。

## <a name="location-match-conditions"></a>位置比對條件

「位置」比對條件可根據要求者的位置識別要求。

名稱 | 目的
-----|--------
[AS 號碼](#as-number) | 識別源自特定網路的要求。
[國家/地區](#country) | 識別源自特定國家/地區的要求。

## <a name="origin-match-conditions"></a>原點比對條件

「原點」比對條件可識別指向內容傳遞網路儲存體或客戶原始伺服器的要求。

名稱 | 目的
-----|--------
[CDN 原點](#cdn-origin) | 識別儲存在內容傳遞網路儲存體中的內容要求。
[客戶原點](#customer-origin) | 識別對儲存於特定客戶原始伺服器上之內容的要求。

## <a name="request-match-conditions"></a>要求比對條件

「要求」比對條件可根據要求的屬性識別它們。

名稱 | 目的
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

名稱 | 目的
-----|--------
URL 路徑目錄 | 依其相對路徑來識別要求。
URL 路徑的副檔名 | 依其副檔名來識別要求。
URL 路徑的檔案名稱 | 依其檔案名稱來識別要求。
URL 路徑常值 | 比較要求的相對路徑與指定的值。
URL 路徑 Regex | 比較要求的相對路徑與指定的規則運算式。
URL 路徑萬用字元 | 比較要求的相對路徑與指定的模式。
URL 查詢常值 | 比較要求的查詢字串與指定的值。
URL 查詢參數 | 識別包含指定查詢字串參數的要求，該參數已設定為符合特定模式的值。
URL 查詢 Regex | 識別包含指定查詢字串參數的要求，該參數已設定為符合特定規則運算式的值。
URL 查詢萬用字元 | 根據要求的查詢字串來比較指定的值。


## <a name="reference-for-rules-engine-match-conditions"></a>規則引擎比對條件的參考

---
### <a name="always"></a>一律

「一律」比對條件可將一組預設功能套用至所有要求。

[回到頁首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="as-number"></a>AS 號碼 
AS 號碼網路是由其自發系統編號 (ASN) 所定義。 其提供一個選項，指出當用戶端的網路「符合」或「不符合」指定的 ASN 時是否符合此條件。

重要資訊：
- 使用單一空格來分隔每個 ASN，藉以指定多個 ASN。 例如，64514 64515 會比對來自 64514 或 64515 的要求。
- 某些要求可能不會傳回有效的 ASN。 問號 (?) 會比對無法判斷有效 ASN 的要求。
- 您必須指定所需網路的整個 ASN。 部分值無法進行比對。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cdn-origin"></a>CDN 原點
當符合下列兩個條件時，即符合「CDN 原點」比對條件：
- 要求的內容來自內容傳遞網路儲存體。
- 要求 URI 使用此比對條件中所定義的內容存取點 (例如 /000001)。
  - 內容傳遞網路 URL：要求 URI 必須包含選取的內容存取點。
  - 邊緣 CNAME URL：對應的邊緣 CNAME 設定必須指向選取的內容存取點。
  
重要資訊：
 - 內容存取點可識別應該提供所要求內容的服務。
 - 請勿使用 AND IF 陳述式來結合特定比對條件。 例如，結合「CDN 原點」比對條件與「客戶原點」比對條件會建立可能永遠無法比對的比對模式。 因此，不可透過 AND IF 陳述式結合兩個「CDN 原點」比對條件。

[回到頁首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="client-ip-address"></a>用戶端 IP 位址
其提供一個選項，指出當用戶端的 IP 位址「符合」或「不符合」指定的 IP 位址時是否符合「用戶端 IP 位址」條件。

重要資訊：
- 請務必使用 CIDR 表示法。
- 使用單一空格來分隔每個 IP 位址及/或 IP 位址區塊，藉以指定多個 IP 位址及/或 IP 位址區塊。
  - **IPv4 範例**：1.2.3.4 10.20.30.40 會比對來自 1.2.3.4 或 10.20.30.40 的任何要求。
  - **IPv6 範例**：1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 會比對來自 1:2:3:4:5:6:7:8 或 10:20:30:40:50:60:70:80 的任何要求。
- IP 位址區塊的語法是基底 IP 位址，後面接著正斜線和首碼大小。
  - **IPv4 範例**：5.5.5.64/26 會比對來自 5.5.5.64 到 5.5.5.127 的任何要求。
  - **IPv6 範例**：1:2:3:/48 會比對來自 1:2:3:0:0:0:0:0 到 1:2:3:ffff:ffff:ffff:ffff:ffff 的任何要求。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter"></a>Cookie 參數
[符合]/[不符合] 選項決定符合「Cookie 參數」比對條件所依據的條件。
- **符合**：要求必須包含指定的 Cookie，且其值至少符合此比對條件中所定義的其中一個值。
- **不符合**：要求必須符合下列任一準則：
  - 它不包含指定的 Cookie。
  - 它包含指定的 Cookie，但其值不符合此比對條件中所定義的任何值。
  
重要資訊：
- Cookie 名稱： 
  - 當您指定 Cookie 名稱時，不支援特殊字元 (包括星號)。 這表示只有完全符合的 Cookie 名稱才有資格進行比較。
  - 此比對條件的每個執行個體只能指定一個 Cookie 名稱。
  - Cookie 名稱比較不區分大小寫。
- Cookie 值： 
  - 使用單一空格來分隔每個 Cookie 值，藉以指定多個 Cookie 值。
  - Cookie 值可以使用特殊字元。 
  - 如果未指定萬用字元，只有完全符合才符合此比對條件。 例如，指定 "Value" 會比對 "Value"，但不會比對 "Value1" 或 "Value2"。
  - [忽略大小寫] 選項決定是否對要求的 Cookie 值執行區分大小寫比較。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter-regex"></a>Cookie 參數 Regex
「Cookie 參數 Regex」比對條件會定義 Cookie 名稱和值。 您可以使用規則運算式來定義所需的 Cookie 值。 

[符合]/[不符合] 選項決定符合此比對條件所依據的條件。
- **符合**：要求必須包含指定的 Cookie，且其值符合指定的規則運算式。
- **不符合**：要求必須符合下列任一準則：
  - 它不包含指定的 Cookie。
  - 它包含指定的 Cookie，但其值不符合指定的規則運算式。
  
重要資訊：
- Cookie 名稱： 
  - 當您指定 Cookie 名稱時，不支援規則運算式和特殊字元 (包括星號)。 這表示只有完全符合的 Cookie 名稱才有資格進行比較。
  - 此比對條件的每個執行個體只能指定一個 Cookie 名稱。
  - Cookie 名稱比較不區分大小寫。
- Cookie 值： 
  - Cookie 值可以使用規則運算式。
  - [忽略大小寫] 選項決定是否對要求的 Cookie 值執行區分大小寫比較。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

--- 
### <a name="country"></a>國家 (地區)
您可以透過國碼 (地區碼) 來指定國家/地區。 其提供一個選項，指出當要求源自的國家/地區「符合」或「不符合」指定的值時是否符合此條件。

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

[回到頁首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="customer-origin"></a>客戶原點

重要資訊： 
- 不論內容的要求方式是透過內容傳遞網路 URL 或指向所選客戶原點的邊緣 CNAME URL，都符合「客戶原點」比對條件。
- 您無法從 [客戶原點] 頁面刪除規則所參考的客戶原點設定。 嘗試刪除客戶原點設定之前，請確定下列設定未參考它：
  - 客戶原點比對條件
  - 邊緣 CNAME 設定
- 請勿使用 AND IF 陳述式來結合特定比對條件。 例如，結合「客戶原點」比對條件與「CDN 原點」比對條件會建立可能永遠無法比對的比對模式。 因此，不可透過 AND IF 陳述式結合兩個「客戶原點」比對條件。

[回到頁首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="device"></a>裝置

「裝置」比對條件可識別從行動裝置根據其屬性所提出的要求。 行動裝置偵測可以透過 [WURFL](http://wurfl.sourceforge.net/) 達成。 下表列出內容傳遞網路規則引擎的 WURFL 功能及其變數。
<br>
> [!NOTE] 
> 下列變數在 [修改用戶端要求標頭] 和 [修改用戶端回應標頭] 功能中受到支援。

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
是平板電腦 | %{wurfl_cap_is_tablet} | 布林值，表示裝置是否為平板電腦。 這是獨立於作業系統的描述。 | true
是無線裝置 | %{wurfl_cap_is_wireless_device} | 布林值，表示裝置是否視為無線裝置。 | true
行銷名稱 | %{wurfl_cap_marketing_name} | 字串，表示裝置的行銷名稱。 | BlackBerry 8100 Pearl
行動瀏覽器 | %{wurfl_cap_mobile_browser} | 字串，表示用來向裝置要求內容的瀏覽器。 | Chrome
行動瀏覽器版本 | %{wurfl_cap_mobile_browser_version} | 字串，表示用來向裝置要求內容的瀏覽器版本。 | 31
模型名稱 | %{wurfl_cap_model_name} | 字串，表示裝置的模型名稱。 | s3
漸進式下載 | %{wurfl_cap_progressive_download} | 布林值，指出裝置是否支援音訊和視訊仍在下載時播放。 | true
發行日期 | %{wurfl_cap_release_date} | 字串，表示裝置新增至 WURFL 資料庫的年和月。<br/><br/>格式： `yyyy_mm` | 2013_december
解析高度 | %{wurfl_cap_resolution_height} | 整數，表示裝置的高度，以像素為單位。 | 768
解析寬度 | %{wurfl_cap_resolution_width} | 整數，表示裝置的寬度，以像素為單位。 | 1024

[回到頁首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="edge-cname"></a>邊緣 Cname
重要資訊： 
- 可用的邊緣 CNAME 清單僅限於 [邊緣 CNAME] 頁面上已設定並對應至將要設定 HTTP 規則引擎之平台的 邊緣 CNAME。
- 嘗試刪除邊緣 CNAME 設定之前，請確定「邊緣 Cname」比對條件未參考它。 您無法從 [邊緣 CNAME] 頁面刪除規則中已定義的邊緣 CNAME 設定。 
- 請勿使用 AND IF 陳述式來結合特定比對條件。 例如，結合「邊緣 Cname」比對條件與「客戶原點」比對條件會建立可能永遠無法比對的比對模式。 因此，不可透過 AND IF 陳述式結合兩個「邊緣 Cname」比對條件。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="referring-domain"></a>轉介網域
與提出內容要求之查閱者建立關聯的主機名稱決定是否符合「參考網域」比對條件。 其提供一個選項，指出當參考主機名稱「符合」或「不符合」指定的值時是否符合此條件。

重要資訊：
- 使用單一空格來分隔每個主機名稱，藉以指定多個主機名稱。
- 此比對條件支援特殊字元。
- 如果指定的值不包含星號，則必須完全符合查閱者的主機名稱。 例如，指定 "mydomain.com" 不會比對 "www.mydomain.com"。
- [忽略大小寫] 選項決定是否執行區分大小寫比較。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-literal"></a>要求標頭常值
[符合]/[不符合] 選項決定符合此比對條件所依據的條件。
- **符合**：要求必須包含指定的標頭。 其值必須符合此比對條件中所定義的值。
- **不符合**：要求必須符合下列任一準則：
  - 它不包含指定的標頭。
  - 它包含指定的標頭，但其值不符合此比對條件中所定義的標頭。
  
重要資訊：
- 標頭名稱比較一律不區分大小寫。 [忽略大小寫] 選項決定標頭值比較是否區分大小寫。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-regex"></a>要求標頭 Regex
[符合]/[不符合] 選項決定符合「要求標頭 Regex」比對條件所依據的條件。
- **符合**：要求必須包含指定的標頭。 其值必須符合指定的規則運算式中所定義的模式。
- **不符合**：要求必須符合下列任一準則：
  - 它不包含指定的標頭。
  - 它包含指定的標頭，但其值不符合指定的規則運算式。

重要資訊：
- 標頭名稱： 
  - 標頭名稱比較不區分大小寫。
  - 標頭名稱中的空格應該以 "%20" 取代。 
- 標頭值： 
  - 標頭值可以使用規則運算式。
  - [忽略大小寫] 選項決定標頭值比較是否區分大小寫。
  - 只有完全符合至少一個指定模式的標頭值才符合此條件。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時 

[回到頁首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-header-wildcard"></a>要求標頭萬用字元
[符合]/[不符合] 選項決定符合「要求標頭萬用字元」比對條件所依據的條件。
- **符合**：要求必須包含指定的標頭。 其值必須至少符合此比對條件中所定義的其中一個值。
- **不符合**：要求必須符合下列任一準則：
  - 它不包含指定的標頭。
  - 它包含指定的標頭，但其值不符合任何一個指定的值。
  
重要資訊：
- 標頭名稱： 
  - 標頭名稱比較不區分大小寫。
  - 標頭名稱中的空格應該以 "%20" 取代。 您也可以使用此值來指定標頭值中的空格。
- 標頭值： 
  - 標頭值可以使用特殊字元。
  - [忽略大小寫] 選項決定標頭值比較是否區分大小寫。
  - 只有完全符合至少一個指定模式的標頭值才符合此條件。
  - 使用單一空格來分隔每個值，藉以指定多個值。
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-method"></a>要求方法
只有透過所選要求方法要求的資產才符合「要求方法」條件。 可用的要求方法包括：
- GET
- HEAD 
- POST 
- OPTIONS 
- PUT 
- 刪除 
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

[回到頁首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-scheme"></a>要求配置
只有透過所選通訊協定要求的資產才符合「要求配置」條件。 可用的通訊協定為 HTTP 和 HTTPS。

重要資訊：
- 由於追蹤快取設定的行為，因而導致此比對條件與下列功能不相容：
  - 完成快取填滿
  - 預設的內部最大壽命
  - 強制執行內部最大壽命
  - 忽略原始的 No-Cache
  - 內部最大過時

[回到頁首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

## <a name="next-steps"></a>後續步驟
* [Azure 內容傳遞網路概觀](cdn-overview.md)
* [規則引擎參考](cdn-rules-engine-reference.md)
* [規則引擎條件運算式](cdn-rules-engine-reference-conditional-expressions.md)
* [規則引擎功能](cdn-rules-engine-reference-features.md)
* [使用規則引擎覆寫預設的 HTTP 行為](cdn-rules-engine.md)

