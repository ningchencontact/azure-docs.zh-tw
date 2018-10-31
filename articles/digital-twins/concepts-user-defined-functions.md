---
title: Azure Digital Twins 的資料處理和使用者定義函式 | Microsoft Docs
description: Azure Digital Twins 的資料處理、比對器和使用者定義函式的概觀
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: b7561848ffd0158e22e97530774112dcee2a9864
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323720"
---
# <a name="data-processing-and-user-defined-functions"></a>資料處理與各項使用者定義功能

Azure Digital Twins 提供進階計算功能。 開發人員可定義自訂函式並對傳入的遙測訊息加以執行，以將事件傳送至預先定義的端點。

## <a name="data-processing-flow"></a>資料處理流程

裝置將遙測資料傳送至 Digital Twins 後，開發人員可依四階段處理資料：_驗證_、_比對_、_計算_和_分派_：

![Digital Twins 資料處理流程][1]

1. _驗證_階段會將傳入的遙測訊息轉換為一般認知的 [`data transfer object`](https://en.wikipedia.org/wiki/Data_transfer_object) 格式。 此階段也會執行裝置和感應器驗證。
1. _比對_階段會尋找適當的使用者定義函式並加以執行。 預先定義的比對器會根據傳入的遙測訊息中包含的裝置、感應器和空間資訊，來尋找使用者定義函式。
1. _計算_階段會執行在上一個階段中比對出來的使用者定義函式。 這些函式可讀取和更新空間圖形節點上的計算值，並可發出自訂通知。
1. _分派_階段會將計算階段所產生的任何自訂通知路由到圖形中定義的端點。

## <a name="data-processing-objects"></a>資料處理物件

Azure Digital Twins 中的資料處理由三個物件的定義所組成：_比對器_、_使用者定義函式_和_角色指派_：

![Digital Twins 資料處理流程][2]

### <a name="matchers"></a>比對器

_比對器_會定義一組條件，據以評估要根據傳入的感應器遙測資料執行的動作。 用來決定相符項目的條件可能包含來自於感應器、感應器的父裝置和感應器父空間的屬性。 這些條件會表示為與 [JSON 路徑](http://jsonpath.com/)間的比較，如下列範例所述：

- 屬於 `Temperature` 資料類型的所有感應器。
- 在其連接埠中具有 `01`。
- 所屬裝置具有設為值 `GoodCorp` 的擴充屬性索引鍵 `Manufacturer`。
- 屬於 `Venue` 類型的空間。
- 屬於父系 `SpaceId` `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD` 的子系。

```JSON
{
  "SpaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Name": "My custom matcher",
  "Description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value GoodCorp and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"GoodCorp\"",
      "comparison": "Equals"
    },
    {
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - JSON 路徑會區分大小寫。
> - JSON 承載與下列項目所將傳回的承載相同：
>   - 感應器的 `/sensors/{id}?includes=properties,types`。
>   - 感應器父裝置的 `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes`。
>   - 感應器父空間的 `/spaces/{id}?includes=properties,types,location,timezone`。
> - 比較不會區分大小寫。

### <a name="user-defined-functions"></a>使用者定義函式

_使用者定義函式_ (或簡稱 _UDF_) 是可在 Azure Digital Twins 中的隔離環境內執行的自訂函式。 UDF 對於接收之初的原始感應器遙測訊息，以及空間圖形和發送器服務，都具有存取權。 在圖形內註冊 UDF 之後 (詳細說明請見上方)，就必須建立比對器以指定何時要執行 UDF。 當 Digital Twins 從指定的感應器接收新的遙測資料時，相符的 UDF，舉例來說，將可計算過去數個感應器讀數的移動平均值。

UDF 可用 JavaScript 撰寫，並可讓開發人員對感應器遙測訊息執行自訂程式碼片段。 此外也有協助程式方法可在使用者定義的執行環境中用來與圖形互動。 透過 UDF，開發人員可以：

- 設定直接在圖形中讀取感應器物件的感應器。
- 根據圖形中的空間內不同的感應器讀數來執行動作。
- 建立傳入的感應器讀數符合特定條件時所發出的通知。
- 在傳送通知之前將圖形中繼資料附加至感應器讀數。

如需詳細資訊，請參閱[如何使用使用者定義函式](how-to-user-defined-functions.md)。

### <a name="role-assignment"></a>角色指派

UDF 的動作會受限於 Digital Twins 的角色形存取控制，以保護服務內的資料。 角色指派可確保指定的 UDF 具有適當的權限可與空間圖形進行互動。 例如，UDF 可能會嘗試建立、讀取、更新或刪除指定空間下的圖形資料。 當 UDF 要求資料的圖形或嘗試執行動作時，系統會檢查 UDF 的存取層級。 如需詳細資訊，請參閱[角色型存取控制](security-create-manage-role-assignments.md)。

比對器有可能觸發沒有任何角色指派的 UDF。 在此情況下，UDF 將無法讀取圖形中的任何資料。

## <a name="next-steps"></a>後續步驟

若要深入了解如何將事件和遙測訊息路由至 Azure 服務，請參閱[路由事件和訊息](concepts-events-routing.md)。

若要深入了解如何建立比對器、使用者定義函式和角色指派，請參閱[使用者定義函式的使用指南](how-to-user-defined-functions.md)。

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
