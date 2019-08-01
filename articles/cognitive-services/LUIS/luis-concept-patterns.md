---
title: 模式協助預測-LUIS
titleSuffix: Azure Cognitive Services
description: 模式可讓您取得更精確的意圖，而不需提供更多的語句。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: bad3bdc2b4508c082ca50647d5de5e7265c763a1
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639188"
---
# <a name="patterns-improve-prediction-accuracy"></a>模式可改善預測精確度
模式設計用來改善數個語句非常類似時的精確度。  模式可讓您取得更精確的意圖，而不需提供更多的語句。 

## <a name="patterns-solve-low-intent-confidence"></a>模式可解決低意圖信賴度
請考慮使用人力資源應用程式，該應用程式會報告與員工相關的組織圖。 若指定員工的名稱和關係，LUIS 就會傳回涉及的員工。 請考慮使用員工 Tom，其經理名字為 Alice，且其下小組名字為：Michael、Rebecca 和 Carl。

![組織圖的影像](./media/luis-concept-patterns/org-chart.png)

|表達方式|預測的意圖|意圖分數|
|--|--|--|
|Who is Tom's subordinate?|GetOrgChart|.30|
|Who is the subordinate of Tom?|GetOrgChart|.30|

如果應用程式含有的 10 到 20 個語句具有不同的句子長度、不同的字組順序，甚至不同的字組 ("subordinate"、"manage"、"report" 的同義字)，LUIS 可能會傳回很低的信賴分數。 建立模式以協助 LUIS 瞭解文字順序的重要性。 

模式可解決下列狀況： 

* 意圖分數偏低
* 正確的意圖不是最高分, 但太接近最高分。 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>模式不是意圖保證
模式會混合使用多個預測技術。 在模式中設定範本語句的意圖並不是意圖預測的保證，但卻是很強的示意訊號。 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>模式不會改善機器學習的實體偵測

模式主要是用來協助預測意圖和角色。 模式。任何實體都是用來將自由格式的實體解壓縮。 當模式使用實體時, 模式不會協助偵測機器學習的實體。  

如果將多個語句摺疊成單一模式，則不要期望看到改善的實體預測。 若要引發簡單實體，您需要新增語句或使用清單實體，否則您的模式不會引發。

## <a name="patterns-use-entity-roles"></a>模式可使用實體角色
如果模式中有兩個以上的實體與內容相關，則模式會使用實體[角色](luis-concept-roles.md)來擷取關於實體的內容資訊。  

## <a name="prediction-scores-with-and-without-patterns"></a>使用和不使用模式的預測分數
假設具有足夠的範例語句，LUIS 就能夠在不使用模式的情況下提高預測信賴度。 模式可提高信賴分數，而不需要提供許多語句。  

## <a name="pattern-matching"></a>模式比對
模式比對會先偵測模式內的實體，然後驗證其餘字組和模式的字組順序。 模式中必須要有實體，才能比對模式。 模式會套用到權杖層級，不是字元層級。 

## <a name="pattern-syntax"></a>模式語法
模式語法是語句的範本。 此範本應該包含您要比對的字組和實體，以及您要忽略的字組和標點符號。 它**不是**一個規則運算式。 

