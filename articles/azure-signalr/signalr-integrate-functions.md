---
title: 整合 Azure Functions 與 Azure SignalR 服務的教學課程 | Microsoft Docs
description: 在本教學課程中，您將會了解如何搭配使用 Azure Functions 與 Azure SignalR 服務
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/24/2018
ms.author: wesmc
ms.openlocfilehash: b1bb6b3fe545d5a42fa985ab85bd7a914128e56b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-integrate-azure-functions-with-azure-signalr-service"></a>教學課程：整合 Azure Functions 與 Azure SignalR 服務

本教學課程會以先前教學課程中所建立的聊天室應用程式為基礎。 如果您尚未完成[使用 SignalR 服務建立聊天室](signalr-quickstart-dotnet-core.md)和 [Azure SignalR 服務驗證](./signalr-authenticate-oauth.md)，請先完成這兩項練習。 

具有即時應用程式的常見案例是要讓源自伺服器的內容更新發佈至 Web 用戶端。 [Azure Functions](../azure-functions/functions-overview.md) 非常適合用來產生這些內容更新。 使用 Azure Functions 的主要優點是，可以在需要時才執行程式碼，而不必擔心整個應用程式的架構，也不必擔心用來執行程式碼的基礎結構。 您也只需要針對程式碼的實際執行時間來付費。  

一般來說，因為 SignalR 會嘗試讓用戶端與伺服器之間保持連線以便推送內容更新，因此這個案例會在嘗試使用 SignalR 時出現問題。 由於程式碼只會在需要時執行，因此無法保持連線。 不過，Azure SignalR 服務可以支援此案例，因為它會在執行階段時為您管理連線。

在本教學課程中，您會使用 Azure Functions 在每一分鐘開使時，使用計時器函式產生訊息。 此函式會將訊息發佈至先前教學課程中所建立聊天室的所有用戶端。 如需計時器函式的詳細資訊，請參閱[計時器函式](../azure-functions/functions-create-scheduled-function.md)。

