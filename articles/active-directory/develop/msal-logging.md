---
title: 在 MSAL 應用程式中記錄 |Microsoft 身分識別平臺
description: 了解 Microsoft 驗證程式庫 (MSAL) 應用程式中的記錄。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dad8a276cd40b1ff04bbced833b5d70cec4fc87
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268592"
---
# <a name="logging-in-msal-applications"></a>MSAL 應用程式中的記錄

Microsoft 驗證程式庫（MSAL）應用程式會產生記錄訊息，以協助診斷問題。 應用程式可透過幾行程式碼來設定記錄，並針對詳細資料層級和是否要記錄個人和組織資料具有自訂控制。 我們建議您建立 MSAL 記錄回呼，並提供一種方法，讓使用者在有驗證問題時提交記錄。

## <a name="logging-levels"></a>記錄層級

MSAL 提供數種層級的記錄詳細資料：

- Error:指出已發生問題並產生錯誤。 用來進行偵錯及識別問題。
- 警告：不一定會發生錯誤或失敗，但適用于診斷和查明問題。
- Info：MSAL 記錄事件的原因為資訊性的目的，而不一定是為了進行偵錯。
- Verbose：預設值。 MSAL 會記錄程式庫行為的完整詳細資料。

## <a name="personal-and-organizational-data"></a>個人和組織資料

根據預設，MSAL 記錄器不會捕捉任何高度敏感的個人或組織資料。 如果您決定要這麼做，程式庫會提供啟用記錄個人和組織資料的選項。

## <a name="logging-in-msalnet"></a>MSAL.NET 中的記錄

 > [!NOTE]
 > 如需有關 MSAL.NET 的詳細資訊，請參閱[MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)。 取得 MSAL.NET 記錄的範例和其他資訊。
 
在 MSAL 3.x 中，記錄必須在每個應用程式的建立期間，使用 `.WithLogging` 建立器修飾詞針對該應用程式進行個別設定。 此方法可接受選擇性參數：

- *Level* 可讓您決定所需的記錄層級。 將它設定為 Errors 將只會取得錯誤
- *PiiLoggingEnabled* 若設定為 true，可讓您記錄個人和組織資料。 根據預設，這會設定為 false，讓您的應用程式不會記錄個人資料。
- *LogCallback* 會設定為執行記錄的委派。 如果 *PiiLoggingEnabled* 為 true，此方法將會接收訊息兩次：一次是在 *containsPii* 參數等於 false 且訊息不包含個人資料的情況下，另一次是在 *containsPii* 參數等於 true 且訊息可能包含個人資料的情況下。 在某些情況下（當訊息不包含個人資料時），訊息將會相同。
- *DefaultLoggingEnabled* 會針對平台啟用預設記錄。 根據預設，它是 false。 如果您將它設定為 true，它會使用桌面/UWP 應用程式中的事件追蹤、iOS 上的 NSLog，以及 Android 上的 logcat。

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>MSAL.js 中的記錄

 您可以在建立 `UserAgentApplication` 執行個體的設定期間傳遞記錄器物件來啟用 MSAL.js 中的記錄。 此記錄器物件具有下列屬性：

- `localCallback`：可由開發人員提供的回呼實例，以自訂的方式取用和發行記錄。 請依您想要將記錄重新導向的方式實作 localCallback 方法。

- `level`（選擇性）：可設定的記錄層級。 支援的記錄層級為：Error、Warning、Info、Verbose。 預設值為 Info。

- `piiLoggingEnabled`（選擇性）：如果設定為 true，則可讓您記錄個人和組織的資料。 根據預設，這會設定為 false，讓您的應用程式不會記錄個人資料。 個人資料記錄永遠不會被寫入如 Console、Logcat 或 NSLog 等的預設輸出。 預設會設定為 false。

- `correlationId`（選擇性）：唯一識別碼，用來將要求對應至用於進行偵錯工具的回應。 預設為 RFC4122 4 版 guid (128 位元)。

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
     system: {
             logger: new Msal.Logger(
                                loggerCallback ,{
                                     level: Msal.LogLevel.Verbose,
                                     piiLoggingEnabled: false,
                                     correlationId: '1234'
                                }
                        )
     }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="logging-in-msal-for-ios-and-macos"></a>適用于 iOS 和 macOS 的 MSAL 登入

設定回呼來捕捉 MSAL 記錄，並將它併入您自己的應用程式記錄中。 回呼的簽章看起來像這樣：

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

例如:

Objective-C
```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

Swift
```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-identifiable-information-pii"></a>個人識別資訊（PII）

根據預設，MSAL 不會捕捉或記錄任何 PII。 程式庫可讓應用程式開發人員透過 MSALLogger 類別中的屬性來開啟此功能。 藉由開啟 PII，應用程式會負責安全地處理高度敏感的資料，並遵循法規需求。

Objective-C
```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

Swift
```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>記錄層級

若要在使用 iOS 和 macOS 的 MSAL 記錄時設定記錄層級，請使用下列其中一個值：

|層級  |描述 |
|---------|---------|
| `MSALLogLevelNothing`| 停用所有記錄 |
| `MSALLogLevelError` | 預設層級，只有在發生錯誤時才會列印資訊 |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  程式庫進入點，包含參數和各種 keychain 作業 |
|`MSALLogLevelVerbose`     |  API 追蹤       |

例如:

Objective-C
```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```
 
 Swift
```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>記錄訊息格式

MSAL 記錄訊息的訊息部分格式為`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

例如:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

提供相互關聯識別碼和時間戳記有助於追蹤問題。 時間戳記和相互關聯識別碼資訊可在記錄訊息中取得。 唯一可取得的可靠位置是從 MSAL 記錄訊息。
