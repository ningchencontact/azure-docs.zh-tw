---
title: Azure Durable Functions 單元測試
description: 深入了解如何針對 Durable Functions 進行單元測試。
services: functions
author: kadimitr
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/11/2018
ms.date: 03/25/2019
ms.author: v-junlch
ms.openlocfilehash: 69cf91f1448e36353f83de7a271abb3b53858bb0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648460"
---
# <a name="durable-functions-unit-testing"></a>Durable Functions 單元測試

單元測試是現代軟體開發實務中很重要的一部分。 單元測試會驗證商務邏輯行為，並且防止在未來引進未注意到的中斷性變更。 Durable Functions 很容易變得複雜，所以引進單元測試有助於避免中斷性變更。 下列章節說明如何針對以下三種類型的函式進行單元測試 - 協調流程用戶端、協調器及活動函式。

## <a name="prerequisites"></a>必要條件

這篇文章中的範例需要下列概念和架構的知識：

* 單元測試

* 長期函式

* [xUnit](https://xunit.github.io/) - 測試架構

* [moq](https://github.com/moq/moq4) - 模擬架構

## <a name="base-classes-for-mocking"></a>模擬的基底類別

模擬會透過 Durable Functions 中的三個抽象類別來提供支援：

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

* [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html)

這些類別是 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)、[DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) 和 [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) 的基底類別，它們會定義協調流程用戶端、協調器和活動方法。 模擬會設定基底類別方法的預期行為，讓單元測試可以驗證商務邏輯。 有一個雙步驟工作流程可以針對協調流程用戶端和協調器中的商務邏輯進行單元測試：

1. 定義協調流程用戶端和協調器的簽章時，使用基底類別而不是具體實作。
2. 在單元測試中模擬基底類別的行為，並且驗證商務邏輯。

在下列段落中尋找測試函式的詳細資料，這些函式使用協調流程用戶端繫結和協調器觸發程序繫結。

## <a name="unit-testing-trigger-functions"></a>針對觸發程序函式進行單元測試

在本節中，單元測試會驗證下列 HTTP 觸發程序函式對於啟動新協調流程的邏輯。

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace VSSample
{
    public static class HttpStart
    {
        [FunctionName("HttpStart")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
            [OrchestrationClient] DurableOrchestrationClientBase starter,
            string functionName,
            ILogger log)
        {
            // Function input comes from the request content.
            dynamic eventData = await req.Content.ReadAsAsync<object>();
            string instanceId = await starter.StartNewAsync(functionName, eventData);

            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

單元測試工作會驗證回應承載中提供的 `Retry-After` 標頭值。 因此單元測試會模擬一些 [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) 方法，以確保可預測的行為。

首先，必須模擬基底類別 [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)。 模擬可以是實作 [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) 的新類別。 不過，使用例如 [moq](https://github.com/moq/moq4) 的模擬架構可以簡化程序：

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

然後模擬 `StartNewAsync` 方法以傳回已知的執行個體識別碼。

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

接下來模擬 `CreateCheckStatusResponse` 以一律傳回空的 HTTP 200 回應。

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

也會模擬 `TraceWriter`：

```csharp
    // Mock TraceWriter
    var traceWriterMock = new Mock<TraceWriter>(TraceLevel.Info);

```  

現在 `Run` 方法是從單元測試呼叫：

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object,
        functionName,
        traceWriterMock.Object);
 ```

 最後一個步驟是比較輸出與預期值：

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

合併所有步驟之後，單元測試會有下列程式碼：

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

namespace VSSample.Tests
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using System.Net.Http.Headers;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Extensions.Logging;
    using Moq;
    using Xunit;

    public class HttpStartTests
    {
        [Fact]
        public async Task HttpStart_returns_retryafter_header()
        {
            // Define constants
            const string functionName = "SampleFunction";
            const string instanceId = "7E467BDB-213F-407A-B86A-1954053D3C24";

            // Mock TraceWriter
            var loggerMock = new Mock<ILogger>();

            // Mock DurableOrchestrationClientBase
            var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();

            // Mock StartNewAsync method
            durableOrchestrationClientBaseMock.
                Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
                ReturnsAsync(instanceId);

            // Mock CreateCheckStatusResponse method
            durableOrchestrationClientBaseMock
                .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
                .Returns(new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.OK,
                    Content = new StringContent(string.Empty),
                    Headers =
                    {
                        RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
                    }
                });

            // Call Orchestration trigger function
            var result = await HttpStart.Run(
                new HttpRequestMessage()
                {
                    Content = new StringContent("{}", Encoding.UTF8, "application/json"),
                    RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
                },
                durableOrchestrationClientBaseMock.Object,
                functionName,
                loggerMock.Object);

            // Validate that output is not null
            Assert.NotNull(result.Headers.RetryAfter);

            // Validate output's Retry-After header value
            Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
        }
    }
}
```

## <a name="unit-testing-orchestrator-functions"></a>針對協調器函式進行單元測試

針對協調器函式進行單元測試更加有趣，因為它們通常有更多商務邏輯。

在本節中，單元測試會驗證 `E1_HelloSequence` 協調器函式的輸出：

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;

namespace VSSample
{
    public static class HelloSequence
    {
        [FunctionName("E1_HelloSequence")]
        public static async Task<List<string>> Run(
            [OrchestrationTrigger] DurableOrchestrationContextBase context)
        {
            var outputs = new List<string>();

            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello_DirectInput", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("E1_SayHello")]
        public static string SayHello([ActivityTrigger] DurableActivityContextBase context)
        {
            string name = context.GetInput<string>();
            return $"Hello {name}!";
        }

        [FunctionName("E1_SayHello_DirectInput")]
        public static string SayHelloDirectInput([ActivityTrigger] string name)
        {
            return $"Hello {name}!";
        }
    }
 }
```

單元測試程式碼會從建立模擬開始：

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

然後會模擬活動方法呼叫：

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

接下來，單元測試會呼叫 `HelloSequence.Run` 方法：

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

最後會驗證輸出：

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

合併所有步驟之後，單元測試會有下列程式碼：

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

namespace VSSample.Tests
{
    using System.Threading.Tasks;
    using Microsoft.Azure.WebJobs;
    using Moq;
    using Xunit;

    public class HelloSequenceTests
    {
        [Fact]
        public async Task Run_returns_multiple_greetings()
        {
            var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
            durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
            durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
            durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello_DirectInput", "London")).ReturnsAsync("Hello London!");

            var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);

            Assert.Equal(3, result.Count);
            Assert.Equal("Hello Tokyo!", result[0]);
            Assert.Equal("Hello Seattle!", result[1]);
            Assert.Equal("Hello London!", result[2]);
        }
    }
}
```

## <a name="unit-testing-activity-functions"></a>針對活動函式進行單元測試

可以使用與非持久函式相同的方式來針對活動函式進行單元測試。

在本節中，單元測試會驗證 `E1_SayHello` 活動函式的行為：

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;

namespace VSSample
{
    public static class HelloSequence
    {
        [FunctionName("E1_HelloSequence")]
        public static async Task<List<string>> Run(
            [OrchestrationTrigger] DurableOrchestrationContextBase context)
        {
            var outputs = new List<string>();

            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello_DirectInput", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("E1_SayHello")]
        public static string SayHello([ActivityTrigger] DurableActivityContextBase context)
        {
            string name = context.GetInput<string>();
            return $"Hello {name}!";
        }

        [FunctionName("E1_SayHello_DirectInput")]
        public static string SayHelloDirectInput([ActivityTrigger] string name)
        {
            return $"Hello {name}!";
        }
    }
 }
```

且單元測試會驗證輸出的格式。 單元測試可以直接使用參數類型，或是模擬 [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html) 類別：

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

namespace VSSample.Tests
{
    using Microsoft.Azure.WebJobs;
    using Xunit;
    using Moq;

    public class HelloSequenceActivityTests
    {
        [Fact]
        public void SayHello_returns_greeting()
        {
            var durableActivityContextMock = new Mock<DurableActivityContextBase>();
            durableActivityContextMock.Setup(x => x.GetInput<string>()).Returns("John");
            var result = HelloSequence.SayHello(durableActivityContextMock.Object);
            Assert.Equal("Hello John!", result);
        }

        [Fact]
        public void SayHello_returns_greeting_direct_input()
        {
            var result = HelloSequence.SayHelloDirectInput("John");
            Assert.Equal("Hello John!", result);
        }
    }
}
```
## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [深入了解 moq](https://github.com/Moq/moq4/wiki/Quickstart)

<!-- Update_Description: wording update -->