您可以使用任何程式碼編輯器來完成本快速入門中的步驟。 不過，Windows、macOS 和 Linux 平台上所提供的 [Visual Studio Code](https://code.visualstudio.com/) 會是不錯的選擇。

本教學課程的程式碼可於 [AzureSignalR-samples GitHub 存放庫](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer)下載。

![具有伺服器訊息的聊天應用程式](./media/signalr-integrate-functions/signalr-functions-complete.png)

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 使用 Azure CLI 以 Azure Functions 建立新的計時器函式。
> * 設定本機 Git 存放庫部署的計時器函式。
> * 將計時器連線至 SignalR 服務以便每分鐘推送更新一次

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>先決條件

若要完成本教學課程，您必須具備下列先決條件：

* [Git](https://git-scm.com/)
* [.NET Core SDK](https://www.microsoft.com/net/download/windows) 
* [已設定 Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* 下載或複製 [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) GitHub 存放庫。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-function-app"></a>建立函數應用程式

您必須建立函式應用程式以定義函式的執行環境。 函式應用程式也可讓您將多個函式群組為邏輯單位，以方便您管理、部署和共用資源。 如需詳細資訊，請參閱[使用 Azure CLI 建立第一個函式](../azure-functions/functions-create-first-azure-function-azure-cli.md)。

在本節中，您會使用 Azure Cloud Shell 來建立新的 Azure 函式應用程式，並設定為從本機 Git 存放庫進行部署。 

請將函式應用程式資源建立在先前教學課程中所使用的相同資源群組中。 這種方法可方便您管理所有教學課程資源。

將下列指令碼複製到文字編輯器中，並將變數參數的值取代為資源的值。 將更新後的指令碼複製並貼到 Azure Cloud Shell，然後按 **Enter** 鍵來建立儲存體帳戶和函式應用程式。

```azurecli-interactive
#====================================================================
#=== Update these variables to match your values from previous    === 
#=== tutorials.                                                   ===
#====================================================================
ResourceGroupName=SignalRTestResources
location=eastus

#====================================================================
#=== Update these variables for the new function app and storage  ===
#=== account.                                                     ===
#====================================================================
functionappName=mysignalfunctionapp
storageAccountName=mystorageaccount

# Create a storage account to hold function app code and settings
az storage account create --resource-group $ResourceGroupName \
--name $storageAccountName \
--location $location --sku Standard_LRS

# Create the function app
az functionapp create --resource-group $ResourceGroupName \
--name $functionappName \
--consumption-plan-location $location \
--storage-account $storageAccountName

```

| 參數 | 說明 |
| -------------------- | --------------- |
| resourceGroupName | 這是先前的教學課程中所建議的資源群組名稱。 您最好將所有教學課程的資源群組在一起。 使用您在先前的教學課程中所使用的相同資源群組。 | 
| location | 將此變數更新為您在先前教學課程中用來建立資源群組的相同位置。 | 
| functionappName | 使用新函式應用程式的唯一名稱更新這個變數。 例如，signalrfunctionapp22665120。 | 
| storageAccountName | 針對用來保存函式應用程式程式碼和設定的新儲存體帳戶，輸入其名稱。 | 



## <a name="configure-the-function-app"></a>設定函式應用程式

在本節中，您會使用含有 Azure SignalR 服務資源連接字串的應用程式設定來設定函式應用程式。 函式的程式碼會使用此設定來對聊天室進行連線和發佈訊息。 您也會將函式應用程式設定為從本機 Git 存放庫進行部署。

複製下列指令碼，並取代參數的值。 將更新後的指令碼貼到 Azure Cloud Shell，然後按 **Enter** 鍵。

```azurecli-interactive
#====================================================================
#=== Update these variables to match your resources.              === 
#====================================================================
ResourceGroupName=SignalRTestResources
functionappName=mysignalfunctionapp

#========================================================================
#=== Replace this value with the connection string for your           ===
#=== SignalR Service resource.                                        ===
#========================================================================
connstring="Endpoint=<service_endpoint>;AccessKey=<access_key>;"

# Add the SignalR Service connection string app setting
az functionapp config appsettings set --resource-group $ResourceGroupName \
    --name $functionappName \
    --setting "Azure:SignalR:ConnectionString=$connstring"

# configure for deployment from a local Git repository
az functionapp deployment source config-local-git --name $functionappName \
    --resource-group $ResourceGroupName

```

| 參數 | 說明 |
| -------------------- | --------------- |
| resourceGroupName | 這是先前的教學課程中所建議的資源群組名稱。 您最好將所有教學課程的資源群組在一起。 使用您在先前的教學課程中所使用的相同資源群組。 | 
| functionappName | 使用新函式應用程式的唯一名稱更新這個變數。 例如，signalrfunctionapp22665120。 | 
| connstring | 輸入 SignalR 服務資源的連接字串。 您可以從 Azure 入口網站的 SignalR 服務資源頁面按一下 [設定] 底下的 [金鑰]，來擷取這個連接字串。 | 



記下從最後一個命令傳回的 Git 部署 URL。 您會使用此 URL 來部署函式程式碼。


## <a name="the-timer-function"></a>計時器函式

計時器函式範例位於下載項目的 /samples/Timer 目錄，或是 [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer) GitHub 存放庫的複製。 計時器函式程式碼位於 TimerFunction.cs。 此程式碼會使用隨預設組態金鑰儲存的連接字串 (Azure:SignalR:ConnectionString) 來對中樞建立 Proxy。 由於函式程式碼會在伺服器端執行，沒有理由要求它以一般用戶端的身分進行驗證。 系統會信任程式碼使用連接字串。 使用此中樞 Proxy，函式程式碼可以呼叫任何您在中樞上所定義的方法。 程式碼會呼叫 `BroadcastMessage` 方法，以發佈含有觸發程序引發當下時間的訊息。

函式程式碼的觸發程序是 timerTrigger，其定義於 TimerFunction/function.json 的繫結中。 其包含 [CRON 運算式](https://wikipedia.org/wiki/Cron#CRON_expression)，以便將計時器觸發程序設定為在每分鐘開始時引發一次。

```json
{
  "bindings": [
    {
      "type": "timerTrigger",
      "schedule": "0 * * * * *",
      "useMonitor": true,
      "runOnStartup": false,
      "name": "myTimer"
    }
  ],
  "disabled": false,
  "scriptFile": "../Timer.dll",
  "entryPoint": "Timer.TimerFunction.Run"
}
```


## <a name="building-the-timer-function"></a>建置計時器函式

在下列步驟中使用 [.NET Core 命令列介面 (CLI)](https://docs.microsoft.com/dotnet/core/tools/)，以建置函式並讓函式準備好進行部署：

1. 瀏覽至下載項目的 /samples/Timer 子目錄，或是 [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) GitHub 存放庫的複製。

2. 使用下列命令還原 NuGet 套件︰

        dotnet restore

3. 使用下列命令建置 Timer 函式應用程式︰

        msbuild /p:Configuration=Release


## <a name="create-and-deploy-the-local-git-repo"></a>建立及部署本機 Git 存放庫

1. 在 Git 殼層中，瀏覽至組建子目錄 /samples/Timer/bin/Release/net461。

        cd ./AzureSignalR-samples/samples/Timer/bin/Release/net461

2. 使用下列命令將目錄初始化為新的 Git 存放庫：

        git init

3. 在組建目錄中新增所有檔案的認可。

        git add -A
        git commit -v -a -m "Initial Timer function commit"        

4. 針對您在函式應用程式設定期間所記下的 Git 部署 URL，新增遠端端點：

        git remote add Azure <enter your Git deployment URL>

5. 部署函式應用程式

        git push Azure master

   程式碼部署完成後，計時器會立即開始每分鐘引發一次，以執行程式碼。

## <a name="test-the-chat-app"></a>測試聊天應用程式

瀏覽至聊天應用程式，您剛才建立的計時器函式現在會在每分鐘開始時回報時間。

![具有伺服器訊息的聊天應用程式](./media/signalr-integrate-functions/signalr-functions-complete.png)



## <a name="clean-up-resources"></a>清除資源

如果您想要繼續測試應用程式，則可以保留已建立的資源。

否則，如果您已完成範例應用程式，便可以將 Azure 資源刪除，以避免衍生費用。 

> [!IMPORTANT]
> 刪除資源群組是無法回復的動作，資源群組和其內的所有資源將會永久刪除。 請確定您不會不小心刪除錯誤的資源群組或資源。 如果您是在包含有需要保留之資源的現有資源群組內，建立用來裝載此範例的資源，您可以從每個資源各自的刀鋒視窗中個別刪除每個資源，而不必刪除正個資源群組。
> 
> 

登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [資源群組]。

在 [依名稱篩選...] 文字方塊中，輸入您的資源群組名稱。 本文的指示是使用名為 SignalRTestResources 的資源群組。 在結果清單中的目標資源群組上方，按一下 **...**，然後按一下 [刪除資源群組]。

   
![刪除](./media/signalr-integrate-functions/signalr-delete-resource-group.png)


系統將會要求您確認是否刪除資源。 輸入您的資源群組名稱來確認，然後按一下 [刪除]。
   
片刻過後，系統便會刪除該資源群組及其所有內含的資源。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何與 Azure Function 整合，以便根據 Azure Function 觸發程序將更新推送至用戶端。 若要深入了解如何使用 Azure SignalR 伺服器，請繼續進行 SignalR 服務的 Azure CLI 範例。

> [!div class="nextstepaction"]
> [Azure SignalR CLI 範例](./signalr-cli-samples.md)



