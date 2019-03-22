---
title: 使用 Visual Studio 在 ASP.NET 專案中新增 Key Vault 支援 - Azure Key Vault | Microsoft Docs
description: 使用本教學課程，以協助您了解如何在 ASP.NET 或 ASP.NET Core Web 應用程式中新增 Key Vault 支援。
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: ghogen
ms.openlocfilehash: de849ae290228826ee500ae1c7e623210e585d34
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113243"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>使用 Visual Studio 連線服務在 Web 應用程式中新增 Key Vault

在本教學課程中，您將了解如何輕鬆地新增所需的項目，以便開始在 Visual Studio 中使用 Azure Key Vault 來管理 Web 專案的祕密，而不論您使用的是 ASP.NET Core 或任何類型的 ASP.NET 專案。 藉由使用 Visual Studio 2017 中的「連線服務」功能，您可以讓 Visual Studio 自動新增所需的所有 NuGet 封裝和組態設定，以連線到 Azure 中的 Key Vault。 

針對「連線服務」在您專案中為啟用 Key Vault 所做的變更，如需詳細資料，請參閱 [Key Vault 連線服務 - 我的 ASP.NET 4.7.1 專案發生什麼情形](vs-key-vault-aspnet-what-happened.md)或 [Key Vault 連線服務 - 我的 ASP.NET Core 專案發生什麼情形](vs-key-vault-aspnet-core-what-happened.md)。

## <a name="prerequisites"></a>必要條件

- **Azure 訂用帳戶**。 如果您沒有訂用帳戶，您可以註冊 [免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。
- **Visual Studio 2017 版本 15.7**，並且已安裝**網頁程式開發**工作負載。 [立即下載](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)。
- 針對 ASP.NET (不是 Core)，您需要預設不會安裝的 .NET Framework 4.7.1 開發工具。 若要安裝它們，請啟動 Visual Studio 安裝程式、選擇 [修改]，然後選擇 [個別元件]，接著在右側展開 [ASP.NET 與網頁程式開發]，並選擇 [.NET Framework 4.7.1 開發工具]。
- ASP.NET 4.7.1 或 ASP.NET Core 2.0 Web 專案隨即開啟。

## <a name="add-key-vault-support-to-your-project"></a>在專案中新增 Key Vault 支援

1. 在 [方案總管] 中，選擇 [新增] > [連線服務]。
   [連線服務] 頁面隨即出現，並顯示您可新增至專案的服務。
1. 在可用服務的功能表中，選擇 [運用 Azure Key Vault 保護祕密的安全]。

   ![選擇 [運用 Azure Key Vault 保護祕密的安全]](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   如果您已登入 Visual Studio，並具有與您帳戶相關聯的 Azure 訂用帳戶，則會出現一個頁面，其中顯示含有您訂用帳戶的下拉式清單。 請確定您已登入 Visual Studio，並且您登入的帳戶與您用於 Azure 訂用帳戶的帳戶相同。

1. 選取您想要使用的訂用帳戶，然後選擇新的或現有的 Key Vault，或選擇 [編輯] 連結來修改自動產生的名稱。

   ![選取您的訂用帳戶](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. 輸入您想要針對 Key Vault 使用的名稱。

   ![將 Key Vault 重新命名並選擇資源群組](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. 選取現有的資源群組，或選擇建立新的自動產生的唯一名稱。  如果想要使用不同的名稱來建立新群組，您可以使用 [Azure 入口網站](https://portal.azure.com)，接著關閉頁面並重新啟動，以重新載入資源群組的清單。
1. 選擇要在其中建立 Key Vault 的區域。 如果您的 Web 應用程式裝載於 Azure 中，請選擇裝載該 Web 應用程式的區域以獲得最佳效能。
1. 選擇定價模型。 如需詳細資訊，請參閱 [Key Vault 價格](https://azure.microsoft.com/pricing/details/key-vault/)。
1. 選擇 [確定] 以接受設定選項。
1. 選擇 [新增] 以建立 Key Vault。 如果您選擇已使用的名稱，則建立程序可能會失敗。  如果發生這種情況，使用 [編輯] 連結來將 Key Vault 重新命名，然後再試一次。

   ![正在將連線服務新增至專案](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. 現在，在 Azure 的 Key Vault 中新增祕密。 若要到達入口網站中的正確位置，按一下 [管理儲存在此 Key Vault 中的祕密] 連結。 如果您關閉了頁面或專案，可以在 [Azure 入口網站](https://portal.azure.com)中，選擇 [安全性] 下方的 [所有服務]、選擇 [Key Vault]，然後選擇您所建立的 Key Vault，就能瀏覽到該頁面或專案。

   ![瀏覽至入口網站](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. 在您所建立 Key Vault 的 [Key Vault] 區段中，依序選擇 [祕密] 和 [產生/匯入]。

   ![產生/匯入祕密](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. 輸入祕密 (例如 "MySecret")，並為它指定任何字串值作為測試，然後選擇 [建立] 按鈕。

   ![建立祕密](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (選擇性) 輸入其他祕密，但此時請將它命名為 "Secrets--MySecret"，藉以將它放置於分類中。 此語法會指定 "Secrets" 分類，其中包含 "MySecret" 祕密。
 
現在，您可透過程式碼存取祕密。 後續步驟會根據您使用的是 ASP.NET 4.7.1 或 ASP.NET Core 而有所差異。

## <a name="access-your-secrets-in-code"></a>在程式碼中存取您的祕密

1. 安裝 [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 和 [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet 程式庫這兩個 nuget 套件。

2. 開啟 Program.cs 檔案，並以下列程式碼取代其中的程式碼： 
   ```
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```
3. 接下來開啟 About.cshtml.cs 檔案，並撰寫下列程式碼
   1. 通過此 using 陳述式包含對 Microsoft.Extensions.Configuration 的參考    
       ```
       using Microsoft.Extensions.Configuration
       ```
   2. 新增此建構函式
       ```
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```
   3. 更新 OnGet 方法。 使用您在上述命令中建立的祕密名稱更新此處顯示的預留位置的值
       ```
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

瀏覽至 [關於] 頁面，在本機執行應用程式。 您應該擷取您的密碼值

## <a name="clean-up-resources"></a>清除資源

不再需要資源群組時，請加以刪除。 這會刪除 Key Vault 及相關資源。 若要透過入口網站刪除資源群組：

1. 在入口網站頂端的 [搜尋] 方塊中，輸入資源群組的名稱。 當您在搜尋結果中看到本快速入門中使用的資源群組時，請加以選取。
2. 選取 [刪除資源群組]。
3. 在 [輸入資源群組名稱:] 方塊中輸入資源群組的名稱，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

若要深入了解 Key Vault 的開發，請閱讀[金鑰保存庫開發人員指南](key-vault-developers-guide.md)