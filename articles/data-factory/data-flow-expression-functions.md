---
title: Azure Data Factory 的對應資料流程功能中的運算式函數
description: 瞭解對應資料流程中的運算式函數。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 03bcde2049178aa5e9c347a3c60aa7be328adbc3
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716894"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>對應資料流程中的資料轉換運算式 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="expression-functions"></a>運算式函數

在 Data Factory 中, 使用對應資料流程功能的運算式語言來設定資料轉換。

---
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
數字配對的正模數。
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
---
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算 consine 的反值* ``acos(1) -> 0.0``
---
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
新增一對字串或數字。 將日期新增至天數。 對某個陣列附加類似類型的另一個陣列。 與 + 運算子相同* ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``
---
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
在日期或時間戳記中增加天數。 與 date 的 + 運算子相同* ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``
---
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
將月份新增至日期或時間戳記* ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
---
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
邏輯 AND 運算子。 與 & 相同 &* ``and(true, false) -> false``
* ``true && false -> false``
---
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算反正弦值* ``asin(0) -> 0.0``
---
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算反正切值* ``atan(0) -> 0.0``
---
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
傳回平面的正 X 軸與座標所指定的點之間的角度 (以弧度為單位)* ``atan2(0, 0) -> 0.0``
---
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
取得資料行值的平均值。* ``avg(sales) -> 7523420.234``
---
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
根據準則取得資料行值的平均值* ``avgIf(region == 'West', sales) -> 7523420.234``
---
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
依名稱選取資料流程中的資料行值。 如果有多個相符專案, 則會傳回第一個相符專案。 如果不符合, 則會傳回 Null 值。 傳回的值必須是由其中一個類型轉換函式 (TO_DATE, TO_STRING ...) 轉換的類型。在設計階段已知的資料行名稱, 應該只依據其名稱來定址。 不支援計算的輸入, 但您可以使用參數替換* ``toString(byName('parent')) -> appa``
* ``toLong(byName('income')) -> 9000000000009``
* ``toBoolean(byName('foster')) -> false``
* ``toLong(byName($debtCol)) -> 123456890``
* ``birthDate -> 12/31/2050``
* ``toString(byName('Bogus Column')) -> NULL``
---
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
依據資料流程中的相對位置 (以1為基礎) 來選取資料行值。 如果位置超出範圍, 則會傳回 Null 值。 傳回的值必須是由其中一個類型轉換函式 (TO_DATE, TO_STRING ...) 轉換的類型不支援計算的輸入, 但您可以使用參數替換* ``toString(byPosition(1)) -> amma``
* ``toDecimal(byPosition(2), 10, 2) -> 199990.99``
* ``toBoolean(byName(4)) -> false``
* ``toString(byName($colName)) -> family``
* ``toString(byPosition(1234)) -> NULL``
---
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
根據替代條件套用一個值或另一個值。 如果輸入的數目是偶數, 另一個則是最後一個條件的 Null* ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``
---
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算數位的 cube 根* ``cbrt(8) -> 2.0``
---
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
傳回不小於數位的最小整數* ``ceil(-0.1) -> 0``
---
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
從一組輸入傳回第一個非 null 值。 所有輸入都應該是相同的類型* ``coalesce(10, 20) -> 10``
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``
---
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
比較相同類型的兩個值。 如果 value1 < value2, 則傳回負整數, 如果 value1 = = value2, 則傳回 0, 如果 value1 > value2 則傳回正值* ``(compare(12, 24) < 1) -> true``
* ``(compare('dumbo', 'dum') > 0) -> true``
---
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
將任意數目的字串串連在一起。 與具有字串的 + 運算子相同* ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
---
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
以分隔符號將任意數目的字串串連在一起。 第一個參數是分隔符號* ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) ->``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
---
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算余弦值* ``cos(10) -> -0.83907152907``
---
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算值的雙曲余弦函數* ``cosh(0) -> 1.0``
---
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
取得值的彙總計數。 如果指定了選擇性的資料行, 它會忽略計數中的 Null 值* ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
---
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
取得一組資料行相異值的匯總計數。* ``countDistinct(custId, custName) -> 60``
---
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
根據條件取得值的彙總計數。 如果指定了選擇性的資料行, 它會忽略計數中的 Null 值* ``countIf(state == 'CA' && commission < 10000, name) -> 100``
---
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
取得兩個數據行之間的人口共變數* ``covariancePopulation(sales, profit) -> 122.12``
---
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
根據條件取得兩個數據行的擴展共變數* ``covariancePopulationIf(region == 'West', sales) -> 122.12``
---
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
取得兩個數據行的樣本共變數* ``covarianceSample(sales, profit) -> 122.12``
---
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
根據條件取得兩個數據行的樣本共變數* ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
---
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
以指定的位元長度為不同基本資料類型的一組資料行計算 CRC32 雜湊，值只能是 0(256)、224、256、384、512。 它可以用來計算資料列的指紋* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
---
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
CumeDist 函式會計算某個值與分割區中所有值的相對位置。 依照分割區的排序，將目前資料列 (含此列) 之前的列數，除以視窗分割區中的總列數，即為結果。 順序中的任何系結值都會評估為相同的位置。
* ``cumeDist() -> 1``
---
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
在此作業開始執行時取得目前的日期。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 當地時區會作為預設值。
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``
---
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
取得作業開始以當地時區執行時的目前時間戳記* ``currentTimestamp() -> 12-12-2030T12:12:12``
---
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
取得 UTC 格式的目前時間戳記。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 預設為目前的時區* ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``
---
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
取得給定日期的月份日期* ``dayOfMonth(toDate('2018-06-08')) -> 08``
---
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
取得指定日期的星期日期。 1-星期日, 2-星期一 ..., 7-星期六* ``dayOfWeek(toDate('2018-06-08')) -> 7``
---
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
取得給定日期的一年中的第幾天* ``dayOfYear(toDate('2016-04-09')) -> 100``
---
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
將弧度轉換成角度* ``degrees(3.141592653589793) -> 180``
---
### <code>denseRank</code>
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
計算某值在值群組中的排名。 依照分割區的排序，將目前資料列 (含此列) 之前的列數加一，即為結果。 這些值不會在序列中產生間距。 即使資料未排序, 也可以進行密集排名, 並尋找值的變更* ``denseRank(salesQtr, salesAmt) -> 1``
---
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
將數字配對相除。 與/運算子相同* ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
---
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
檢查字串是否以提供的字串結尾* ``endsWith('great', 'eat') -> true``
---
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
「等於」比較運算子。 與 = = 運算子相同* ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'bad'=='bad' -> true``
* ``'good'== NULL -> false``
* ``NULL===NULL -> false``
---
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
忽略大小寫的「等於」比較運算子。 與 < = > 運算子相同* ``'abc'<==>'abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
---
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
計算數位的階乘* ``factorial(5) -> 120``
---
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
一律傳回 false 值。 如果有名為 ' false ' 的資料行, 請使用函數語法 (false ())* ``isDiscounted == false()``
* ``isDiscounted() == false``
---
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
取得資料行群組的第一個值。 如果省略第二個參數 ignoreNulls, 則會假設為 false* ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
---
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
傳回不大於數位的最大整數* ``floor(-0.1) -> -1``
---
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
將轉換成 UTC 時間戳記。 您可以選擇性地以 ' GMT '、' PST '、' UTC '、' 北美洲/開曼 ' 的格式來傳遞時區。 預設為目前的時區* ``fromUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
---
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
「大於」比較運算子。 與 > 運算子相同* ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
---
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
「大於或等於」比較運算子。 與 > = 運算子相同* ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
---
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
傳回值清單中最大的值作為輸入。 如果所有輸入都是 null, 則傳回 null* ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
---
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
取得時間戳記的小時值。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 當地時區會作為預設值。
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``
---
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
根據條件套用一個值或另一個值。 如果未指定其他, 則會將它視為 Null。 這兩個值都必須相容 (數值、字串 ...)* ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
---
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
檢查項目是否在陣列中* ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
---
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
將每個單字的第一個字母轉換成大寫。 將單字識別為以空格分隔* ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
---
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
尋找子字串在字串中的位置 (從 1 開始)。 如果找不到, 則傳回0* ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
---
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
檢查資料列是否有刪除標示。 針對採用多個輸入資料流程的轉換, 您可以傳遞資料流程的 (以1為基的) 索引。 資料流程索引的預設值為1* ``isDelete() -> true``
* ``isDelete(1) -> false``
---
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
檢查資料列是否標示為錯誤。 針對採用多個輸入資料流程的轉換, 您可以傳遞資料流程的 (以1為基的) 索引。 資料流程索引的預設值為1* ``isError() -> true``
* ``isError(1) -> false``
---
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
檢查資料列是否標示為要忽略。 針對採用多個輸入資料流程的轉換, 您可以傳遞資料流程的 (以1為基的) 索引。 資料流程索引的預設值為1* ``isIgnore() -> true``
* ``isIgnore(1) -> false``
---
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
檢查資料列是否有插入標示。 針對採用多個輸入資料流程的轉換, 您可以傳遞資料流程的 (以1為基的) 索引。 資料流程索引的預設值為1* ``isInsert() -> true``
* ``isInsert(1) -> false``
---
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
檢查在查閱時是否比對資料列。 針對採用多個輸入資料流程的轉換, 您可以傳遞資料流程的 (以1為基的) 索引。 資料流程索引的預設值為1* ``isMatch() -> true``
* ``isMatch(1) -> false``
---
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
檢查值是否為 Null* ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
---
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
檢查資料列是否有更新標示。 針對採用多個輸入資料流程的轉換, 您可以傳遞資料流程的 (以1為基的) 索引。 資料流程索引的預設值為1* ``isUpdate() -> true``
* ``isUpdate(1) -> false``
---
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
取得資料行的峰度* ``kurtosis(sales) -> 122.12``
---
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據準則, 取得資料行的峰度* ``kurtosisIf(region == 'West', sales) -> 122.12``
---
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
取得第一個參數對目前資料列之前的 n 個資料列評估的值。 第二個參數是要查看的資料列數目, 而預設值為1。 如果沒有多個資料列, 除非指定了預設值, 否則會傳回 null 值* ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
---
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
取得資料行群組的最後一個值。 如果省略第二個參數 ignoreNulls, 則會假設為 false* ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
---
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
取得給定日期的當月最後一個日期* ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
---
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
取得第一個參數對目前資料列之後的 n 個資料列評估的值。 第二個參數是要向前查看的資料列數目, 而預設值為1。 如果沒有多個資料列, 除非指定了預設值, 否則會傳回 null 值* ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
---
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
「小於或等於」比較運算子。 與 < = 運算子相同* ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
---
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
從索引 1 處開始擷取含特定字元數的子字串。 與 SUBSTRING 相同 (str, 1, n)* ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
---
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
傳回字串的長度* ``length('kiddo') -> 5``
---
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
「小於」比較運算子。 與 < 運算子相同* ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
---
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
「小於或等於」比較運算子。 與 < = 運算子相同* ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
---
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
取得兩個字串之間的 levenshtein 距離* ``levenshtein('boys', 'girls') -> 4``
---
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
模式是一種符合字面的字串。 例外狀況有下列特殊符號: _ 符合輸入中的任何一個字元 (類似于)。 posix 規則運算式中的 .)。% 會比對出輸入中的零或多個字元 (類似於 posix 規則運算式中的 .*)。
逸出字元為 ''。 如果特殊符號或另一個逸出字元前面有逸出字元，就會逐字比對下列字元。 逸出任何其他字元的動作是無效的。
* ``like('icecream', 'ice%') -> true``
---
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
尋找子字串在字串中從特定位置起算的位置 (從 1 開始)。 如果省略位置，則視為字串的開頭。 如果找不到, 則傳回0* ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
---
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
計算對數值。 可以提供選擇性的基底, 否則使用歐拉數位* ``log(100, 10) -> 2``
---
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
根據10個基底計算記錄值* ``log10(100) -> 2``
---
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Lowercases 字串* ``lower('GunChus') -> 'gunchus'``
---
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
從左側為字串填補提供的填補內容，直到達到特定長度。 如果字串等於或大於長度, 則會將* ``lpad('great', 10, '-') -> '-----great'``它視為無 op 
* ``lpad('great', 4, '-') -> 'great'`` 
* ' ' lpad (' 絕佳 '、8、' < > ')-> ' < ><great'``
---
### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
從左側修剪開頭字元的字串。 若未指定第二個參數，則會修剪空白字元。 否則, 它會修剪第二個參數中指定的任何字元* ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
---
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
取得資料行的最大值* ``MAX(sales) -> 12312131.12``
---
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
根據條件取得資料行的最大值* ``maxIf(region == 'West', sales) -> 99999.56``
---
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
為不同基本資料類型的一組資料行計算 MD5 摘要，並傳回 32 個字元的十六進位字串。 它可以用來計算資料列的指紋* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``
---
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
取得資料行的平均值。 與 AVG 相同* ``mean(sales) -> 7523420.234``
---
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
根據條件取得資料行的平均值。 與 avgIf 相同* ``meanIf(region == 'West', sales) -> 7523420.234``
---
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
取得日期的毫秒值。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 當地時區會作為預設值。
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
---
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
取得資料行的最小值* ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
---
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
根據條件取得資料行的最小值* ``minIf(region == 'West', sales) -> 00.01``
---
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
減去數字。 從日期中減去天數。 與-運算子相同* ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
---
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
取得時間戳記的分鐘值。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 當地時區會作為預設值。
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``
---
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
數字配對的模數。 與% 運算子相同* ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
---
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
取得日期或時間戳記的月份值。* ``month(toDate('2012-8-8')) -> 8``
---
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
取得兩個 datesYou 之間的月份數, 可以使用 ' GMT '、' PST '、' UTC '、' 北美洲/開曼 ' 的格式來傳遞選擇性的時區。 當地時區會作為預設值。
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
---
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
將數字配對相乘。 與 * 運算子相同* ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
---
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
NTile 函式會將每個視窗分割區中的資料列分成從 1 到 `n` 的 `n` 個貯體。 貯體值的最大差異為 1。 如果分割區中的資料列數目無法平均分成貯體數，其餘值將會逐一均分給各個貯體，從第一個貯體開始。 NTile 函數適用于計算 tertiles、分量、十分位數和其他常見的摘要統計資料。 此函式會在初始化期間計算兩個變數：一般值區的大小會新增一個額外的資料列。 這兩個變數都是以目前分割區的大小為基礎。 在計算過程中，此函式會追蹤目前的資料列數目、目前的貯體數目，和貯體將有所變更的資料列號碼 (bucketThreshold)。 當目前的資料列數目達到貯體閾值時，貯體值將會加一，而閾值會依貯體大小增加 (如果目前的貯體已填補，則額外再加一)。
* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``
---
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
將數字轉換為負數。 將正數變成負值, 反之亦然* ``negate(13) -> -13``
---
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
傳回下一個唯一序列。 此數目只會在資料分割內連續, 而且前面會加上 partitionId* ``nextSequence() -> 12313112``
---
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
將字串值正規化以區分重音的 unicode 字元* ``normalize('boys') -> 'boys'``
---
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
邏輯否定運算子* ``not(true) -> false``
* ``not(premium)``
---
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
「不等於」比較運算子。 與! = 運算子相同* ``12!=24 -> true``
* ``'abc'!='abc' -> false``
---
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
傳回 NULL 值。 如果有名為 'null' 的資料行，則使用函式 syntax(null())。 使用的任何作業都會導致 Null* ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``
---
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
邏輯 OR 運算子。 與 | | 相同* ``or(true, false) -> true``
* ' ' true || false-> true ' '---
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
數字配對的正模數。
* ``pmod(-20, 8) -> 4``
---
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
將一個數位提高至另一個的乘冪* ``power(10, 2) -> 100``
---
### <code>rank</code>
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
計算某值在值群組中的排名。 依照分割區的排序，將目前資料列 (含此列) 之前的列數加一，即為結果。 這些值會在序列中產生間距。 即使資料未排序, 排名也會運作, 並尋找值的變更* ``rank(salesQtr, salesAmt) -> 1``
---
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
擷取指定規則運算式模式的相符子字串。 最後一個參數會識別比對群組，如果省略則預設為 1。 使用 '<regex>' (後引號) 來比對字串而不進行轉義* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
---
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
檢查字串是否符合指定的規則運算式模式。 使用 '<regex>' (後引號) 來比對字串而不進行轉義* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
---
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
以指定之字串中的另一個子字串取代所有出現的 RegEx 模式<regex>, 並使用 ' ' (後引號) 來比對字串, 而不進行轉義* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
---
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
根據以 RegEx 為基礎的分隔符號分割字串, 並傳回字串陣列* ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
---
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
以指定字串中的另一個子字串取代所有出現的子字串* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
---
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
反轉字串* ``reverse('gunchus') -> 'suhcnug'``
---
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
從右側擷取含特定字元數的子字串。 與 SUBSTRING 相同 (str, LENGTH (str)-n, n)* ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
---
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
檢查字串是否符合指定的 RegEx 模式* ``rlike('200.50', '(\d+).(\d+)') -> true``
---
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
根據指定的選擇性比例和選擇性的進位模式來四捨五入數位。 如果省略小數值, 則預設為0。  如果省略模式, 則預設為 ROUND_HALF_UP (5)。 進位的值包括 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY* ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
---
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
為視窗中的資料列指派連續的資料列編號 (從1開始)* ``rowNumber() -> 1``
---
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
從右側為字串填補提供的填補內容，直到達到特定長度。 如果字串等於或大於長度, 則會將它視為無 op * ``rpad('great', 10, '-') -> 'great-----'``rtrim 
* ``rpad('great', 4, '-') -> 'great'`` 
* ``rpad('great', 8, '<>') -> 'great<><'`` 
---
### <code>rtrim</code></code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
從右側修剪開頭字元的字串。 若未指定第二個參數，則會修剪空白字元。 否則, 它會修剪第二個參數中指定的任何字元* ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
---
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
取得日期的秒值。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 當地時區會作為預設值。
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``
---
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
為不同基本資料類型的一組資料行計算 SHA-1 摘要，並傳回 40 個字元的十六進位字串。 它可以用來計算資料列的指紋* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``
---
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
根據指定的位長度, 計算不同基本資料類型之一組資料行的 SHA-2 摘要, 其值只能是 0 (256)、224、256、384、512。 它可以用來計算資料列的指紋* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``
---
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算正弦值* ``sin(2) -> 0.90929742682``
---
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算雙曲正弦值* ``sinh(0) -> 0.0``
---
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
取得資料行的偏斜* ``skewness(sales) -> 122.12``
---
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據條件取得資料行的偏斜* ``skewnessIf(region == 'West', sales) -> 122.12``
---
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
從某個位置擷取陣列的子集。 位置從 1 開始。 如果省略長度, 則預設為字串的結尾* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
---
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
取得字串的 soundex 代碼* ``soundex('genius') -> 'G520'``
---
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
根據分隔符號分割字串, 並傳回字串陣列* ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
---
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算數位的平方根* ``sqrt(9) -> 3``
---
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
檢查字串是否以提供的字串開頭* ``startsWith('great', 'gr') -> true``
---
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
取得資料行的標準差* ``stdDev(sales) -> 122.12``
---
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據條件取得資料行的標準差* ``stddevIf(region == 'West', sales) -> 122.12``
---
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
取得資料行的人口標準差* ``stddevPopulation(sales) -> 122.12``
---
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據條件, 取得資料行的人口標準差* ``stddevPopulationIf(region == 'West', sales) -> 122.12``
---
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
取得資料行的樣本標準差* ``stddevSample(sales) -> 122.12``
---
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據條件, 取得資料行的樣本標準差* ``stddevSampleIf(region == 'West', sales) -> 122.12``
---
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
從日期中減去月數。 與 date 的-operator 相同* ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
---
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
從日期或時間戳記減去月份* ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
---
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
從某個位置擷取特定長度的子字串。 位置從 1 開始。 如果省略長度, 則預設為字串的結尾* ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
---
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
取得數值資料行的匯總總和* ``sum(col) -> value``
---
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
取得數值資料行相異值的匯總總和* ``sumDistinct(col) -> value``
---
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
根據條件取得數值資料行的彙總總和。 條件可以根據任何資料行* ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales)``
---
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
根據條件取得數值資料行的彙總總和。 條件可以根據任何資料行* ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales)``
---
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算正切值* ``tan(0) -> 0.0``
---
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算雙曲正切值* ``tanh(0) -> 0.0``
---
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
將 (' t '、' true '、' y '、' yes '、' 1 ') 的值, 轉換為 true, 並將 (' f '、' false '、' n '、' no '、' 0 ') 轉換為 false, 並將任何其他值設為 Null* ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
---
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
將字串轉換成指定的日期格式的日期。 如需所有可能的格式, 請參閱 JAVA SimpleDateFormat。 如果省略日期格式，則接受下列項目的組合。 [yyyy, yyyy-[M] M, yyyy-[M] M-[d] d, yyyy-[M] M-[d] d, yyyy-[M] M-[d] d, yyyy-[M] M-[d] dT *]* ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``
---
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
將任何數值或字串轉換為小數值。 若未指定有效位數和小數位數，則預設為 (10,2)。可使用選用的 Java 十進位格式進行轉換。 選擇性地區設定格式, 採用 BCP47 語言, 例如 en-us、de、zh-CN* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``
---
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
將任何數值或字串轉換為雙精準數值。 可使用選擇性的 Java 十進位格式進行轉換。 選擇性地區設定格式, 採用 BCP47 語言, 例如 en-us、de、zh-CN* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``
---
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
將任何數值或字串轉換為浮點值。 可使用選擇性的 Java 十進位格式進行轉換。 截斷任何 double* ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``
---
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
將任何數值或字串轉換成整數值。 可使用選擇性的 Java 十進位格式進行轉換。 截斷任何 long、float、double* ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
---
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
將任何數值或字串轉換為長值。 可使用選擇性的 Java 十進位格式進行轉換。 截斷任何 float、double* ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
---
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
將任何數值或字串轉換為短值。 可使用選擇性的 Java 十進位格式進行轉換。 截斷任何整數、long、float、double* ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
---
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
將基本資料類型轉換為字串。 針對數字和日期，可以指定格式。 若未指定，則會選擇系統預設值。對於數字會使用 Java 十進位格式。 如需所有可能的日期格式, 請參閱 JAVA SimpleDateFormat。預設格式為 yyyy-mm-dd* ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``
---
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
以指定的選擇性時間戳記格式將字串轉換為日期。 如需所有可能的格式, 請參閱 JAVA SimpleDateFormat。 如果省略時間戳記, 則會使用預設模式 yyyy-[M] M-[d] d hh: mm: ss [. f ...]* ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``
---
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
將時間戳記轉換為 UTC。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 預設為目前的時區* ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
---
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
將字串中的一組字元取代為另一組字元。 字元有1到1個取代* ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
---
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
修剪開頭和結尾字元的字串。 若未指定第二個參數，則會修剪空白字元。 否則, 它會修剪第二個參數中指定的任何字元* ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``
---
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
一律傳回 true 值。 如果有名為 ' true ' 的資料行, 請使用函數語法 (true ())* ``isDiscounted == true()``
* ``isDiscounted() == true``
---
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
比對資料行的類型。 只能用於模式運算式中。數位符合 short、integer、long、double、float 或 decimal、integer 符合 short、integer、long、小數符合 double、float、decimal 和 datetime 符合日期或時間戳記類型* ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``
---
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Uppercases 字串* ``upper('bojjus') -> 'BOJJUS'``
---
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
取得資料行的變異數* ``variance(sales) -> 122.12``
---
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據條件取得資料行的變異數* ``varianceIf(region == 'West', sales) -> 122.12``
---
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
取得資料行的擴展變異數* ``variancePopulation(sales) -> 122.12``
---
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據條件取得資料行的擴展變異數* ``variancePopulationIf(region == 'West', sales) -> 122.12``
---
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
取得資料行的非偏誤變異數* ``varianceSample(sales) -> 122.12``
---
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據條件取得資料行的非偏誤變異數* ``varianceSampleIf(region == 'West', sales) -> 122.12``
---
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
取得給定日期的年份周* ``weekOfYear(toDate('2008-02-20')) -> 8``
---
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
邏輯 XOR 運算子。 與 ^ 運算子相同* ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
---
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
取得日期的年份值* ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>後續步驟

[瞭解如何使用運算式](concepts-data-flow-expression-builder.md)產生器。
