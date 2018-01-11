
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>呼叫 Microsoft Graph API 從 iOS 應用程式

本指南也說明如何在原生 iOS 應用程式 (Swift) 可以呼叫需要從 Microsoft Azure Active Directory (Azure AD) v2.0 端點的存取權杖的 Api。 本指南說明如何取得存取權杖，並將其用於呼叫 Microsoft Graph API 和其他應用程式開發介面。

完成本指南中的練習之後，您的應用程式可以呼叫受保護的應用程式開發介面，從任何的公司或組織有 Azure AD。 您的應用程式可以進行受保護的應用程式開發介面呼叫，藉由使用個人的帳戶，例如 outlook.com、 live.com 和其他項目，如工作或學校帳戶。

## <a name="prerequisites"></a>必要條件
- XCode 版本 8.x 做是為了建立本指南中的範例。 您可以下載從 XCode [iTunes 網站](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode 下載 URL")。
- [迦太基](https://github.com/Carthage/Carthage)相依性管理員是必要的封裝管理。

## <a name="how-this-guide-works"></a>本指南使用方式

![本指南使用方式](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.png)

在本指南中，範例應用程式可讓查詢 Microsoft Graph API 或 web API 可接受來自 Azure AD v2.0 端點的語彙基元的 iOS 應用程式。 對於此案例中，權杖會新增到 HTTP 要求使用**授權**標頭。 權杖取得和更新作業是由 Microsoft Authentication Library (MSAL) 處理。


### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>處理受保護的 web Api 的存取權杖取得

使用者驗證之後，範例應用程式會收到權杖。 權杖用來查詢 Microsoft Graph API 或 web 應用程式開發介面中受到 Azure AD v2.0 端點。

應用程式開發介面，例如 Microsoft Graph 需要允許存取特定資源的存取權杖。 需要讀取使用者的設定檔、 存取使用者的行事曆、 傳送電子郵件，等的語彙基元。 您的應用程式可以要求存取權杖使用 MSAL 並指定應用程式開發介面的範圍。 存取權杖加入至 HTTP**授權**針對受保護的資源進行的每個呼叫的標頭。

MSAL 會為您管理快取和重新整理存取權杖，因此您的應用程式不需要執行這些作業。


## <a name="libraries"></a>程式庫

本指南使用下列程式庫：

|程式庫|說明|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|適用於 iOS 的 Microsoft Authentication Library 預覽|

