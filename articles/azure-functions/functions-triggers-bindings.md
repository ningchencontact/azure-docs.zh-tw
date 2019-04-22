---
title: Azure Functions 中的觸發程序和繫結
description: 了解如何使用触发器和绑定将 Azure 函数连接到联机事件和基于云的服务。
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 3865f748a9ca2fe09660d6454542d64f73a8e3c1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58889833"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 觸發程序和繫結概念

本文概要介绍有关函数触发器和绑定的概念。

触发器是导致函数运行的因素。 触发器定义函数的调用方式，一个函数必须刚好有一个触发器。 触发器具有关联的数据，这些数据通常作为函数的有效负载提供。 

绑定到函数是以声明方式将另一个资源连接到该函数的一种方式；绑定可以输入绑定和/或输出绑定的形式进行连接。 绑定中的数据作为参数提供给函数。

可根据需要，混合搭配不同的绑定。 绑定是可选的，一个函数可以有一个或多个输入绑定和/或输出绑定。

使用触发器和绑定可以避免对其他服务进行硬编码访问。 您的函式會接收函式參數中的資料 (例如佇列訊息的內容)。 您可以使用函式的傳回值來傳送資料 (例如用以建立佇列訊息)。 

以下示例演示如何实现不同的函数。

| 範例案例 | 觸發程序 | 输入绑定 | 输出绑定 |
|-------------|---------|---------------|----------------|
| 新的队列消息抵达，此时会运行一个函数来写入到另一个队列。 | 队列<sup>*</sup> | *None* | 队列<sup>*</sup> |
|计划的作业读取 Blob 存储内容，并创建新的 Cosmos DB 文档。 | 計時器 | Blob 儲存體 | Cosmos DB |
|事件网格用于读取 Blob 存储中的映像以及 Cosmos DB 中的文档，以发送电子邮件。 | Event Grid | Blob 存储和 Cosmos DB | SendGrid |
| 一个 Webhook，它使用 Microsoft Graph 来更新 Excel 工作表。 | HTTP | *None* | Microsoft Graph |

<sup>\*</sup> 表示不同的队列

这些示例并不详尽，旨在演示如何同时使用触发器和绑定。

###  <a name="trigger-and-binding-definitions"></a>触发器和绑定的定义

触发器和绑定的定义根据开发方法的不同而异。

| 平台 | 触发器和绑定的配置方式... |
|-------------|--------------------------------------------|
| C# 類別庫 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;使用 C# 特性修饰方法和参数 |
| 其他所有（包括 Azure 门户） | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;更新 [function.json](./functions-reference.md)（[架构](http://json.schemastore.org/function)） |

门户为此配置提供了一个 UI，但你可以通过函数的“集成”选项卡打开“高级编辑器”，来直接编辑文件。

在 .NET 中，参数类型定义了输入数据的数据类型。 例如，使用 `string` 绑定到队列触发器的文本、一个要读取为二进制内容的字节数组，以及一个要反序列化为对象的自定义类型。

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
- 某些繫結支援特殊方向 `inout`。 如果使用 `inout`，则只能通过门户中的“集成”选项卡使用“高级编辑器”。

當您使用[類別庫中的屬性](functions-dotnet-class-library.md)來設定觸發程序和繫結時，請在屬性建構函式中提供方向，或從參數類型推斷方向。

## <a name="supported-bindings"></a>支援的繫結

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

如需哪些繫結為預覽狀態或已核准可用於實際執行環境的資訊，請參閱[支援的語言](supported-languages.md)。

## <a name="resources"></a>資源
- [绑定表达式和模式](./functions-bindings-expressions-patterns.md)
- [使用 Azure 函数返回值](./functions-bindings-return-value.md)
- [如何注册绑定表达式](./functions-bindings-register.md)
- 測試：
  - [在 Azure Functions 中測試程式碼的策略](functions-test-a-function.md)
  - [手動執行非 HTTP 觸發的函式](functions-manually-run-non-http.md)
- [处理绑定错误](./functions-bindings-errors.md)

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [注册 Azure Functions 绑定扩展](./functions-bindings-register.md)
