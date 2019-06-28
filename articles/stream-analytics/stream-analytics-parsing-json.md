---
title: 在 Azure 串流分析中剖析 JSON 和 AVRO
description: 本文說明如何對複雜資料類型 (如 陣列、JSON、CSV 格式的資料) 進行作業。
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: daf5b97e4ac586f89e5964ee16ee73c86f59b01d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329350"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>在 Azure 串流分析中剖析 JSON 和 Avro 資料

Azure Stream Analytics 支援 CSV、 JSON 和 Avro 資料格式的處理事件。 JSON 和 Avro 資料可以結構化，並包含一些複雜的類型，例如巢狀的物件 （記錄） 和陣列。 




## <a name="record-data-types"></a>資料列資料類型
當輸入資料流中是使用資料列資料類型時，此資料類型會用來代表 JSON 和 Avro。 這些範例示範讀取 JSON 格式輸入事件的範例感應器。 以下是單一事件的範例：

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```


### <a name="access-nested-fields-in-known-schema"></a>已知的結構描述中的巢狀的存取欄位
您可以使用點標記法 （.） 來輕鬆地直接從您的查詢中存取巢狀的欄位。 比方說，這個查詢會選取在先前的 JSON 資料的緯度和經度座標，在 [位置] 屬性。 點標記法可用來瀏覽多個層級，如下所示。

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>選取所有屬性
您可以使用 '*' 萬用字元選取巢狀資料列的所有屬性。 參考下列範例：

```SQL
SELECT input.Location.*
FROM input
```

結果如下：

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>存取巢狀欄位屬性名稱時，變數
使用[GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue)函式的屬性名稱為變數。 

例如，假設需要每個裝置感應器的參考資料包含臨界值與聯結的範例資料流。 這類的參考資料的程式碼片段如下所示。

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 75
}
```

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
    -- the where statement selects the property value coming from the reference data
```

### <a name="convert-record-fields-into-separate-events"></a>將記錄的欄位轉換成不同的事件
若要將資料列欄位轉換成個別的事件，請搭配使用 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 運算子和 [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) 函式。 例如，如果前一個範例如 SensorReading 數筆記錄，下列查詢可用來將它們解壓縮到不同的事件：

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>陣列資料類型

陣列資料類型是已排序的值集合。 以下詳述陣列值的一些典型作業。 這些範例假設輸入事件具有名為 "arrayField" 的屬性，其資料類型為陣列。

這些範例使用函式 [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics)、[GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics)、[GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)，以及 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 運算子。

### <a name="working-with-a-specific-array-element"></a>使用特定的陣列項目
選取在指定索引的陣列元素 (選取第一個陣列元素)：

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>選取的陣列長度

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>將陣列元素轉換成不同的事件
以個別事件選取所有陣列元素。 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 運算子和 [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) 內建函式一起擷取所有陣列元素且視為個別事件：

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>另请参阅
[Azure 串流分析中的資料類型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) \(英文\)
