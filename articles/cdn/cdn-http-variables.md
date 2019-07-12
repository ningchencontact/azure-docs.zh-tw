---
title: Azure CDN 規則引擎的 HTTP 變數 | Microsoft Docs
description: HTTP 變數可讓您擷取 HTTP 要求和回應中繼資料。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 53ad0c516547e17801bd57c2fd6b0d1704383797
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593825"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Azure CDN 規則引擎的 HTTP 變數
HTTP 變數能提供擷取 HTTP 要求和回應中繼資料的方法。 此中繼資料可以接著用來動態調整要求或回應。 HTTP 變數的使用，僅限於下列規則引擎功能：

- [快取索引鍵重寫](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [修改用戶端要求標頭](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [修改用戶端回應標頭](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL 重新導向](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL 重寫](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>定義
下表描述支援的 HTTP 變數。 在無法針對特定要求提供地理中繼資料 (例如郵遞區號) 時，系統將會傳回空白值。


| 名稱 | 變數 | 描述 | 範例值 |
| ---- | -------- | ----------- | ------------ |
| ASN (要求者) | %{geo_asnum} | 表示要求者的 AS 號碼。 <br /><br />**已淘汰：** %{virt_dst_asnum}。 <br />此變數已由 %{geo_asnum} 取代。 雖然使用此已淘汰變數的規則仍然可以運作，您應該更新規則以使用新的變數。 | AS15133 |
| 城市 (要求者) | %{geo_city} | 表示要求者的城市。 | 洛杉磯 |
| 大陸 (要求者) | %{geo_continent} | 透過縮寫表示要求者的大陸。 <br />有效值為： <br />AF:非洲<br />做為：亞洲<br />歐洲：歐洲<br />NA:北美洲<br />OC:大洋洲<br />SA:南美洲<br /><br />**已淘汰：** %{virt_dst_continent}。 <br />此變數已被取代，而 %{geo_continent}。 <br />雖然使用此已淘汰變數的規則仍然可以運作，您應該更新規則以使用新的變數。| N/A |
| Cookie 值 | %{cookie_Cookie} | 傳回 Cookie 字詞所識別 Cookie 索引鍵的對應值。 | 範例用法： <br />%{cookie__utma}<br /><br />範例值：<br />111662281.2.10.1222100123 |
| 國家 (要求者) | %{geo_country} | 透過國碼 (地區碼) 表示要求者的國家/地區。 <br />**已淘汰：** %{virt_dst_country}。 <br /><br />此變數已由 %{geo_country} 取代。 雖然使用此已淘汰變數的規則仍然可以運作，您應該更新規則以使用新的變數。 | US |
| 指定市場地區 (Requester) | %{geo_dma_code} |依地區碼表示要求者的媒體市場。 <br /><br />此欄位僅適用於源自美國的要求。| 745 |
| HTTP 要求方法 | %{request_method} | 表示 HTTP 要求方法。 | GET |
| HTTP 狀態碼 | %{status} | 表示回應的 HTTP 狀態碼。 | 200 |
| IP 位址 (要求者) | %{virt_dst_addr} | 表示要求者的 IP 位址。 | 192.168.1.1 |
| 緯度 (要求者) | %{geo_latitude} | 表示要求者的緯度。 | 34.0995 |
| 經度 (要求者) | %{geo_longitude} | 表示要求者的經度。 | -118.4143 |
| 都會統計資料區域 (要求者) | %{geo_metro_code} | 表示要求者的都會區域。 <br /><br />此欄位僅適用於源自美國的要求。<br />| 745 |
| 連接埠 (要求者) | %{virt_dst_port} | 表示要求者的暫時連接埠。 | 55885 |
| 郵遞區號 (要求者) | %{geo_postal_code} | 表示要求者的郵遞區號。 | 90210 |
| 找到查詢字串 | %{is_args} | 此變數的值會根據要求是否包含查詢字串而有所不同。<br /><br />- 找到查詢字串：?<br />-沒有查詢字串：NULL | ? |
| 找到查詢字串參數 | %{is_amp} | 此變數的值會根據要求是否包含至少一個查詢字串參數而有所不同。<br /><br />- 找到參數：&<br />-無參數：NULL | & |
| 查詢字串參數值 | %{arg_&lt;parameter&gt;} | 傳回 &lt;parameter&gt; 字詞所識別查詢字串參數的對應值。 | 範例用法： <br />%{arg_language}<br /><br />範例查詢字串參數： <br />?language=en<br /><br />範例值：en |
| 查詢字串值 | %{query_string} | 表示定義於要求 URL 中的完整查詢字串值。 |key1=val1&key2=val2&key3=val3 |
| 推薦者網域 | %{referring_domain} | 表示訪客來源的要求標頭中定義的網域。 | <www.google.com> |
| 地區 (要求者) | %{geo_region} | 透過英數字元縮寫表示要求者的地區 (例如州或省)。 | CA |
| 要求標頭值 | %{http_RequestHeader} | 傳回 RequestHeader 字詞所識別要求標頭的對應值。 <br /><br />如果要求標頭包含破折號 (例如 User-Agent)，則會以底線來取代 (例如 User_Agent)。| 範例用法：%{http_Connection}<br /><br />範例值：保持連線 | 
| 要求主機 | %{host} | 表示定義於要求 URL 中的主機。 | <www.mydomain.com> |
| 要求通訊協定 | %{request_protocol} | 表示要求通訊協定。 | HTTP/1.1 |
| 要求配置 | %{scheme} | 表示要求配置。 |http |
| 要求 URI (相對) | %{request_uri} | 表示定義於要求 URI 中的相對路徑 (包括查詢字串)。 | /marketing/foo.js?loggedin=true |
| 要求 URI (相對，不含查詢字串) | %{uri} | 表示被要求內容的相對路徑。 <br /><br/>重要資訊：<br />- 此相對路徑不包含查詢字串。<br />- 此相對路徑會反映 URL 重寫。 URL 將會在下列情況下被重寫：<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL 重寫功能：這項功能重寫要求 URI 中所定義的相對路徑。<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;邊緣 CNAME URL:此類型的要求會重寫為對應的 CDN URL。 |/800001/corigin/rewrittendir/foo.js |
| 要求 URI | %{request} | 描述要求。 <br />語法：&lt;HTTP 方法&gt;&lt;相對路徑&gt; &lt;HTTP 通訊協定&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| 回應標頭值 | %{resp_&lt;ResponseHeader&gt;} | 傳回 &lt;ResponseHeader&gt; 字詞所識別回應標頭的對應值。 <br /><br />如果回應標頭包含破折號 (例如 User-Agent)，則會以底線來取代 (例如 User_Agent)。 | 範例用法：%{resp_Content_Length}<br /><br />範例值：100 |

## <a name="usage"></a>使用量
下表說明指定 HTTP 變數的正確語法。


| 語法 | 範例 | 描述 |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | 使用此語法以取得對應至指定 &lt;HTTPVariable&gt; 的完整值。 |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | 使用此語法針對對應至指定 &lt;HTTPVariableDelimiter&gt; 的完整值設定大小寫。 |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | 使用針對 &lt;HTTPVariableDelimiterExpression&gt; 的規則運算式來取代、刪除或操作 HTTP 變數的值。 |

HTTP 變數名稱僅支援字母字元和底線。 系統會將不支援的字元轉換為底線。

## <a name="delimiter-reference"></a>分隔符號參考
可以在 HTTP 變數之後指定分隔符號，以達成下列任一效果：

- 轉換變數關聯值。

     範例：將整個值轉換成小寫。

- 刪除變數關聯值。

- 操作變數關聯值。

     範例：您可以使用規則運算式來變更與 HTTP 變數相關聯的值。

下表會說明這些分隔符號。

| 分隔符號 | 描述 |
| --------- | ----------- |
| := | 表示將會在變數滿足下列其中一個條件時，將預設值指派給變數： <br />- 遺漏 <br />- 設定為 NULL。 |
| :+ | 表示在變數有指派值的情況下，將預設值指派給變數。 |
| ： | 表示將會展開變數指派值的子字串。 |
| # | 表示若在變數關聯值的開頭找到此分隔符號之後所指定的模式時，應該將該模式刪除。 |
| % | 表示若在變數關聯值的尾端找到在此分隔符號之後所指定的模式時，應該將該模式刪除。 <br />只有在使用 % 符號作為分隔符號時才適用此定義。 |
| / | 分隔 HTTP 變數或模式。 |
| // | 尋找並取代指定模式的所有執行個體。 |
| /= | 尋找，複製並重寫指定模式的所有發生案例。 |
| ， | 將 HTTP 變數關聯值轉換成小寫。 |
| ^ | 將 HTTP 變數關聯值轉換成大寫。 |
| ,, | 將 HTTP 變數關聯值中指定字元的所有執行個體轉換成小寫。 |
| ^^ | 將 HTTP 變數關聯值中指定字元的所有執行個體轉換成大寫。 |

## <a name="exceptions"></a>例外狀況
下表說明系統不會將指定文字視為 HTTP 變數的情況。

| 條件 | 描述 | 範例 |
| --------- | ----------- | --------|
| 逸出 % 符號 | 百分比符號可以使用反斜線來逸出。 <br />右側的範例值將會被系統視為常值而非 HTTP 變數。| \%{host} |
| Unknown variables | An empty string is always returned for unknown variables. | %{unknown_variable} |
| Invalid characters or syntax | Variables that contain invalid characters or syntax are treated as literal values. <br /><br />Example #1: The specified value contains an invalid character (for example, -). <br /><br />Example #2: The specified value contains a double set of curly braces. <br /><br />Example #3: The specified value is missing a closing curly brace.<br /> | Example #1: %{resp_user-agent} <br /><br />Example #2: %{{host}} <br /><br />Example #3: %{host |
| Missing variable name | A NULL value is always returned when a variable is not specified. | %{} |
| Trailing characters | Characters that trail a variable are treated as literal values. <br />The sample value to the right contains a trailing curly brace that will be treated as a literal value. | %{host}} |

## Setting default header values
A default value can be assigned to a header when it meets any of the following conditions:
- Missing/unset
- Set to NULL.

The following table describes how to define a default value.

| Condition | Syntax | Example | Description |
| --------- | ------ | --------| ----------- |
| Set a header to a default value when it meets any of the following conditions: <br /><br />- Missing Header <br /><br />- Header value is set to NULL.| %{Variable:=Value} | %{http_referrer:=unspecified} | The Referrer header will only be set to *unspecified* when it is either missing or set to NULL. No action will take place if it has been set. |
| Set a header to a default value when it is missing. | %{Variable=Value} | %{http_referrer=unspecified} | The Referrer header will only be set to *unspecified* when it is missing. No action will take place if it has been set. |
| Set the header to a default value when it does not meet any of the following conditions: <br /><br />- Missing<br /><br /> - Set to NULL. | %{Variable:+Value} | %{http_referrer:+unspecified} | The Referrer header will only be set to *unspecified* when a value has been assigned to it. No action will take place if it is either missing or set to NULL. |

## Manipulating variables
Variables can be manipulated in the following ways:

- Expanding substrings
- Removing patterns

### Substring expansion
By default, a variable will expand to its full value. Use the following syntax to only expand a substring of the variable's value.

`%<Variable>:<Offset>:<Length>}`

Key information:

- The value assigned to the Offset term determines the starting character of the substring:

     - Positive: The starting character of the substring is calculated from the first character in the string.
     - Zero: The starting character of the substring is the first character in the string.
     - Negative: The starting character of the substring is calculated from the last character in the string.

- The length of the substring is determined by the *Length* term:

     - Omitted: Omitting the Length term allows the substring to include all characters between the starting character and the end of the string.
     - Positive: Determines the length of the substring from the starting character to the right.
     - Negative: Determines the length of the substring from the starting character to the left.

#### Example:

The following example relies on the following sample request URL:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

The following string demonstrates various methods for manipulating variables:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Based on the sample request URL, the above variable manipulation will produce the following value:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### Pattern removal
Text that matches a specific pattern can be removed from either the beginning or the end of a variable's value.

| Syntax | Action |
| ------ | ------ |
| %{Variable#Pattern} | Remove text when the specified pattern is found at the beginning of a variable's value. |
| %{Variable%Pattern} | Remove text when the specified pattern is found at the end of a variable's value. |

#### Example:

In this sample scenario, the *request_uri* variable is set to:

`/800001/myorigin/marketing/product.html?language=en-US`

The following table demonstrates how this syntax works.

| Sample syntax | Results | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Because the variable starts with the pattern, it was replaced. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Because the variable doesn't end with the pattern, there was no change.|

### Find and replace
The find and replace syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/Find/Replace} | Find and replace first occurrence of the specified pattern. |
| %{Variable//Find/Replace} | Find and replace all occurrences of the specified pattern. |
| %{Variable^} |Convert the entire value to uppercase. |
| %{Variable^Find} | Convert the first occurrence of the specified pattern to uppercase. |
| %{Variable,} | Convert the entire value to lowercase. |
| %{Variable,Find} | Convert the first occurrence of the specified pattern to lowercase. |

### Find and rewrite
For a variation on find and replace, use the text that matches the specified pattern when rewriting it. The find and rewrite syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Find, copy, and rewrite all occurrences of the specified pattern. |
| %{Variable/^Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the start of the variable. |
| %{Variable/$Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the end of the variable. |
| %{Variable/Find} | Find and delete all occurrences of the specified pattern. |

Key information:

- Expand text that matches the specified pattern by specifying a dollar sign followed by a whole integer (for example, $1).

- Multiple patterns can be specified. The order in which the pattern is specified determines the integer that will be assigned to it. In the following example, the first pattern matches "www.," the second pattern matches the second-level domain, and the third pattern matches the top-level domain:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- The rewritten value can consist of any combination of text and these placeholders.

    In the previous example, the hostname is rewritten to `cdn.$2.$3:80` (for example, cdn.mydomain.com:80).

- The case of a pattern placeholder (for example, $1) can be modified through the following flags:
     - U: Uppercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Lowercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- An operator must be specified before the pattern. The specified operator determines the pattern-capturing behavior:

     - `=`: Indicates that all occurrences of the specified pattern must be captured and rewritten.
     - `^`: Indicates that only text that starts with the specified pattern will be captured.
     - `$`: Indicates that only text that ends with the specified pattern will be capture.
 
- If you omit the */Rewrite* value, the text that matches the pattern is deleted.

\`{host}le: HTTP variables for Azure CDN rules engine | Microsoft Docs
description: HTTP variables allow you to retrieve HTTP request and response metadata.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''

ms.assetid: 
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus


---
# HTTP variables for Azure CDN rules engine
HTTP variables provide the means through which you can retrieve HTTP request and response metadata. This metadata can then be used to dynamically alter a request or a response. The use of HTTP variables is restricted to the following rules engine features:

- [Cache-Key Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modify Client Request Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modify Client Response Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL Redirect](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## Definitions
The following table describes the supported HTTP variables. A blank value is returned when GEO metadata (for example, postal code) is unavailable for a particular request.


| Name | Variable | Description | Sample value |
| ---- | -------- | ----------- | ------------ |
| ASN (Requester) | %{geo_asnum} | Indicates the requester's AS number. <br /><br />**Deprecated:** %{virt_dst_asnum}. <br />This variable has been deprecated in favor of %{geo_asnum}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | AS15133 |
| City (Requester) | %{geo_city} | Indicates the requester's city. | Los Angeles |
| Continent (Requester) | %{geo_continent} | Indicates the requester's continent through its abbreviation. <br />Valid values are: <br />AF: Africa<br />AS: Asia<br />EU: Europe<br />NA: North America<br />OC: Oceania<br />SA: South America<br /><br />**Deprecated:** %{virt_dst_continent}. <br />This variable has been deprecated in favor of %{geo_continent}. <br />Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable.| N/A |
| Cookie Value | %{cookie_Cookie} | Returns the value corresponding to the cookie key identified by the Cookie term. | Sample Usage: <br />%{cookie__utma}<br /><br />Sample Value:<br />111662281.2.10.1222100123 |
| Country (Requester) | %{geo_country} | Indicates the requester's country of origin through its country code. <br />**Deprecated:** %{virt_dst_country}. <br /><br />This variable has been deprecated in favor of %{geo_country}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | US |
| Designated Market Area (Requester) | %{geo_dma_code} |Indicates the requester's media market by its region code. <br /><br />This field is only applicable to requests that originate from the United States.| 745 |
| HTTP Request Method | %{request_method} | Indicates the HTTP request method. | GET |
| HTTP Status Code | %{status} | Indicates the HTTP status code for the response. | 200 |
| IP Address (Requester) | %{virt_dst_addr} | Indicates the requester's IP address. | 192.168.1.1 |
| Latitude (Requester) | %{geo_latitude} | Indicates the requester's latitude. | 34.0995 |
| Longitude (Requester) | %{geo_longitude} | Indicates the requester's longitude. | -118.4143 |
| Metropolitan Statistical Area (Requester) | %{geo_metro_code} | Indicates the requester's metropolitan area. <br /><br />This field is only applicable to requests that originate from the United States.<br />| 745 |
| Port (Requester) | %{virt_dst_port} | Indicates the requester's ephemeral port. | 55885 |
| Postal Code (Requester) | %{geo_postal_code} | Indicates the requester's postal code. | 90210 |
| Query String Found | %{is_args} | The value for this variable varies according to whether the request contains a query string.<br /><br />- Query String Found: ?<br />- No Query String: NULL | ? |
| Query String Parameter Found | %{is_amp} | The value for this variable varies according to whether the request contains at least one query string parameter.<br /><br />- Parameter Found: &<br />- No Parameters: NULL | & |
| Query String Parameter Value | %{arg_&lt;parameter&gt;} | Returns the value corresponding to the query string parameter identified by the &lt;parameter&gt; term. | Sample Usage: <br />%{arg_language}<br /><br />Sample Query String Parameter: <br />?language=en<br /><br />Sample Value: en |
| Query String Value | %{query_string} | Indicates the entire query string value defined in the request URL. |key1=val1&key2=val2&key3=val3 |
| Referrer Domain | %{referring_domain} | Indicates the domain defined in the Referrer request header. | <www.google.com> |
| Region (Requester) | %{geo_region} | Indicates the requester's region (for example, state or province) through its alphanumeric abbreviation. | CA |
| Request Header Value | %{http_RequestHeader} | Returns the value corresponding to the request header identified by the RequestHeader term. <br /><br />If the name of the request header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent).| Sample Usage: %{http_Connection}<br /><br />Sample Value: Keep-Alive | 
| Request Host | %{host} | Indicates the host defined in the request URL. | <www.mydomain.com> |
| Request Protocol | %{request_protocol} | Indicates the request protocol. | HTTP/1.1 |
| Request Scheme | %{scheme} | Indicates the request scheme. |http |
| Request URI (Relative) | %{request_uri} | Indicates the relative path, including the query string, defined in the request URI. | /marketing/foo.js?loggedin=true |
| Request URI (Relative without query string) | %{uri} | Indicates the relative path to the requested content. <br /><br/>Key information:<br />- This relative path excludes the query string.<br />- This relative path reflects URL rewrites. A URL will be rewritten under the following conditions:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL Rewrite Feature: This feature rewrites the relative path defined in the request URI.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge CNAME URL: This type of request is rewritten to the corresponding CDN URL. |/800001/corigin/rewrittendir/foo.js |
| Request URI | %{request} | Describes the request. <br />Syntax: &lt;HTTP method&gt; &lt;relative path&gt; &lt;HTTP protocol&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Response Header Value | %{resp_&lt;ResponseHeader&gt;} | Returns the value corresponding to the response header identified by the &lt;ResponseHeader&gt; term. <br /><br />If the name of the response header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent). | Sample Usage: %{resp_Content_Length}<br /><br />Sample Value: 100 |

## Usage
The following table describes the proper syntax for specifying an HTTP variable.


| Syntax | Example | Description |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Use this syntax to get the entire value corresponding to the specified &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Use this syntax to set the case for the entire value corresponding to the specified  &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Use a regular expression for &lt;HTTPVariableDelimiterExpression&gt; to replace, delete, or manipulate an HTTP variable's value. |

HTTP variable names only support alphabetic characters and underscores. Convert unsupported characters to underscores.

## Delimiter reference
A delimiter can be specified after an HTTP variable to achieve any of the following effects:

- Transform the value associated with the variable.

     Example: Convert the entire value to lowercase.

- Delete the value associated with the variable.

- Manipulate the value associated with the variable.

     Example: Use regular expressions to change the value associated with the HTTP variable.

The delimiters are described in the following table.

| Delimiter | Description |
| --------- | ----------- |
| := | Indicates that a default value will be assigned to the variable when it is either: <br />- Missing <br />- Set to NULL. |
| :+ | Indicates that a default value will be assigned to the variable when a value has been assigned to it. |
| : | Indicates that a substring of the value assigned to the variable will be expanded. |
| # | Indicates that the pattern specified after this delimiter should be deleted when it is found at the beginning of the value associated with the variable. |
| % | Indicates that the pattern specified after this delimiter should be deleted when it is found at the end of the value associated with the variable. <br />This definition is only applicable when the % symbol is used as a delimiter. |
| / | Delimits an HTTP variable or a pattern. |
| // | Find and replace all instances of the specified pattern. |
| /= | Find, copy, and rewrite all occurrences of the specified pattern. |
| , | Convert the value associated with the HTTP variable to lowercase. |
| ^ | Convert the value associated with the HTTP variable to uppercase. |
| ,, | Convert all instances of the specified character in the value associated with the HTTP variable to lowercase. |
| ^^ | Convert all instances of the specified character in the value associated with the HTTP variable to uppercase. |

## Exceptions
The following table describes circumstances under which the specified text isn't treated as an HTTP variable.

| Condition | Description | Example |
| --------- | ----------- | --------|
| Escaping % symbol | The percentage symbol can be escaped through the use of a backslash. <br />The sample value to the right will be treated as a literal value and not as an HTTP variable.| \%{host} |
| 未知的變數 | 系統針對未知的變數一律會傳回空字串。 | %{unknown_variable} |
| 無效的字元或語法 | 包含無效的字元或語法的變數會被系統視為常值。 <br /><br />範例 #1:指定的值包含無效的字元 (例如，-)。 <br /><br />範例 #2:指定的值包含一組雙大括號。 <br /><br />範例 #3:指定的值遺漏右大括號。<br /> | 範例 #1：%{resp_user-agent} <br /><br />範例 #2：%{{host}} <br /><br />範例 #3：%{host |
| 遺漏變數名稱 | 在沒有指定變數的情況下，一律會傳回 NULL 值。 | %{} |
| 尾端字元 | 位於變數尾端的字元一律會被視為常值。 <br />右側的範例值包含尾端的大括號，系統將會把它視為常值。 | %{host}} |

## <a name="setting-default-header-values"></a>設定預設標頭值
當標頭符合下列任一條件時，便可以將預設值指派給標頭：
- 遺漏/未設定
- 設定為 NULL。

下表說明如何定義預設值。

| 條件 | 語法 | 範例 | 描述 |
| --------- | ------ | --------| ----------- |
| 在標頭符合下列任一條件時，將標頭設定為預設值： <br /><br />- 遺漏標頭 <br /><br />- 標頭值已設為 NULL。| %{Variable:=Value} | %{http_referrer:=unspecified} | 訪客來源的標頭將只會設定為*未指定*時遺失或設定為 NULL。 在已設定的情況下，系統將不會採取任何動作。 |
| 在遺漏標頭的情況下，將標頭設定為預設值。 | %{Variable=Value} | %{http_referrer=unspecified} | 訪客來源的標頭將只會設定為*未指定*時遺失。 在已設定的情況下，系統將不會採取任何動作。 |
| 在標頭不符合下列任一條件時，將標頭設定為預設值： <br /><br />- 遺漏<br /><br /> - 設定為 NULL。 | %{Variable:+Value} | %{http_referrer:+unspecified} | 訪客來源的標頭將只會設定為*未指定*進行指定的值。 在遺漏或設定為 NULL 的情況下，系統將不會採取任何動作。 |

## <a name="manipulating-variables"></a>操作變數
變數可以透過下列方式進行操作：

- 展開子字串
- 移除模式

### <a name="substring-expansion"></a>子字串展開
根據預設，變數會展開成其完整的值。 使用下列語法來僅展開變數值的子字串。

`%<Variable>:<Offset>:<Length>}`

重要資訊：

- 指派至 Offset 字詞的值會決定子字串的起始字元：

     - 正值：子字串開始的字元會計算從字串中的第一個字元。
     - 零：子字串開始的字元是在字串中的第一個字元。
     - 負數︰子字串開始的字元會計算從字串中的最後一個字元。

- 子字串的長度是由 *Length*字詞所決定：

     - 省略：省略長度一詞，可讓要起始的字元和字串的結尾之間的所有字元的子字串。
     - 正值：判斷起始字元右邊的子字串的長度。
     - 負數︰判斷左側的起始字元的子字串的長度。

#### <a name="example"></a>範例：

下列範例需仰賴下列範例要求 URL：

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

下列字串示範操作變數的各種不同方法：

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

根據範例要求 URL，上述變數操作將會產生下列值：

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>模式移除
可以從變數值的開頭或結尾移除符合指定模式的文字。

| 語法 | 動作 |
| ------ | ------ |
| %{Variable#Pattern} | 在於變數值的開頭找到指定模式時移除文字。 |
| %{Variable%Pattern} | 在於變數值的結尾找到指定模式時移除文字。 |

#### <a name="example"></a>範例：

在此範例案例中，*request_uri* 變數是設為：

`/800001/myorigin/marketing/product.html?language=en-US`

下表示範此語法的運作方式。

| 範例語法 | 結果 | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | 由於變數是以該模式作為開頭，因此會被取代。 |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | 由於變數沒有以該模式作為結尾，因此沒有任何變更。|

### <a name="find-and-replace"></a>尋找並取代
下表描述尋找並取代的語法。

| 語法 | 動作 |
| ------ | ------ |
| %{Variable/Find/Replace} | 尋找並取代指定模式的首個發生案例。 |
| %{Variable//Find/Replace} | 尋找並取代指定模式的所有發生案例。 |
| %{Variable^} |將整個值轉換成大寫。 |
| %{Variable^Find} | 將指定模式的首個發生案例轉換成大寫。 |
| %{Variable,} | 將整個值轉換成小寫。 |
| %{Variable,Find} | 將指定模式的首個發生案例轉換成小寫。 |

### <a name="find-and-rewrite"></a>尋找並重寫
尋找並取代的變化型態，會在重寫時使用符合指定模式的文字。 下表描述尋找並重寫的語法。

| 語法 | 動作 |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | 尋找，複製並重寫指定模式的所有發生案例。 |
| %{Variable/^Find/Rewrite} | 在指定模式於變數開頭發生時，尋找，複製並重寫該指定模式。 |
| %{Variable/$Find/Rewrite} | 在指定模式於變數結尾發生時，尋找，複製並重寫該指定模式。 |
| %{Variable/Find} | 尋找並刪除指定模式的所有發生案例。 |

重要資訊：

- 透過指定後面接著整數的貨幣符號 (例如 $1) 來展開符合指定模式的文字。

- 可以指定多個模式。 指定模式的順序會決定指派給模式的整數。 在下列範例中，第一個模式符合 "www."，第二個模式符合第二層網域，而第三個模式則符合最上層網域：

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- 重寫的值可以包含任意組合的文字和這些預留位置。

    在上述範例中，主機名稱會重寫為 `cdn.$2.$3:80` (例如，cdn.mydomain.com:80)。

- 模式預留位置 (例如 $1) 的大小寫可以透過下列旗標來修改：
     - U:大寫的展開的值。

         範例語法：

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L:小寫的展開的值。

         範例語法：

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- 必須在模式之前指定運算子。 指定的運算子會決定模式擷取行為：

     - `=`:表示必須擷取指定之模式的所有項目，而且重寫。
     - `^`:表示將會擷取指定之模式的開頭的文字。
     - `$`:指出指定之模式的結尾的文字會擷取。
 
- 如果您省略 */Rewrite* 值，系統將會刪除符合模式的文字。


