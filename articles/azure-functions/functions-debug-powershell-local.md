---
title: 在本機偵錯 PowerShell Azure 函式
description: 了解如何使用 PowerShell 來開發函式。
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha, glenga
ms.openlocfilehash: b699379448863c8df84fda0e059fc10846c09931
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230085"
---
# <a name="debug-powershell-azure-functions-locally"></a>在本機偵錯 PowerShell Azure 函式

Azure Functions 可讓您開發您的函式，以 PowerShell 指令碼。

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

可以在本機一樣使用下列標準開發工具的任何 PowerShell 指令碼偵錯您的 PowerShell 函式：

* [Visual Studio Code](https://code.visualstudio.com/)：Microsoft 的免費、 輕量級，且開放原始碼的文字編輯器，具有 PowerShell 延伸模組，可提供完整的 PowerShell 開發體驗。
* PowerShell 主控台：使用相同的命令，您會使用偵錯任何其他 PowerShell 處理程序的偵錯。

[Azure Functions Core Tools](functions-run-local.md)支援本機偵錯 Azure 函式，包括 PowerShell 函式。

## <a name="example-function-app"></a>範例函式應用程式

這篇文章中所用的函式應用程式具有單一的 HTTP 觸發函式，並含有下列檔案：

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

此函式應用程式是當您完成時，得到類似[PowerShell 快速入門](functions-create-first-function-powershell.md)。

中的函式程式碼`run.ps1`看起來像下列的指令碼：

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>設定附加點

若要偵錯任何 PowerShell 函式，函式必須要附加偵錯工具停止。 `Wait-Debugger` Cmdlet 停止執行，並等候偵錯工具。

您是將呼叫加入`Wait-Debugger`cmdlet 正上方`if`陳述式，如下所示：

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

在開始偵錯`if`陳述式。 

使用`Wait-Debugger`就地的情況下，您現在可以偵錯使用 Visual Studio Code 或 PowerShell 主控台中的函式。

## <a name="debug-in-visual-studio-code"></a>Visual Studio Code 中偵錯

若要偵錯您的 PowerShell 函式，在 Visual Studio Code 中，您必須具備下列延伸模組適用於 Visual Studio Code:

* [PowerShell](/powershell/scripting/components/vscode/using-vscode)
* [Azure Functions](functions-create-first-function-vs-code.md)

在安裝後的 PowerShell 和 Azure Functions 的延伸模組，會載入現有的函式應用程式專案。 您也可以[建立函式專案](functions-create-first-function-vs-code.md)。

>[!NOTE]
> 您的專案不應有的所需的組態檔，系統會提示您將它們加入。

### <a name="start-the-function-app"></a>啟動函數應用程式

確認`Wait-Debugger`設定函式中要附加偵錯工具。  使用`Wait-Debugger`加入，您可以偵錯使用 Visual Studio Code 的函式應用程式。

選擇**偵錯**窗格，然後**附加至 PowerShell 函式**。

![偵錯工具](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

您也可以按 F5 鍵開始偵錯。

開始偵錯作業會執行下列工作：

* 執行`func extensions install`函式應用程式所需的任何 Azure Functions 擴充功能安裝終端機中。
* 執行`func host start`在終端機中的 Functions 主機啟動函數應用程式。
* 將 PowerShell 偵錯工具附加到 Functions 執行階段中的 PowerShell runspace。

函式應用程式執行，您需要不同的 PowerShell 主控台，來呼叫 HTTP 觸發函式。

在此情況下，PowerShell 主控台是用戶端。 `Invoke-RestMethod`用來觸發函式。

在 PowerShell 主控台中，執行下列命令：

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

您會發現不立即傳回回應。 這是因為`Wait-Debugger`已附加偵錯工具和 PowerShell 執行會因為它無法進入中斷模式。 這是因為[BreakAll 概念](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place)，稍後會說明。 您按下後`continue` 按鈕，偵錯工具立即中斷該行之後`Wait-Debugger`。

此時，偵錯工具附加，而且您可以執行所有一般偵錯工具操作。 如需有關如何使用 Visual Studio Code 中的偵錯工具的詳細資訊，請參閱 <<c0> [ 官方文件](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)。

在您繼續，並完全叫用您的指令碼之後，您會發現：

* PowerShell 主控台執行`Invoke-RestMethod`已傳回的結果
* Visual Studio Code 中的 PowerShell 整合式主控台正在等候執行的指令碼

當您叫用相同的函式，用來偵錯工具擴充功能會中斷之後的 PowerShell 中的後續階段`Wait-Debugger`。

## <a name="debugging-in-a-powershell-console"></a>在 PowerShell 主控台中偵錯

>[!NOTE]
> 本節假設您已閱讀[Azure Functions Core Tools docs](functions-run-local.md)了解如何使用和`func host start`命令以啟動您的函式應用程式。

主控台中，開啟`cd`到您的函式應用程式，並執行下列命令的目錄：

```sh
func host start
```

與執行的函式應用程式和`Wait-Debugger`就地的情況下，您可以附加至處理序。 您需要兩個更多的 PowerShell 主控台。

在主控台的其中一個做為用戶端。 從這裡開始，您呼叫`Invoke-RestMethod`觸發的函式。 例如，您可以執行下列命令：

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

您會注意到，它不會傳回回應，也就是結果的`Wait-Debugger`。 PowerShell runspace 目前正在等待連接偵錯工具。 讓我們取得連接。

在其他 PowerShell 主控台中，執行下列命令：

```powershell
Get-PSHostProcessInfo
```

此 cmdlet 會傳回資料表，看起來像下列的輸出：

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

請記下的`ProcessId`具有資料表中的項目`ProcessName`做為`dotnet`。 此程序是函式應用程式。

接下來，執行下列程式碼片段：

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

一旦啟動，偵錯工具中斷，並顯示類似下列輸出：

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

此時，您會在中斷點處停止[PowerShell 偵錯工具](/powershell/module/microsoft.powershell.core/about/about_debuggers)。 從這裡開始，您可以執行所有一般的偵錯作業、 不進入、 逐步執行、 繼續、 結束，和其他人。 若要查看完整的主控台中可用的偵錯命令集，請執行`h`或`?`命令。

您也可以設定中斷點，在此層級使用`Set-PSBreakpoint`cmdlet。

當您繼續，而且完全叫用您的指令碼時，您會發現：

* 您執行的 PowerShell 主控台`Invoke-RestMethod`現在傳回結果。
* 您執行的 PowerShell 主控台`Debug-Runspace`正在等候執行的指令碼。

您可以一次叫用相同的函式 (使用`Invoke-RestMethod`比方說) 和偵錯工具中斷在緊接著`Wait-Debugger`命令。

## <a name="considerations-for-debugging"></a>偵錯的考量

請記住下列的問題進行偵錯函式程式碼時。

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` 可能會導致您的偵錯工具中斷未預期的地方

PowerShell 延伸模組會使用`Debug-Runspace`，它接著會依賴 PowerShell 的`BreakAll`功能。 這項功能會告知停止在第一個命令所執行的 PowerShell。 此行為可讓您設定中斷點，偵錯的 runspace 內的機會。

Azure Functions 執行階段會執行數個命令之前實際叫用您`run.ps1`指令碼，因此可以偵錯工具會在中斷`Microsoft.Azure.Functions.PowerShellWorker.psm1`或`Microsoft.Azure.Functions.PowerShellWorker.psd1`。

應該會發生這項中斷，執行`continue`或`c`略過此中斷點的命令。 您接著在預期的中斷點停止。

## <a name="next-steps"></a>後續步驟

若要深入了解如何開發使用 PowerShell 函式，請參閱[Azure Functions PowerShell 開發人員指南](functions-reference-powershell.md)。
