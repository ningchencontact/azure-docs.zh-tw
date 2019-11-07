---
title: Azure CDN 標準規則引擎參考 |Microsoft Docs
description: Azure CDN 標準規則引擎比對條件和動作的參考檔。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 6fb7e704f3d33cff8c29386b8aba9d8289037cbb
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615932"
---
# <a name="azure-cdn-from-microsoft-rules-engine-reference"></a>來自 Microsoft 的 Azure CDN 規則引擎參考

本文會針對 Azure 內容傳遞網路（CDN）[標準規則引擎](cdn-standard-rules-engine.md)，列出可用比對條件和功能的詳細說明。

此規則引擎是設計為特定類型要求如何由 CDN 處理的最終授權。

**常見用途**：

- 覆寫或定義自訂快取原則。
- 將要求重新導向。
- 修改 HTTP 要求和回應標頭

## <a name="terminology"></a>術語

規則是透過使用[**match 條件**](cdn-standard-rules-engine-match-conditions.md)和[**動作**](cdn-standard-rules-engine-actions.md)來定義。 這些元素會在下圖中反白顯示：

 ![CDN 規則結構](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

每個規則最多可以有4個符合條件和3個動作。 每個 CDN 端點最多可有5個規則。 此外，預設會有一個規則，稱為**全域規則**。 這是不含符合條件的規則，其中定義的動作一律會觸發。 此規則包含在目前的5個規則限制中。

## <a name="syntax"></a>語法

處理特殊字元的方式會根據比對條件或 actopm 處理文字值的方式而有所不同。 比對條件或功能可能會以下列其中一種方式解譯文字︰

1. [**常值**](#literal-values)
2. [**萬用字元值**](#wildcard-values)


### <a name="literal-values"></a>常值

解譯為常值的文字會將所有特殊字元 (除了 % 符號之外)，視為必須符合值的一部分。 換句話說，設定為 `\'*'\` 的常值比對條件，只有在找到確切值 (亦即，`\'*'\`) 時獲得滿足。

百分比符號是用來表示 URL 編碼（例如，`%20`）。

### <a name="wildcard-values"></a>萬用字元值

解譯為萬用字元值的文字會將額外意義指派給特殊字元。 下表描述如何解譯下列字元集：

Character | 說明
----------|------------
\ | 反斜線是用來逸出此資料表中指定的任何字元。 必須在逸出特殊字元之前指定反斜線。<br/>例如，下列語法逸出星號︰`\*`
% | 百分比符號是用來表示 URL 編碼（例如，`%20`）。
\* | 星號為萬用字元，表示一或多個字元。
外太空 | 空白字元，表示比對條件可能藉由指定值或模式獲得滿足。
'value' | 單引號不具有特殊意義。 不過，單引號組是用來表示值應視為常值。 使用方式如下：<br><br/>- 它可讓比對條件在指定值符合比較值的任何部分時獲得滿足。  例如，`'ma'` 會比對下列任何字串︰ <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- 它可讓特殊字元指定為常值字元。 例如，您可以指定常值空白字元，方法是在單引號組內括住空白字元 (亦即，`' '` 或 `'sample value'`)。<br/>- 它可以指定空白值。 藉由指定單引號組來指定空白值 (亦即，'')。<br /><br/>**重要事項：**<br/>- 如果指定值不包含萬用字元，則它會自動被視為常值，這表示不一定要指定一組單引號。<br/>- 如果反斜線不會逸出此資料表中的另一個字元，它會在於一組單引號內指定時遭到忽略。<br/>- 另一種將特殊字元指定為常值字元的方式是使用反斜線逸出它 (亦即，`\`)。

## <a name="next-steps"></a>後續步驟

- [標準規則引擎比對條件](cdn-standard-rules-engine-match-conditions.md)
- [標準規則引擎動作](cdn-standard-rules-engine-actions.md)
- [使用標準規則引擎強制執行 HTTPS](cdn-standard-rules-engine.md)
- [Azure CDN 概觀](cdn-overview.md)
