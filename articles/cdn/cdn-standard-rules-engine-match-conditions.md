---
title: 來自 Microsoft 標準規則引擎比對條件的 Azure CDN |Microsoft Docs
description: 來自 Microsoft 標準規則引擎比對條件的 Azure 內容傳遞網路參考檔。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: a72452d37b152a9463a5aee0e199fd42ea852236
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615958"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-match-conditions"></a>來自 Microsoft 標準規則引擎比對條件的 Azure CDN

本文會針對來自 Microsoft [Standard 規則引擎](cdn-standard-rules-engine.md)的 Azure 內容傳遞網路（CDN），列出可用比對條件的詳細說明。

規則的第一個部分是一組比對條件。 每個規則最多可以有4個相符的條件。 比對條件會識別特定類型的要求，而規則中定義的動作將會在其中執行。 如果您使用多個符合條件，則會使用和邏輯將它們群組在一起。

例如，您可以使用比對條件：

- 篩選從特定 IP 位址或國家/地區產生的要求。
- 依標頭資訊篩選要求。
- 從行動裝置或桌上型電腦篩選要求。

## <a name="match-conditions"></a>符合條件

下列比對條件可供使用。 

### <a name="device-type"></a>裝置類型 

「裝置類型」比對條件會根據行動裝置或桌面裝置的屬性來識別提出的要求。  

**必要欄位**

運算子 | 支援的值
---------|----------------
Equals，不等於 | 行動裝置、桌上型電腦


### <a name="http-version"></a>HTTP 版本

HTTP 版本比對條件會根據要求抵達的 HTTP 版本來識別要求。

**必要欄位**

運算子 | 支援的值
---------|----------------
Equals，不等於 | 2.0、1.1、1.0、0.9、全部


### <a name="request-cookies"></a>要求 cookie

「要求 cookie」比對條件會根據傳入要求中的 cookie 資訊來識別要求。

**必要欄位**

