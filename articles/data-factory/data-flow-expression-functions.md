---
title: Azure Data Factory 的對應資料流程功能中的運算式函數
description: 瞭解對應資料流程中的運算式函數。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 27d968aa5202fbeb38be9a2416514d2185c1d8b9
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436750"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>對應資料流程中的資料轉換運算式 



## <a name="expression-functions"></a>運算式函數

在 Data Factory 中，使用對應資料流程功能的運算式語言來設定資料轉換。

___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
數位的絕對值。
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算反余弦值 * ``acos(1) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
新增一對字串或數字。 將日期新增至天數。 將持續時間新增至時間戳記。 對某個陣列附加類似類型的另一個陣列。 與 + 運算子相同 * ``add(10, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
在日期或時間戳記中增加天數。 與 date * ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')`` @ no__t-1 @ no__t-2 的 + 運算子相同<br/><br/>
將月份新增至日期或時間戳記。 您可以選擇性地傳遞時區 * ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
邏輯 AND 運算子。 與 & & * ``and(true, false) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
計算反正弦值 * ``asin(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
計算反正切值 * ``atan(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
傳回平面的正 X 軸與座標所指定的點之間的角度（以弧度為單位），* ``atan2(0, 0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
取得資料行的平均值 * ``avg(sales)`` @ no__t-1 @ no__t-2<br/><br/>
根據條件取得資料行的平均值 * ``avgIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
依名稱選取資料流程中的資料行值。 您可以傳遞選擇性的資料流程名稱作為第二個引數。 如果有多個相符專案，則會傳回第一個相符專案。 如果不符合，則會傳回 Null 值。 傳回的值必須是由其中一個類型轉換函式（TO_DATE，TO_STRING ...）轉換的類型。 在設計階段已知的資料行名稱，應該只依據其名稱來定址。 不支援計算的輸入，但是您可以使用參數替代 * ``toString(byName('parent'))`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
依據資料流程中的相對位置（以1為基礎）來選取資料行值。 如果位置超出範圍，則會傳回 Null 值。 傳回的值必須是由其中一個類型轉換函式（TO_DATE，TO_STRING ...）轉換的類型不支援計算的輸入，但是您可以使用參數替代 * ``toString(byPosition(1))`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
根據替代條件套用一個值或另一個值。 如果輸入的數目為偶數，則會針對最後一個條件預設為 Null，* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
計算數位的 cube 根 * ``cbrt(8) -> 2.0`` @ no__t-1 @ no__t-2<br/><br/>
傳回不小於數位的最小整數 * ``ceil(-0.1) -> 0`` @ no__t-1 @ no__t-2<br/><br/>
從一組輸入傳回第一個非 null 值。 所有輸入都應該是相同的類型 * ``coalesce(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
比較相同類型的兩個值。 如果 value1 < value2，則傳回負整數，0表示 value1 = = value2，如果 value1 > value2 * ``(compare(12, 24) < 1) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
將任意數目的字串串連在一起。 相當於 + 運算子加上字串 * ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``
* ``isNull('sql' + null) -> true``
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
以分隔符號將任意數目的字串串連在一起。 第一個參數是分隔符號 * ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
如果提供之陣列中的任何元素在所提供的述詞中評估為 true，則傳回 true。 Contains 需要述詞函式中某個元素的參考，如 #item * ``contains([1, 2, 3, 4], #item == 3) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
計算余弦值 * ``cos(10) -> -0.8390715290764524`` @ no__t-1 @ no__t-2<br/><br/>
計算值的雙曲余弦函數 * ``cosh(0) -> 1.0`` @ no__t-1 @ no__t-2<br/><br/>
取得值的彙總計數。 如果指定了選擇性的資料行，它會忽略計數中的 Null 值 * ``count(custId)`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
取得一組資料行的相異值匯總計數 * ``countDistinct(custId, custName)`` @ no__t-1 @ no__t-2<br/><br/>
根據條件取得值的彙總計數。 如果指定了選擇性的資料行，它會忽略計數中的 Null 值 * ``countIf(state == 'CA' && commission < 10000, name)`` @ no__t-1 @ no__t-2<br/><br/>
取得兩個數據行之間的人口共變數 * ``covariancePopulation(sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
根據條件，取得兩個數據行的人口共變數 * ``covariancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
取得兩個數據行的樣本共變數 * ``covarianceSample(sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
根據條件，取得兩個數據行的樣本共變數 * ``covarianceSampleIf(region == 'West', sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
以指定的位元長度為不同基本資料類型的一組資料行計算 CRC32 雜湊，值只能是 0(256)、224、256、384、512。 它可以用來計算資料列的指紋 * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L`` @ no__t-1 @ no__t-2<br/><br/>
CumeDist 函式會計算某個值與分割區中所有值的相對位置。 依照分割區的排序，將目前資料列 (含此列) 之前的列數，除以視窗分割區中的總列數，即為結果。 順序中的任何系結值都會評估為相同的位置。
* ``cumeDist()``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
在此作業開始執行時取得目前的日期。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 本機時區會當做預設值使用。如需可用的格式，請參閱 JAVA 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``currentDate() == toDate('2250-12-31') -> false`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
取得工作開始以當地時區執行時的目前時間戳記 * ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false`` @ no__t-1 @ no__t-2<br/><br/>
取得目前的時間戳記（UTC）。 如果您想要將目前時間轉譯成與您的叢集時區不同的時區，您可以用 ' GMT '、' PST '、' UTC '、' 北美洲/開曼 ' 的格式傳遞選擇性的時區。 預設為目前的時區。 如需可用的格式，請參閱 JAVA 的 SimpleDateFormat。 使用[SimpleDateFormat](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html)將 UTC 時間轉換成不同的時區使用 fromUTC （）。
* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
取得給定日期 * ``dayOfMonth(toDate('2018-06-08')) -> 8`` @ no__t-1 @ no__t-2 的月份日<br/><br/>
取得指定日期的星期日期。 1-星期日，2-星期一 ...，7-星期六 * ``dayOfWeek(toDate('2018-06-08')) -> 6`` @ no__t-1 @ no__t-2<br/><br/>
取得給定日期 * ``dayOfYear(toDate('2016-04-09')) -> 100`` @ no__t-1 @ no__t-2 的一年中的第幾天<br/><br/>
天數 * ``days(2) -> 172800000L`` @ no__t-1 @ no__t-2 的持續時間（以毫秒為單位）<br/><br/>
將弧度轉換成度數 * ``degrees(3.141592653589793) -> 180`` @ no__t-1 @ no__t-2<br/><br/>
計算某值在值群組中的排名。 依照分割區的排序，將目前資料列 (含此列) 之前的列數加一，即為結果。 這些值不會在序列中產生間距。 即使資料未經過排序，密集排名也會運作，並尋找值 * ``denseRank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2 的變更<br/><br/>
將數字配對相除。 與/運算子相同 * ``divide(20, 10) -> 2`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
檢查字串是否以提供的字串結尾，* ``endsWith('dumbo', 'mbo') -> true`` @ no__t-1 @ no__t-2<br/><br/>
「等於」比較運算子。 與 = = 運算子 * ``equals(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
忽略大小寫的「等於」比較運算子。 等同于 < = > 運算子 * ``'abc'<=>'Abc' -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
計算數位的階乘 * ``factorial(5) -> 120`` @ no__t-1 @ no__t-2<br/><br/>
一律傳回 false 值。 如果有名為 ' false ' 的資料行 * ``(10 + 20 > 30) -> false`` @ no__t-1 @ no__t-2 @ no__t-3，請使用函數語法（false （））<br/><br/>
篩選出不符合所提供述詞之陣列中的元素。 篩選準則預期述詞函式中某個元素的參考 #item * ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
取得資料行群組的第一個值。 如果省略第二個參數 ignoreNulls，則會假設為 false * ``first(sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
傳回不大於 * ``floor(-0.1) -> -1`` @ no__t-1 @ no__t-2 數位的最大整數<br/><br/>
將轉換成 UTC 時間戳記。 您可以選擇性地以 ' GMT '、' PST '、' UTC '、' 北美洲/開曼 ' 的格式來傳遞時區。 其預設為目前 timezoneRefer JAVA 的 SimpleDateFormat，以取得可用的格式。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
「大於」比較運算子。 與 > 運算子相同 * ``greater(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
「大於或等於」比較運算子。 與 > = 運算子相同，* ``greaterOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
當輸入略過 null 值時，傳回值清單中的最大值。 如果所有輸入都是 null，則傳回 null * ``greatest(10, 30, 15, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
依名稱檢查資料流程中的資料行值。 您可以傳遞選擇性的資料流程名稱作為第二個引數。  在設計階段已知的資料行名稱，應該只依據其名稱來定址。 不支援計算的輸入，但您可以使用參數替代 * ``hasColumn('parent')`` @ no__t-1 @ no__t-2<br/><br/>
取得時間戳記的小時值。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 本機時區會當做預設值使用。如需可用的格式，請參閱 JAVA 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
小時數的持續時間（以毫秒為單位） * ``hours(2) -> 7200000L`` @ no__t-1 @ no__t-2<br/><br/>
使用提供的運算式，將陣列的每個元素對應至新的專案。 對應預期運算式函式中的一個元素參考為 #item，而元素索引的參考則為 #index * ``iMap([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]`` @ no__t-1 @ no__t-2<br/><br/>
根據條件套用一個值或另一個值。 如果未指定其他，則會將它視為 Null。 這兩個值都必須相容（數值、字串 ...） * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
檢查值是否不是 Null，並傳回其他傳回替代的。 它會測試所有輸入，直到找到第一個非 null 值 * ``iifNull(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
檢查項目是否在陣列中 * ``in([10, 20, 30], 10) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
將每個單字的第一個字母轉換成大寫。 單字會識別為以空白字元分隔 * ``initCap('cool iceCREAM') -> 'Cool Icecream'`` @ no__t-1 @ no__t-2<br/><br/>
尋找子字串在字串中的位置 (從 1 開始)。 如果找不到，則傳回 0 * ``instr('dumbo', 'mbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
檢查資料列是否有刪除標示。 針對採用多個輸入資料流程的轉換，您可以傳遞資料流程的（以1為基的）索引。 資料流程索引的預設值為 1 * ``isDelete()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
檢查資料列是否標示為錯誤。 針對採用多個輸入資料流程的轉換，您可以傳遞資料流程的（以1為基的）索引。 資料流程索引的預設值為 1 * ``isError()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
檢查資料列是否標示為要忽略。 針對採用多個輸入資料流程的轉換，您可以傳遞資料流程的（以1為基的）索引。 資料流程索引的預設值為 1 * ``isIgnore()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
檢查資料列是否有插入標示。 針對採用多個輸入資料流程的轉換，您可以傳遞資料流程的（以1為基的）索引。 資料流程索引的預設值為 1 * ``isInsert()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
檢查在查閱時是否比對資料列。 針對採用多個輸入資料流程的轉換，您可以傳遞資料流程的（以1為基的）索引。 資料流程索引的預設值為 1 * ``isMatch()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
檢查值是否為 Null * ``isNull(NULL()) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
檢查資料列是否有更新標示。 針對採用多個輸入資料流程的轉換，您可以傳遞資料流程的（以1為基的）索引。 資料流程索引的預設值為 1 * ``isUpdate()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
檢查資料列是否有插入標示。 針對採用多個輸入資料流程的轉換，您可以傳遞資料流程的（以1為基的）索引。 資料流程索引的預設值為 1 * ``isUpsert()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
取得資料行 * ``kurtosis(sales)`` @ no__t-1 @ no__t-2 的峰度<br/><br/>
根據條件，取得資料行的峰度 * ``kurtosisIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
取得第一個參數對目前資料列之前的 n 個資料列評估的值。 第二個參數是要查看的資料列數目，而預設值為1。 如果沒有多個資料列，除非指定了預設值，否則會傳回 null 值，* ``lag(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
取得資料行群組的最後一個值。 如果省略第二個參數 ignoreNulls，則會假設為 false * ``last(sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
取得給定日期 * ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')`` @ no__t-1 @ no__t-2 的當月最後一個日期<br/><br/>
取得第一個參數對目前資料列之後的 n 個資料列評估的值。 第二個參數是要向前查看的資料列數目，而預設值為1。 如果沒有多個資料列，除非指定了預設值，否則會傳回 null 值，* ``lead(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
「小於或等於」比較運算子。 與 < = 運算子相同，* ``least(10, 30, 15, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
從索引 1 處開始擷取含特定字元數的子字串。 與 SUBSTRING （str，1，n） * ``left('bojjus', 2) -> 'bo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
傳回字串的長度 * ``length('dumbo') -> 5`` @ no__t-1 @ no__t-2<br/><br/>
「小於」比較運算子。 與 < 運算子相同 * ``lesser(12, 24) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
「小於或等於」比較運算子。 與 < = 運算子相同，* ``lesserOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
取得兩個字串之間的 levenshtein 距離，* ``levenshtein('boys', 'girls') -> 4`` @ no__t-1 @ no__t-2<br/><br/>
模式是一種符合字面的字串。 例外狀況有下列特殊符號： _ 符合輸入中的任何一個字元（類似于）。 posix 規則運算式中的 .)。% 會比對出輸入中的零或多個字元 (類似於 posix 規則運算式中的 .*)。
逸出字元為 ''。 如果特殊符號或另一個逸出字元前面有逸出字元，就會逐字比對下列字元。 逸出任何其他字元的動作是無效的。
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
尋找子字串在字串中從特定位置起算的位置 (從 1 開始)。 如果省略位置，則視為字串的開頭。 如果找不到，則傳回 0 * ``locate('mbo', 'dumbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
計算對數值。 可以提供選擇性的基底，如果使用 * ``log(100, 10) -> 2`` @ no__t-1 @ no__t-2 則為歐拉數位<br/><br/>
根據10個基底 @no__t 計算記錄值-0 @ no__t-1 @ no__t-2<br/><br/>
Lowercases 字串 * ``lower('GunChus') -> 'gunchus'`` @ no__t-1 @ no__t-2<br/><br/>
從左側為字串填補提供的填補內容，直到達到特定長度。 如果字串等於或大於長度，則會修剪到長度 * ``lpad('dumbo', 10, '-') -> '-----dumbo'`` @ no__t-1 @ no__t-2 ' ' lpad （' dumbo '，8，' < > '）-> ' < > <dumbo'``
___
### <code>ltrim @ no__t-4 @ no__t-5<br/><br/>
從左側修剪開頭字元的字串。 若未指定第二個參數，則會修剪空白字元。 否則，它會修剪第二個參數中指定的任何字元 * ``ltrim('  dumbo  ') -> 'dumbo  '`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
使用提供的運算式，將陣列的每個元素對應至新的專案。 對應預期運算式函數中的一個元素參考為 #item * ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
取得資料行的最大值 * ``max(sales)`` @ no__t-1 @ no__t-2<br/><br/>
根據條件，取得資料行的最大值 * ``maxIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
為不同基本資料類型的一組資料行計算 MD5 摘要，並傳回 32 個字元的十六進位字串。 它可以用來計算資料列的指紋 * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'`` @ no__t-1 @ no__t-2<br/><br/>
取得資料行的平均值。 與 AVG @no__t 相同-0 @ no__t-1 @ no__t-2<br/><br/>
根據條件取得資料行的平均值。 與 avgIf 相同 * ``meanIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
取得日期的毫秒值。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 本機時區會當做預設值使用。如需可用的格式，請參閱 JAVA 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
毫秒數的持續時間（以毫秒為單位） * ``seconds(2) -> 2L`` @ no__t-1 @ no__t-2<br/><br/>
取得資料行的最小值 * ``min(sales)``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
根據條件，取得資料行的最小值 * ``minIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
減去數字。 從日期中減去天數。 從時間戳記減法的持續時間。 減法兩個時間戳記，以毫秒為單位來取得差異。 與-operator * ``minus(20, 10) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
取得時間戳記的分鐘值。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 本機時區會當做預設值使用。如需可用的格式，請參閱 JAVA 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
分鐘數的持續時間（以毫秒為單位） * ``minutes(2) -> 120000L`` @ no__t-1 @ no__t-2<br/><br/>
數字配對的模數。 與% 運算子相同 * ``mod(20, 8) -> 4`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
取得日期或時間戳記 * ``month(toDate('2012-8-8')) -> 8`` @ no__t-1 @ no__t-2 的月份值<br/><br/>
取得兩個日期之間的月數。 您可以從計算中舍入。您可以用 ' GMT '、' PST '、' UTC '、' 北美洲/開曼 ' 的格式傳遞選擇性的時區。 本機時區會當做預設值使用。如需可用的格式，請參閱 JAVA 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
將數字配對相乘。 與 * 運算子相同 * ``multiply(20, 10) -> 200`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
NTile 函式會將每個視窗分割區中的資料列分成從 1 到 `n` 的 `n` 個貯體。 貯體值的最大差異為 1。 如果分割區中的資料列數目無法平均分成貯體數，其餘值將會逐一均分給各個貯體，從第一個貯體開始。 NTile 函數適用于計算 tertiles、分量、十分位數和其他常見的摘要統計資料。 函式會在初始化期間計算兩個變數：一般值區的大小會新增一個額外的資料列。 這兩個變數都是以目前分割區的大小為基礎。 在計算過程中，此函式會追蹤目前的資料列數目、目前的貯體數目，和貯體將有所變更的資料列號碼 (bucketThreshold)。 當目前的資料列數目達到貯體閾值時，貯體值將會加一，而閾值會依貯體大小增加 (如果目前的貯體已填補，則額外再加一)。
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
將數字轉換為負數。 將正數變成負數，反之亦然 * ``negate(13) -> -13`` @ no__t-1 @ no__t-2<br/><br/>
傳回下一個唯一序列。 此數目只會在資料分割內連續，而且前面會加上 partitionId * ``nextSequence() == 12313112 -> false`` @ no__t-1 @ no__t-2<br/><br/>
將字串值正規化，以分隔重音 unicode 字元 * ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'`` @ no__t-1 @ no__t-2<br/><br/>
邏輯否定運算子 * ``not(true) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
「不等於」比較運算子。 與！ = 運算子 * ``12 != 24 -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
傳回 NULL 值。 如果有名為 'null' 的資料行，則使用函式 syntax(null())。 使用的任何作業都會產生 Null * ``isNull('dumbo' + null) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
邏輯 OR 運算子。 與 | | 相同* ``or(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
數字配對的正模數。
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
傳回輸入資料列位於 * ``partitionId()`` @ no__t-1 @ no__t-2 中的目前資料分割識別碼<br/><br/>
將一個數位引發另一個 * ``power(10, 2) -> 100`` @ no__t-1 @ no__t-2 的乘冪<br/><br/>
計算某值在值群組中的排名。 依照分割區的排序，將目前資料列 (含此列) 之前的列數加一，即為結果。 這些值會在序列中產生間距。 即使資料未排序，排名也會運作，並尋找值的變更 * ``rank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2<br/><br/>
累加陣列中的元素。 減少預期第一個運算式函式中的累加器和一個專案的參考是 #acc 和 #item 而且它預期產生的值會當做第二個運算式函式中使用的 #result * ``reduce([1, 2, 3, 4], 0, #acc + #item, #result) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
擷取指定規則運算式模式的相符子字串。 最後一個參數會識別比對群組，如果省略則預設為 1。 使用 ' <regex> ' （後引號）來比對字串，而不需要進行轉義 * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
檢查字串是否符合指定的規則運算式模式。 使用 ' <regex> ' （後引號）來比對字串，而不需要進行轉義 * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
以指定之字串中的另一個子字串取代所有出現的 RegEx 模式，使用 ' <regex> ' （後引號）來比對字串，而不需要進行轉義 * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
根據 RegEx 分割字串，並傳回字串陣列 * ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
將子字串的所有出現專案取代為指定字串中的另一個子字串。 如果省略最後一個參數，則預設為空字串 * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
反轉字串 * ``reverse('gunchus') -> 'suhcnug'`` @ no__t-1 @ no__t-2<br/><br/>
從右側擷取含特定字元數的子字串。 與 SUBSTRING （str，LENGTH （str）-n，n） * ``right('bojjus', 2) -> 'us'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
檢查字串是否符合指定的 RegEx 模式 * ``rlike('200.50', `(\d+).(\d+)`) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
根據指定的選擇性比例和選擇性的進位模式來四捨五入數位。 如果省略小數值，則預設為0。  如果省略模式，則預設為 ROUND_HALF_UP （5）。 進位的值包括 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY * ``round(100.123) -> 100.0`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
從 1 * ``rowNumber()`` @ no__t-1 @ no__t-2 開始，為視窗中的資料列指派連續的資料列編號<br/><br/>
從右側為字串填補提供的填補內容，直到達到特定長度。 如果字串等於或大於長度，則會將其修剪為長度 * ``rpad('dumbo', 10, '-') -> 'dumbo-----'`` @ no__t-1 @ no__t-2 @ no__t-3rtrim @ no__t-4 @ no__t-5<br/><br/>
從右側修剪開頭字元的字串。 若未指定第二個參數，則會修剪空白字元。 否則，它會修剪第二個參數中指定的任何字元 * ``rtrim('  dumbo  ') -> '  dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
取得日期的秒值。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 本機時區會當做預設值使用。如需可用的格式，請參閱 JAVA 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
秒數的持續時間（以毫秒為單位） * ``seconds(2) -> 2000L`` @ no__t-1 @ no__t-2<br/><br/>
為不同基本資料類型的一組資料行計算 SHA-1 摘要，並傳回 40 個字元的十六進位字串。 它可以用來計算資料列的指紋 * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'`` @ no__t-1 @ no__t-2<br/><br/>
根據指定的位長度，計算不同基本資料類型之一組資料行的 SHA-2 摘要，其值只能是0（256）、224、256、384、512。 它可以用來計算資料列的指紋 * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'`` @ no__t-1 @ no__t-2<br/><br/>
計算 * ``sin(2) -> 0.9092974268256817`` @ no__t-1 @ no__t-2 的正弦值<br/><br/>
計算雙曲正弦值 * ``sinh(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
取得資料行的偏斜 * ``skewness(sales)`` @ no__t-1 @ no__t-2<br/><br/>
根據條件，取得資料行的偏斜 * ``skewnessIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
從某個位置擷取陣列的子集。 位置從 1 開始。 如果省略長度，則預設為字串結尾 * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
使用提供的述詞函式來排序陣列。 排序預期運算式函式中的兩個連續元素參考為 #item1，#item2 * ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
取得字串的 soundex 代碼，* ``soundex('genius') -> 'G520'`` @ no__t-1 @ no__t-2<br/><br/>
根據分隔符號分割字串，並傳回字串陣列 * ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8<br/><br/>
計算數位的平方根，* ``sqrt(9) -> 3`` @ no__t-1 @ no__t-2<br/><br/>
檢查字串是否以提供的字串開頭，* ``startsWith('dumbo', 'du') -> true`` @ no__t-1 @ no__t-2<br/><br/>
取得資料行 * ``stdDev(sales)`` @ no__t-1 @ no__t-2 的標準差<br/><br/>
根據條件，取得資料行的標準差 * ``stddevIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
取得資料行的人口標準差 * ``stddevPopulation(sales)`` @ no__t-1 @ no__t-2<br/><br/>
根據條件，取得資料行的人口標準差 * ``stddevPopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
取得資料行 * ``stddevSample(sales)`` @ no__t-1 @ no__t-2 的樣本標準差<br/><br/>
根據條件，取得資料行 * ``stddevSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2 的樣本標準差<br/><br/>
從日期或時間戳記減去月份。 與 date * ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')`` @ no__t-1 @ no__t-2 相同的-operator<br/><br/>
從日期或時間戳記減去月份 * ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')`` @ no__t-1 @ no__t-2<br/><br/>
從某個位置擷取特定長度的子字串。 位置從 1 開始。 如果省略長度，則預設為字串的結尾，* ``substring('Cat in the hat', 5, 2) -> 'in'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
取得數值資料行的匯總總和 * ``sum(col)`` @ no__t-1 @ no__t-2<br/><br/>
取得數值資料行之相異值的匯總總和 * ``sumDistinct(col)`` @ no__t-1 @ no__t-2<br/><br/>
根據條件取得數值資料行的彙總總和。 條件可以根據任何資料行 * ``sumDistinctIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
根據條件取得數值資料行的彙總總和。 條件可以根據任何資料行 * ``sumIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
計算正切值 * ``tan(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
計算雙曲正切值 * ``tanh(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
將任何數值/日期/時間戳記/字串轉換為二進位表示 * ``toBinary(3) -> [0x11]`` @ no__t-1 @ no__t-2<br/><br/>
將（' t '、' true '、' y '、' yes '、' 1 '）的值轉換為 true，並將（' f '、' false '、' n '、' no '、' 0 '）轉換為 false，而任何其他值則為 Null，* ``toBoolean('true') -> true`` @ no__t 1 @ no__t-2 @ no__t-3 @ no__t<br/><br/>
使用選擇性的輸入日期格式，將輸入日期字串轉換為日期。 如需可用的格式，請參閱 JAVA 的 SimpleDateFormat。 如果省略輸入日期格式，預設格式為 yyyy-[M] M-[d] d。 接受的格式為： [yyyy，yyyy-[M] M，yyyy-[M] M-[d] d，yyyy-[M] M-[d] dT *] * ``toDate('2012-8-18') -> toDate('2012-08-18')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
將任何數值或字串轉換為小數值。 若未指定有效位數和小數位數，則預設為 (10,2)。可使用選用的 Java 十進位格式進行轉換。 選擇性地區設定格式，採用 BCP47 語言，例如 en-us、de、zh-CN * ``toDecimal(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
將任何數值或字串轉換為雙精準數值。 可使用選擇性的 Java 十進位格式進行轉換。 選擇性地區設定格式，採用 BCP47 語言，例如 en-us、de、zh-CN * ``toDouble(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
將任何數值或字串轉換為浮點值。 可使用選擇性的 Java 十進位格式進行轉換。 截斷任何 double * ``toFloat(123.45) -> 123.45f`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
將任何數值或字串轉換成整數值。 可使用選擇性的 Java 十進位格式進行轉換。 截斷任何 long、float、double * ``toInteger(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
將任何數值或字串轉換為長值。 可使用選擇性的 Java 十進位格式進行轉換。 截斷任何 float、double * ``toLong(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
將任何數值或字串轉換為短值。 可使用選擇性的 Java 十進位格式進行轉換。 截斷任何整數、long、float、double * ``toShort(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
將基本資料類型轉換為字串。 針對數字和日期，可以指定格式。 若未指定，則會選擇系統預設值。對於數字會使用 Java 十進位格式。 如需所有可能的日期格式，請參閱 JAVA SimpleDateFormat。預設格式為 yyyy-mm-dd * ``toString(10) -> '10'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
指定選擇性的時間戳記格式，將字串轉換為時間戳記。 如需所有可能的格式，請參閱 JAVA SimpleDateFormat。 如果省略時間戳記，則為預設模式。 yyyy-[M] M-[d] d hh： mm： ss [. f ...] 是使用。 您可以用 ' GMT '、' PST '、' UTC '、' 北美洲/開曼 ' 的格式傳遞選擇性的時區。時間戳記支援最多毫秒的精確度，其值為 999Refer JAVA 的 SimpleDateFormat，以取得可用的格式。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
將時間戳記轉換為 UTC。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 其預設為目前 timezoneRefer JAVA 的 SimpleDateFormat，以取得可用的格式。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
將字串中的一組字元取代為另一組字元。 字元有1到1的取代 * ``translate('(bojjus)', '()', '[]') -> '[bojjus]'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
修剪開頭和結尾字元的字串。 若未指定第二個參數，則會修剪空白字元。 否則，它會修剪第二個參數中指定的任何字元 * ``trim('  dumbo  ') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
一律傳回 true 值。 如果有名為 ' true ' 的資料行 * ``(10 + 20 == 30) -> true`` @ no__t-1 @ no__t-2 @ no__t-3，請使用函數語法（true （））<br/><br/>
比對資料行的類型。 只能用於模式運算式中。數位符合 short、integer、long、double、float 或 decimal、integer 符合 short、integer、long、小數符合 double、float、decimal 和 datetime 符合日期或時間戳記類型 * ``typeMatch(type, 'number')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Uppercases 字串 * ``upper('bojjus') -> 'BOJJUS'`` @ no__t-1 @ no__t-2<br/><br/>
傳回產生的 UUID * ``uuid()`` @ no__t-1 @ no__t-2<br/><br/>
取得資料行的變異數 * ``variance(sales)`` @ no__t-1 @ no__t-2<br/><br/>
根據條件，取得資料行的變異數 * ``varianceIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
取得資料行的擴展變異數 * ``variancePopulation(sales)`` @ no__t-1 @ no__t-2<br/><br/>
根據條件，取得資料行的人口變異數 * ``variancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
取得資料行的非偏誤變異數 * ``varianceSample(sales)`` @ no__t-1 @ no__t-2<br/><br/>
根據條件，取得資料行的非偏誤變異數 * ``varianceSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
取得給定日期 * ``weekOfYear(toDate('2008-02-20')) -> 8`` @ no__t-1 @ no__t-2 的年份周<br/><br/>
周數的持續時間（以毫秒為單位） * ``weeks(2) -> 1209600000L`` @ no__t-1 @ no__t-2<br/><br/>
邏輯 XOR 運算子。 與 ^ 運算子相同 * ``xor(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
取得日期 * ``year(toDate('2012-8-8')) -> 2012`` 的年份值

## <a name="next-steps"></a>後續步驟

[瞭解如何使用運算式](concepts-data-flow-expression-builder.md)產生器。
