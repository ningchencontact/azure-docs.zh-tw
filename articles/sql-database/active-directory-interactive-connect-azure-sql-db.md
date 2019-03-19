---
title: ActiveDirectoryInteractive 連線至 SQL | Microsoft Docs
description: C# 程式碼範例 (具有說明)，使用 SqlAuthenticationMethod.ActiveDirectoryInteractive 模式來連線到 Azure SQL Database。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi
ms.date: 03/12/2019
manager: craigg
ms.openlocfilehash: 5f089148bafbab21721c83c0d4b6977a7db86320
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57834582"
---
# <a name="connect-to-azure-sql-database-with-active-directory-mfa"></a>使用 Active Directory MFA 連線到 Azure SQL Database

本文提供 C# 程式，可以連線到您的 Microsoft Azure SQL Database。 程式會使用互動模式驗證，其支援 [Azure Active Directory (Azure AD) 多重要素驗證 (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)。

如需適用於 SQL 工具之 MFA 支援的詳細資訊，請參閱 [SQL Server Data Tools (SSDT) 中的 Azure Active Directory 支援](https://docs.microsoft.com/sql/ssdt/azure-active-directory)。

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Azure SQL Database 的多重要素驗證

從 .NET Framework 4.7.2 版開始，列舉 [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) 有新的值 `ActiveDirectoryInteractive`。 在用戶端 C# 程式中，列舉值會將系統導向為使用 Azure AD 互動模式，該模式支援使用 MFA 連線至 Azure SQL Database。 執行程式的使用者會看到下列對話方塊：

* 對話方塊會顯示 Azure AD 使用者名稱，並要求使用者密碼。

   若使用者的網域與 Azure AD 同盟，此對話方塊不會顯示，因為無需密碼。

   若 Azure AD 原則對使用者強制執行 MFA，就會顯示接下來的兩個對話方塊。

* 使用者首次進行 MFA 時，系統會顯示對話方塊，要求提供行動電話號碼以傳送簡訊。 每個訊息會提供*驗證碼*，使用者必須在下一個對話方塊中輸入該驗證碼。

* 對話方塊會要求 MFA 驗證碼，即系統先前傳送到行動電話的驗證碼。

如需如何設定 Azure AD 以要求 MFA 的詳細資訊，請參閱[開始在雲端中使用 Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)。

如需這些對話方塊的螢幕擷取畫面，請參閱[設定適用於 SQL Server Management Studio 和 Azure AD 的多重要素驗證](sql-database-ssms-mfa-authentication-configure.md)。

> [!TIP]
> 您可以使用 **.NET API 瀏覽器**工具頁面搜尋 .NET Framework API：
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> 您也可以透過選擇性的 **?term=&lt;搜尋值&gt;** 參數進行直接搜尋：
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)

## <a name="configure-your-c-application-in-the-azure-portal"></a>在 Azure 入口網站中設定您的 C# 應用程式

開始之前，您應已建立可用的 [Azure SQL Database 伺服器](sql-database-get-started-portal.md)。

### <a name="register-your-app-and-set-permissions"></a>註冊應用程式並設定權限


若要使用 Azure AD 驗證，您的 C# 程式必須註冊為 Azure AD 應用程式。 若要註冊應用程式，您必須是 Azure AD 管理員或受指派為 Azure AD「應用程式開發人員」角色的使用者。 如需有關指派角色的詳細資訊，請參閱[使用 Azure Active Directory 將系統管理員和非系統管理員角色指派給使用者。](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)

 應用程式註冊完成後，會產生與顯示**應用程式識別碼**。 您的程式必須包含此識別碼才能連線。


若要為應用程式註冊與設定必要權限，請遵循下列步驟：

