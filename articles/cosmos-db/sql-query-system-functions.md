---
title: 系統函式
description: 深入了解 Azure Cosmos DB 中的 SQL 系統函數。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: 11a6fdad187670bcb5af4c56198fd7343680690d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342688"
---
# <a name="system-functions"></a>系統函式

 Cosmos DB 提供許多內建 SQL 函式。 內建函式的分類如下所示。  
  
|函式|描述|  
|--------------|-----------------|  
|[數學函式](#mathematical-functions)|每個數學函數都會執行計算，通常以提供做為引數的輸入值為基礎，並且會傳回數值。|  
|[類型檢查函式](#type-checking-functions)|類型檢查函數可讓您檢查 SQL 查詢中的運算式類型。|  
|[字串函式](#string-functions)|下列字串函式會對字串輸入值執行作業，並傳回字串、數值或布林值。|  
|[陣列函式](#array-functions)|下列陣列函式會對陣列輸入值執行作業，並傳回數值、布林值或陣列值。|
|[日期和時間函數](#date-time-functions)|日期和時間函數可讓您以兩種形式，取得目前的 UTC 日期和時間數值的時間戳記，其值為 Unix epoch 以來毫秒，或為符合 ISO 8601 格式的字串。|
|[空間函式](#spatial-functions)|下列空間函數會對空間物件輸入值執行作業，並傳回數值或布林值。|  

以下是每個分類內的函式的清單：

| 函式群組 | 作業 |
|---------|----------|
| 數學函數 | ABS、CEILING、EXP、FLOOR、LOG、LOG10、POWER、ROUND、SIGN、SQRT、SQUARE、TRUNC、ACOS、ASIN、ATAN、ATN2、COS、COT、DEGREES、PI、RADIANS、SIN、TAN |
| 類型檢查函式 | IS_ARRAY、IS_BOOL、IS_NULL、IS_NUMBER、IS_OBJECT、IS_STRING、IS_DEFINED、IS_PRIMITIVE |
| 字串函數 | CONCAT、CONTAINS、ENDSWITH、INDEX_OF、LEFT、LENGTH、LOWER、LTRIM、REPLACE、REPLICATE、REVERSE、RIGHT、RTRIM、STARTSWITH、SUBSTRING、UPPER |
| 陣列函數 | ARRAY_CONCAT、ARRAY_CONTAINS、ARRAY_LENGTH 和 ARRAY_SLICE |
| 日期和時間函數 | GETCURRENTDATETIME，GETCURRENTTIMESTAMP，  |
| 空間函數 | ST_DISTANCE、ST_WITHIN、ST_INTERSECTS、ST_ISVALID、ST_ISVALIDDETAILED |

如果您目前使用的使用者定義的函數 (UDF) 已提供內建函式，相對應的內建函式會加快執行速度且更有效率。

Cosmos DB 函式和 ANSI SQL 函式的主要差異是 Cosmos DB 函數的設計適用於無結構描述和混合的結構描述的資料。 比方說，如果屬性已遺失，或具有非數值例如`unknown`，項目會略過而不是傳回錯誤。

##  <a name="mathematical-functions"></a>數學函式  

每個數學函式都會執行計算，通常以提供來作為引數的輸入值為基礎，並傳回數值。

您可以執行查詢，如下列範例所示：

```sql
    SELECT VALUE ABS(-4)
```

結果如下：

```json
    [4]
```

以下是支援的內建數學函數資料表。
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[CEILING](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[DEGREES](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANS](#bk_radians)|[ROUND](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[SQUARE](#bk_square)|[SIGN](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 傳回指定之數值運算式的絕對 (正) 值。  
  
 **語法**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例顯示在三個不同的數字上使用 ABS 函式的結果。  
  
```  
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 以下為結果集。  
  
```  
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 傳回角度，以弧度為單位，它的餘弦是指定的數值運算式；也稱為反餘弦值。  
  
 **語法**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回 -1 的 ACOS。  
  
```  
SELECT ACOS(-1) AS acos 
```  
  
 以下為結果集。  
  
```  
[{"acos": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 傳回角度，以弧度為單位，其正弦函數是指定的數值運算式。 這也稱為反正弦值。  
  
 **語法**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回 -1 的 ASIN。  
  
```  
SELECT ASIN(-1) AS asin  
```  
  
 以下為結果集。  
  
```  
[{"asin": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 傳回角度，以弧度為單位，其正切函數是指定的數值運算式。 這也稱為反正切值。  
  
 **語法**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回指定值的 ATAN。  
  
```  
SELECT ATAN(-45.01) AS atan  
```  
  
 以下為結果集。  
  
```  
[{"atan": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 傳回 y/x 有向徑正切函數的主體值，以弧度表示。  
  
 **語法**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會計算指定 X 和 Y 元件的 ATN2 值。  
  
```  
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 以下為結果集。  
  
```  
[{"atn2": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 傳回大於或等於指定之數值運算式的最小整數值。  
  
 **語法**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例顯示搭配 CEILING 函式的正數、負數和零值。  
  
```  
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 以下為結果集。  
  
```  
[{c1: 124, c2: -123, c3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 在指定運算式中傳回指定角度的三角餘弦函數，以弧度為單位。  
  
 **語法**  
  
```  
COS(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會計算指定角度的 COS 值。  
  
```  
SELECT COS(14.78) AS cos  
```  
  
 以下為結果集。  
  
```  
[{"cos": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 在指定的數值運算式中傳回指定角度的三角餘切函數，以弧度為單位。  
  
 **語法**  
  
```  
COT(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會計算指定角度的 COS 值。  
  
```  
SELECT COT(124.1332) AS cot  
```  
  
 以下為結果集。  
  
```  
[{"cot": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 針對以弧度指定的角度，傳回對應的角度 (以度為單位)。  
  
 **語法**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回 PI/2 弧度的角度度數。  
  
```  
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 以下為結果集。  
  
```  
[{"degrees": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 傳回小於或等於指定之數值運算式的最大整數。  
  
 **語法**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例顯示搭配 FLOOR 函式的正數、負數和零值。  
  
```  
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 以下為結果集。  
  
```  
[{fl1: 123, fl2: -124, fl3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 傳回指定之數值運算式的指數值。  
  
 **語法**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **備註**  
  
  常數 **e** (2.718281…)，為自然對數的基礎。  
  
  數字的指數是常數**e**的數字乘冪。 例如 EXP(1.0) = e^1.0 = 2.71828182845905 和 EXP(10) = e^10 = 22026.4657948067。  
  
  數字的自然對數之指數為該數字本身：EXP (LOG (n)) = n。 且數字的指數之自然對數為該數字本身：LOG (EXP (n)) = n。  
  
  **範例**  
  
  下列範例會宣告一個變數，並傳回指定變數 (10) 的指數值。  
  
```  
SELECT EXP(10) AS exp  
```  
  
 以下為結果集。  
  
```  
[{exp: 22026.465794806718}]  
```  
  
 下列範例會傳回自然對數的指數值 20，以及指數的自然對數 20。 因為這些函式是彼此的反向函數，傳回值以浮點數學計算，四捨五入之後在這兩種情況均為 20。  
  
```  
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 以下為結果集。  
  
```  
[{exp1: 19.999999999999996, exp2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 傳回指定數值運算式的自然對數。  
  
 **語法**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
- `base`  
  
   選用數值引數會設定對數基數。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **備註**  
  
  依預設，LOG() 會傳回自然對數。 您可以使用選用基數參數，將對數基數變更為其他值。  
  
  自然對數為基數 **e** 的對數，其中 **e** 為無理常數，大約等於 2.718281828。  
  
  數字的指數之自然對數為該數字本身：LOG( EXP( n ) ) = n。 且數字的自然對數之指數為該數字本身：EXP( LOG( n ) ) = n。  
  
  **範例**  
  
  下列範例會宣告一個變數，並傳回指定變數 (10) 的對數值。  
  
```  
SELECT LOG(10) AS log  
```  
  
 以下為結果集。  
  
```  
[{log: 2.3025850929940459}]  
```  
  
 下列範例會計算數字指數的 LOG。  
  
```  
SELECT EXP(LOG(10)) AS expLog  
```  
  
 以下為結果集。  
  
```  
[{expLog: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 傳回指定之數值運算式的以 10 為基底的對數。  
  
 **語法**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **備註**  
  
  LOG10 和 POWER 函式彼此成反比相關。 例如，10 ^ LOG10(n) = n。  
  
  **範例**  
  
  下列範例會宣告一個變數，並傳回指定變數 (100) 的 LOG10 值。  
  
```  
SELECT LOG10(100) AS log10 
```  
  
 以下為結果集。  
  
```  
[{log10: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 傳回 PI 的常數值。  
  
 **語法**  
  
```  
PI ()  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回 PI 值。  
  
```  
SELECT PI() AS pi 
```  
  
 以下為結果集。  
  
```  
[{"pi": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 將指定之運算式的值傳回給指定的乘冪。  
  
 **語法**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
- `y`  
  
   為要提高至 `numeric_expression` 的乘冪。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例示範如何將乘冪數字提高到 3 (數字立方體)。  
  
```  
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 以下為結果集。  
  
```  
[{pow1: 8, pow2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 當您輸入數值運算式 (以度為單位) 時，傳回弧度。  
  
 **語法**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會使用幾個角度作為輸入，並傳回其對應的弧度值。  
  
```  
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  
  
  以下為結果集。  
  
```  
[{  
       "r1": -0.7855726963226477,  
       "r2": -3.1592204790349356,  
       "r3": 0,  
       "r4": 0.0025704127119236249,  
       "r5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 傳回數值，四捨五入到最接近的整數值。  
  
 **語法**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **備註**
  
  執行的捨入作業會遵循遠離零四捨五入的中間點。 如果輸入是介於兩個整數之間的確切的數值運算式的結果會是最接近的整數值，遠離零。  
  
  |<numeric_expression>|圓角|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0.5|1|
  |6.5000|7||
  
  **範例**  
  
  下列範例會將以下正數和負數數字四捨五入至最接近的整數。  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  以下為結果集。  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  
  
####  <a name="bk_sign"></a> SIGN  
 傳回指定之數值運算式的正數 (+1)、零 (0) 或負數 (-1) 符號。  
  
 **語法**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回從-2 到 2 的 SIGN 值數字。  
  
```  
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 以下為結果集。  
  
```  
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 在指定運算式中傳回指定角度的三角正弦函數 (以弧度為單位)。  
  
 **語法**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會計算指定角度的 SIN 值。  
  
```  
SELECT SIN(45.175643) AS sin  
```  
  
 以下為結果集。  
  
```  
[{"sin": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 傳回指定之數值的平方根。  
  
 **語法**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回數字 1 到 3 的平方根值。  
  
```  
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 以下為結果集。  
  
```  
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 傳回指定之數值的平方。  
  
 **語法**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回數字 1 到 3 的平方值。  
  
```  
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 以下為結果集。  
  
```  
[{s1: 1, s2: 4, s3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 在指定運算式中傳回指定角度的正切函式 (以弧度為單位)。  
  
 **語法**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會計算 PI()/2 的正切函數。  
  
```  
SELECT TAN(PI()/2) AS tan 
```  
  
 以下為結果集。  
  
```  
[{"tan": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 傳回數值，截斷至最接近的整數值。  
  
 **語法**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會將以下正數和負數數字截斷為最接近的整數。  
  
```  
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 以下為結果集。  
  
```  
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```

## <a id="type-checking-functions"></a>類型檢查函式

類型檢查函數可讓您檢查 SQL 查詢中的運算式類型。 若要判斷在即時的項目屬性的型別在變數或未知時，您可以使用類型檢查函式。 以下是支援的內建類型檢查函數的資料表：

下列函式支援針對輸入值檢查類型，並且會傳回布林值。  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 傳回布林值，表示指定之運算式的類型為陣列。  
  
 **語法**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **引數**  
  
- `expression`  
  
   為任何有效運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會使用 IS_ARRAY 函式，檢查 JSON 布林值的物件、數字、字串、Null、物件、陣列以及未定義的類型。  
  
```  
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 以下為結果集。  
  
```  
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 傳回布林值，表示指定之運算式的類型為布林值。  
  
 **語法**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **引數**  
  
- `expression`  
  
   為任何有效運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會使用 IS_BOOL 函式，檢查 JSON 布林值的物件、數字、字串、Null、物件、陣列以及未定義的類型。  
  
```  
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 以下為結果集。  
  
```  
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 傳回布林值，表示屬性是否已經指派值。  
  
 **語法**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **引數**  
  
- `expression`  
  
   為任何有效運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會檢查指定的 JSON 文件內的屬性是否存在。 第一次會傳回 True，因為出現了 "a"，但第二次就會傳回 False 因為 "b" 不存在。  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 以下為結果集。  
  
```  
[{"isDefined1":true,"isDefined2":false}]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 傳回布林值，表示指定之運算式的類型為 null。  
  
 **語法**  
  
```  
IS_NULL(<expression>)  
```  
  
 **引數**  
  
- `expression`  
  
   為任何有效運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會使用 IS_NULL 函式，檢查 JSON 布林值的物件、數字、字串、Null、物件、陣列以及未定義的類型。  
  
```  
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 以下為結果集。  
  
```  
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 傳回布林值，表示指定之運算式的類型為數字。  
  
 **語法**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **引數**  
  
- `expression`  
  
   為任何有效運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會使用 IS_NULL 函式，檢查 JSON 布林值的物件、數字、字串、Null、物件、陣列以及未定義的類型。  
  
```  
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 以下為結果集。  
  
```  
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 傳回布林值，表示指定之運算式的類型為 JSON 物件。  
  
 **語法**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **引數**  
  
- `expression`  
  
   為任何有效運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會使用 IS_OBJECT 函式，檢查 JSON 布林值的物件、數字、字串、Null、物件、陣列以及未定義的類型。  
  
```  
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 以下為結果集。  
  
```  
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 傳回布林值，表示指定之運算式的類型為基本類型 (字串、布林值、數值或 Null)。  
  
 **語法**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **引數**  
  
- `expression`  
  
   為任何有效運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會使用 IS_PRIMITIVE 函式，檢查 JSON 布林值的物件、數字、字串、Null、物件、陣列以及未定義的類型。  
  
```  
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 以下為結果集。  
  
```  
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 傳回布林值，表示指定之運算式的類型為字串。  
  
 **語法**  
  
```  
IS_STRING(<expression>)  
```  
  
 **引數**  
  
- `expression`  
  
   為任何有效運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會使用 IS_STRING 函式，檢查 JSON 布林值的物件、數字、字串、Null、物件、陣列以及未定義的類型。  
  
```  
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 以下為結果集。  
  
```  
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a id="string-functions"></a>字串函式

下列純量函數會對字串輸入值執行作業，並傳回字串、 數值或布林值：
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[LENGTH](#bk_length)|  
|[LOWER](#bk_lower)|[LTRIM](#bk_ltrim)|[REPLACE](#bk_replace)|  
|[REPLICATE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[SUBSTRING](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[UPPER](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 傳回字串，該字串是串連兩個或多個字串值的結果。  
  
 **語法**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例會傳回指定值的串連字串。  
  
```  
SELECT CONCAT("abc", "def") AS concat  
```  
  
 以下為結果集。  
  
```  
[{"concat": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 傳回布林值，表示第一個字串運算式是否包含第二個字串運算式。  
  
 **語法**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會檢查 "abc" 是否包含 "ab" 及 "d"。  
  
```  
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 以下為結果集。  
  
```  
[{"c1": true, "c2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 傳回布林值，表示第一個字串運算式是否以第二個字串運算式結尾。  
  
 **語法**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會傳回以 "b" 或 "bc" 結尾的 "abc"。  
  
```  
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 以下為結果集。  
  
```  
[{"e1": false, "e2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 傳回第一個指定的字串運算式中，第二個字串運算式第一次出現的開始位置，或者如果找不到字串，則為 -1。  
  
 **語法**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回 "abc" 內各種子字串的索引。  
  
```  
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 以下為結果集。  
  
```  
[{"i1": 0, "i2": 1, "i3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 傳回具有指定字元數目的字串左側部分。  
  
 **語法**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
- `num_expr`  
  
   為任何有效的數值運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例會傳回 "abc"左側部分的各種長度值。  
  
```  
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 以下為結果集。  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  
####  <a name="bk_length"></a> LENGTH  
 傳回指定字串運算式的字元數目。  
  
 **語法**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例會傳回字串的長度。  
  
```  
SELECT LENGTH("abc") AS len 
```  
  
 以下為結果集。  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 傳回將大寫字元資料轉換成小寫之後的字串運算式。  
  
 **語法**  
  
```  
LOWER(<str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例示範如何在查詢中使用 LOWER。  
  
```  
SELECT LOWER("Abc") AS lower
```  
  
 以下為結果集。  
  
```  
[{"lower": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 傳回移除開頭空白之後的字串運算式。  
  
 **語法**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例示範如何在查詢中使用 LTRIM。  
  
```  
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 以下為結果集。  
  
```  
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> REPLACE  
 使用其他字串值取代指定的字串值的所有項目。  
  
 **語法**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例示範如何在查詢中使用 REPLACE。  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 以下為結果集。  
  
```  
[{"replace": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 將字串值重複指定的次數。  
  
 **語法**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
- `num_expr`  
  
   為任何有效的數值運算式。 如果 num_expr 為負數為非有限，則結果為未定義。

  > [!NOTE]
  > 結果的最大長度為 10,000 個字元，亦即 (length(str_expr)  *  num_expr) <= 10,000。
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例示範如何在查詢中使用 REPLICATE。  
  
```  
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 以下為結果集。  
  
```  
[{"replicate": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 傳回反向順序的字串值。  
  
 **語法**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例示範如何在查詢中使用 REVERSE。  
  
```  
SELECT REVERSE("Abc") AS reverse  
```  
  
 以下為結果集。  
  
```  
[{"reverse": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 傳回具有指定字元數目的字串右側部分。  
  
 **語法**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
- `num_expr`  
  
   為任何有效的數值運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例會傳回 "abc"右側部分的各種長度值。  
  
```  
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 以下為結果集。  
  
```  
[{"r1": "c", "r2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 傳回移除結尾空白之後的字串運算式。  
  
 **語法**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例示範如何在查詢中使用 RTRIM。  
  
```  
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 以下為結果集。  
  
```  
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 傳回布林值，表示第一個字串運算式是否以第二個字串運算式開頭。  
  
 **語法**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會檢查字串 "abc" 是否以 "b" 和 "a" 開頭。  
  
```  
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 以下為結果集。  
  
```  
[{"s1": false, "s2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 傳回轉譯成陣列的運算式。 如果運算式無法轉譯，會傳回未定義。  
  
 **語法**  
  
```  
StringToArray(<expr>)  
```  
  
 **引數**  
  
- `expr`  
  
   是任何有效的純量運算式評估為 JSON 陣列運算式。 請注意，必須為有效的雙引號括住寫入巢狀的字串值。 如需 JSON 格式的詳細資訊，請參閱[json.org](https://json.org/)
  
  **傳回類型**  
  
  傳回陣列運算式，或未定義。  
  
  **範例**  
  
  下列範例會示範 StringToArray 跨不同類型的運作方式。 
  
 以下是範例，以有效的輸入。

```
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

以下為結果集。

```
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

以下是輸入的無效的範例。 
   
 陣列中的單引號不是有效的 JSON。
即使是在查詢中有效，它們不會剖析有效的陣列。 字串陣列字串內必須是逸出"[\\"\\"]"或周圍的引號必須是單一 ' [""]'。

```
SELECT
    StringToArray("['5','6','7']")
```

以下為結果集。

```
[{}]
```

以下是輸入的範例無效。
   
 傳遞的運算式會剖析為 JSON 陣列;下列不會評估輸入陣列，並因此會傳回未定義。
   
```
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

以下為結果集。

```
[{}]
```

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 傳回轉譯成布林值的運算式。 如果運算式無法轉譯，會傳回未定義。  
  
 **語法**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **引數**  
  
- `expr`  
  
   是任何有效的純量運算式，才能評估為布林的運算式。  
  
  **傳回類型**  
  
  傳回布林運算式，或未定義。  
  
  **範例**  
  
  下列範例會示範 StringToBoolean 跨不同類型的運作方式。 
 
 以下是範例，以有效的輸入。

只有之前或之後"true"/"false"，將允許空白字元。

```  
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 以下為結果集。  
  
```  
[{"b1": true, "b2": false, "b3": false}]
```  

以下是範例具有無效的輸入。

 布林值會區分大小寫，而且必須寫入所有小寫字元，也就是"true"和"false"。

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

以下為結果集。  
  
```  
[{}]
``` 

傳遞的運算式會剖析為布林的運算式;這些輸入不會評估輸入布林值，並因此會傳回未定義。

```  
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

以下為結果集。  
  
```  
[{}]
```  

####  <a name="bk_stringtonull"></a> StringToNull  
 傳回轉譯成 null 的運算式。 如果運算式無法轉譯，會傳回未定義。  
  
 **語法**  
  
```  
StringToNull(<expr>)  
```  
  
 **引數**  
  
- `expr`  
  
   是任何有效的純量運算式，才能評估為 null 的運算式。
  
  **傳回類型**  
  
  傳回 null 的運算式，或未定義。  
  
  **範例**  
  
  下列範例會示範 StringToNull 跨不同類型的運作方式。 

以下是範例，以有效的輸入。

 只有之前或之後"null"，將允許空白字元。

```  
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 以下為結果集。  
  
```  
[{"n1": null, "n2": null, "n3": true}]
```  

以下是範例具有無效的輸入。

Null 會區分大小寫，而且必須寫入所有小寫字元也就是 「 null 的 」。

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 以下為結果集。  
  
```  
[{}]
```  

傳遞的運算式會剖析為 null 的運算式;這些輸入不會評估輸入 null，因此會傳回未定義。

```  
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 以下為結果集。  
  
```  
[{}]
```  

####  <a name="bk_stringtonumber"></a> StringToNumber  
 傳回轉譯成數字的運算式。 如果運算式無法轉譯，會傳回未定義。  
  
 **語法**  
  
```  
StringToNumber(<expr>)  
```  
  
 **引數**  
  
- `expr`  
  
   是任何有效的純量運算式評估為 JSON 數字運算式。 在 JSON 中的數字必須是整數或浮點數。 如需 JSON 格式的詳細資訊，請參閱[json.org](https://json.org/)  
  
  **傳回類型**  
  
  傳回數字的運算式，或未定義。  
  
  **範例**  
  
  下列範例會示範 StringToNumber 跨不同類型的運作方式。 

只有之前或之後的數字，將允許空白字元。

```  
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 以下為結果集。  
  
```  
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

在 JSON 中必須是有效的數字是整數或浮點數。

```  
SELECT   
    StringToNumber("0xF")
```  
  
 以下為結果集。  
  
```  
{{}}
```  

傳遞的運算式會剖析為編號的運算式;這些輸入不會評估輸入數字，因此會傳回未定義。 

```  
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 以下為結果集。  
  
```  
{{}}
```  

####  <a name="bk_stringtoobject"></a> StringToObject  
 傳回轉譯成物件的運算式。 如果運算式無法轉譯，會傳回未定義。  
  
 **語法**  
  
```  
StringToObject(<expr>)  
```  
  
 **引數**  
  
- `expr`  
  
   是任何有效的純量運算式評估為 JSON 物件運算式。 請注意，必須為有效的雙引號括住寫入巢狀的字串值。 如需 JSON 格式的詳細資訊，請參閱[json.org](https://json.org/)  
  
  **傳回類型**  
  
  傳回的物件運算式，或未定義。  
  
  **範例**  
  
  下列範例會示範 StringToObject 跨不同類型的運作方式。 
  
 以下是範例，以有效的輸入。

``` 
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

以下為結果集。

```
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 以下是範例具有無效的輸入。
即使是在查詢中有效，它們不會剖析有效的物件。 物件的字串內的字串必須是逸出"{\\」\\":\\"str\\"}"或周圍的引號必須是單一 ' {"a":"str"}'。

單引號周圍的屬性名稱不是有效的 JSON。

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

以下為結果集。

```  
[{}]
```  

不含周圍的引號的屬性名稱不是有效的 JSON。

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

以下為結果集。

```  
[{}]
``` 

以下是範例具有無效的輸入。

 傳遞的運算式會剖析為 JSON 物件;這些輸入不會評估輸入物件，並因此會傳回未定義。

``` 
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 以下為結果集。

```
[{}]
```

####  <a name="bk_substring"></a> SUBSTRING  
 傳回字串運算式的部分，從指定字元以零為起始的位置開始，直到指定的長度，或直到字串的結尾。  
  
 **語法**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
- `num_expr`  
  
   是任何用來表示開始和結束字元的有效數值運算式。    
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例會傳回從 1 開始，且長度為 1 個字元的子字串 "abc"。  
  
```  
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 以下為結果集。  
  
```  
[{"substring": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 傳回純量運算式的字串表示法。 
  
 **語法**  
  
```  
ToString(<expr>)
```  
  
 **引數**  
  
- `expr`  
  
   為任何有效的純量運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例顯示 ToString 在不同類型之間的行為方式。   
  
```  
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 以下為結果集。  
  
```  
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
```  
 指定下列輸入︰
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 下列範例顯示 ToString 可以如何與其他字串函式 (例如 CONCAT) 搭配使用。   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

以下為結果集。  
  
```  
[{"$1":"4lb" },
{"$1":"32kg"},
{"$1":"400g" },
{"$1":"8999mg" }]

```  
指定下列輸入。
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
下列範例顯示 ToString 可以如何與其他字串函式 (例如 REPLACE) 搭配使用。   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
以下為結果集。  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
``` 
 
####  <a name="bk_trim"></a> TRIM  
 傳回移除開頭和尾端空白之後的字串運算式。  
  
 **語法**  
  
```  
TRIM(<str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例示範如何在查詢中使用 TRIM。  
  
```  
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 以下為結果集。  
  
```  
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 
####  <a name="bk_upper"></a> UPPER  
 傳回將小寫字元資料轉換成大寫之後的字串運算式。  
  
 **語法**  
  
```  
UPPER(<str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例示範如何在查詢中使用 UPPER  
  
```  
SELECT UPPER("Abc") AS upper  
```  
  
 以下為結果集。  
  
```  
[{"upper": "ABC"}]  
```

## <a id="array-functions"></a>陣列函數

下列純量函數會執行上陣列輸入的值並傳回數值、 布林值或陣列值的作業：
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 傳回串連兩個或多個陣列值之結果的陣列。  
  
 **語法**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **引數**  
  
- `arr_expr`  
  
   為任何有效陣列運算式。  
  
  **傳回類型**  
  
  傳回陣列運算式。  
  
  **範例**  
  
  下列範例顯示如何串連兩個陣列。  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 以下為結果集。  
  
```  
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
傳回布林值，表示陣列是否包含指定值。 您可以在命令中使用布林值運算式，以檢查物件為部分相符或完全相符。 

**語法**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **引數**  
  
- `arr_expr`  
  
   為任何有效陣列運算式。  
  
- `expr`  
  
   為任何有效運算式。  

- `bool_expr`  
  
   為任何布林運算式。 如果設定為 'true' 且指定的搜尋值是物件，則該命令會針對部分相符進行檢查 (搜尋物件是其中一個物件的子集)。 如果設定為 'false'，則此命令會針對完全相符來檢查陣列中所有物件。 若未指定，則預設值為 false。 
  
  **傳回類型**  
  
  傳回布林值。  
  
  **範例**  
  
  下列範例說明如何使用 ARRAY_CONTAINS 檢查陣列中的成員資格。  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 以下為結果集。  
  
```  
[{"b1": true, "b2": false}]  
```  

下列範例說明如何使用 ARRAY_CONTAINS 檢查陣列中的 JSON 部份相符。  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 以下為結果集。  
  
```  
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 傳回指定陣列運算式的元素數目。  
  
 **語法**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **引數**  
  
- `arr_expr`  
  
   為任何有效陣列運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例說明如何使用 ARRAY_LENGTH 取得陣列的長度。  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 以下為結果集。  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 傳回陣列運算式的一部分。
  
 **語法**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **引數**  
  
- `arr_expr`  
  
   為任何有效陣列運算式。  
  
- `num_expr`  
  
   以零為起始的數值索引，可用來開始陣列。 負數值可能會用來指定相對於陣列最後一個元素的起始索引，例如 -1 會參考陣列中的最後一個元素。  

- `num_expr`  

   結果陣列中的元素數目上限。    

  **傳回類型**  
  
  傳回陣列運算式。  
  
  **範例**  
  
  下列範例示範如何使用 ARRAY_SLICE 取得陣列的不同配量。  
  
```  
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 以下為結果集。  
  
```  
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  
## <a id="date-time-functions"></a>日期和時間函式

下列純量函數可讓您以兩種形式，取得目前的 UTC 日期和時間數值的時間戳記，其值為 Unix epoch 以來毫秒，或為符合 ISO 8601 格式的字串。 

|||
|-|-|
|[GETCURRENTDATETIME](#bk_get_current_date_time)|[GETCURRENTTIMESTAMP](#bk_get_current_timestamp)||

####  <a name="bk_get_current_date_time"></a> GETCURRENTDATETIME
 傳回目前的 UTC 日期和時間以 ISO 8601 字串形式。
  
 **語法**
  
```
GETCURRENTDATETIME ()
```
  
  **傳回類型**
  
  傳回目前 UTC 日期和時間使用 ISO 8601 字串值。 

  這表示，格式為 YYYY-MM-DDThh:mm:ss.sssZ 其中：
  
  |||
  |-|-|
  |YYYY|四位數年份|
  |MM|兩位數月份 (01 = 年 1 月，等等。)|
  |DD|兩位數天數的月份 (01 到 31)|
  |T|從開始時間項目的 signifier|
  |hh|兩位數的小時 (從 00 到 23)|
  |mm|兩位數的分鐘 (00 到 59)|
  |ss|兩位數的秒鐘 (00 到 59)|
  |.sss|小數秒的三個數字|
  |Z|UTC （國際標準時間） 指示項||
  
  如需有關 ISO 8601 格式的詳細資訊，請參閱[ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

  **備註**

  GETCURRENTDATETIME 是不具決定性的函式。 
  
  傳回的結果會是 UTC （國際標準時間）。

  **範例**  
  
  下列範例示範如何取得目前的 UTC 日期時間使用 GetCurrentDateTime 內建函式。
  
```  
SELECT GETCURRENTDATETIME() AS currentUtcDateTime
```  
  
 以下是範例結果集。
  
```  
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

####  <a name="bk_get_current_timestamp"></a> GETCURRENTTIMESTAMP
 傳回 00:00:00 1970 年 1 月 1 日，星期四以來所經過的毫秒的數。 
  
 **語法**  
  
```  
GETCURRENTTIMESTAMP ()  
```  
  
  **傳回類型**  
  
  傳回數值，目前的以來所經過的 Unix epoch 以來也就是 00:00:00 1970 年 1 月 1 日，星期四以來所經過的毫秒數的毫秒數。

  **備註**

  GETCURRENTTIMESTAMP 是不具決定性的函式。
  
  傳回的結果會是 UTC （國際標準時間）。

  **範例**  
  
  下列範例示範如何取得目前的時間戳記使用 GetCurrentTimestamp 內建函式。
  
```  
SELECT GETCURRENTTIMESTAMP() AS currentUtcTimestamp
```  
  
 以下是範例結果集。
  
```  
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a id="spatial-functions"></a>空間函式

Cosmos DB 支援下列「開放地理空間協會」(OGC) 內建地理空間查詢函數。 下列純量值函式會對空間物件輸入值執行作業，並傳回數值或布林值。  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 傳回兩個 GeoJSON Point、Polygon 或 LineString 運算式之間的距離。  
  
 **語法**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **引數**  
  
- `spatial_expr`  
  
   為任何有效的 GeoJSON 點、Polygon 或 LineString 物件運算式。  
  
  **傳回類型**  
  
  傳回數值運算式，其中包含距離。 這在預設參照系統中以公尺表示。  
  
  **範例**  
  
  以下範例顯示如何使用ST_DISTANCE 內建函式傳回所有家族文件，且這些文件在 30 公里的指定位置內。 上也提供本文中使用的原始碼。  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 以下為結果集。  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 傳回布林運算式，指出第一個引數中指定的 GeoJSON 物件 (Point、Polygon 或 LineString) 是否位在第二個引數中的 GeoJSON (Point、Polygon 或 LineString) 內。  
  
 **語法**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **引數**  
  
- `spatial_expr`  
  
   為任何有效的 GeoJSON 點、Polygon 或 LineString 物件運算式。  
 
- `spatial_expr`  
  
   為任何有效的 GeoJSON 點、Polygon 或 LineString 物件運算式。  
  
  **傳回類型**  
  
  傳回布林值。  
  
  **範例**  
  
  下列範例說明如何使用 ST_WITHIN 尋找多邊形內的所有家族文件。  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 以下為結果集。  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 傳回布林運算式，指出第一個引數交集中指定的 GeoJSON 物件 (Point、Polygon 或 LineString) 是否位在第二個引數中的 GeoJSON (Point、Polygon 或 LineString) 內。  
  
 **語法**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **引數**  
  
- `spatial_expr`  
  
   為任何有效的 GeoJSON 點、Polygon 或 LineString 物件運算式。  
 
- `spatial_expr`  
  
   為任何有效的 GeoJSON 點、Polygon 或 LineString 物件運算式。  
  
  **傳回類型**  
  
  傳回布林值。  
  
  **範例**  
  
  下列範例說明如何尋找與給定的多邊形交集的所有區域。  
  
```  
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 以下為結果集。  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 傳回布林值，指出指定的 GeoJSON Point、Polygon 或 LineString 運算式是否有效。  
  
 **語法**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **引數**
  
- `spatial_expr`  
  
   為任何有效的 GeoJSON 點、Polygon 或 LineString 運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例說明如何使用 ST_VALID 檢查點是否有效。  
  
  例如，此點的維度值不在數值 [-90, 90] 的有效範圍內，因此查詢會傳回 False。  
  
  對於多邊形，GeoJSON 規格需要此資料；若要建立一個封閉的形狀，最後一個座標組應該與第一個座標組相同。 多邊形內的點必須以逆時針順序指定。 以順時針順序指定多邊形，代表區域內的反轉。  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 以下為結果集。  
  
```  
[{ "b": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 如果指定的 GeoJSON Point、Polygon 或 LineString 運算式有效，就傳回包含布林值的 JSON 值；但如果是無效的，就會額外加上做為字串值的原因。  
  
 **語法**  
  
```  
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
 **引數**  
  
- `spatial_expr`  
  
   為任何有效的 GeoJSON 點或多邊形運算式。  
  
  **傳回類型**  
  
  如果指定的 GeoJSON 點或多邊形運算式是有效的，就會傳回包含布林值的 JSON 值；但如果是無效的，就會額外加上做為字串值的原因。  
  
  **範例**  
  
  下列範例說明如何使用 ST_ISVALIDDETAILED 檢查有效性 (包含詳細資料)。  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 以下為結果集。  
  
```  
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 簡介](introduction.md)
- [UDFs](sql-query-udfs.md)
- [彙總](sql-query-aggregates.md)