---
title: 在 ASP.NET Core 應用程式中使用 Azure 應用程式組態 Key Vault 參考的教學課程 | Microsoft Docs
description: 在此教學課程中，您將了解如何從 ASP.NET Core 應用程式中使用 Azure 應用程式組態的 Key Vault 參考
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: a14cb3035c159c82df44f686da7f7b78ef942943
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035813"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>教學課程：在 ASP.NET Core 應用程式中使用 Key Vault 參考

在此教學課程中，您將了解如何搭配 Azure Key Vault 一起使用 Azure 應用程式組態服務。 這些都是互補服務，將在大部分的應用程式部署中並行使用。 為了協助您一起使用它們，應用程式組態讓您能夠建立金鑰來參考 Key Vault 中所儲存的值。 當您這麼做時，應用程式組態會將 URI 儲存至 Key Vault 值，而不是值本身。 您的應用程式會使用應用程式組態用戶端提供者來擷取此金鑰的值，就像儲存於應用程式組態中的任何其他金鑰一樣。 用戶端提供者會將它辨識為 Key Vault 參考，並呼叫 Key Vault 來擷取該值。 您的應用程式會負責適當地驗證應用程式組態和 Key Vault。 這兩個服務不會直接通訊。

此教學課程會示範如何在程式碼中實作 Key Vault 參考。 本文會以快速入門中介紹的 Web 應用程式作為基礎。 繼續進行之前，請先完成[使用應用程式設定建立 ASP.NET Core 應用程式](./quickstart-aspnet-core-app.md)。

您可以使用任何程式碼編輯器來進行本教學課程中的步驟。 Windows、macOS 及 Linux 平台上都有提供的 [Visual Studio Code](https://code.visualstudio.com/) 是一個絕佳的選項。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立應用程式組態金鑰以參考儲存於 Key Vault 中的值
> * 從 ASP.NET Core Web 應用程式中存取此金鑰的值

## <a name="prerequisites"></a>必要條件

若要進行本教學課程，請安裝 [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>建立保存庫

1. 選取 Azure 入口網站左上角的 [建立資源]  選項。

    ![Key Vault 建立完成後的輸出](./media/quickstarts/search-services.png)
2. 在 [搜尋] 方塊中輸入 **Key Vault**。
3. 從結果清單中，選擇 [Key Vault]  。
4. 在 [金鑰保存庫] 區段上選擇 [建立]  。
5. 在 [建立金鑰保存庫]  區段上提供下列資訊：
    - **名稱**：唯一名稱是必要項。 在本快速入門中，我們使用 **Contoso-vault2**。 
    - 訂用帳戶  ：選擇訂用帳戶。
    - 在 [資源群組]  底下，選擇 [新建]  ，然後輸入資源群組名稱。
    - 在 [位置]  下拉式功能表中選擇位置。
    - 將其他的選項保留預設值。
6. 提供上述資訊之後，請選取 [建立]  。

此時，您的 Azure 帳戶是唯一獲得授權可存取此新保存庫的帳戶。

![Key Vault 建立完成後的輸出](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>將祕密新增至 Key Vault

若要將祕密新增至保存庫，您只需要採取一些額外步驟。 在此情況下，我們會新增一則訊息，供我們用來測試 Key Vault 的擷取。 此訊息稱為 **Message**，而我們會將 **Hello from Key Vault** 的值儲存於其中。

1. 在 Key Vault 屬性頁面上，選取 [祕密]  。
1. 按一下 [產生/匯入]  。
1. 在 [建立祕密]  畫面上選擇下列值：
    - **上傳選項**：手動。
    - **名稱**：訊息
    - **值**：Hello from Key Vault
    - 將其他的值保留預設值。 按一下頁面底部的 [新增]  。

## <a name="add-a-key-vault-reference-to-app-config"></a>將 Key Vault 參考新增至應用程式組態

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [所有資源]  ，然後選取您在快速入門中建立的應用程式組態存放區執行個體。

1. 按一下 [組態總管]  。

1. 按一下 [+ 建立]   > [金鑰保存庫參考]  ，然後選擇下列值：
    - **金鑰**：TestApp:Settings:KeyVaultMessage
    - **標籤**︰保留空白
    - **訂用帳戶**、**資源群組**、**金鑰保存庫**：選擇與您在上一節中建立之 Key Vault 對應的選項。
    - **祕密**：選取您在上一節中建立之名為 **Message** 的秘密。

## <a name="connect-to-key-vault"></a>連線到 Key Vault

1. 在此教學課程中，您將使用服務主體來驗證 KeyVault。 若要建立此服務主體，請使用 Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令：

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    這項作業會傳回一系列的金鑰/值組。 

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. 執行下列命令，以允許服務主體存取您的金鑰保存庫：

        az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey

1. 將 *clientId* 和 *clientSecret* 的秘密新增至秘密管理員。 這些命令必須在和 *.csproj* 檔案相同的目錄中執行。

        dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>        
        dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>

> [!NOTE]
> 這些 Key Vault 認證只會在您的應用程式中使用。 您的應用程式會使用這些認證直接驗證 Key Vault。 它們永遠不會傳遞至應用程式組態服務。

## <a name="update-your-code-to-use-a-key-vault-reference"></a>更新您的程式碼以使用 Key Vault 參考

1. 開啟 *Program.cs*，並將參考新增至所需的套件。

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. 藉由呼叫 `config.AddAzureAppConfiguration()` 方法將 `CreateWebHostBuilder` 方法更新為使用應用程式設定。 包含 `UseAzureKeyVault` 選項，將新的 `KeyVaultClient` 參考傳入至您的 Key Vault。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                KeyVaultClient kvClient = new KeyVaultClient(async (authority, resource, scope) =>
                {
                    var adCredential = new ClientCredential(settings["ConnectionStrings:KeyVaultClientId"], settings["ConnectionStrings:KeyVaultClientSecret"]);
                    var authenticationContext = new AuthenticationContext(authority, null);
                    return (await authenticationContext.AcquireTokenAsync(resource, adCredential)).AccessToken;
                });

                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseAzureKeyVault(kvClient); });
            })
            .UseStartup<Startup>();
    ```

1. 當您將 *KeyVaultClient* 傳遞給 `UseAzureKeyVault` 方法之後，在將與應用程式組態的連線初始化時，您可以使用存取一般應用程式組態金鑰值的相同方式來存取 Key Vault 參考的值。 若要查看此程序的運作情形，請在 [檢視] > 主目錄中開啟 *Index.cshtml*。 以下列程式碼取代其內容：

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    您會以與組態值 *TestApp:Settings:Message* 相同的方式來存取 Key Vault 參考的值 *TestApp:Settings:KeyVaultMessage*。

## <a name="build-and-run-the-app-locally"></a>於本機建置並執行應用程式

1. 若要使用 .NET Core CLI 來建置應用程式，請在命令殼層中執行下列命令：

        dotnet build

2. 建置成功完成後，請執行下列命令以在本機執行 Web 應用程式：

        dotnet run

3. 開啟瀏覽器視窗，並前往 `http://localhost:5000` (這是本機所裝載 Web 應用程式的預設 URL)。

    ![快速入門應用程式啟動本機](./media/key-vault-reference-launch-local.png)


## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已新增 Azure 受控服務識別來簡化應用程式設定的存取，以及改善您應用程式的認證管理。 若要深入了解「應用程式組態」的使用方式，請繼續進行 Azure CLI 範例。

> [!div class="nextstepaction"]
> [CLI 範例](./cli-samples.md)
