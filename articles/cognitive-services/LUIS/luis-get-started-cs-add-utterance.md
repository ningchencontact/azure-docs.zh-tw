---
title: 變更、定型應用程式 (C#)
titleSuffix: Language Understanding - Azure Cognitive Services
description: 在此 C# 快速入門中，會將範例語句新增至「家庭自動化」應用程式，並且將應用程式定型。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 04/08/2019
ms.author: diberry
ms.openlocfilehash: e9f8d274d81cdefbf9dfb41708cd537b2d60471a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273459"
---
# <a name="quickstart-change-model-using-c"></a>快速入門：使用 C# 變更模型

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>必要條件

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* 最新 [**Visual Studio Community 版本**](https://www.visualstudio.com/downloads/)。
* 安裝 C# 程式設計語言。
* [JsonFormatterPlus](https://www.nuget.org/packages/JsonFormatterPlus) (英文) 和 [CommandLine](https://www.nuget.org/packages/CommandLineParser/) (英文) NuGet 套件

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>範例語句 JSON 檔案

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>建立快速入門程式碼 

在 Visual Studio 中，使用 .NET Framework 建立新的 [Windows 傳統桌面主控台] 應用程式。 將專案命名為 `ConsoleApp1`。

![Visual Studio 專案類型](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

### <a name="add-the-systemweb-dependency"></a>新增 System.Web 相依性

Visual Studio 專案需要 **System.Web**。 在 [方案總管] 中，以滑鼠右鍵按一下 [參考]，然後從 [組件] 區段中選取 [新增參考]。

![新增 System.web 參考](./media/luis-quickstart-cs-add-utterance/system.web.png)

### <a name="add-other-dependencies"></a>新增其他相依性

Visual Studio 專案需要 **JsonFormatterPlus** 和 **CommandLineParser**。 請在 [方案總管] 中，以滑鼠右鍵按一下 [參考]，然後選取 [管理 NuGet 套件...]。瀏覽及新增這兩個套件。 

![新增第三方相依性](./media/luis-quickstart-cs-add-utterance/add-dependencies.png)


### <a name="write-the-c-code"></a>撰寫 C# 程式碼
**Program.cs 檔案**應該是：

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

更新相依性，使其變成：

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=1-11 "Add the dependencies")]


將 LUIS 識別碼和字串新增至 **Program** 類別。

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=19-30&dedent=8 "Add the LUIS IDs and strings")]

將用來管理命令列參數的類別新增至 **Program** 類別。

   [!code-csharp[Add class to manage command line parameters.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=32-46 "Add class to manage command-line parameters.")]

將 GET 要求方法新增至 **Program** 類別。

   [!code-csharp[Add the GET request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=49-59 "Add the GET request.")]


將 POST 要求方法新增至 **Program** 類別。 

   [!code-csharp[Add the POST request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=60-76 "Add the POST request.")]

將檔案方法的範例語句新增至 **Program** 類別。

   [!code-csharp[Add example utterances from file.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=77-86 "Add example utterances from file.")]

在變更套用至模型之後，將模型定型。 將方法新增至 **Program** 類別。

   [!code-csharp[After the changes are applied to the model, train the model.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=87-96 "After the changes are applied to the model, train the model.")]

定型可能無法立即完成，請檢查狀態以確認定型已完成。 將方法新增至 **Program** 類別。

   [!code-csharp[Training may not complete immediately, check status to verify training is complete.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=97-103 "Training may not complete immediately, check status to verify training is complete.")]

若要管理命令列引數，請新增主要程式碼。 將方法新增至 **Program** 類別。

   [!code-csharp[To manage command line arguments, add the main code.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=104-137 "To manage command-line arguments, add the main code.")]

### <a name="copy-utterancesjson-to-output-directory"></a>將 utterances.json 複製到輸出目錄

若要新增 `utterances.json`，請在 [方案總管] 中以滑鼠右鍵按一下方案總管的專案名稱，然後依序選取 [新增] 和 [現有項目]。 選取 `utterances.json` 檔案。 這會將檔案新增至專案中。 然後，必須將其新增至輸出目錄。 請以滑鼠右鍵按一下 `utterances.json`，然後選取 [屬性]。 在 [屬性] 視窗中，將 [建置動作] 標示為 `Content`，以及將 [複製到輸出目錄] 標示為 `Copy Always`。  

![將 JSON 檔案標示為內容](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="build-code"></a>建置程式碼

在 Visual Studio 中建置程式碼。 

## <a name="run-code"></a>執行程式碼

在專案的 /bin/Debug 目錄中，從命令列執行應用程式。 

```console
ConsoleApp1.exe --add utterances.json --train --status
```

此命令列會顯示呼叫新增語句 API 的結果。 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>清除資源
當您完成快速入門時，請移除在本快速入門中建立的所有檔案。 

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"] 
> [以程式設計方式建置 LUIS 應用程式](luis-tutorial-node-import-utterances-csv.md) 
