---
title: MSAL 應用程式中的記錄 | Azure
description: 了解 Microsoft 驗證程式庫 (MSAL) 應用程式中的記錄。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/22/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09590fbd340ac3945f05346f99254ec0b76dcd76
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834989"
---
# <a name="logging"></a>記錄
Microsoft 驗證程式庫 (MSAL) 應用程式能產生記錄訊息，其可用來協助診斷問題及提供詳細資料。 應用程式可透過幾行程式碼來設定記錄，並針對詳細資料層級和是否要記錄個人和組織資料具有自訂控制。 建議您設定 MSAL 記錄回呼，並為使用者提供在遇到驗證問題時提交記錄的方法。

## <a name="logging-levels"></a>記錄層級

MSAL 的記錄器允許擷取數個層級的詳細資料：

- Error:指出已發生問題並產生錯誤。 用來進行偵錯及識別問題。
- 警告：發生可疑的事件，且應用程式針對該事件需要更多資訊。 不一定已經發生錯誤或失敗，但應該進行診斷並查明問題。
- Info：MSAL 記錄事件的原因為資訊性的目的，而不一定是為了進行偵錯。
- Verbose：預設值。 MSAL 將會記錄大量資訊，並針對程式庫行為提供完整詳細資料。

## <a name="personal-and-organizational-data"></a>個人和組織資料
根據預設，MSAL 記錄器並不會擷取任何具高度敏感性的個人或組織資料。 程式庫可讓您選擇在需要時啟用記錄個人和組織資料的功能。

## <a name="logging-in-msalnet"></a>MSAL.NET 中的記錄
在 MSAL 3.x 中，記錄必須在每個應用程式的建立期間，使用 `.WithLogging` 建立器修飾詞針對該應用程式進行個別設定。 此方法可接受選擇性參數：

- *Level* 可讓您決定所需的記錄層級。 將它設定為 Errors 將只會取得錯誤
- *PiiLoggingEnabled* 若設定為 true，可讓您記錄個人和組織資料。 此參數預設為 false，這會使您的應用程式不會記錄個人資料。
- *LogCallback* 會設定為執行記錄的委派。 如果 *PiiLoggingEnabled* 為 true，此方法將會接收訊息兩次：一次是在 *containsPii* 參數等於 false 且訊息不包含個人資料的情況下，另一次是在 *containsPii* 參數等於 true 且訊息可能包含個人資料的情況下。 在某些情況下 (當訊息不包含個人資料時)，這兩個訊息將會相同。
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

- *localCallback*：可由開發人員提供來以自訂方式使用並發行記錄的回呼執行個體。 請依您想要將記錄重新導向的方式實作 localCallback 方法。

- *level* (選擇性)：可設定的記錄層級。 支援的記錄層級為：Error、Warning、Info、Verbose。 預設值為 Info。

- *piiLoggingEnabled* (選擇性)：若設定為 true，可讓您記錄個人和組織資料。 此參數預設為 false，這會使您的應用程式不會記錄個人資料。 個人資料記錄永遠不會被寫入如 Console、Logcat 或 NSLog 等的預設輸出。 預設會設定為 false。

- *correlationId* (選擇性)：唯一識別項，用來針對偵錯目的將要求對應至回應。 預設為 RFC4122 4 版 guid (128 位元)。

```javascript

function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
    system: {
        logger: {
            localCallback: loggerCallback,
            level: Msal.LogLevel.Verbose,
            piiLoggingEnabled: false,
            correlationId: '1234'
        }
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```
