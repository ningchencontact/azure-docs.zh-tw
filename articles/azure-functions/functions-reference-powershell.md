---
title: 適用於 Azure Functions 的 PowerShell 開發人員參考
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
ms.openlocfilehash: 46b1e5c99dd86fed6f87ac3b8f0ff6555187899b
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833511"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions PowerShell 開發人員指南

這篇文章提供有關如何撰寫使用 PowerShell 的 Azure Functions 的詳細資料。

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

PowerShell 的 Azure 函式 （函式） 被以觸發時執行的 PowerShell 指令碼。 每個函式指令碼有一個相關`function.json`定義函式的運作方式，例如觸發方式的檔案和其輸入和輸出參數。 若要進一步了解，請參閱[觸發程序和繫結文章](functions-triggers-bindings.md)。 

其他種類的函式，例如 PowerShell 指令碼函式會採用在參數中所定義的所有輸入繫結的名稱相符的`function.json`檔案。 A`TriggerMetadata`參數也會傳遞包含啟動函式的觸發程序的其他資訊。

本文假設您已經讀過 [Azure Functions 開發人員參考](functions-reference.md)。 您應該也已經完成[適用於 PowerShell 的 Functions 快速入門](functions-create-first-function-powershell.md)來建立您的第一個 PowerShell 函式。

## <a name="folder-structure"></a>資料夾結構

