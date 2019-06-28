---
title: Azure Stream Analytics 的診斷記錄資料錯誤
description: 這篇文章會說明不同的輸入和輸出資料錯誤時使用 Azure Stream Analytics 可能發生的。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: ecc7077bf208adf1ac89adcce2f2e480ce34888e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329589"
---
# <a name="azure-stream-analytics-data-errors"></a>Azure Stream Analytics 資料錯誤

資料錯誤是在處理資料時所發生的錯誤。  這些錯誤通常發生在資料還原序列化，序列化期間，和寫入作業。  發生資料錯誤時，Stream Analytics 就會寫入至診斷記錄的詳細的資訊和範例的事件。  在某些情況下，這項資訊的摘要也提供透過入口網站的通知。

本文章概述各種錯誤類型、 原因和診斷記錄的輸入和輸出資料錯誤的詳細資料。

## <a name="diagnostic-log-schema"></a>診斷記錄結構描述

請參閱[使用診斷記錄檔中疑難排解 Azure Stream Analytics](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema)以查看診斷記錄檔的結構描述。 下列 JSON 是範例值**屬性**了資料錯誤的診斷記錄的欄位。

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>輸入的資料錯誤

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* 原因：選取的輸入的壓縮類型不符合的資料。
* 提供的入口網站通知：是
* 診斷記錄層級：警告
* 影響：從輸入會捨棄任何包含無效的壓縮類型的還原序列化錯誤的訊息。
* 記錄詳細資料
   * 輸入訊息識別項。 事件中樞的識別項是 PartitionId、 位移和序號。

**錯誤訊息**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* 原因：輸入資料的標頭無效。 例如，CSV 都有名稱重複的資料行。
* 提供的入口網站通知：是
* 診斷記錄層級：警告
* 影響：包含無效的標頭的任何還原序列化錯誤的訊息會從輸入卸除。
* 記錄詳細資料
   * 輸入訊息識別項。 
   * 實際最多幾 kb 的承載。

**錯誤訊息**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* 原因：搭配 CREATE TABLE 或透過時間戳記所定義的輸入資料行不存在。
* 提供的入口網站通知：是
* 診斷記錄層級：警告
* 影響：從輸入會捨棄具有遺失的資料行的事件。
* 記錄詳細資料
   * 輸入訊息識別項。 
   * 遺漏的資料行名稱。 
   * 實際最多幾 kb 的承載。

**錯誤訊息**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* 原因：無法將輸入轉換成 CREATE TABLE 陳述式中指定的類型。
* 提供的入口網站通知：是
* 診斷記錄層級：警告
* 影響：從輸入會捨棄具有類型轉換錯誤的事件。
* 記錄詳細資料
   * 輸入訊息識別項。 
   * 預期的類型與資料行的名稱。

**錯誤訊息**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* 原因：輸入的資料不正確的格式。 例如，輸入不是有效的 JSON。
* 提供的入口網站通知：是
* 診斷記錄層級：警告
* 影響：從輸入，會卸除無效的資料錯誤發生後的訊息中的所有事件。
* 記錄詳細資料
   * 輸入訊息識別項。 
   * 實際最多幾 kb 的承載。

**錯誤訊息**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* 原因：TIMESTAMP BY 運算式的值無法轉換成日期時間。
* 提供的入口網站通知：是
* 診斷記錄層級：警告
* 影響：從輸入會捨棄無效的輸入時間戳記的事件。
* 記錄詳細資料
   * 輸入訊息識別項。 
   * 錯誤訊息。 
   * 實際最多幾 kb 的承載。

**錯誤訊息**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* 原因：TIMESTAMP BY OVER timestampColumn 的值是 NULL。
* 提供的入口網站通知：是
* 診斷記錄層級：警告
* 影響：從輸入會捨棄具有無效的輸入時間戳記索引鍵的事件。
* 記錄詳細資料
   * 最多幾 kb 實際的承載。

