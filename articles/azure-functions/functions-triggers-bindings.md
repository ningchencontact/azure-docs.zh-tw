---
title: Azure Functions 中的觸發程序和繫結
description: 了解如何使用觸發程序和繫結到線上事件和雲端服務連線您的 Azure 函式。
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 0896a829a24335f3e62581fa3b63e013478781bc
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481001"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 觸發程序和繫結概念

在本文中，您會學習函式觸發程序和繫結相關的高階概念。

觸發程序是何種狀況導致要執行的函式。 觸發程序可讓您定義如何叫用函式和函式必須有一個觸發程序。 觸發程序都有相關聯的資料，這通常提供做為函式的裝載。 

繫結至函式是一種以宣告方式連接到函式; 的 其他資源繫結可能連線做*輸入繫結*，*輸出繫結*，或兩者。 繫結中的資料會提供給函式作為參數。

您可以混合和比對不同的繫結，以符合您需求。 繫結是選擇性的函式可能會有一或多個輸入和/或輸出繫結。

觸發程序和繫結可讓您避免硬式編碼至其他服務的存取。 您的函式會接收函式參數中的資料 (例如佇列訊息的內容)。 您可以使用函式的傳回值來傳送資料 (例如用以建立佇列訊息)。 

請考慮下列的範例，您可以如何實作不同的函式。

| 範例案例 | 觸發程序 | 輸入繫結 | 輸出繫結 |
|-------------|---------|---------------|----------------|
| 新的佇列訊息抵達時執行的函式，以寫入另一個佇列。 | 佇列<sup>*</sup> | *None* | 佇列<sup>*</sup> |
|排程的工作會讀取 Blob 儲存體內容，並建立新的 Cosmos DB 文件。 | 計時器 | Blob 儲存體 | Cosmos DB |
|Event Grid 用來讀取 Blob 儲存體和文件從 Cosmos DB 傳送電子郵件中的映像。 | Event Grid | Blob 儲存體和 Cosmos DB | SendGrid |
| 若要更新的 Excel 工作表使用 Microsoft Graph webhook。 | HTTP | *None* | Microsoft Graph |

<sup>\*</sup> 代表不同的佇列

這些範例並非詳盡資訊，但將說明如何使用觸發程序和繫結在一起。

###  <a name="trigger-and-binding-definitions"></a>觸發程序和繫結的定義

觸發程序和繫結，則會根據開發方式以不同的方式定義。

| 平台 | 藉由設定觸發程序和繫結... |
|-------------|--------------------------------------------|
| C# 類別庫 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;裝飾方法和參數與C#屬性 |
| 所有其他人 （包括 Azure 入口網站） | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;updating [function.json](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

此設定時，入口網站提供 UI，但您可以編輯檔案直接開啟**進階的編輯器**可透過**整合** 索引標籤，您的函式。

在.NET 中，參數型別會定義輸入資料的資料類型。 比方說，使用`string`繫結至佇列觸發程序，若要以二進位檔和自訂的型別還原序列化的物件讀取的位元組陣列的文字。

對於 JavaScript 等具有動態類型的語言，則會使用 *function.json* 檔案中的 `dataType` 屬性。 例如，若要讀取二進位格式的 HTTP 要求內容，請將 `dataType` 設定為 `binary`：

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

`dataType` 也另具有 `stream` 和 `string` 兩種選項。

## <a name="binding-direction"></a>繫結方向

所有觸發程序和繫結在 [function.json](./functions-reference.md) 檔案中都具有 `direction` 屬性：

- 對於觸發程序，方向一律為 `in`
- 輸入和輸出繫結使用 `in` 和 `out`
- 某些繫結支援特殊方向 `inout`。 如果您使用`inout`，則僅**進階的編輯器**可透過**整合**入口網站中的索引標籤。

當您使用[類別庫中的屬性](functions-dotnet-class-library.md)來設定觸發程序和繫結時，請在屬性建構函式中提供方向，或從參數類型推斷方向。

## <a name="supported-bindings"></a>支援的繫結

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

如需哪些繫結為預覽狀態或已核准可用於實際執行環境的資訊，請參閱[支援的語言](supported-languages.md)。

## <a name="resources"></a>資源
- [繫結運算式和模式](./functions-bindings-expressions-patterns.md)
- [使用 Azure 函式的傳回值](./functions-bindings-return-value.md)
- [如何註冊繫結運算式](./functions-bindings-register.md)
- 測試：
  - [在 Azure Functions 中測試程式碼的策略](functions-test-a-function.md)
  - [手動執行非 HTTP 觸發的函式](functions-manually-run-non-http.md)
- [處理繫結錯誤](./functions-bindings-errors.md)

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [註冊 Azure Functions 繫結延伸模組](./functions-bindings-register.md)
