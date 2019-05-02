---
title: 對應資料流程功能的 Azure Data Factory 中的運算式函式
description: 深入了解對應資料流程中的運算式函式。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 6b4df70114dd481566ae1a666c91c1c9b5bad86f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717009"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>對應資料流程中的資料轉換運算式 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="expression-functions"></a>運算式函式

Data Factory 中使用運算式語言對應的資料流功能的設定資料轉換。

*********************************
<code>abs</code>
==============================
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
這個運算式會將標示的數字配對正數的模數。
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
*********************************
<code>acos</code>
==============================
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會計算反餘弦函數值。
* ``acos(1) -> 0.0``
*********************************
<code>add</code>
==============================
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
這個運算式會將一組的字串或數字。 它會加入一個日期的天數。 附加到另一個類似的類型陣列。 
* ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``

**新增**操作員是相同**+** 運算子。
*********************************
<code>addDays</code>
==============================
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
這個運算式會將天加入至日期或時間戳記。 
* ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``

**AddDays**操作員是相同**+** 日期的運算子。
*********************************
<code>addMonths</code>
==============================
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
這個運算式會將幾個月加入至日期或時間戳記。
* ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
*********************************
<code>and</code>
==============================
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
這是邏輯**和**運算子。 這是相同**&&** 運算子。
* ``and(true, false) -> false``
* ``true && false -> false``
*********************************
<code>asin</code>
==============================
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會計算反正弦值。
* ``asin(0) -> 0.0``
*********************************
<code>atan</code>
==============================
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會計算反向的正切值。
* ``atan(0) -> 0.0``
*********************************
<code>atan2</code>
==============================
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會傳回以弧度為單位的平面正 x 軸和提供座標的點之間的角度。
* ``atan2(0, 0) -> 0.0``
*********************************
<code>avg</code>
==============================
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
這個運算式會取得資料行值的平均值。
* ``avg(sales) -> 7523420.234``
*********************************
<code>avgIf</code>
==============================
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
根據準則而定，這個運算式會取得資料行值的平均值。
* ``avgIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>byName</code>
==============================
<code><b>byName(<i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
此運算式會選取資料行值的資料流中的名稱。 如果有多個相符項目，則會傳回第一個相符項目。 如果沒有相符項目，則運算式會傳回 NULL 值。 傳回的值必須是型別轉換的其中一個類型轉換函式 （TO_DATE、 TO_STRING...）。在設計階段已知的資料行名稱應該解決只是根據其名稱。 不支援計算的輸入，但您可以使用參數的替代項目。

* ``toString(byName('parent')) -> appa``
* ``toLong(byName('income')) -> 9000000000009``
* ``toBoolean(byName('foster')) -> false``
* ``toLong(byName($debtCol)) -> 123456890``
* ``birthDate -> 12/31/2050``
* ``toString(byName('Bogus Column')) -> NULL``
*********************************
<code>byPosition</code>
==============================
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
此運算式會選取資料流中的資料行值的相對位置 （以 1 為基礎）。 如果位置超出範圍時，它會傳回 NULL 值。 傳回的值必須是型別轉換的其中一個類型轉換函式 （TO_DATE、 TO_STRING，等等）。 不支援計算的輸入，但您可以使用參數的替代項目。

* ``toString(byPosition(1)) -> amma``
* ``toDecimal(byPosition(2), 10, 2) -> 199990.99``
* ``toBoolean(byName(4)) -> false``
* ``toString(byName($colName)) -> family``
* ``toString(byPosition(1234)) -> NULL``
*********************************
<code>case</code>
==============================
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
根據替代條件，這個運算式會套用或另一個值。 
* ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``

如果輸入數目為偶數，另一個值會是 NULL，最後一個條件。
*********************************
<code>cbrt</code>
==============================
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會計算數字的立方根。
* ``cbrt(8) -> 2.0``
*********************************
<code>ceil</code>
==============================
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
這個運算式會傳回不小於數字的最小整數。
* ``ceil(-0.1) -> 0``
*********************************
<code>concat</code>
==============================
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
這個運算式會串連字串變數的數目。 **Concat**操作員是相同**+** 運算子與字串。
* ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
*********************************
<code>concatWS</code>
==============================
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
這個運算式會串聯的變數與分隔符號的字串數目。 第一個參數是分隔符號。
* ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) ->``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
*********************************
<code>cos</code>
==============================
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會計算餘弦值。
* ``cos(10) -> -0.83907152907``
*********************************
<code>cosh</code>
==============================
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會計算數值的雙曲餘弦值。
* ``cosh(0) -> 1.0``
*********************************
<code>count</code>
==============================
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
這個運算式取得值的彙總計數。 如果選擇性的資料行或資料行指定，NULL 會忽略在計數中的值。
* ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
*********************************
<code>countDistinct</code>
==============================
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
這個運算式會取得一組資料行的相異值的彙總計數。
* ``countDistinct(custId, custName) -> 60``
*********************************
<code>countIf</code>
==============================
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
根據準則而定，這個運算式會取得值的彙總計數。 如果選擇性的資料行指定的 NULL 會忽略在計數中的值。
* ``countIf(state == 'CA' && commission < 10000, name) -> 100``
*********************************
<code>covariancePopulation</code>
==============================
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會取得兩個資料行之間的母體共變數。
* ``covariancePopulation(sales, profit) -> 122.12``
*********************************
<code>covariancePopulationIf</code>
==============================
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
根據準則而定，這個運算式會取得兩個資料行的母體共變數。
* ``covariancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>covarianceSample</code>
==============================
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會取得兩個資料行的樣本共變數。
* ``covarianceSample(sales, profit) -> 122.12``
*********************************
<code>covarianceSampleIf</code>
==============================
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
根據準則而定，這個運算式會取得兩個資料行的樣本共變數。
* ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
*********************************
<code>crc32</code>
==============================
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
這個運算式會計算一組不同的基本資料類型，指定其值可為唯一 0(256)，位元長度的資料行的 CRC32 雜湊 224、 256、 384、 512。 您可以使用**crc32**運算子來計算的資料列的指紋。
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
*********************************
<code>cumeDist</code>
==============================
<code><b>cumeDist() => integer</b></code><br/><br/>
此函數會計算相對於所有值之值的資料分割中的位置。 結果會是上述或依順序排列的資料分割中，然後再除以視窗分割區中的資料列的總數等於目前的資料列的資料列數目。 在此順序中的任何繫結值會傳回相同的位置。
* ``cumeDist() -> 1``
*********************************
<code>currentDate</code>
==============================
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
這個運算式會取得目前的日期，若要執行的作業啟動時執行。 
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``

