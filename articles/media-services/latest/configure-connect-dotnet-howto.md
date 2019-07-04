---
title: 連線到 Azure 媒體服務 v3 API-.NET
description: 了解如何連接到媒體服務 v3 API 與.NET。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: juliako
ms.openlocfilehash: a256eb787d7e3dbd800ec2e630cac591b07ca0fc
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444174"
---
# <a name="connect-to-media-services-v3-api---net"></a>連線到媒體服務 v3 API-.NET

這篇文章說明您如何連接到 Azure 媒體服務 v3.NET SDK 使用服務主體登入方法。

## <a name="prerequisites"></a>必要條件

- [建立媒體服務帳戶](create-account-cli-how-to.md)。 請務必記住的資源群組名稱和媒體服務帳戶名稱
- 安裝您想要使用適用於.NET 開發的工具。 這篇文章中的步驟示範如何使用[Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)。 您可以使用 Visual Studio Code，請參閱[使用C# ](https://code.visualstudio.com/docs/languages/csharp)。 或者，您可以使用不同的程式碼編輯器。

## <a name="create-a-console-application"></a>建立主控台應用程式

1. 啟動 Visual Studio。 
1. 從**檔案**功能表上，按一下**新增** > **專案**。 
1. 建立 **.NET Core**主控台應用程式。

本主題中，範例應用程式為目標`netcoreapp2.0`。 程式碼會使用 'async main'，也就是可以使用開頭為C#7.1。 請參閱此[部落格](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/)如需詳細資訊。

## <a name="add-required-nuget-packages"></a>新增必要的 NuGet 套件

1. 在 Visual Studio 中，選取**工具** > **NuGet 套件管理員** > **NuGet 管理員主控台**。
2. 在  **Package Manager Console**視窗中，使用`Install-Package`命令以新增以下 NuGet 套件。 例如： `Install-Package Microsoft.Azure.Management.Media` 。

|封裝|描述|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure 媒體服務 SDK。 <br/>若要確定您使用最新的 Azure 媒體服務封裝，請檢查[Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media)。|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|Azure SDK for.NET 的 ADAL 驗證程式庫|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|讀取環境變數和本機 JSON 檔案中的組態值|
|`Microsoft.Extensions.Configuration.Json`|讀取環境變數和本機 JSON 檔案中的組態值
|`WindowsAzure.Storage`|儲存體 SDK|

## <a name="create-and-configure-the-app-settings-file"></a>建立及設定應用程式設定檔

### <a name="create-appsettingsjson"></a>建立 appsettings.json

1. 移移**一般** > **文字檔**。
1. 名稱"appsettings.json 」。
1. 的.json 檔案，以"時才複製 」 的 「 複製到輸出的目錄 屬性設定 （使應用程式能夠存取該發行時）。

### <a name="set-values-in-appsettingsjson"></a>在 appsettings.json 中的設定值

執行`az ams account sp create`命令中所述[存取 Api](access-api-cli-how-to.md)。 此命令會傳回 json，您應該將它複製到您 「 appsettings.json"。
 
## <a name="add-configuration-file"></a>新增組態檔

為了方便起見，將會負責讀取值從"appsettings.json"的組態檔。

1. 將新的.cs 類別新增至您的專案。 將它命名為 `ConfigWrapper` 
1. 此檔案中貼上下列程式碼 (此範例假設您擁有的命名空間是`ConsoleApp1`)。

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>連線至.NET 用戶端

若要開始搭配使用媒體服務 API 與 .NET，您需要建立 **AzureMediaServicesClient** 物件。 若要建立物件，您需要提供必要的認證，讓用戶端使用 Azure AD 連線至 Azure。 下列程式碼，GetCredentialsAsync 函式會建立本機組態檔中提供的認證為基礎的 ServiceClientCredentials 物件。

1. 開啟 `Program.cs`。
1. 貼上下列程式碼：

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>後續步驟

- [教學課程：上傳、編碼和串流影片 - .NET](stream-files-tutorial-with-api.md) 
- [教學課程：透過媒體服務 v3 進行即時串流 - .NET](stream-live-tutorial-with-api.md)
- [教學課程：透過媒體服務 v3 分析影片 - .NET](analyze-videos-tutorial-with-api.md)
- [從本機檔案建立作業輸入 - .NET](job-input-from-local-file-how-to.md)
- [從 HTTPS URL 建立作業輸入 - .NET](job-input-from-http-how-to.md)
- [使用自訂轉換進行編碼 - .NET](customize-encoder-presets-how-to.md)
- [使用 AES-128 動態加密和金鑰傳遞服務 - .NET](protect-with-aes128.md)
- [使用 DRM 動態加密與授權傳遞服務 - .NET](protect-with-drm.md)
- [從現有原則取得簽署金鑰 - .NET](get-content-key-policy-dotnet-howto.md)
- [使用媒體服務建立篩選 - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [搭配使用 Azure Functions v2 與媒體服務 v3 的進階影片隨選範例](https://aka.ms/ams3functions)

## <a name="see-also"></a>請參閱

[.NET 參考](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
