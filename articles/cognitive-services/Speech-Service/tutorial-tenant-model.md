---
title: 建立租用戶模型 (預覽) - 語音服務
titleSuffix: Azure Cognitive Services
description: 自動產生利用您 Office365 資料的自訂語音模型，針對組織特定詞彙以安全且符合規範的方式，提供最佳語音辨識。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 85b9291ee24c024ebc8ce81ddba46d04f7744081
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500396"
---
# <a name="create-a-tenant-model-preview"></a>建立租用戶模型 (預覽)

租用戶模型是 Office365 企業客戶可選擇加入的服務，它會從您組織的 Office365 資料自動產生自訂語音辨識模型。 所建立的模型已針對技術詞彙、術語和人員的名字進行最佳化，完全以安全且符合規範的方式進行。

> [!IMPORTANT]
> 如果您的組織註冊租用戶模型，語音服務可能會存取由 Office 365 資源 (例如電子郵件和文件) 所產生的組織語言模型。 您組織的 Office 365 系統管理員，可以使用 Office 365 系統管理入口網站來開啟/關閉全組織語言模型的使用。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 在 Microsoft 365 系統管理中心內註冊以使用租用戶模型
> * 取得語音訂用帳戶金鑰
> * 建立租用戶模型
> * 部署租用戶模型
> * 搭配語音 SDK 使用租用戶模型

![租用戶模型圖表](media/tenant-language-model/tenant-language-model-diagram.png)

## <a name="enroll-using-the-microsoft-365-admin-center"></a>使用 Microsoft 365 系統管理中心註冊

您必須先使用 Microsoft 365 系統管理中心來註冊，才能部署您的租用戶模型。 此工作只能由您的 Microsoft 365 管理員完成。

1. 登入 [Microsoft 365 系統管理中心](https://admin.microsoft.com )。
2. 從左面板依序選取 [設定]  、[應用程式]  。

   ![租用戶模型圖表](media/tenant-language-model/tenant-language-model-enrollment.png)

3. 找出並選取 [Azure 語音服務]  。

   ![租用戶模型圖表](media/tenant-language-model/tenant-language-model-enrollment-2.png)

4. 按一下核取方塊並儲存。

如果您需要關閉租用戶模型，請瀏覽回到此畫面，然後取消選取核取方塊並儲存。

## <a name="get-a-speech-subscription-key"></a>取得語音訂用帳戶金鑰

若要搭配語音 SDK 使用租用戶模型，您會需要語音資源及其相關聯的訂用帳戶金鑰。

1. 登入 [Azure 入口網站](https://aka.ms/azureportal)。
2. 選取 [建立資源]  。
3. 在搜尋列中輸入：**語音**。
4. 選取 [語音]  ，然後按一下 [建立]  。
5. 遵循畫面上的指示來建立資源。 請確定：
   * [位置]  設定為 [eastus]  或 [westus]  。
   * [定價層]  設定為 [S0]  。
6. 按一下頁面底部的 [新增]  。
7. 幾分鐘後，就會建立您的資源。 您可以在資源的 [概觀]  區段中找到訂用帳戶金鑰。

## <a name="create-a-model"></a>建立模型

當您的管理員已為組織啟用租用戶模型之後，您就可以建立以您的 Office365 資料為基礎的語言模型。

1. 登入 [Speech Studio](https://speech.microsoft.com/) \(英文\)。
2. 在右上角，找出並按一下齒輪圖示 (設定)，然後選取 [租用戶模型設定]  。

   ![[設定] 功能表](media/tenant-language-model/tenant-language-settings.png)

3. 此時，您會看到訊息，告知您您有資格建立租用戶模型。
   > [!NOTE]
   > 在北美洲的 Office 365 企業客戶有資格建立租用戶模型 (英文)。 如果您是客戶加密箱 (CLB) 或客戶金鑰 (CK) 客戶，則無法使用此功能。 若要判斷您是否為「客戶加密箱」或「客戶金鑰」客戶，請遵循這些指示：
   > * [客戶加密箱](https://docs.microsoft.com/office365/securitycompliance/controlling-your-data-using-customer-key#FastTrack) \(部分機器翻譯\)
   > * [客戶金鑰](https://docs.microsoft.com/microsoft-365/compliance/customer-lockbox-requests) \(部分機器翻譯\)

4. 接下來，選取 [加入]  。 當您的租用戶模型準備好時，您會收到包含指示的電子郵件。

## <a name="deploy-your-model"></a>部署模型

當您的租用戶模型準備好時，請遵循這些步驟來部署模型：

1. 按一下您所收到確認電子郵件中的 [檢視模型]  按鈕，或登入 [Speech Studio](https://speech.microsoft.com/) \(英文\)。
2. 在右上角，找出並按一下齒輪圖示 (設定)，然後選取 [租用戶模型設定]  。

   ![[設定] 功能表](media/tenant-language-model/tenant-language-settings.png)

3. 按一下 [ **部署**]。
4. 當您的模型已部署時，狀態會變更為 [已部署]  。

## <a name="use-your-model-with-the-speech-sdk"></a>搭配語音 SDK 使用您的模型

現在您已部署模型，您可以搭配語音 SDK 使用它。 在此節中，您會使用提供的範例程式碼，來使用 Azure AD 驗證呼叫語音服務。

讓我們看一下您會用 C# 呼叫語音 SDK 的程式碼。 在此範例中，您會使用租用戶模型執行語音辨識。 本指南假設您的平台已設定好。 如果您需要設定方面的協助，請參閱[快速入門：辨識語音，C# (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)。

請將下列程式碼複製到您的專案中：

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // Note: ServiceApplicationId is a fixed value.  No need to change.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

接下來，您必須從命令列重建並執行專案。 執行命令之前，您將必須先更新幾個參數。

1. 將 `<Username>` 和 `<Password>` 取代為有效租用戶使用者的值。
2. 將 `<Subscription-Key>` 取代為您語音資源的訂用帳戶金鑰。 您可以在 [Azure 入口網站](https://aka.ms/azureportal)中，於您語音資源的 [概觀]  區段中找到此值。
3. 將 `<Endpoint-Uri>` 取代為以下端點。 請務必將 `{your-region}` 取代為您語音資源所建立在的區域。 支援這些區域：`westus`、`westus2` 和 `eastus`。 您可以在 [Azure 入口網站](https://aka.ms/azureportal)中，於您語音資源的 [概觀]  區段中找到您的區域資訊。
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
4. 執行命令：
   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

## <a name="next-steps"></a>後續步驟

* [Speech Studio](https://speech.microsoft.com/) \(英文\)
* [語音 SDK](speech-sdk.md)
