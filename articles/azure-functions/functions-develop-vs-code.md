---
title: 使用 Visual Studio Code 開發 Azure Functions |Microsoft Docs
description: 了解如何開發及測試 Azure Functions Visual Studio Code 使用 Azure Functions 擴充功能。
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: glenga
ms.openlocfilehash: 17550e148ea61eb69a20fc6a3215dfb63b65f18e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452692"
---
# <a name="develop-azure-functions-using-visual-studio-code"></a>使用 Visual Studio Code 開發 Azure Functions

[適用於 Visual Studio Code 的 Azure Functions 擴充功能]可讓您在本機開發及部署至 Azure 的函式。 如果這是您第一次體驗 Azure Functions，可至 [Azure Functions 簡介](functions-overview.md)深入了解。

Azure Functions 擴充功能會提供下列優點： 

* 在本機開發電腦上編輯、建置及執行函數。 
* 直接將 Azure Functions 專案發佈至 Azure。 
* 以各種語言撰寫您的函式，同時擁有所有的 Visual Studio Code 的優點。 

擴充功能可以搭配 Azure Functions 版本 2.x 執行階段支援下列語言版本： 

* [C#編譯](functions-dotnet-class-library.md) 
* [C#指令碼](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>您需要您[設定C#做為預設專案語言的指令碼](#c-script-projects)。

在本文中，範例目前只適用於 JavaScript (Node.js) 和C#類別程式庫函式。  

這篇文章提供有關如何使用 Azure Functions 擴充功能來開發函式，並將其發行至 Azure 的詳細資料。 在閱讀本文之前，您應該[建立第一個函式使用 Visual Studio Code](functions-create-first-function-vs-code.md)。

> [!IMPORTANT]
> 請勿在相同函式應用程式中混用本機開發與入口網站開發。 當您從本機專案發佈至函式應用程式時，部署程序將會覆寫您在入口網站開發的任何函式。

## <a name="prerequisites"></a>必要條件

在您安裝並執行之前[Azure Functions 擴充功能][適用於 Visual Studio Code 的 Azure Functions 擴充功能]，您必須符合下列需求：

* [Visual Studio Code](https://code.visualstudio.com/)上的其中一個安裝[支援的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)。

* 有效的 Azure 訂用帳戶。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

您的訂用帳戶中建立其他所需資源，例如 Azure 儲存體帳戶，當您[發佈使用 Visual Studio Code](#publish-to-azure)。

> [!IMPORTANT]
> 您可以開發函式在本機，並將發佈至 Azure，而不必啟動，並在本機執行它們。 有其他需求，您功能在本機執行，包括自動下載的 Azure Functions Core Tools。 若要進一步了解，請參閱[本機執行的其他需求](#additional-requirements-to-run-locally)。 

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>建立 Azure Functions 專案

函式延伸模組可讓您建立函式應用程式專案，以及您的第一個函式。 下列步驟示範如何建立新的函式專案中的 HTTP 觸發函式。 [HTTP 觸發程序](functions-bindings-http-webhook.md)是為了示範最簡單的函式觸發程序範本。

1. 從 [Azure: 函式]  中，選擇 [建立函式] 圖示。

    ![建立函式](./media/functions-develop-vs-code/create-function.png)

1. 選取您的函式應用程式專案的資料夾，然後**選取您的函式專案的語言**。 

1. 選取  **HTTP 觸發程序**函式樣板，或您可以選擇**現在略過**建立不含函式的專案。 您可以隨時[將功能加入您的專案](#add-a-function-to-your-project)時間較晚。 

    ![選擇 HTTP 觸發程序範本](./media/functions-develop-vs-code/create-function-choose-template.png)

1. 型別`HTTPTrigger`作為函式名稱，然後按 Enter，然後選取**函式**授權。 此授權層級會要求您提供[函式金鑰](functions-bindings-http-webhook.md#authorization-keys)時呼叫的函式端點。

    ![選擇函式驗證](./media/functions-develop-vs-code/create-function-auth.png)

    系統隨即會使用由 HTTP 觸發的函式範本，以您所選的語言來建立函式。

    ![Visual Studio Code 中的 HTTP 觸發函式範本](./media/functions-develop-vs-code/new-function-full.png)

專案範本建立專案，在您選擇的語言中，會安裝必要的相依性。 任何語言、 新的專案會有下列檔案：

* **host.json**：可讓您設定 Functions 主機。 這些設定同時適用於在本機執行及在 Azure 中執行。 如需詳細資訊，請參閱 [host.json 參考](functions-host-json.md)。

* **local.settings.json**：維護在本機執行函數時所使用的設定。 在本機執行時，只會使用這些設定。 如需詳細資訊，請參閱 <<c0> [ 本機設定檔](#local-settings-file)。

    >[!IMPORTANT]
    >由於 local.settings.json 檔案可以包含祕密，因此，您必須從專案原始檔控制中排除它。

到目前為止，您可以新增輸入和輸出繫結至您的函式所[修改 function.json](#javascript-2)，或由[加入的參數C#類別程式庫函式](#c-class-library-2)。

您也可以[專案中加入新的函式](#add-a-function-to-your-project)。

## <a name="install-binding-extensions"></a>安裝繫結延伸模組

除了 HTTP 和計時器觸發程序繫結會實作延伸模組套件。 您必須安裝延伸模組套件，觸發程序和繫結需要它們。 您安裝繫結延伸模組的方式取決於您專案的語言。

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>C\#類別庫

執行[dotnet 新增套件](/dotnet/core/tools/dotnet-add-package)命令，在終端機視窗中安裝您需要在您的專案中的延伸模組套件。 下列範例會安裝 Azure 儲存體擴充功能，它會實作的 Blob、 佇列和資料表儲存體繫結。

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

## <a name="add-a-function-to-your-project"></a>將函式新增至您的專案

您可以透過其中一個預先定義的函式觸發程序範本，將新的函式新增至現有的專案中。 若要新增新的函式觸發程序，請按 F1 鍵開啟命令選擇區中，然後搜尋並執行命令**Azure Functions:建立函數...** .遵循提示來選擇您的觸發程序類型和定義的觸發程序必要的屬性。 如果您的觸發程序需要存取金鑰或連接字串以連線至服務，準備好之前建立的函式觸發程序。 

這項作業的結果取決於您專案的語言：

### <a name="javascript"></a>JavaScript

在專案中，其中包含新的 function.json 檔案和新的 JavaScript 程式碼檔案建立新的資料夾。

### <a name="c-class-library"></a>C\#類別庫

新C#類別庫 (.cs) 檔案加入至專案。

## <a name="add-input-and-output-bindings"></a>新增輸入和輸出繫結

您可以展開您的函式，將輸入和輸出繫結。 採用這種方式取決於您專案的語言。 若要深入了解繫結，請參閱[Azure Functions 觸發程序和繫結概念](functions-triggers-bindings.md)。 

下列範例會連接到名為儲存體佇列`outqueue`，其中的儲存體帳戶的連接字串中設定`MyStorageConnection`local.settings.json 中的應用程式設定。 

### <a name="javascript"></a>JavaScript

Visual Studio Code 可讓您將繫結新增至您的 function.json 檔案中，遵循一組方便提示。 若要建立繫結，以滑鼠右鍵按一下 （Ctrl + 按一下，在 macOS 上的）`function.json`函式資料夾中的檔案，然後選擇**新增繫結...** . 

![新增繫結至現有的 JavaScript 函式 ](media/functions-develop-vs-code/function-add-binding.png)

以下是範例提示來定義新的儲存體輸出繫結：

| Prompt | 值 | 描述 |
| -------- | ----- | ----------- |
| **選取繫結方向** | `out` | 此繫結為輸出繫結。 |
| **選取繫結方向...** | `Azure Queue Storage` | 此繫結是 Azure 儲存體佇列繫結。 |
| **用來識別此繫結程式碼中的名稱** | `msg` | 識別您的程式碼中參考之繫結參數的名稱。 |
| **傳送訊息的佇列** | `outqueue` | 作為繫結寫入目標的佇列名稱。 當 *queueName* 不存在，繫結會在第一次使用時加以建立。 |
| **選取 從"local.setting.json 」 的設定** | `MyStorageConnection` | 包含儲存體帳戶的連接字串的應用程式設定名稱。 `AzureWebJobsStorage` 設定會包含您以函式應用程式建立之儲存體帳戶的連接字串。 |

在此範例中，加入下列繫結至`bindings`function.json 檔案中的陣列：

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

您也可以直接將您的 function.json 新增相同的繫結定義。

在您的函式程式碼`msg`繫結從存取`context`，如下列範例所示：

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

若要進一步了解，請參閱[佇列儲存體輸出繫結](functions-bindings-storage-queue.md#output---javascript-example)參考。

### <a name="c-class-library"></a>C\#類別庫

更新函式方法，以新增下列參數，`Run`方法定義：

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

此程式碼會要求您加入下列`using`陳述式：

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

`msg`參數是`ICollector<T>`完成訊息寫入至輸出繫結時的函式的集合所代表的類型。 您新增一或多個訊息至集合中，哪些函式完成時，會傳送至佇列。

若要進一步了解，請參閱[佇列儲存體輸出繫結](functions-bindings-storage-queue.md#output---c-example)參考。

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="publish-to-azure"></a>發佈至 Azure

Visual Studio Code 可讓您將函式專案直接發佈到 Azure。 在這過程中，您會在 Azure 訂用帳戶中建立函式應用程式和相關的資源。 函式應用程式會為函式提供執行內容。 專案會封裝並部署到您 Azure 訂用帳戶中的新函式應用程式。

發行時從 Visual Studio Code，會使用兩種部署方法之一：

* [壓縮部署與執行-從-套件啟用](functions-deployment-technologies.md#zip-deploy)： 用於大部分的 Azure Functions 部署。
* [外部套件 URL](functions-deployment-technologies.md#external-package-url)： 用於部署至 Linux 應用程式上[取用方案](functions-scale.md#consumption-plan)。

### <a name="quick-function-app-creation"></a>建立快速的函式應用程式

根據預設，Visual Studio Code 會自動產生的函式應用程式所需的 Azure 資源的值。 這些值會根據您選擇的函式應用程式名稱。 如需使用預設為將專案發佈至新的函式應用程式在 Azure 中的範例，請參閱 < [Visual Studio Code 的快速入門文章](functions-create-first-function-vs-code.md#publish-the-project-to-azure)。

如果您想要提供明確名稱建立的資源，您必須啟用發行使用進階的選項。

### <a name="enabled-publishing-with-advanced-create-options"></a>使用進階的已啟用的發行建立選項

若要讓您控制設定相關聯建立 Azure 函式應用程式時，更新的 Azure Functions 擴充功能，以啟用進階的設定。

1. 按一下 **檔案 > 喜好設定 > 設定**

1. 瀏覽**使用者設定 > 擴充功能 > Azure 函式**

1. 檢查的核取方塊**Azure 函式：進階的建立**

### <a name="publish-to-a-new-function-app-in-azure-with-advanced-creation"></a>發行的新函式應用程式在 Azure 中使用進階的建立

下列步驟將專案發佈至新建立的函式應用程式使用進階建立選項。

1. 在 [Azure：  函式] 區域中，選取 [部署至函數應用程式] 圖示。

    ![函式應用程式設定](./media/functions-develop-vs-code/function-app-publish-project.png)

1. 如果未登入，系統會提示您**登入 Azure**。 您也可以建立**免費 Azure 帳戶**。 從瀏覽器成功登入之後，請返回 Visual Studio Code。

1. 如果您有多個訂用帳戶，請為函數應用程式**選取訂用帳戶**，然後選擇 [+ 在 Azure 中建立新函數應用程式]  。

1. 下列提示字元中，提供下列資訊：

    | Prompt | 值 | 描述 |
    | ------ | ----- | ----------- |
    | 在 Azure 中的選取函式應用程式 | + 在 Azure 中建立新的函式應用程式 | 在下一步 提示字元中，輸入可識別您新的函式應用程式的全域唯一名稱，然後按 Enter。 函式應用程式名稱的有效字元為 `a-z`、`0-9` 和 `-`。 |
    | 選取 OS | Windows | Windows 上執行的函式應用程式 |
    | 選取主控方案 | 取用方案 | 無伺服器[使用情況方案裝載](functions-scale.md#consumption-plan)用。 |
    | 選取新的應用程式的執行階段 | 您專案的語言 | 執行階段必須符合您要發行的專案。 |
    | 選取新的資源的資源群組 | 建立新的資源群組 | 在下一步 提示字元中，輸入資源群組名稱，例如`myResourceGroup`，然後按 enter 鍵。 您也可以選擇現有的資源群組。 |
    | 選取儲存體帳戶 | 建立新的儲存體帳戶 | 在下一步 提示字元中，新的儲存體帳戶類型的全域唯一的名稱使用您的函式應用程式，然後按 Enter。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。 您也可以選擇現有的帳戶。 |
    | 選取新的資源的位置 | region | 在[區域](https://azure.microsoft.com/regions/)中選擇位置，此位置應靠近您或靠近函式會存取的其他服務。 |

    建立函式應用程式並套用部署套件之後，即會顯示通知。 在通知中選取 [檢視輸出]  ，即可檢視建立和部署結果，包括您所建立的 Azure 資源。

## <a name="republish-project-files"></a>重新發佈專案檔

當您設定[持續部署](functions-continuous-deployment.md)，每當連線的來源位置中更新來源檔案時，會更新函式應用程式在 Azure 中的。 雖然我們建議此開發做法，也可以重新發佈您的專案檔更新，從 Visual Studio Code。 

> [!IMPORTANT]
> 發佈至現有的函式應用程式會覆寫該應用程式在 Azure 中的內容。

1. 在 Visual Studio Code 中，按 f1 鍵開啟命令選擇區。 在命令選擇區中，搜尋並選取`Azure Functions: Deploy to function app...`。

1. 如果未登入，系統會提示您**登入 Azure**。 從瀏覽器成功登入之後，請返回 Visual Studio Code。 如果您有多個訂用帳戶**選取的訂用帳戶**，其中包含您的函式應用程式。

1. 在 Azure 中選擇現有的函式應用程式。 當之前接到警告覆寫函式應用程式中的所有檔案，選擇**部署**認可此警告並繼續。 

專案已重建、 重新封裝，並上傳至 Azure。 新的封裝，取代現有的專案，所以函式應用程式重新啟動。

## <a name="get-deployed-function-url"></a>取得已部署的函式 URL

若要能夠呼叫的 HTTP 觸發函式，您需要部署至您的函式應用程式時，此函式的 URL。 此 URL 會包含任何必要[函式金鑰](functions-bindings-http-webhook.md#authorization-keys)。 您可以使用擴充功能，以取得這些 Url，您已部署的函式。

1. 按下 F1 鍵開啟命令選擇區中，然後搜尋並執行命令**Azure Functions:複製函式 URL**。

1. 遵循提示來選擇 Azure，然後您要叫用特定的 HTTP 觸發的函式應用程式。 

URL 已複製到剪貼簿，以及任何必要的金鑰，使用傳遞的函式`code`查詢參數。 您可以使用的 HTTP 工具提交 POST 要求或遠端函式的 GET 要求的瀏覽器。  

## <a name="run-functions-locally"></a>在本機執行函式

Azure Functions 擴充功能可讓您在本機開發電腦上執行 functions 專案。 本機執行階段是相同的執行階段裝載您在 Azure 中的函式應用程式。 本機設定從讀取[local.settings.json 檔案](#local-settings-file)。

### <a name="additional-requirements-to-run-locally"></a>若要在本機執行的其他需求

若要能夠執行 Functions 專案在本機，您也必須符合這些額外的需求：

* 安裝 2.x 版的 [Azure Functions Core Tools](functions-run-local.md#v2)。 Core Tools 套件會下載並安裝程式會自動啟動時在本機的專案。 Core Tools 會包含整個 Azure Functions 執行階段，因此下載和安裝可能需要一些時間。

* 安裝所選語言的特定需求：

    | 語言 | 需求 |
    | -------- | --------- |
    | **C#** | [C# 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[.NET core CLI 工具](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Java 延伸模組的偵錯工具](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3+](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Python 延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6+](https://www.python.org/downloads/)|

    <sup>*</sup>Active LTS 和維護 LTS 版本 （8.11.1 和 10.14.1 建議使用）。

### <a name="configure-the-project-to-run-locally"></a>設定專案以在本機執行

Functions 執行階段針對 HTTP 和 webhook 以外的所有觸發程序類型，請以在內部使用的 Azure 儲存體帳戶。 這表示，您必須設定**Values.AzureWebJobsStorage**金鑰為有效的 Azure 儲存體帳戶連接字串。

本節會使用[適用於 Visual Studio Code 的 Azure 儲存體擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)具有[Microsoft Azure 儲存體總管](https://storageexplorer.com/)來連接並擷取儲存體連接字串。   

設定儲存體帳戶連接字串：

1. 在 Visual Studio 中，開啟 [Cloud Explorer]  ，展開 [儲存體帳戶]   > 「您的儲存體帳戶」  ，然後選取 [屬性]  並複製 [主要連接字串]  值。

2. 在您的專案中，開啟 local.settings.json 檔案並將 **AzureWebJobsStorage** 機碼的值設定為您所複製的連接字串。

3. 重複上一步，針對函數所需的其他任何連接，將唯一機碼新增至 [值]  陣列。

如需詳細資訊，請參閱 <<c0> [ 本機設定檔](#local-settings-file)。

### <a name="debugging-functions-locally"></a>在本機偵錯函式  

若要偵錯您的函式，請按 F5。 如果您尚未下載[Core Tools][Azure Functions Core Tools]，系統會提示您這麼做。 當安裝 Core Tools，並執行，輸出會顯示終端機中。 這是執行相同`func host start`來自終端機中，但具有額外的核心工具命令建置工作和已附加偵錯工具。  

執行專案時，您可以在部署至 Azure 時觸發您的函式。 偵錯模式中執行時，會叫用中斷點在 Visual Studio Code 中，如預期般運作。

HTTP 觸發程序的要求 URL 會顯示在 終端機中的輸出。 在本機執行時，不會使用 HTTP 觸發程序的功能鍵。 如需詳細資訊，請參閱[在 Azure Functions 中測試程式碼的策略](functions-test-a-function.md)。  

若要進一步了解，請參閱[使用 Azure Functions Core Tools][Azure Functions Core Tools]。

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

根據預設，在專案發佈至 Azure 時，這些設定將不會自動移轉。 發佈完成後，您可以在 Azure 中發佈 local.settings.json 函式應用程式的設定選項。 若要進一步了解，請參閱[發行應用程式設定](#publish-application-settings)。

**ConnectionStrings** 中的值永遠不會發佈。

函式應用程式設定值也可以讀取在您的程式碼中，但會做為環境變數。 如需詳細資訊，請參閱這些特定語言的參考文件的 [環境變數] 區段：

* [先行編譯 C#](functions-dotnet-class-library.md#environment-variables)
* [C# 指令碼 (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>在 Azure 中的應用程式設定

Local.settings.json 檔案中您的專案中的設定應該在函式應用程式在 Azure 中的應用程式設定相同。 您將新增至 local.settings.json 的任何設定必須也會新增至 Azure 中的函式應用程式。 當您發行專案時，不會自動上傳這些設定。 同樣地，由您建立函數應用程式中的任何設定[入口網站中](functions-how-to-use-azure-function-app-settings.md#settings)必須下載至本機專案。

### <a name="publish-application-settings"></a>發行應用程式設定

將必要的設定發佈到您的函式應用程式，在 Azure 中的最簡單方式是使用**上傳設定**之後，您已成功發行您的專案會顯示的連結。

![部署完成上傳應用程式設定](./media/functions-develop-vs-code/upload-app-settings.png)

您也可以使用發佈設定`Azure Functions: Upload Local Setting`命令在命令選擇區。 在 Azure 中的應用程式設定中新增個別的設定使用`Azure Functions: Add New Setting...`命令。 

> [!TIP]
> 請務必將 local.settings.json 檔案儲存在發佈前的時間。

如果本機檔案已加密，則會解密、 發行，並再加密一次。 如果設定存在使用不同的值，在兩個位置，系統會要求您選擇要如何繼續進行。

檢視中的現有應用程式設定**Azure:函式**區域中展開您的訂用帳戶中，而您的函式應用程式，並**應用程式設定**。

![在 Visual Studio Code 中的檢視函式應用程式設定](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>從 Azure 下載設定

如果您已在 Azure 中建立應用程式設定，您就可以下載到 local.settings.json 檔案。 使用`Azure Functions: Download Remote Settings...`命令。 

使用上傳，如果本機檔案已加密、 解密、 更新，並再加密一次。 如果設定存在使用不同的值，在兩個位置，系統會要求您選擇要如何繼續進行。

## <a name="monitoring-functions"></a>監視函式

當您[本機執行](#run-functions-locally)，記錄資料串流處理至終端機主控台。 在 Azure 中的函式應用程式中執行 functions 專案時，您也可以取得記錄資料。 您是連接到資料流以近乎即時的記錄資料，請參閱 < Azure 中的記錄，或您可以啟用 Application Insights 是函式應用程式其運作方式的更完整了解。

### <a name="streaming-logs"></a>串流記錄

開發應用程式時，如果能夠幾近即時地檢視記錄資訊，通常會很實用。 您可以檢視您的函式所產生的記錄檔的資料流。 下列的輸出是範例資料流記錄 HTTP 要求觸發的函式：

![資料流輸出 HTTP 觸發程序的記錄](media/functions-develop-vs-code/streaming-logs-vscode-console.png) 

若要進一步了解，請參閱[串流記錄](functions-monitoring.md#streaming-logs)。 

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> 串流記錄檔支援單一執行個體的 Functions 主機。 當您的函式會調整為多個執行個體時，來自其他執行個體的資料不會顯示在記錄資料流中。 [即時計量 Stream](../azure-monitor/app/live-stream.md) Application Insights 中會支援多個執行個體。 同時也在近乎即時，串流分析也基於[取樣的資料](functions-monitoring.md#configure-sampling)。

### <a name="application-insights"></a>Application Insights

若要監視您的函式的執行情況，建議您整合您的函式應用程式與 Azure Application Insights。 當您在 Azure 入口網站中建立函式應用程式時，系統會依預設為您完成這項整合。 不過，當您在 Visual Studio 發佈期間建立函式應用程式時，則不會完成您的函式應用程式在 Azure 中的整合。

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

若要深入了解，請參閱[監視 Azure Functions](functions-monitoring.md)。

## <a name="c-script-projects"></a>C\#指令碼專案

根據預設，所有C#專案都會建立為[C#編譯類別庫專案](functions-dotnet-class-library.md)。 如果您改為想要使用C#指令碼專案，您必須選取C#作為 Azure 函式延伸模組設定的預設語言的指令碼。

1. 按一下 **檔案 > 喜好設定 > 設定**。

1. 瀏覽**使用者設定 > 擴充功能 > Azure Functions**。

1. 選擇**C #Script**從**Azure 函式：專案語言**。

此時，對基礎的核心工具的呼叫都包含`--csx`選項，它會產生和發佈C#指令碼 (.csx) 專案檔案。 使用指定的預設語言，建立所有專案的預設值為C#指令碼專案。 您不會要求設定預設值時，請選擇 專案語言。 若要建立其他語言的專案，您必須變更此設定，或移除使用者 settings.json 檔案。 移除這項設定之後，系統會再次要求您建立專案時，選擇您的語言。

## <a name="command-palette-reference"></a>命令選擇區參考

Azure Functions 擴充功能提供實用的圖形化介面，在 Azure 區域與您在 Azure 中的函式應用程式互動。 也提供在命令選擇區 (F1) 中的命令相同的功能。 可使用下列 Azure 的函式特有的命令：

|Azure 函式命令  | 描述  |
|---------|---------|
|**加入新的設定...**  |  在 Azure 中建立新的應用程式設定。 若要進一步了解，請參閱[發行應用程式設定](#publish-application-settings)。 您可能也需要[這個設定下載至您的本機設定](#download-settings-from-azure)。 |
| **設定部署來源...** | 連接至本機 Git 存放庫的函式應用程式在 Azure 中。 若要進一步了解，請參閱[Azure Functions 的持續部署](functions-continuous-deployment.md)。 |
| **連接到 GitHub 存放庫...** | 連接您的函式應用程式的 GitHub 存放庫。 |
| **複製函式 URL** | 取得遠端的 HTTP URL 觸發函式在 Azure 中執行。 若要進一步了解，請參閱如何[取得已部署的函式 URL](#get-deployed-function-url)。 |
| **在 Azure 中建立函式應用程式...** | 在您的訂用帳戶，在 Azure 中建立新的函式應用程式。 若要進一步了解，請參閱如何[發佈至新的函式應用程式在 Azure 中](#publish-to-azure)。        |
| **解密設定** | 用來解密[本機設定](#local-settings-file)加密，使用`Azure Functions: Encrypt Settings`。  |
| **刪除函式應用程式...** | 從 Azure 訂用帳戶中移除現有的函式應用程式。 當 App Service 方案中沒有任何其他應用程式時，您會取得要同時刪除的選項。 其他資源，例如儲存體帳戶和資源群組，不會刪除。 若要移除的所有資源，您應該改用[刪除資源群組](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources)。 本機專案不會受到影響。 |
|**刪除函式...**  | 在 Azure 中的函式應用程式中移除現有的函式。 因為這項刪除作業並不會影響您的本機專案，改為考慮移除在本機的函式，然後[重新發行您的專案](#republish-project-files)。 |
| **刪除 Proxy...** | 從您在 Azure 中的函式應用程式中移除 Azure Functions proxy。 若要深入了解 proxy，請參閱[使用 Azure Functions Proxy](functions-proxies.md)。 |
| **刪除設定...** | 刪除 Azure 中的函式應用程式設定。 不會影響在 local.settings.json 檔案中的設定。 |
| **中斷存放庫...**  | 移除[持續部署](functions-continuous-deployment.md)在 Azure 中的函式應用程式與原始檔控制存放庫之間的連線。 |
| **下載遠端設定...** | 從所選函式應用程式在 Azure 中，到 local.settings.json 檔案會下載設定。 如果已加密的本機檔案，它是解密、 更新，並再加密一次。 如果設定存在使用不同的值，在兩個位置，系統會要求您選擇要如何繼續進行。 請確定您已儲存的變更到 local.settings.json 檔案再執行此命令。 |
| **編輯設定...** | 在 Azure 中的現有函式應用程式設定的值變更。 不會影響在 local.settings.json 檔案中的設定。  |
| **加密設定** | 加密中的個別項目`Values`陣列[本機設定](#local-settings-file)。 在此檔案中，`IsEncrypted`也會設定為`true`，這會告知來解密設定，才能使用本機執行階段。 加密本機設定，以降低風險的流失寶貴的資訊。 在 Azure 中，應用程式設定一律會儲存加密。 |
| **現在執行函式** | 啟動[計時器觸發函式](functions-bindings-timer.md)在 Azure 中手動為測試用途。 若要深入了解觸發非 HTTP 函式，在 Azure 中的，請參閱[手動執行非 HTTP 觸發的函式](functions-manually-run-non-http.md)。 |
| **使用 VS Code 中，用於初始化專案...** | 將所需的 Visual Studio 程式碼專案檔加入至現有的函式專案。 使用此命令來使用您建立使用 Core Tools 專案。 |
| **更新 Azure Functions Core Tools 的安裝** | 安裝或更新[Azure Functions Core Tools] ，用來在本機執行。 |
| **Redeploy**  | 可讓您重新部署至 Azure 中的特定部署的專案檔從已連線的 Git 存放庫。 若要重新發佈 Visual Studio Code 中，從本機更新[重新發佈您的專案](#republish-project-files)。 |
| **重新命名設定...** | 變更現有的函式應用程式設定在 Azure 中的索引鍵名稱。 不會影響在 local.settings.json 檔案中的設定。 您應該在重新命名在 Azure 中的設定之後,[下載至本機專案的這些變更](#download-settings-from-azure)。 |
| **重新啟動** | 在 Azure 中，重新啟動函數應用程式。 將更新部署也會重新啟動函數應用程式。 |
| **設定 AzureWebJobStorage...**| 設定的值`AzureWebJobStorage`應用程式設定。 這項設定所需的 Azure 函式時，會設定在 Azure 中建立函數應用程式。 |
| **啟動** | 在 Azure 中啟動已停止的函式應用程式。 | 
| **啟動串流記錄** | 開始在 Azure 中的函式應用程式的資料流記錄檔。 用於遠端疑難排解在 Azure 中若要查看這項資訊在幾近即時串流記錄檔。 若要進一步了解，請參閱[串流記錄](#streaming-logs)。 |
| **停止** | 關閉縮小在 Azure 中執行的函式應用程式。 |
| **停止串流記錄** | 停止函式應用程式，在 Azure 中的資料流記錄檔。 |
| **切換為 位置設定** | 啟用時，可確保應用程式設定持續存在指定的部署位置。 |
| **解除安裝 Azure Functions Core Tools** | 移除延伸模組所需 Azure Functions Core Tools。 |
| **上傳本機設定...** | 上傳至所選函式應用程式在 Azure 中將 local.settings.json 檔案中的設定。 如果本機檔案已加密，則會解密、 上傳，並再加密一次。 如果設定存在使用不同的值，在兩個位置，系統會要求您選擇要如何繼續進行。 請確定您已儲存的變更到 local.settings.json 檔案再執行此命令。 |
| **在 GitHub 中的檢視認可** | 顯示最新的認可中的特定部署函式應用程式連接至儲存機制時。 |
| **檢視部署記錄檔** | 在 Azure 中，顯示您函數應用程式的特定部署的記錄檔。 |

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Functions Core Tools，請參閱[在本機撰寫和測試 Azure Functions 程式碼](functions-run-local.md)。

若要深入了解如何將函式開發為 .NET 類別庫，請參閱 [Azure Functions C# 開發人員參考](functions-dotnet-class-library.md)。 本文也會連結至範例以示範如何使用屬性宣告 Azure Functions 所支援的各種繫結類型。    

[適用於 Visual Studio Code 的 Azure Functions 擴充功能]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md