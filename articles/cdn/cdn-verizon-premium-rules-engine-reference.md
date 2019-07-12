---
title: Azure CDN 規則引擎參考 | Microsoft Docs
description: Azure CDN 規則引擎比對條件和功能的參考文件。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 5fc611af75a7f733576f9343a4375fb56cacc030
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593154"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>來自 Verizon Premium 規則引擎參考的 azure CDN

本文會針對 Azure 內容傳遞網路 (CDN) [規則引擎](cdn-verizon-premium-rules-engine.md)列出可用比對條件和功能的詳細描述。

此規則引擎是設計為特定類型要求如何由 CDN 處理的最終授權。

**常見用途**：

- 覆寫或定義自訂快取原則。
- 保護或拒絕機密內容的要求。
- 將要求重新導向。
- 儲存自訂記錄檔資料。

## <a name="terminology"></a>術語

規則是透過使用[**條件運算式**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)、[**比對條件**](cdn-verizon-premium-rules-engine-reference-match-conditions.md)和[**功能**](cdn-verizon-premium-rules-engine-reference-features.md)來定義。 這些元素會在下圖中反白顯示：

 ![CDN 相符條件](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>語法

處理特殊字元的方式會根據比對條件或功能如何處理文字值而異。 比對條件或功能可能會以下列其中一種方式解譯文字︰

1. [**常值**](#literal-values)
2. [**萬用字元值**](#wildcard-values)
3. [**規則運算式**](#regular-expressions)

### <a name="literal-values"></a>常值

解譯為常值的文字會將所有特殊字元 (除了 % 符號之外)，視為必須符合值的一部分。 換句話說，設定為 `\'*'\` 的常值比對條件，只有在找到確切值 (亦即，`\'*'\`) 時獲得滿足。

百分比符號是用來表示 URL 編碼 (例如 `%20`)。

### <a name="wildcard-values"></a>萬用字元值

解譯為萬用字元值的文字會將額外意義指派給特殊字元。 下表描述如何解譯下列字元集：

Character | 描述
----------|------------
\ | 反斜線是用來逸出此資料表中指定的任何字元。 必須在逸出特殊字元之前指定反斜線。<br/>例如，下列語法逸出星號︰`\*`
% | 百分比符號是用來表示 URL 編碼 (例如 `%20`)。
\* | 星號為萬用字元，表示一或多個字元。
空白字元 | 空白字元，表示比對條件可能藉由指定值或模式獲得滿足。
'value' | 單引號不具有特殊意義。 不過，單引號組是用來表示值應視為常值。 使用方式如下：<br><br/>- 它可讓比對條件在指定值符合比較值的任何部分時獲得滿足。  例如，`'ma'` 會比對下列任何字串︰ <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- 它可讓特殊字元指定為常值字元。 例如，您可以指定常值空白字元，方法是在單引號組內括住空白字元 (亦即，`' '` 或 `'sample value'`)。<br/>- 它可以指定空白值。 藉由指定單引號組來指定空白值 (亦即，'')。<br /><br/>**重要事項：**<br/>- 如果指定值不包含萬用字元，則它會自動被視為常值，這表示不一定要指定一組單引號。<br/>- 如果反斜線不會逸出此資料表中的另一個字元，它會在於一組單引號內指定時遭到忽略。<br/>- 另一種將特殊字元指定為常值字元的方式是使用反斜線逸出它 (亦即，`\`)。

### <a name="regular-expressions"></a>規則運算式

規則運算式會定義要在文字值中搜尋的模式。 規則運算式標記法會將特定意義定義為各種符號。 下表指出支援規則運算式的比對條件和功能如何處理特殊字元。

特殊字元 | 描述
------------------|------------
\ | 反斜線會逸出緊接在後的字元，這會導致該字元被視為常值，而非採用它的規則運算式的意義。 例如，下列語法逸出星號︰`\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | An asterisk allows the preceding character to be matched zero or more times.
Space | A space character is typically treated as a literal character.
'value' | Single quotes are treated as literal characters. A set of single quotes does not have special meaning.

## Next steps

- [Rules engine match conditions](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)
- [Azure CDN overview](cdn-overview.md百分比符號的意義取決於其使用方式。 Docs
descr`%{HTTPVariable}`:此語法會識別 HTTP 變數。match`%{HTTPVariable%Pattern}`:此語法會使用百分比符號來識別 HTTP 變數，並做為分隔符號。1/2019`\``: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\`20`).f the available match conditions and features for the Azure Content Delivery Network (CDN) [rules engine](cdn-verizon-premium-rules-engine.md).

The rules engine is designed to be the final authority on how specific types of requests are processed by the CDN.

**Common uses**:

- Override or define a custom cache policy.
- Secure or deny requests for sensitive content.
- Redirect requests.
- Store custom log data.

## Terminology

A rule is defined through the use of [**conditional expressions**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**match conditions**](cdn-verizon-premium-rules-engine-reference-match-conditions.md), and [**features**](cdn-verizon-premium-rules-engine-reference-features.md). These elements are highlighted in the following illustration:

 ![CDN match condition](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## Syntax

The manner in which special characters are treated varies according to how a match condition or feature handles text values. A match condition or feature may interpret text in one of the following ways:

1. [**Literal values**](#literal-values)
2. [**Wildcard values**](#wildcard-values)
3. [**Regular expressions**](#regular-expressions)

### Literal values

Text that is interpreted as a literal value treats all special characters, with the exception of the % symbol, as a part of the value that must be matched. In other words, a literal match condition set to `\'*'\` is only satisfied when that exact value (that is, `\'*'\`) is found.

A percentage symbol is used to indicate URL encoding (for example, `%20`).

### Wildcard values

Text that is interpreted as a wildcard value assigns additional meaning to special characters. The following table describes how the following set of characters is interpreted:

Character | Description
----------|------------
\ | A backslash is used to escape any of the characters specified in this table. A backslash must be specified directly before the special character that should be escaped.<br/>For example, the following syntax escapes an asterisk: `\*`
% | A percentage symbol is used to indicate URL encoding (for example, `%20`).
\* | An asterisk is a wildcard that represents one or more characters.
Space | A space character indicates that a match condition may be satisfied by either of the specified values or patterns.
'value' | A single quote does not have special meaning. However, a set of single quotes is used to indicate that a value should be treated as a literal value. It can be used in the following ways:<br><br/>- It allows a match condition to be satisfied whenever the specified value matches any portion of the comparison value.  For example, `'ma'` would match any of the following strings: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- It allows a special character to be specified as a literal character. For example, you may specify a literal space character by enclosing a space character within a set of single quotes (that is, `' '` or `'sample value'`).<br/>- It allows a blank value to be specified. Specify a blank value by specifying a set of single quotes (that is, '').<br /><br/>**Important:**<br/>- If the specified value does not contain a wildcard, then it is automatically considered a literal value, which means that it is not necessary to specify a set of single quotes.<br/>- If a backslash does not escape another character in this table, it is ignored when it is specified within a set of single quotes.<br/>- Another way to specify a special character as a literal character is to escape it using a backslash (that is, `\`).

### Regular expressions

Regular expressions define a pattern that is searched for within a text value. Regular expression notation defines specific meanings to a variety of symbols. The following table indicates how special characters are treated by match conditions and features that support regular expressions.

Special Character | Description
------------------|------------
\ | A backslash escapes the character the follows it, which causes that character to be treated as a literal value instead of taking on its regular expression meaning. For example, the following syntax escapes an asterisk: `\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | 星號可讓前置字元比對零或多次。
空白字元 | 空白字元通常會被視為常值字元。
'value' | 單引號會被視為常值字元。 單引號組不具有特殊意義。

## <a name="next-steps"></a>後續步驟

- [規則引擎比對條件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [規則引擎條件運算式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [規則引擎功能](cdn-verizon-premium-rules-engine-reference-features.md)
- [使用規則引擎覆寫 HTTP 行為](cdn-verizon-premium-rules-engine.md)
- [Azure CDN 概觀](cdn-overview.md)