您可以傳遞選擇性時區的形式`'GMT'`， `'PST'`， `'UTC'`， `'America/Cayman'`。 預設值為當地時區。
*********************************
<code>currentTimestamp</code>
==============================
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
使用本地時區執行的工作開始時，這個運算式會取得目前的時間戳記。
* ``currentTimestamp() -> 12-12-2030T12:12:12``
*********************************
<code>currentUTC</code>
==============================
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
這個運算式取得目前的時間戳記為 UTC。 
* ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``

您可以傳遞選擇性時區的形式`'GMT'`， `'PST'`， `'UTC'`， `'America/Cayman'`。 預設值為當地時區。
*********************************
<code>dayOfMonth</code>
==============================
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定日期，這個運算式會取得月份天數。
* ``dayOfMonth(toDate('2018-06-08')) -> 08``
*********************************
<code>dayOfWeek</code>
==============================
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定日期，這個運算式會取得一週的星期幾。 
* ``dayOfWeek(toDate('2018-06-08')) -> 7``

日期值如下所示：
- 1-星期日
- 2-星期一 
- ...
- 7-星期六
*********************************
<code>dayOfYear</code>
==============================
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定日期，這個運算式會取得的一年天數。
* ``dayOfYear(toDate('2016-04-09')) -> 100``
*********************************
<code>degrees</code>
==============================
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會將弧度轉換為度。
* ``degrees(3.141592653589793) -> 180``
*********************************
<code>denseRank</code>
==============================
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
這個運算式會計算值的群組中值的陣序規範。 依照分割區的排序，將目前資料列 (含此列) 之前的列數加一，即為結果。 值將不會產生序列中的間距。 
* ``denseRank(salesQtr, salesAmt) -> 1``