**錯誤訊息**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* 原因：應用程式的時間與抵達時間之間的差異大於延遲傳入容錯時間。
* 提供的入口網站通知：否
* 診斷記錄層級：資訊
* 影響：延遲輸入的事件會根據 「 處理其他事件 」 來處理設定在事件排序作業的組態區段。 如需詳細資訊，請參閱[時間處理原則](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)。
* 記錄詳細資料
   * 應用時間與抵達時間。 
   * 實際最多幾 kb 的承載。

**錯誤訊息**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* 原因：應用程式的時間與抵達時間之間的差異是 5 分鐘以上。
* 提供的入口網站通知：否
* 診斷記錄層級：資訊
* 影響：早期輸入的事件會根據 「 處理其他事件 」 來處理設定在事件排序作業的組態區段。 如需詳細資訊，請參閱[時間處理原則](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)。
* 記錄詳細資料
   * 應用時間與抵達時間。 
   * 實際最多幾 kb 的承載。

**錯誤訊息**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* 原因：事件會被視為根據所定義的順序錯亂容許視窗按順序。
* 提供的入口網站通知：否
* 診斷記錄層級：資訊
* 影響：根據 「 處理其他事件 」 會處理事件的順序設定在事件工作組態的 [訂購] 區段。 如需詳細資訊，請參閱[時間處理原則](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)。
* 記錄詳細資料
   * 實際最多幾 kb 的承載。

**錯誤訊息**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>輸出資料錯誤

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* 原因：所需的輸出資料行不存在。 比方說，資料行定義為 Azure 資料表 PartitionKey 不存在。
* 提供的入口網站通知：是
* 診斷記錄層級：警告
* 影響：根據處理的所有輸出資料轉換錯誤包括遺漏必要的資料行[輸出資料原則](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)設定。
* 記錄詳細資料
   * 名稱資料行和資料錄的識別項或記錄的一部分。

**錯誤訊息**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* 原因：資料行的值不符合的輸出。 例如，資料行名稱不是有效的 Azure 資料表資料行。
* 提供的入口網站通知：是
* 診斷記錄層級：警告
* 影響：包含無效的資料行名稱所有的輸出資料轉換錯誤的處理根據[輸出資料原則](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)設定。
* 記錄詳細資料
   * 名稱資料行和記錄識別碼或記錄的一部分。

**錯誤訊息**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* 原因：資料行無法轉換成有效的型別，在輸出中。 例如，資料行的值是與條件約束或 SQL 資料表中定義的類型不相容。
* 提供的入口網站通知：是
* 診斷記錄層級：警告
* 影響：根據處理的所有輸出資料轉換錯誤包括型別轉換錯誤[輸出資料原則](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)設定。
* 記錄詳細資料
   * 資料行的名稱。
   * 記錄識別碼或記錄的一部分。

**錯誤訊息**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* 原因：訊息的值大於支援的輸出大小。 例如，記錄是大於 1 MB，做為事件中樞輸出。
* 提供的入口網站通知：是
* 診斷記錄層級：警告
* 影響：處理包括記錄超過的大小限制所有的輸出資料轉換錯誤的根據[輸出資料原則](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)設定。
* 記錄詳細資料
   * 記錄識別碼或記錄的一部分。

**錯誤訊息**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* 原因：記錄已經包含具有相同名稱的系統資料行的資料行。 比方說，CosmosDB 輸出資料行識別碼資料行是以不同的資料行名為 ID。
* 提供的入口網站通知：是
* 診斷記錄層級：警告
* 影響：根據處理的所有輸出資料轉換錯誤包括重複的索引鍵[輸出資料原則](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)設定。
* 記錄詳細資料
   * 資料行的名稱。
   * 記錄識別碼或記錄的一部分。

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>後續步驟

* [使用診斷記錄進行疑難排解 Azure Stream Analytics](stream-analytics-job-diagnostic-logs.md)

* [了解 Stream Analytics 作業的監視，以及如何監視查詢](stream-analytics-monitoring.md)