模式中的實體是用大括弧 `{}` 括住。 模式可以包含實體，以及具有角色的實體。 [Pattern。 any](luis-concept-entity-types.md#patternany-entity)是僅用於模式的實體。 

模式語法支援下列語法:

|函數|語法|嵌套層級|範例|
|--|--|--|--|
|實體| {}-大括弧|2|其中的格式為 {entity-name}？|
|選擇性|[]-方括弧<BR><BR>任何選擇性和群組組合的嵌套層級上有3個限制 |2|問號是選擇性的 [？]|
|群組|()-括弧|2|為 (a \| b)|
|或| \|-分隔號 (管線)<br><br>在一個群組中的分隔號 (或) 上有2個限制 |-|其中的格式為 ({格式名稱-簡短} &#x7c; {表單名稱-long} &#x7c; {表單編號})| 
|語句的開始和/或結束|^-插入號|-|^ 開始語句<br>語句已完成 ^<br>具有 {number} 個實體 ^ 的整個語句之 ^ strict 常值比對|

## <a name="nesting-syntax-in-patterns"></a>在模式中嵌套語法

**選擇性**的語法 (以方括弧括住) 可以嵌套兩個層級。 例如： `[[this]is] a new form` 。 這個範例允許下列語句: 

|Nested 選擇性語句範例|說明|
|--|--|
|這是新的表單|符合模式中的所有文字|
|是新的表單|符合模式中的外部選擇性單字和非選擇性單字|
|新表單|僅符合必要的單字|

使用括弧的**群組**語法可以嵌套兩個層級。 例如： `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )` 。 這項功能可讓三個實體中的任何一個相符。 

如果 Entity1 是具有「來源」 (西雅圖) 和「目的地」 (Cairo) 等角色的位置, 而「實體2」是來自清單實體 (RedWest) 的已知建築物名稱, 則下列語句會對應到此模式:

|嵌套群組語句範例|說明|
|--|--|
|RedWest-C|符合外部群組實體|
|Seattle|符合其中一個內部群組實體|
|Cairo|符合其中一個內部群組實體|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>使用選擇性語法為群組嵌套限制

使用**選擇性**語法**分組**的組合具有3個嵌套層級的限制。

|已允許|範例|
|--|--|
|是|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|否|( [ ( [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>使用 or-ing 語法來嵌套群組的限制

**群組**與**或-ing**語法的組合具有2個分隔號的限制。

|已允許|範例|
|--|--|
|是|(test1 &#x7c; test2 &#x7c; (test3 &#x7c; test4))|
|否|(test1 &#x7c; test2 &#x7c; test3 &#x7c; (test4 &#x7c; test5)) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>將實體新增至模式範本的語法
若要將實體新增至模式範本，請用大括弧括住實體名稱，例如 `Who does {Employee} manage?`。 

|模式與實體|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>將實體和角色新增至模式範本的語法
實體角色以 `{entity:role}` 的形式表示，其格式為實體名稱後接冒號，然後接著角色名稱。 若要將具有角色的實體新增至模式範本，請用大括弧括住實體名稱和角色名稱，例如 `Book a ticket from {Location:Origin} to {Location:Destination}`。 

|模式與實體角色|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>將 Pattern.any 新增至模式範本的語法
Pattern.any 實體可讓您將變動長度的實體新增至模式。 只要遵循模式範本，Pattern.any 就可以是任意長度。 

若要將 **Pattern.any** 實體新增至模式範本，請用大括弧括住 Pattern.any 實體，例如 `How much does {Booktitle} cost and what format is it available in?`。  

|模式與 Pattern.any 實體|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|模式中的書名|
|--|
|How much does **steal this book** cost and what format is it available in?|
|How much does **ask** cost and what format is it available in?|
|How much does **The Curious Incident of the Dog in the Night-Time** cost and what format is it available in?| 

書籍標題的單字不會對 LUIS 造成混淆, 因為 LUIS 會根據模式來知道書名的結尾。任何實體。

## <a name="explicit-lists"></a>明確清單

透過撰寫 API 建立[明確清單](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8), 以便在下列情況中允許例外狀況:

* 您的模式包含[模式。](luis-concept-entity-types.md#patternany-entity)
* 而且該模式語法允許根據語句來進行不正確的實體解壓縮。 

例如，假設您有一個模式，其包含選用語法 `[]` 和實體語法 `{}`，且以錯誤擷取資料的方式合併在一起。

請考慮使用模式 `[find] email about {subject} [from {person}]'。

在下列語句中，系統會正確及錯誤地擷取 **subject** 和 **person** 實體：

|語句|實體|正確擷取|
|--|--|:--:|
|email about dogs from Chris|subject=dogs<br>person=Chris|✔|
|email about the man from La Mancha|subject=the man<br>person=La Mancha|X|

在上表中, 主旨應該是`the man from La Mancha` (書籍標題), 但因為主旨包含選擇性的單字`from`, 所以不會正確預測標題。 

若要修正模式的這個例外狀況，請使用[撰寫明確清單的 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)，新增 `the man from la mancha` 作為符合 {subject} 實體的明確清單。

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>在範本語句中標記選用文字的語法
您可以使用規則運算式的方括弧語法 `[]`，在語句中標記選用文字。 選用文字最多只能在方括弧中套嵌兩個大括弧。

|模式與選用文字|意義|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`和`from {person}`是選擇性的|
|' 可以協助我 [？]|標點符號是選擇性的|

應該忽略標點符號`?`( `!`、 `.`、), 而且您必須在模式中使用方括弧語法來忽略它們。 

## <a name="pattern-only-apps"></a>僅限模式的應用程式
只要有每個意圖的模式, 您就可以使用沒有範例語句的意圖來建立應用程式。 針對僅限模式的應用程式, 模式不應包含機器學習的實體, 因為這需要範例語句。 

## <a name="best-practices"></a>最佳做法
了解[最佳做法](luis-concept-best-practices.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何實作本教學課程中的模式](luis-tutorial-pattern.md)