**DenseRank**運算子可甚至當資料在未排序。 它會尋找在值中的變更。
*********************************
<code>divide</code>
==============================
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
這個運算式會將數字配對。 **劃分**操作員是相同**/** 運算子。
* ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
*********************************
<code>endsWith</code>
==============================
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
這個運算式會檢查字串結尾是否為提供的字串。
* ``endsWith('great', 'eat') -> true``
*********************************
<code>equals</code>
==============================
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
這是比較等於運算子。 它等同於**==** 運算子。
* ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'bad'=='bad' -> true``
* ``'good'== NULL -> false``
* ``NULL===NULL -> false``
*********************************
<code>equalsIgnoreCase</code>
==============================
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
**EqualsIgnoreCase**運算子是忽略大小寫的比較等於運算子。 它等同於**<=>** 運算子。
* ``'abc'=='abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
*********************************
<code>factorial</code>
==============================
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
這個運算式會計算數字的階乘。
* ``factorial(5) -> 120``
*********************************
<code>false</code>
==============================
<code><b>false() => boolean</b></code><br/><br/>
這個運算式一律會傳回 false 的值。 
* ``isDiscounted == false()``
* ``isDiscounted() == false``

使用`syntax(false())`函式，如果資料行的名稱為*false*。
*********************************
<code>first</code>
==============================
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
這個運算式會取得資料行群組的第一個值。 如果您省略第二個參數`ignoreNulls`，它會假設為 false。
* ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
*********************************
<code>floor</code>
==============================
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
這個運算式會傳回數字小於或等於最大整數。
* ``floor(-0.1) -> -1``
*********************************
<code>fromUTC</code>
==============================
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
這個運算式會將與 utc 之間的時間戳記。 您可以選擇性的形式傳遞的時區`'GMT'`， `'PST'`， `'UTC'`， `'America/Cayman'`。 預設為目前的時區。

* ``fromUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
<code>greater</code>
==============================
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
這是比較**大於**運算子。 它等同於**>** 運算子。
* ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
*********************************
<code>greaterOrEqual</code>
==============================
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
這是比較大於或等於運算子。 這位操作員便相同**>=** 運算子。
* ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
*********************************
<code>greatest</code>
==============================
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
這個運算式會傳回做為輸入的值清單上的最大值。 如果所有輸入都是 NULL，則會傳回 NULL。
* ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
*********************************
<code>hour</code>
==============================
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
這個運算式會取得時間戳記的小時值。 
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``

您可以傳遞選擇性時區的形式`'GMT'`， `'PST'`， `'UTC'`， `'America/Cayman'`。 預設值為當地時區。
*********************************
<code>iif</code>
==============================
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
根據條件，這個運算式會套用或另一個值。 如果未指定其他值，則會視為 NULL。 這兩個值必須是相容 （數值或字串，例如）。
* ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
*********************************
<code>in</code>
==============================
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
這個運算式會檢查陣列中的項目。
* ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
*********************************
<code>initCap</code>
==============================
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
這個運算式會將每個單字的第一個字母轉換成大寫。 識別泛空白字元分隔字組。
* ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
*********************************
<code>instr</code>
==============================
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
這個運算式會尋找在字串內子字串的位置 （以 1 為基礎）。 如果找不到位置，則會傳回 0。 
* ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
*********************************
<code>isDelete</code>
==============================
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
這個運算式會檢查標示為刪除的資料列。 
* ``isDelete() -> true``
* ``isDelete(1) -> false``

如果您的轉型會接受一個以上的輸入資料流，您可以傳遞之資料流 （以 1 起始） 索引。 資料流索引的預設值為 1。
*********************************
<code>isError</code>
==============================
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
這個運算式會檢查資料列標示為錯誤。
* ``isError() -> true``
* ``isError(1) -> false``

如果您的轉型會接受一個以上的輸入資料流，您可以傳遞之資料流 （以 1 起始） 索引。 資料流索引的預設值為 1。
*********************************
<code>isIgnore</code>
==============================
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
這個運算式會檢查資料列標示為忽略。
* ``isIgnore() -> true``
* ``isIgnore(1) -> false``

如果您的轉型會接受一個以上的輸入資料流，您可以傳遞之資料流 （以 1 起始） 索引。 資料流索引的預設值為 1。
*********************************
<code>isInsert</code>
==============================
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
這個運算式會檢查標示為要插入的資料列。 
* ``isInsert() -> true``
* ``isInsert(1) -> false``

如果您的轉型會接受一個以上的輸入資料流，您可以傳遞之資料流 （以 1 起始） 索引。 資料流索引的預設值為 1。
*********************************
<code>isMatch</code>
==============================
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
這個運算式會檢查在查閱比對的資料列。 
* ``isMatch() -> true``
* ``isMatch(1) -> false``

如果您的轉型會接受一個以上的輸入資料流，您可以傳遞之資料流 （以 1 起始） 索引。 資料流索引的預設值為 1。
*********************************
<code>isNull</code>
==============================
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
這個運算式會檢查其值為 NULL。
* ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
*********************************
<code>isUpdate</code>
==============================
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
這個運算式會檢查標示為更新的資料列。 
* ``isUpdate() -> true``
* ``isUpdate(1) -> false``

如果您的轉型會接受一個以上的輸入資料流，您可以傳遞之資料流 （以 1 起始） 索引。 資料流索引的預設值為 1。
*********************************
<code>kurtosis</code>
==============================
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會取得資料行的峰值。
* ``kurtosis(sales) -> 122.12``
*********************************
<code>kurtosisIf</code>
==============================
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據準則而定，這個運算式會取得資料行的峰值。
* ``kurtosisIf(region == 'West', sales) -> 122.12``
*********************************
<code>lag</code>
==============================
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
這個運算式取得評估的第一個參數的值*n*目前資料列之前的資料列。 第二個參數是要回溯查看的資料列數目。 預設值為 1。 如果沒有多個資料列，除非指定預設值，會傳回 NULL 值。
* ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
*********************************
<code>last</code>
==============================
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
這個運算式會取得資料行群組的最後一個值。 如果您省略第二個參數`ignoreNulls`，則運算式會傳回`false`。
* ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
*********************************
<code>lastDayOfMonth</code>
==============================
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
指定日期，這個運算式會取得每月的最後一個日期。
* ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
*********************************
<code>lead</code>
==============================
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
這個運算式取得評估的第一個參數的值*n*目前資料列之後的資料列。 第二個參數是要正向查看的資料列數目。 預設值為 1。 如果沒有多個資料列，除非指定預設值，會傳回 NULL 值。
* ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
*********************************
<code>least</code>
==============================
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
這是比較小於或等於運算子。 它等同於**<=** 運算子。
* ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
*********************************
<code>left</code>
==============================
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
這個運算式會擷取子字串開始，位於索引 1，並使用的字元數。 **左**操作員是相同**子字串 (str，1，n)**。
* ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
*********************************
<code>length</code>
==============================
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
這個運算式會傳回字串的長度。
* ``length('kiddo') -> 5``
*********************************
<code>lesser</code>
==============================
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
這是小於比較的運算子。 它等同於**<** 運算子。
* ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
*********************************
<code>lesserOrEqual</code>
==============================
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
這是比較小於或等於運算子。 它等同於**<=** 運算子。
* ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
*********************************
<code>levenshtein</code>
==============================
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
這個運算式會取得兩個字串之間的 Levenshtein 距離。
* ``levenshtein('boys', 'girls') -> 4``
*********************************
<code>like</code>
==============================
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
在此運算式中，模式是會直接比對的字串。 
* ``like('icecream', 'ice%') -> true``

例外狀況為下列特殊符號：  
* `_` 這比對輸入中的任何單一字元。 類似於`.`POSIX 規則運算式中。
* `%` 這比對零或多個輸入中的字元。 這個符號是類似於`.*`POSIX 規則運算式中。
* `''` 這是逸出字元。 如果特殊符號或另一個逸出字元前面有逸出字元，就會逐字比對下列字元。 您無法逸出任何其他字元。
*********************************
<code>locate</code>
==============================
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
這個運算式會尋找特定的位置開始的字串內子字串的位置 （以 1 為基礎）。 如果您省略的位置，評估會從字串的開頭。 如果找不到位置，則會傳回 0。 
* ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
*********************************
<code>log</code>
==============================
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
這個運算式會計算記錄值。 您可以提供選擇性基底或歐拉數字，如果使用它。
* ``log(100, 10) -> 2``
*********************************
<code>log10</code>
==============================
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此運算式會使用基底為 10，若要計算記錄值。
* ``log10(100) -> 2``
*********************************
<code>lower</code>
==============================
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
此運算式會設定以小寫的字串。
* ``lower('GunChus') -> 'gunchus'``
*********************************
<code>lpad</code>
==============================
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
使用提供的填補，這個運算式左側填補字串直到字傳達到特定長度。 如果字串為等於或大於的長度，它將其視為任何作業 （無作業）。
* ``lpad('great', 10, '-') -> '-----great'``
* ``lpad('great', 4, '-') -> 'great'``
* ``lpad('great', 8, '<>') -> '<><great'``
*********************************
<code>ltrim</code>
==============================
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
這個運算式左邊修剪前置字元的字串。 如果未指定第二個參數，則運算式會修剪空白字元。 否則，它會修剪第二個參數指定的字元。
* ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
*********************************
<code>max</code>
==============================
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
這個運算式會取得資料行的最大值。
* ``MAX(sales) -> 12312131.12``
*********************************
<code>maxIf</code>
==============================
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
根據準則而定，這個運算式會取得資料行的最大值。
* ``maxIf(region == 'West', sales) -> 99999.56``
*********************************
<code>md5</code>
==============================
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
這個運算式會計算一組不同的基本資料類型的資料行的 MD5 摘要。 它會傳回 32 個字元的十六進位字串。 
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``

您可以使用**md5**運算子來計算的資料列的指紋。
*********************************
<code>mean</code>
==============================
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
這個運算式會取得資料行值的平均值。 **表示**運算子等同於 AVG.
* ``mean(sales) -> 7523420.234``
*********************************
<code>meanIf</code>
==============================
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
根據準則而定，這個運算式會取得的資料行值的平均值。 **MeanIf**操作員是相同**avgIf**。
* ``meanIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>min</code>
==============================
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
這個運算式會取得資料行的最小值。
* ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
*********************************
<code>minIf</code>
==============================
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
根據準則而定，這個運算式會取得資料行的最小值。
* ``minIf(region == 'West', sales) -> 00.01``
*********************************
<code>minus</code>
==============================
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
這個運算式個數字相減。 比方說，它也可以減去日期的天數。 **減**操作員是相同**-** 運算子。
* ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
*********************************
<code>minute</code>
==============================
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
這個運算式會取得每分鐘的時間戳記值。 
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``

您可以傳遞選擇性時區的形式`'GMT'`， `'PST'`， `'UTC'`， `'America/Cayman'`。 預設值為當地時區。
*********************************
<code>mod</code>
==============================
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
**Mod**運算子標記數字模數配對。 這是相同**%** 運算子。
* ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
*********************************
<code>month</code>
==============================
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
這個運算式會取得日期或時間戳記的月份值。
* ``month(toDate('2012-8-8')) -> 8``
*********************************
<code>monthsBetween</code>
==============================
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
這個運算式會取得兩個日期之間的月數。 
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``

您可以傳遞選擇性時區的形式`'GMT'`， `'PST'`， `'UTC'`， `'America/Cayman'`。 預設值為當地時區。
*********************************
<code>multiply</code>
==============================
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
此運算式會將數字配對。 **乘**運算子等同於 * 運算子。
* ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
*********************************
<code>nTile</code>
==============================
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
**NTile**函式會將每個視窗資料分割成的資料列*n*貯體，範圍從 1 到最多*n*。 最多 1 不同貯體值。 如果分割區中的資料列數目不平均分配到貯體數目，每個其餘的值會被散發至貯體中，從第一個貯體。 

* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``

**NTile**函數相當實用，當您需要計算 tertiles、 四分位，十分位數和其他常見的摘要統計資料。 函式會計算兩個變數，在初始化期間。 一個額外的資料列會加入到規則的貯體。 這兩個變數根據目前的資料分割的大小。 

在計算時，目前的資料列數目、 目前的值區數目和資料列數目的值區會變更 (bucketThreshold)，會追蹤的函式。 當資料列數目達到貯體臨界值時，值區值會增加 1。 臨界值會增加貯體大小，加上一個額外如果目前的值區會填補。
*********************************
<code>negate</code>
==============================
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
這個運算式變換正負號數字。 正數會變成負數，反之亦然。
* ``negate(13) -> -13``
*********************************
<code>nextSequence</code>
==============================
<code><b>nextSequence() => long</b></code><br/><br/>
這個運算式會傳回下一個唯一的序列。 數字是只在資料分割內的連續項目。 前面加上`partitionId`。
* ``nextSequence() -> 12313112``
*********************************
<code>normalize</code>
==============================
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
標準化來區隔重音符號的 unicode 字元的字串值 * ``normalize('boys') -> 'boys'``
*********************************
<code>not</code>
==============================
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
**不**運算子是一個邏輯負運算子。
* ``not(true) -> false``
* ``not(premium)``
*********************************
<code>notEquals</code>
==============================
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
**NotEquals**運算子是比較不等於運算子。 它等同於 **！ =** 運算子。
* ``12!=24 -> true``
* ``'abc'!='abc' -> false``
*********************************
<code>null</code>
==============================
<code><b>null() => null</b></code><br/><br/>
這個運算式會傳回 NULL 值。 
* ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``

使用函式**syntax(null())** 如果資料行的名稱為*null*。 使用 NULL 運算子的任何作業會導致 NULL。
*********************************
<code>or</code>
==============================
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
**或是**運算子是邏輯**OR**運算子。 它等同於**||** 運算子。
* ``or(true, false) -> true``
* ``true || false -> true``
*********************************
<code>pMod</code>
==============================
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
**PMod**運算子標記正模數的數字配對。
* ``pmod(-20, 8) -> 4``
*********************************
<code>power</code>
==============================
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會引發另一個數的字。
* ``power(10, 2) -> 100``
*********************************
<code>rank</code>
==============================
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
這個運算式會計算值的群組中值的陣序規範。 依照分割區的排序，將目前資料列 (含此列) 之前的列數加一，即為結果。 的值會產生序列中的間距。 
* ``rank(salesQtr, salesAmt) -> 1``

**陣序規範**運算子可甚至當資料在未排序。 它會尋找在值中的變更。
*********************************
<code>regexExtract</code>
==============================
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
此運算式會擷取相符的子字串，指定的 regex 模式。 最後一個參數識別的相符項目。 如果您省略的最後一個參數，預設值為 1。 
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``

使用`<regex>`（後引號） 來比對的字串，而不需要逸出。
*********************************
<code>regexMatch</code>
==============================
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
這個運算式會檢查字串是否符合指定的 regex 模式。 
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``

使用`<regex>`（後引號） 來比對的字串，而不需要逸出。
*********************************
<code>regexReplace</code>
==============================
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
這個運算式以指定字串中的另一個子字串取代所有出現的 regex 模式。
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``

使用`<regex>`（後引號） 來比對的字串，而不需要逸出。
*********************************
<code>regexSplit</code>
==============================
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
這個運算式會根據根據 regex 分隔符號將字串分割。 它會傳回字串陣列。
* ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
*********************************
<code>replace</code>
==============================
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
這個運算式會將所有出現的子字串都取代指定的字串中的另一個子字串。
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
*********************************
<code>reverse</code>
==============================
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
這個運算式會反轉的字串。
* ``reverse('gunchus') -> 'suhcnug'``
*********************************
<code>right</code>
==============================
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
這個運算式會從右邊擷取子字串的字元數。 
* ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``

**右**函式是子字串 (str，LENGTH(str)-n，n) 相同。
*********************************
<code>rlike</code>
==============================
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
這個運算式會檢查字串是否符合指定的 regex 模式。
* ``rlike('200.50', '(\d+).(\d+)') -> true``
*********************************
<code>round</code>
==============================
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
有選用的小數位數和選擇性的捨入模式，此運算式將數字四捨五入。 如果您省略小數位數，預設值為 0。 如果您省略模式時，預設會為 ROUND_HALF_UP(5)。 

* ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``

這些是四捨五入的值：
* 1-ROUND_UP
* 2-ROUND_DOWN
* 3-ROUND_CEILING
* 4-ROUND_FLOOR
* 5-ROUND_HALF_UP
* 6 - ROUND_HALF_DOWN
* 7 - ROUND_HALF_EVEN
* 8-ROUND_UNNECESSARY

*********************************
<code>rowNumber</code>
==============================
<code><b>rowNumber() => integer</b></code><br/><br/>
這個運算式會將指派的循序資料列的資料列在視窗中，從 1 開始編號。
* ``rowNumber() -> 1``
*********************************
<code>rpad</code>
==============================
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
此運算式右側填補字串是由提供的邊框距離字串直到達到特定長度。 如果字串為等於或大於的長度，它將其視為任何作業 （無作業）。
* ``rpad('great', 10, '-') -> 'great-----'``
* ``rpad('great', 4, '-') -> 'great'``
* ``rpad('great', 8, '<>') -> 'great<><'``
*********************************
<code>rtrim</code>rtrim</code>
==============================
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
這個運算式右邊修剪前置字元的字串。 如果您未指定第二個參數，則運算式會修剪空白字元。 否則，它會修剪第二個參數指定的任何字元。
* ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
*********************************
<code>second</code>
==============================
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
這個運算式會取得第二個日期的值。 
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``

您可以傳遞選擇性時區的形式`'GMT'`， `'PST'`， `'UTC'`， `'America/Cayman'`。 預設值為當地時區。
*********************************
<code>sha1</code>
==============================
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
這個運算式會計算 sha-1 摘要的一組不同的基本資料類型的資料行。 它會傳回為 40 個字元的十六進位字串。 
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``

您可以使用`sha1`來計算的資料列的指紋。
*********************************
<code>sha2</code>
==============================
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
這個運算式會計算一組不同的基本資料類型，指定其值可以是只 0(256) 位元長度的資料行的 sha-2 摘要 224、 256、 384、 512。 
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``

您可以使用`sha2`來計算的資料列的指紋。
*********************************
<code>sin</code>
==============================
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會計算正弦值。
* ``sin(2) -> 0.90929742682``
*********************************
<code>sinh</code>
==============================
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會計算雙曲正弦值。
* ``sinh(0) -> 0.0``
*********************************
<code>skewness</code>
==============================
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會取得資料行的偏態。
* ``skewness(sales) -> 122.12``
*********************************
<code>skewnessIf</code>
==============================
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據準則而定，這個運算式會取得資料行的偏態。
* ``skewnessIf(region == 'West', sales) -> 122.12``
*********************************
<code>slice</code>
==============================
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
這個運算式會擷取位置的陣列子集。 位置是以 1 為基礎。 如果您省略長度時，預設值是字串的結尾。
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
*********************************
<code>soundex</code>
==============================
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
這個運算式會取得字串的 soundex 代碼。
* ``soundex('genius') -> 'G520'``
*********************************
<code>split</code>
==============================
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
這個運算式會根據分隔符號將字串分割。 它會傳回字串陣列。
* ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
*********************************
<code>sqrt</code>
==============================
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會計算數字的平方根。
* ``sqrt(9) -> 3``
*********************************
<code>startsWith</code>
==============================
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
這個運算式會檢查字串是否要開始使用提供的字串。
* ``startsWith('great', 'gr') -> true``
*********************************
<code>stddev</code>
==============================
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會取得資料行的標準差。
* ``stdDev(sales) -> 122.12``
*********************************
<code>stddevIf</code>
==============================
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據準則而定，這個運算式會取得資料行的標準差。
* ``stddevIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevPopulation</code>
==============================
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會取得資料行的母體擴展標準差。
* ``stddevPopulation(sales) -> 122.12``
*********************************
<code>stddevPopulationIf</code>
==============================
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據準則而定，這個運算式會取得資料行的母體擴展標準差。
* ``stddevPopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevSample</code>
==============================
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會取得資料行的樣本標準差。
* ``stddevSample(sales) -> 122.12``
*********************************
<code>stddevSampleIf</code>
==============================
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據準則而定，這個運算式會取得資料行的樣本標準差。
* ``stddevSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>subDays</code>
==============================
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
這個運算式會減去日期的月份。 
* ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
**SubDays**操作員是相同**-** 日期的運算子。
*********************************
<code>subMonths</code>
==============================
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
這個運算式會減去月之前的日期或時間戳記。
* ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
*********************************
<code>substring</code>
==============================
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
這個運算式會將特定長度的子字串擷取的位置。 位置是以 1 為基礎。 如果您省略長度時，預設值是字串的結尾。
* ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
*********************************
<code>sum</code>
==============================
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
這個運算式會取得彙總總和的數值資料行。
* ``sum(col) -> value``
*********************************
<code>sumDistinct</code>
==============================
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
這個運算式會取得彙總的數值資料行的相異值的總和。
* ``sumDistinct(col) -> value``
*********************************
<code>sumDistinctIf</code>
==============================
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
根據準則而定，這個運算式會取得彙總總和的數值資料行。 您可以根據任何資料行的條件。
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales)``
*********************************
<code>sumIf</code>
==============================
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
根據準則而定，這個運算式會取得彙總總和的數值資料行。 您可以根據任何資料行的條件。
* ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales)``
*********************************
<code>tan</code>
==============================
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會計算正切函數的值。
* ``tan(0) -> 0.0``
*********************************
<code>tanh</code>
==============================
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會計算雙曲線正切函數值。
* ``tanh(0) -> 0.0``
*********************************
<code>toBoolean</code>
==============================
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
這個運算式會將值轉換`('t', 'true', 'y', 'yes', '1')`設為 true。 它會將轉換`('f', 'false', 'n', 'no', '0')`設為 false。 任何其他值，它會傳回 NULL。
* ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
*********************************
<code>toDate</code>
==============================
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
指定選擇性的日期格式，此運算式將字串轉換成日期。 如需所有可能的日期格式，請參閱 Java SimpleDateFormat。 
* ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``

如果您省略日期格式時，會接受下列組合: [yyyy，yyyy-[M] M、 yyyy-[M] M-[d] d，yyyy [M] M-[d] d，yyyy [M] M-[d] d，yyyy-[M] M-[d] dT *]。
*********************************
<code>toDecimal</code>
==============================
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : integral], [<i>&lt;value3&gt;</i> : integral], [<i>&lt;value4&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
此運算式會將任何數值或字串轉換成十進位值。 
* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``

如果您未指定有效位數和小數位數，預設值會是 (10,2)。 您可以使用選擇性的 Java 十進位格式進行轉換。

*********************************
<code>toDouble</code>
==============================
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => double</b></code><br/><br/>
此運算式會將任何數值或字串轉換為雙精度值。 您可以使用選擇性的 Java 十進位格式進行轉換。
* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
*********************************
<code>toFloat</code>
==============================
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => float</b></code><br/><br/>
這個運算式會將任何數值或字串轉換成浮點值。 您可以使用選擇性的 Java 十進位格式進行轉換。 
* ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``

**ToFloat**函式會截斷任何雙精度浮點數。
*********************************
<code>toInteger</code>
==============================
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => integer</b></code><br/><br/>
此運算式會將任何數值或字串轉換成整數值。 您可以使用選擇性的 Java 十進位格式進行轉換。 
* ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``

**ToInteger**函式會截斷任何 long、 float、 或 double。
*********************************
<code>toLong</code>
==============================
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => long</b></code><br/><br/>
此運算式會將任何數值或字串轉換為 long 值。 您可以使用選擇性的 Java 十進位格式進行轉換。 
* ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``

**ToLong**函式會截斷任何浮點數或雙精度浮點數。
*********************************
<code>toShort</code>
==============================
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => short</b></code><br/><br/>
這個運算式會將任何數值或字串轉換成短整數值。 您可以使用選擇性的 Java 十進位格式進行轉換。 
* ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``

**ToShort**函式會截斷任何整數、 long、 float、 或 double。
*********************************
<code>toString</code>
==============================
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
這個運算式會將基本資料類型轉換為字串。 
* ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``

您可以指定數字和日期的格式。 如果您未指定的格式，則會使用系統預設值。 預設的格式是`yyyy-MM-dd`。 數字會遵循 Java 十進位格式。 對於所有可能的日期格式，請參閱 Java SimpleDateFormat。 
*********************************
<code>toTimestamp</code>
==============================
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
指定選擇性的時間戳記格式，此運算式將字串轉換成日期。 
* ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``

如需所有可能的格式，請參閱 Java SimpleDateFormat。 如果您省略的時間戳記的預設模式`yyyy-[M]M-[d]d hh:mm:ss[.f...]`用。
*********************************
<code>toUTC</code>
==============================
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
這個運算式會將時間戳記轉換為 UTC。 
* ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``

您可以傳遞選擇性時區的形式`'GMT'`， `'PST'`， `'UTC'`， `'America/Cayman'`。 預設為目前的時區。
*********************************
<code>translate</code>
==============================
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
這個運算式會取代另一組字元在字串中的一組字元。 字元會有一對一的取代。
* ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
*********************************
<code>trim</code>
==============================
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
這個運算式會修剪前置和後置字元的字串。 
* ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``

如果您未指定第二個參數，此函式會修剪空白字元。 否則，它會修剪第二個參數指定的任何字元。

*********************************
<code>true</code>
==============================
<code><b>true() => boolean</b></code><br/><br/>
這個運算式一律會傳回`true`值。  
* ``isDiscounted == true()``
* ``isDiscounted() == true``

如果資料行的名稱為*真*，使用函式**syntax(true())**。
*********************************
<code>typeMatch</code>
==============================
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
此運算式會比對資料行的類型。 
* ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``

只在模式運算式中使用此函式： 數目可符合簡短、 整數、 long、 double、 浮點數或十進位，整數類資料符合簡單地說，整數、 小數、 比對，double、 float、 decimal、 和 datetime 相符項目日期或時間戳記類型。
*********************************
<code>upper</code>
==============================
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
此運算式會設定為大寫的字串。
* ``upper('bojjus') -> 'BOJJUS'``
*********************************
<code>variance</code>
==============================
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會取得資料行的變異數。
* ``variance(sales) -> 122.12``
*********************************
<code>varianceIf</code>
==============================
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據準則而定，這個運算式會取得資料行的變異數。
* ``varianceIf(region == 'West', sales) -> 122.12``
*********************************
<code>variancePopulation</code>
==============================
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會取得資料行的母體擴展變異數。
* ``variancePopulation(sales) -> 122.12``
*********************************
<code>variancePopulationIf</code>
==============================
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據準則而定，這個運算式會取得資料行的母體擴展變異數。
* ``variancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>varianceSample</code>
==============================
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
這個運算式會取得資料行的非偏誤變異數。
* ``varianceSample(sales) -> 122.12``
*********************************
<code>varianceSampleIf</code>
==============================
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據準則而定，這個運算式會取得資料行的非偏誤變異數。
* ``varianceSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>weekOfYear</code>
==============================
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定日期，這個運算式會取得一年的週。
* ``weekOfYear(toDate('2008-02-20')) -> 8``
*********************************
<code>xor</code>
==============================
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
此運算式使用的邏輯**xor**運算子。 這位操作員便相同**^** 運算子。
* ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
*********************************
<code>year</code>
==============================
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定日期，這個運算式會取得的年份值。
* ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>後續步驟

[了解如何使用運算式產生器](concepts-data-flow-expression-builder.md)。