Cookie 名稱 | 運算子 | Cookie 值 | 大小寫轉換
------------|----------|--------------|---------------
字串 | [標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

重要資訊
- 指定 cookie 名稱時，不支援萬用字元值，包括星號（*），只有精確的 cookie 名稱符合才有資格進行比較。
- 此比對條件的每個執行個體只能指定一個 Cookie 名稱。
- Cookie 名稱比較不區分大小寫。
- 使用單一空格來分隔每個 Cookie 值，藉以指定多個 Cookie 值。 
- Cookie 值可以利用萬用字元值。
- 如果尚未指定萬用字元值，則只有完全相符的會符合此比對條件。 例如，指定 "Value" 會比對 "Value"，但不符合 "Value1"。 

### <a name="post-argument"></a>Post 引數

**必要欄位**

引數名稱 | 運算子 | 引數值 | 大小寫轉換
--------------|----------|----------------|---------------
字串 | [標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

### <a name="query-string"></a>查詢字串

查詢字串比對條件會識別包含指定之查詢字串參數的要求。 這個參數會設定為符合指定之模式的值。 要求 URL 中的查詢字串參數 (例如，parameter=value) 會決定是否符合此條件。 此比對條件會依查詢字串參數的名稱來識別查詢字串參數，並接受一個或多個參數值的值。

**必要欄位**

運算子 | 查詢字串 | 大小寫轉換
---------|--------------|---------------
[標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

### <a name="remote-address"></a>遠端位址

「遠端位址」比對條件會根據要求者的位置或 IP 位址來識別要求。

**必要欄位**

運算子 | 支援的值
---------|-----------------
任意 | N/A
地理位置比對 | 國碼
IP 相符 | IP 位址（分隔空格）
不是任何 | N/A
不符合地理位置 | 國碼
不符合 IP | IP 位址（分隔空格）

重要資訊：

- 使用 CIDR 標記法。
- 使用單一空格來分隔每個 IP 位址及/或 IP 位址區塊，藉以指定多個 IP 位址及/或 IP 位址區塊。 例如：
  - **IPv4 範例**：1.2.3.4 10.20.30.40 會比對來自位址 1.2.3.4 或 10.20.30.40 的任何要求。
  - **IPv6 範例**：1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 會比對來自位址 1:2:3:4:5:6:7:8 或 10:20:30:40:50:60:70:80 的任何要求。
- IP 位址區塊的語法是基底 IP 位址，後面接著正斜線和首碼大小。 例如：
  - **IPv4 範例**：5.5.5.64/26 會比對來自 5.5.5.64 到 5.5.5.127 的任何要求。
  - **IPv6 範例**：1:2:3:/48 會比對來自位址 1:2:3:0:0:0:0:0 到 1:2:3:ffff:ffff:ffff:ffff:ffff 的任何要求。

### <a name="request-body"></a>要求本文

**必要欄位**

運算子 | 要求本文 | 大小寫轉換
---------|--------------|---------------
[標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

### <a name="request-header"></a>要求標頭

**必要欄位**
標頭名稱 | 運算子 | 標頭值 | 大小寫轉換
------------|----------|--------------|---------------
字串 | [標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

### <a name="request-method"></a>要求方法

**必要欄位**

運算子 | 支援的值
---------|----------------
Equals，不等於 | GET、POST、PUT、DELETE、HEAD、OPTIONS、TRACE

重要資訊：

- 只有 GET 要求方法可以在 CDN 上產生快取的內容。 所有其他要求方法則是透過網路進行 Proxy 處理。 

### <a name="request-protocol"></a>要求通訊協定

**必要欄位**

運算子 | 支援的值
---------|----------------
Equals，不等於 | HTTP、HTTPS

### <a name="request-url"></a>要求 URL

**必要欄位**

運算子 | 要求 URL | 大小寫轉換
---------|-------------|---------------
[標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

重要資訊：

- 輸入要求 URL 時，請確定您包含通訊協定資訊。 例如 "https://www。[yourdomain] .com "

### <a name="url-file-extension"></a>URL 副檔名

**必要欄位**

運算子 | 分機 | 大小寫轉換
---------|-----------|---------------
[標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

重要資訊：

- 對於延伸模組，請勿包含前置句點;例如，使用 html，而不是 .html。

### <a name="url-file-name"></a>URL 檔案名

**必要欄位**

運算子 | 檔案名稱 | 大小寫轉換
---------|-----------|---------------
[標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

重要資訊：

- 若要指定多個檔案名，請以單一空格分隔每個檔案名。 

### <a name="url-path"></a>URL 路徑

**必要欄位**

運算子 | 值 | 大小寫轉換
---------|-------|---------------
[標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

重要資訊：

- 檔案名值可以利用萬用字元值。 例如，每個檔案名稱模式可包含一個或多個星號 (*)，其中每個星號都符合一個或多個字元序列。

## <a name="reference-for-rules-engine-match-conditions"></a>規則引擎比對條件的參考

### <a name="standard-operator-list"></a>標準操作員清單

對於包含標準運算子清單的規則，下列運算子是有效的：

- 任意
- Equals 
- Contains 
- 開頭為 
- Ends with 
- 小於
- 小於或等於
- 大於
- 大於或等於
- 不是任何
- 不包含
- 開頭不是 
- 結尾不是 
- 不小於
- 不小於或等於
- 不大於
- 不大於或等於

對於數值運算子（例如「小於」或「大於或等於」），所使用的比較會以長度為基礎。 在此情況下，比對條件中的值應該是整數，等於您想要比較的長度。 

---

[回到頁首](#match-conditions)

</br>

## <a name="next-steps"></a>後續步驟

- [Azure 內容傳遞網路概觀](cdn-overview.md)
- [規則引擎參考](cdn-standard-rules-engine-reference.md)
- [規則引擎動作](cdn-standard-rules-engine-actions.md)
- [使用標準規則引擎強制執行 HTTPS](cdn-standard-rules-engine.md)