1. Azure 入口網站 > [Azure Active Directory] > [應用程式註冊] > [新應用程式註冊]

    ![應用程式註冊](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    應用程式註冊一建立便會產生與顯示**應用程式識別碼**值。

    ![顯示的應用程式識別碼](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. [註冊的應用程式] > [設定] > [必要權限] > [新增]

    ![已註冊應用程式的權限設定](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. [必要權限] > [新增] > [選取 API] > [Azure SQL Database]

    ![將存取權新增至 Azure SQL Database 的 API](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. [API 存取權] > [選取權限] > [委派的權限]

    ![將權限委派給 Azure SQL Database 的 API](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>為您的 SQL Database 伺服器設定 Azure AD 管理員


為了讓您的 C# 程式能夠執行，Azure SQL 伺服器管理員需要為您的 Azure SQL 伺服器指派 Azure AD 管理員。 

 * [SQL Server] > [Active Directory 系統管理員] > [設定系統管理員]

如需 Azure SQL Database 的 Azure AD 管理員和使用者的詳細資訊，請參閱[設定及管理對 SQL Database 的 Azure Active Directory 驗證](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)，在**為您的 Azure SQL Database 伺服器佈建 Azure Active Directory 系統管理員**一節中的螢幕擷取畫面。

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>將非管理員使用者新增至特定資料庫 (選用)

SQL Database 伺服器的 Azure AD 管理員可以執行 C# 範例程式。 若 Azure AD 使用者在資料庫內，他們可以執行程式。 已經存在於資料庫並擁有 `ALTER ANY USER` 權限的 Azure AD SQL 管理員或 Azure AD 使用者可以新增使用者。

您可以使用 SQL 的 [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) 命令將使用者新增至資料庫。 例如： `CREATE USER [<username>] FROM EXTERNAL PROVIDER`。

如需詳細資訊，請參閱[使用 Azure Active Directory 驗證向 SQL Database、受控執行個體或 SQL 資料倉儲進行驗證](sql-database-aad-authentication.md)。

## <a name="new-authentication-enum-value"></a>新的驗證列舉值

C# 範例仰賴 [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) 命名空間。 多重要素驗證特別值得注意的是列舉 `SqlAuthenticationMethod`，此列舉具有下列值：

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   將此值與 Azure AD 使用者名稱搭配使用以實作 MFA。 這個值是目前文章的焦點。 它會透過顯示對話方塊來要求輸入使用者密碼，接著若強制對此使用者執行 MFA，則會要求進行 MFA 驗證，來產生互動式體驗。 此值從 .NET Framework 4.7.2 版開始可供使用。

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  針對「同盟」帳戶使用此值。 針對同盟帳戶，Windows 網域已知使用者名稱。 這個驗證方法不支援 MFA。

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  針對要求 Azure AD 使用者名稱和密碼的驗證使用此值。 Azure SQL Database 會進行驗證。 這個方法不支援 MFA。

## <a name="set-c-parameter-values-from-the-azure-portal"></a>從 Azure 入口網站設定 C# 參數值

為了成功執行 C# 程式，您必須將適當的值指派給靜態欄位。 此處列出欄位和範例值， 以及取得所需值的 Azure 入口網站位置：

| 靜態欄位名稱 | 範例值 | 在 Azure 入口網站中的位置 |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | [SQL Server] > [依名稱篩選] |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | [Azure Active Directory] > [使用者] > [新增來賓使用者] |
| Initial_DatabaseName | "myDatabase" | [SQL Server] > [SQL 資料庫] |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | [Azure Active Directory] > [應用程式註冊] > [依名稱搜尋] > [應用程式識別碼] |
| RedirectUri | new Uri("https://mywebserver.com/") | [Azure Active Directory] > [應用程式註冊] > [依名稱搜尋] > [Your-App-regis] > [設定] > [RedirectURIs]<br /><br />對於這篇文章，任何有效值都適用於 RedirectUri，因為該值不會在此處使用。 |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio-ssms"></a>驗證 SQL Server Management Studio (SSMS)

在執行 C# 程式前，建議先檢查 SSMS 中的設定和組態都正確無誤。 如此一來，任何 C# 程式失敗就會是原始程式碼的問題。

### <a name="verify-sql-database-firewall-ip-addresses"></a>驗證 SQL Database 防火牆 IP 位址

在您計畫執行 C# 程式的相同組建中，從相同的電腦執行 SSMS。 針對此測試，任何**驗證**模式皆可使用。 如果有任何指示表示資料庫伺服器防火牆不接受您的 IP 位址，請參閱 [Azure SQL Database 伺服器層級和資料庫層級防火牆規則](sql-database-firewall-configure.md)以取得說明。

### <a name="verify-azure-active-directory-mfa"></a>驗證 Azure Active Directory MFA

再次執行 SSMS，但是這次將**驗證**設為 **Active Directory - 通用 (具有 MFA 支援)**。 此選項需要 SSMS 17.5 或更新版本。

如需詳細資訊，請參閱[設定 SSMS 和 Azure AD 的多重要素驗證](sql-database-ssms-mfa-authentication-configure.md)。

> [!NOTE]
> 如果您在資料庫中是來賓使用者，您也必須為資料庫提供 Azure AD 網域名稱：[選項] > [AD 網域名稱或租用戶 ID]。 若要在 Azure 入口網站中尋找網域名稱，請選取 [Azure Active Directory] > [自訂網域名稱]。 在 C# 範例程式中，提供網域名稱並非必要。

## <a name="c-code-example"></a>C# 程式碼範例

C# 程式範例依賴 [*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL 組件。

若要安裝此封裝，請在 Visual Studio 中選取 [專案] > [管理 NuGet 封裝]。 搜尋並安裝 **Microsoft.IdentityModel.Clients.ActiveDirectory**。

### <a name="c-source-code"></a>C# 原始程式碼

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program .

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2 .
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider .
} // EONamespace.  End of entire program source code.

```

&nbsp;

#### <a name="actual-test-output-from-c"></a>C# 的實際測試輸出

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>後續步驟

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database，仍然支援 PowerShell 的 Azure Resource Manager 模組，但所有未來的開發是 Az.Sql 模組。 這些指令程式，請參閱 < [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 在 Az 模組和 AzureRm 模組中命令的引數是本質上相同的。

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)