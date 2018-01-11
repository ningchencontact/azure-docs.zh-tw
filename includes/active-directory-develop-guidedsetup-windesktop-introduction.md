# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>從 Windows 傳統型應用程式呼叫 Microsoft 圖形 API

本指南示範如何取得存取權杖和 Microsoft Graph API 或其他需要從 Azure Active Directory v2 端點的存取權杖的 Api 呼叫原生的 Windows 桌面.NET (XAML) 應用程式。

當您完成本指南後時，您的應用程式可以呼叫受保護的 API 所使用的個人帳戶 （包括 outlook.com、 live.com 和其他項目）。 應用程式也會使用的工作及學校帳戶，從任何的公司或組織使用 Azure Active Directory。  

> [!NOTE] 
> 本指南會需要 Visual Studio 2015 Update 3 或 Visual Studio 2017。  沒有任一版本嗎？ [免費下載 Visual Studio 2017](https://www.visualstudio.com/downloads/)。

## <a name="how-this-guide-works"></a>本指南使用方式

![本指南使用方式](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

您使用本指南來建立範例應用程式可讓查詢 Microsoft Graph API 或 Web API 可接受來自 Azure Active Directory v2 端點的語彙基元的 Windows 桌面應用程式。 此案例中，將權杖加入透過授權標頭的 HTTP 要求。 Microsoft 驗證程式庫 (MSAL) 處理權杖取得和更新。

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>處理權杖取得以存取受保護的 Web API

使用者經過驗證之後，範例應用程式會收到可用於查詢 Microsoft Graph API 或 Azure Active Directory v2 受保護的 Web API 的語彙基元。

例如，Microsoft Graph Api 需要允許存取特定資源的語彙基元。 例如，權杖是需要讀取使用者的設定檔、 存取使用者的行事曆，或傳送電子郵件。 您的應用程式可以存取這些資源，藉由指定應用程式開發介面範圍使用 MSAL 要求存取權杖。 此存取權杖，接著會新增至每個呼叫，會對受保護資源的 HTTP 授權標頭。 

MSAL 管理快取，並為您重新整理存取權杖，以便在您的應用程式不需要。

## <a name="nuget-packages"></a>NuGet 套件

本指南會使用以下 NuGet 套件：

|程式庫|說明|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|