PowerShell 專案的必要的資料夾結構看起來如下所示。 此預設值可以變更。 如需詳細資訊，請參閱下面的 [scriptFile](#configure-function-scriptfile) 一節。

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

在專案根目錄中，沒有共用[ `host.json` ](functions-host-json.md)可用來設定函數應用程式的檔案。 每個函式已有自己的程式碼檔案 (.ps1) 和繫結組態檔的資料夾 (`function.json`)。 Function.json 檔案的上層目錄的名稱永遠是您的函式的名稱。

特定繫結需要`extensions.csproj`檔案。 繫結延伸模組，需要[版本 2.x](functions-versions.md) Functions 執行階段中定義`extensions.csproj`檔案，與實際的程式庫檔案中`bin`資料夾。 在本機開發時，您必須[註冊繫結擴充功能](functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles)。 開發 Azure 入口網站中的函式時，就會為您完成這項註冊。

在 PowerShell 函式應用程式，您可能會選擇`profile.ps1`函式應用程式開始執行時執行 (否則知道*[冷啟動](#cold-start)*。 如需詳細資訊，請參閱 < [PowerShell 設定檔](#powershell-profile)。

## <a name="defining-a-powershell-script-as-a-function"></a>函式中定義的 PowerShell 指令碼

根據預設，Functions 執行階段會在 `run.ps1` 中尋找您的函式，其中 `run.ps1` 與對應的 `function.json` 會共用相同的父目錄。

您的指令碼會傳遞引數上執行。 若要處理這些參數，新增`param`區塊，以您的指令碼，如下列範例所示的頂端：

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata 參數

`TriggerMetadata`參數用來提供觸發程序的其他資訊。 額外的中繼資料會繫結至繫結而有所不同，但它們都包含`sys`屬性，其中包含下列資料：

```powershell
$TriggerMetadata.sys
```

| 屬性   | 描述                                     | Type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | 時，以 utc 格式，此函式已觸發        | DateTime |
| MethodName | 已觸發的函式名稱     | string   |
| RandGuid   | 此執行函式的唯一 guid | string   |

每個觸發程序型別具有一組不同的中繼資料。 例如， `$TriggerMetadata` for`QueueTrigger`包含`InsertionTime`， `Id`， `DequeueCount`，以及其他項目。 如需有關佇列觸發程序的中繼資料的詳細資訊，請移至[佇列觸發程序的正式文件](functions-bindings-storage-queue.md#trigger---message-metadata)。 檢查文件上[觸發程序](functions-triggers-bindings.md)您正在使用以查看功能隨附於觸發程序中繼資料。

## <a name="bindings"></a>繫結

在 PowerShell 中，[繫結](functions-triggers-bindings.md)設定及函式的 function.json 中定義。 Functions 會透過數種方式與繫結互動。

### <a name="reading-trigger-and-input-data"></a>讀取觸發程序和輸入資料

觸發程序和輸入繫結會讀取做為參數傳遞至您的函式。 輸入繫結都有`direction`設定為`in`function.json 中。 `name`中所定義的屬性`function.json`是中的參數名稱`param`區塊。 PowerShell 會使用具名的參數繫結，因為並不重要參數的順序。 不過，最好遵循中定義的繫結順序`function.json`。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>寫入輸出資料

在函數中，有的輸出繫結`direction`設定為`out`function.json 中。 您可以使用寫入的輸出繫結`Push-OutputBinding`cmdlet，可用於 Functions 執行階段。 在所有情況下，`name`屬性中所定義的繫結`function.json`對應至`Name`參數`Push-OutputBinding`cmdlet。

下圖顯示如何呼叫`Push-OutputBinding`函式指令碼中：

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

您也可以針對特定的繫結，透過管線傳遞的值。

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` 根據指定的值的行為`-Name`:

* 當指定的名稱無法解析為有效的輸出繫結時，則會擲回錯誤。

* 當輸出繫結可接受值的集合時，您可以呼叫`Push-OutputBinding`重複將多個值。

* 當輸出繫結只會接受單一值時，呼叫`Push-OutputBinding`第二次會引發錯誤。

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` 語法

以下是有效的參數呼叫`Push-OutputBinding`:

| 名稱 | Type | 位置 | 說明 |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | 您要設定輸出繫結的名稱。 |
| **`-Value`** | Object | 2 | 輸出繫結的值要設定，其可接受來自管線 ByValue。 |
| **`-Clobber`** | SwitchParameter | 已命名 | （選擇性）指定時，會強制設為指定的輸出繫結的值。 | 

也支援下列的一般參數： 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

如需詳細資訊，請參閱 <<c0> [ 關於 CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)。

#### <a name="push-outputbinding-example-http-responses"></a>推播 OutputBinding 範例：HTTP 回應

HTTP 觸發程序傳回回應，使用名為輸出繫結`response`。 在下列範例中，輸出繫結的`response`"output 1"的值：

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

輸出會是 HTTP，它會接受單一值，因為發生錯誤時擲回`Push-OutputBinding`呼叫第二次。

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

若為只接受單一值的輸出，您可以使用`-Clobber`參數來覆寫舊的值，而不是嘗試要加入至集合。 下列範例假設您已新增的值。 使用`-Clobber`，下列範例回應會覆寫現有的值來傳回 「 輸出 #3 」 的值：

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>推播 OutputBinding 範例：佇列輸出繫結

`Push-OutputBinding` 用來將資料傳送至輸出繫結，例如[Azure 佇列儲存體輸出繫結](functions-bindings-storage-queue.md#output)。 在下列範例中，寫入佇列的訊息會有 「 輸出 #1 」 的值：

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

儲存體佇列輸出繫結可接受多個輸出值。 在此情況下，寫入至佇列兩個項目清單的第一個之後，呼叫下列的範例: 「 輸出 #1 」 和 「 輸出 #2 」。

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

下列範例中，當呼叫上述兩者之後, 會加入輸出集合中的兩個其他價值：

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

當寫入佇列，訊息會包含這四個值: 「 輸出 #1 」、 「 輸出 #2 」、 「 輸出 #3 」 和 「 輸出 #4 」。

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` 指令程式

您可以使用`Get-OutputBinding`cmdlet 來擷取目前為您的輸出繫結設定的值。 此 cmdlet 可擷取雜湊表，其中包含其各自的值與輸出繫結的名稱。 

以下是使用的範例`Get-OutputBinding`傳回目前繫結的值：

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` 也包含名為的參數`-Name`，可用來篩選傳回的繫結，如下列範例所示：

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

在支援使用萬用字元 （*） `Get-OutputBinding`。

## <a name="logging"></a>記錄

登入 PowerShell 函式的運作方式類似一般的 PowerShell 記錄。 您可以使用記錄 cmdlet 来寫入每個輸出資料流。 每個 cmdlet 會對應至函式所使用的記錄層級。

| 記錄層級的函式 | 記錄 cmdlet |
| ------------- | -------------- |
| Error | **`Write-Error`** |
| 警告 | **`Write-Warning`**  | 
| 資訊 | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | 資訊 | 寫入_資訊_層級記錄。 |
| 偵錯 | **`Write-Debug`** |
| 追蹤 | **`Write-Progress`** <br /> **`Write-Verbose`** |

除了這些 cmdlet，寫入到管線的任何項目會重新導向至`Information`記錄層級，以預設 PowerShell 的格式顯示。

> [!IMPORTANT]
> 使用`Write-Verbose`或`Write-Debug`cmdlet 尚不足以查看詳細資訊和偵錯層級記錄。 您也必須設定記錄層級臨界值，其宣告的真正關心的記錄層級。 若要進一步了解，請參閱[設定的函式應用程式記錄層級](#configure-the-function-app-log-level)。

### <a name="configure-the-function-app-log-level"></a>設定函式應用程式記錄層級

Functions 可讓您定義的臨界值層級，讓您輕鬆地控制方式函式會寫入記錄檔。 若要設定寫入至主控台的所有追蹤的閾值，請使用`logging.logLevel.default`中的屬性[`host.json`檔案][host.json 參考]。 這個設定會套用到函式應用程式中的所有函式。

下列範例設定的臨界值，以啟用所有的函式的詳細資訊記錄，但設定的臨界值，若要啟用名為函式的偵錯記錄`MyFunction`:

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

如需詳細資訊，請參閱 [host.json 參考]。

### <a name="viewing-the-logs"></a>檢視記錄檔

如果您的函式應用程式在 Azure 中執行，您可以使用 Application Insights 來監視它。 參閱[監視 Azure Functions](functions-monitoring.md) 深入了解如何檢視和查詢函式記錄。

如果您執行您的函式應用程式，在本機進行開發，記錄預設檔案系統。 若要查看記錄檔，在主控台中，設定`AZURE_FUNCTIONS_ENVIRONMENT`環境變數，以`Development`之前啟動函式應用程式。

## <a name="triggers-and-bindings-types"></a>觸發程序和繫結類型

有許多觸發程序和繫結可讓您使用您的函式應用程式。 觸發程序和繫結的完整清單[可以在這裡找到](functions-triggers-bindings.md#supported-bindings)。

以幾個實際的資料類型，會將所有的觸發程序和繫結表示程式碼中：

* 雜湊表
* string
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

這份清單中的前五個型別是標準的.NET 類型。 這兩個只供[HttpTrigger 觸發程序](#http-triggers-and-bindings)。

在您的函式中的每個繫結參數必須是其中一種類型。

### <a name="http-triggers-and-bindings"></a>HTTP 觸發程序和繫結

HTTP 和 Webhook 觸發程序以及 HTTP 輸出繫結會使用要求和回應物件來代表 HTTP 傳訊。

#### <a name="request-object"></a>要求物件

傳遞至指令碼的要求物件是型別的`HttpRequestContext`，其具有下列屬性：

| 屬性  | 描述                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | 包含要求本文的物件。 `Body` 會序列化為以資料為依據的最佳類型。 比方說，如果資料是 JSON，它會傳入做為雜湊表。 如果資料是字串，它傳入做為字串。 | 物件 |
| **`Headers`** | 字典，包含要求標頭。                | Dictionary<string,string><sup>*</sup> |
| **`Method`** | 要求的 HTTP 方法。                                | string                    |
| **`Params`**  | 包含要求之路由傳送參數的物件。 | Dictionary<string,string><sup>*</sup> |
| **`Query`** | 包含查詢參數的物件。                  | Dictionary<string,string><sup>*</sup> |
| **`Url`** | 要求的 URL。                                        | string                    |

<sup>*</sup> 所有`Dictionary<string,string>`索引鍵不區分大小寫。

#### <a name="response-object"></a>回應物件

您應該傳回的回應物件是型別的`HttpResponseContext`，其具有下列屬性：

| 屬性      | 描述                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | 包含回應本文的物件。           | 物件                    |
| **`ContentType`** | 簡短的指針的回應內容類型設定。 | string                    |
| **`Headers`** | 包含回應標頭的物件。               | 字典或雜湊表   |
| **`StatusCode`**  | 回應的 HTTP 狀態碼。                       | 字串或整數             |

#### <a name="accessing-the-request-and-response"></a>存取要求和回應

當您使用 HTTP 觸發程序時，您可以在相同的方式就和使用任何其他輸入繫結時存取的 HTTP 要求。 處於`param`區塊。

使用`HttpResponseContext`物件傳回回應，如下列所示：

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

叫用此函式的結果會是︰

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>觸發程序和繫結的型別轉換

針對某些像是 blob 繫結的繫結，您可以指定參數的型別。

比方說，若要從 Blob 儲存體提供做為字串的資料，請將下列類型轉換成我`param`區塊：

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell 設定檔

在 PowerShell 中，沒有 PowerShell 設定檔的概念。 如果您不熟悉如何使用 PowerShell 設定檔，請參閱[設定檔的相關](/powershell/module/microsoft.powershell.core/about/about_profiles)。

在 PowerShell 函式，函式應用程式啟動時執行設定檔指令碼。 函式應用程式開始，當第一次部署，而且正在閒置之後 ([冷啟動](#cold-start))。

當您建立一個使用工具，例如 Visual Studio Code 和 Azure Functions Core Tools，預設值的函式應用程式`profile.ps1`會為您建立。 預設設定檔會維護[Core 工具 GitHub 存放庫上](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1)並且包含：

* 自動 MSI 向 Azure 進行驗證。
* 若要開啟 Azure PowerShell 的能力`AzureRM`PowerShell 別名，如果您想要。

## <a name="powershell-version"></a>PowerShell 版本

下表顯示每個 Functions 執行階段的主要版本所使用的 PowerShell 版本：

| Functions 版本 | PowerShell 版本                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 （由執行階段鎖定） |
| 2.x               | PowerShell Core 6                              |

您可以看到目前的版本，透過列印`$PSVersionTable`從任何函式。

## <a name="dependency-management"></a>相依性管理

PowerShell 函式支援服務所管理的 Azure 模組。 藉由修改 host.json 並啟用 managedDependency 屬性設定為 true，將會處理 requirements.psd1 檔案。 最新的 Azure 模組會自動下載並提供給函式。

host.json
```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

requirements.psd1

```powershell
@{
    Az = '1.*'
}
```

利用您自己的自訂模組或[PowerShell 資源庫](https://powershellgallery.com)與您該怎麼做它通常有點不同。

當您在本機電腦上安裝的模組時，它會在全域可用的資料夾中的一個您`$env:PSModulePath`。 在 Azure 中執行您的函式，因為您不需要存取您的電腦上安裝的模組。 這需要`$env:PSModulePath`PowerShell 函式應用程式與`$env:PSModulePath`一般的 PowerShell 指令碼中。

在函數中，`PSModulePath`包含兩個路徑：

* A`Modules`存在的函式應用程式根目錄的資料夾。
* 通往`Modules`PowerShell 語言的背景工作角色內的資料夾。

### <a name="function-app-level-modules-folder"></a>函式應用程式層級`Modules`資料夾

若要從 PowerShell 資源庫中使用自訂模組或 PowerShell 模組，您可以將放在您的函式所相依的模組`Modules`資料夾。 從這個資料夾中，模組會自動提供給 functions 執行階段。 函式應用程式中的任何函式可以使用這些模組。

若要利用這項功能，建立`Modules`函式應用程式的根目錄中的資料夾。 儲存您想要使用您的函式，在這個位置中的模組。

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

使用 `Save-Module`儲存的所有模組會使用您的函式，或複製到您自己自訂的模組`Modules`資料夾。 與模組資料夾中，您的函式應用程式應該有下列資料夾結構：

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyGalleryModule
 | | - MyOtherGalleryModule
 | | - MyCustomModule.psm1
 | - local.settings.json
 | - host.json
```

當您啟動您的函式應用程式時，PowerShell 語言的背景工作角色加入這`Modules`資料夾，以`$env:PSModulePath`，讓您可以依賴自動載入的模組就如同一般的 PowerShell 指令碼中。

### <a name="language-worker-level-modules-folder"></a>語言背景工作層級`Modules`資料夾

PowerShell 語言的背景工作角色通常會使用數個模組。 這些模組中的最後一個位置定義`PSModulePath`。 

目前的模組清單如下所示：

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive)： 用於封存，使用類似的模組`.zip`， `.nupkg`，和其他人。
* **ThreadJob**:執行緒為基礎的 PowerShell 工作 Api 實作。

函式會使用這些模組的最新版本。 若要使用這些模組的特定版本，您可以將特定版本放在`Modules`函式應用程式的資料夾。

## <a name="environment-variables"></a>環境變數

在 Functions 中，[應用程式設定](functions-app-settings.md) (例如服務連接字串) 在執行期間會公開為環境變數。 您可以使用這些設定`$env:NAME_OF_ENV_VAR`，如下列範例所示：

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

在本機執行時，應用程式設定會讀取自 [local.settings.json](functions-run-local.md#local-settings-file) 專案檔。

## <a name="concurrency"></a>並行

根據預設，函式的 PowerShell 執行階段只能處理一次一個叫用函式。 不過，這個並行存取層級可能無法完全在下列情況：

* 當您嘗試同時處理大量的引動過程。
* 當您有叫用相同的函式應用程式內其他函式的函式。

您可以藉由將下列環境變數設定為整數值來變更此行為：

```
PSWorkerInProcConcurrencyUpperBound
```

您設定這個環境變數[應用程式設定](functions-app-settings.md)函式應用程式。

### <a name="considerations-for-using-concurrency"></a>使用並行存取的考量

PowerShell 是_單一執行緒_預設指令碼語言。 不過，並行處理可以新增相同的處理序中使用多個 PowerShell runspace。 這項功能是 Azure Functions PowerShell 執行階段的運作方式。

有一些缺點，這種方法。

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>並行存取只會顯示為電腦執行

如果您的函式應用程式上執行[App Service 方案](functions-scale.md#app-service-plan)，僅支援單一核心，則並行存取將不會說明許多。 這是因為沒有任何額外的核心，以協助平衡負載。 在此情況下，效能而有所不同時單一核心內容切換之間的 runspace。

[耗用量計劃](functions-scale.md#consumption-plan)都是使用只有一個核心，因此您不能運用並行執行。 如果您想要充分利用並行處理，改為部署您的函式的函式應用程式專用的 App Service 方案，具有足夠的核心上執行。

#### <a name="azure-powershell-state"></a>Azure PowerShell 的狀態

Azure PowerShell 會使用一些_處理序層級_內容和狀態，以協助您設定輸入的過多。 不過，如果您開啟 函數應用程式中的 並行存取，並叫用變更狀態的動作，您就會產生競爭情形。 這些競爭情形很難進行偵錯，因為一個引動過程是特定狀態，而其他引動過程會變更狀態。

因為某些作業可能需要花一些時間在使用 Azure PowerShell 的並行沒有龐大的值。 不過，您必須小心。 如果您懷疑您遇到競爭情形，將並行處理回到`1`並再試一次要求。

## <a name="configure-function-scriptfile"></a>設定函式 `scriptFile`

根據預設，從執行的 PowerShell 函式`run.ps1`，共用相同的父目錄，為其對應的檔案`function.json`。

`scriptFile`屬性中的`function.json`可用來取得資料夾結構，如下列範例所示：

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

在此情況下， `function.json` for`myFunction`包含`scriptFile`參考匯出的函式來執行具有檔案的屬性。

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>使用 PowerShell 模組，藉由設定 進入點

這篇文章已示範在預設的 PowerShell 函式`run.ps1`範本所產生的指令碼檔案。
不過，您也可以在 PowerShell 模組中包含您的函式。 您可以參考特定的函式程式碼模組中使用`scriptFile`和`entryPoint`function.json 中的欄位 ' 組態檔。

在此情況下，`entryPoint`是函式或參考中的 PowerShell 模組中 cmdlet 的名稱`scriptFile`。

請考慮下列資料夾結構：

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

其中`PSFunction.psm1`包含：

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

在此範例中的組態`myFunction`包含`scriptFile`參照屬性`PSFunction.psm1`，這是另一個資料夾中的 PowerShell 模組。  `entryPoint`屬性參考`Invoke-PSTestFunc`函式，這是模組中的進入點。

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

使用此設定時，`Invoke-PSTestFunc`取得執行的完全相同`run.ps1`會。

## <a name="considerations-for-powershell-functions"></a>PowerShell 函式的考量

當您使用 PowerShell 函式時，請注意下列各節中的考量事項。

### <a name="cold-start"></a>冷啟動

當開發中的 Azure Functions[無伺服器裝載模型](functions-scale.md#consumption-plan)、 冷啟動會成真。 *冷啟動*是指一段時間就會採用函式應用程式開始執行處理的要求。 冷啟動會發生頻率較高的耗用量計劃，因為您的函式應用程式會無活動期間加以關閉。

### <a name="bundle-modules-instead-of-using-install-module"></a>套件組合模組，而不是使用 `Install-Module`

您的指令碼會執行每次叫用。 請避免使用`Install-Module`指令碼中。 改為使用`Save-Module`之前發行，讓您的函式不需要浪費時間下載模組。 如果冷啟動會影響您的函式，請考慮部署您的函式應用程式[App Service 方案](functions-scale.md#app-service-plan)設為*alwayson*上，或者[Premium 方案](functions-scale.md#premium-plan-public-preview)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure Functions 開發人員參考](functions-reference.md)
* [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)

[host.json 參考]: functions-host-json.md
