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
ms.openlocfilehash: 7f151c45920a51b3c9a25f36de34b2c1800e4e71
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520678"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>連接到 Azure SQL Database 與 Azure Multi-factor Authentication

這篇文章提供C#連接到 Azure SQL Database 的程式。 程式會使用互動模式驗證，支援[Azure Multi-factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)。

如需 Multi-factor Authentication 支援 SQL 工具的詳細資訊，請參閱[Azure Active Directory 支援在 SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory)。

## <a name="multi-factor-authentication-for-azure-sql-database"></a>適用於 Azure SQL Database 多重要素驗證

從.NET Framework 4.7.2，列舉[ `SqlAuthenticationMethod` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod)有新的值： `ActiveDirectoryInteractive`。 在用戶端C#程式中，列舉值會指示系統使用支援多重要素驗證來連接到 Azure SQL database 的 Azure Active Directory (Azure AD) 互動模式。 執行程式的使用者會看到下列對話方塊：

* 對話方塊會顯示 Azure AD 使用者名稱，並要求使用者密碼。

   與 Azure AD 同盟使用者的網域時，如果未出現此對話方塊中，因為不需要密碼。

   Azure AD 原則會強加 Multi-factor Authentication 使用者，則會顯示下面兩個對話方塊。

* 第一次使用者經歷了 Multi-factor Authentication，系統會顯示對話方塊，要求您提供來傳送簡訊到行動電話號碼。 每個訊息會提供*驗證碼*，使用者必須在下一個對話方塊中輸入該驗證碼。

* 要求多重要素驗證該驗證碼，系統已傳送到行動電話的對話方塊。

如需如何設定 Azure AD，以要求多重要素驗證的資訊，請參閱[開始使用雲端中的 Azure Multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)。

這些對話方塊的螢幕擷取畫面，請參閱 < [SQL Server Management Studio 和 Azure AD 設定多重要素驗證](sql-database-ssms-mfa-authentication-configure.md)。

> [!TIP]
> 您可以搜尋與.NET Framework Api [.NET API 瀏覽器工具頁面](https://docs.microsoft.com/dotnet/api/)。
>
> 您也可以直接與搜尋[選擇性？ 詞彙 =&lt;搜尋值&gt;參數](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)。

## <a name="configure-your-c-application-in-the-azure-portal"></a>在 Azure 入口網站中設定您的 C# 應用程式

開始之前，您應已建立可用的 [Azure SQL Database 伺服器](sql-database-get-started-portal.md)。

### <a name="register-your-app-and-set-permissions"></a>註冊應用程式並設定權限

若要使用 Azure AD 驗證，您的 C# 程式必須註冊為 Azure AD 應用程式。 若要註冊應用程式，您必須是 Azure AD 管理員或受指派為 Azure AD「應用程式開發人員」角色的使用者。 如需有關如何將角色指派的詳細資訊，請參閱 <<c0> [ 將系統管理員和非系統管理員角色指派給使用者與 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。

完成應用程式註冊會產生並顯示**應用程式識別碼**。 您的程式必須包含此識別碼才能連線。

若要為應用程式註冊與設定必要權限，請遵循下列步驟：

1. 在 Azure 入口網站中，選取 [Azure Active Directory] > [應用程式註冊] > [新應用程式註冊]。

    ![應用程式註冊](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    建立應用程式註冊之後，**應用程式識別碼**值會產生並顯示。

    ![顯示的應用程式識別碼](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. 選取 **註冊的應用程式** > **設定** > **必要權限** > **新增**。

    ![已註冊應用程式的權限設定](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. 選取 **必要的權限** > **新增** > **選取 API** > **Azure SQL Database**.

    ![將存取權新增至 Azure SQL Database 的 API](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. 選取  **API 存取權** > **選取 權限** > **委派的權限**。

    ![將權限委派給 Azure SQL Database 的 API](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>為您的 SQL Database 伺服器設定 Azure AD 管理員

如您C#來執行程式、 Azure SQL server 系統管理員必須指派您的 SQL Database 伺服器的 Azure AD 系統管理員。 

在  **SQL Server**頁面上，選取**Active Directory 系統管理員** > **設定系統管理員**。

多個 Azure SQL Database Azure AD 系統管理員和使用者的詳細資訊，請參閱中的螢幕擷取畫面[設定和管理 SQL Database 與 Azure Active Directory 驗證](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)。

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>將非管理員使用者新增至特定資料庫 (選用)

SQL Database 伺服器的 Azure AD 管理員可以執行 C# 範例程式。 若 Azure AD 使用者在資料庫內，他們可以執行程式。 已經存在於資料庫並擁有 `ALTER ANY USER` 權限的 Azure AD SQL 管理員或 Azure AD 使用者可以新增使用者。

您可以使用 SQL 的 [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) 命令將使用者新增至資料庫。 例如 `CREATE USER [<username>] FROM EXTERNAL PROVIDER`。

如需詳細資訊，請參閱[使用 Azure Active Directory 驗證向 SQL Database、受控執行個體或 SQL 資料倉儲進行驗證](sql-database-aad-authentication.md)。

## <a name="new-authentication-enum-value"></a>新的驗證列舉值

C# 範例仰賴 [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) 命名空間。 Multi-factor authentication 的特殊感興趣的是列舉`SqlAuthenticationMethod`，其中包含下列值：

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   使用此值與 Azure AD 使用者名稱，實作多重要素驗證。 這個值是目前文章的焦點。 它藉由顯示對話方塊，在使用者密碼，然後再針對 Multi-factor Authentication 驗證，如果 Multi-factor Authentication 會加諸於此使用者產生的互動式體驗。 此值從 .NET Framework 4.7.2 版開始可供使用。

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  針對「同盟」帳戶使用此值。 針對同盟帳戶，Windows 網域已知使用者名稱。 這種驗證方法不支援多重要素驗證。

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  針對要求 Azure AD 使用者名稱和密碼的驗證使用此值。 Azure SQL Database 會進行驗證。 這個方法不支援多重要素驗證。

## <a name="set-c-parameter-values-from-the-azure-portal"></a>從 Azure 入口網站設定 C# 參數值

為了成功執行 C# 程式，您必須將適當的值指派給靜態欄位。 此處列出欄位和範例值， 也會顯示是 Azure 的入口網站位置，您可以在哪裡取得所需的值。

| 靜態欄位名稱 | 範例值 | 在 Azure 入口網站中的位置 |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | [SQL Server] > [依名稱篩選] |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | [Azure Active Directory] > [使用者] > [新增來賓使用者] |
| Initial_DatabaseName | "myDatabase" | [SQL Server] > [SQL 資料庫] |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | [Azure Active Directory] > [應用程式註冊] > [依名稱搜尋] > [應用程式識別碼] |
| RedirectUri | new Uri("https://mywebserver.com/") | **Azure Active Directory** > **應用程式註冊** > **依名稱搜尋** > *[您的應用程式-註冊]*  > **設定** > **RedirectURIs**<br /><br />在本文中，因為它不會使用以下，是適合 RedirectUri，任何有效的值。 |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>使用 SQL Server Management Studio 來驗證

在執行之前C#程式，它是個不錯的主意，來檢查您的安裝和設定正確的 SQL Server Management Studio (SSMS)。 如此一來，任何 C# 程式失敗就會是原始程式碼的問題。

### <a name="verify-sql-database-firewall-ip-addresses"></a>驗證 SQL Database 防火牆 IP 位址

在您計畫執行 C# 程式的相同組建中，從相同的電腦執行 SSMS。 此測試的任何**驗證**模式是 [確定]。 如果有任何指示表示資料庫伺服器防火牆不接受您的 IP 位址，請參閱 [Azure SQL Database 伺服器層級和資料庫層級防火牆規則](sql-database-firewall-configure.md)以取得說明。

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>驗證 Azure Active Directory 多重要素驗證

再次執行 SSMS，但是這次將**驗證**設為 **Active Directory - 通用 (具有 MFA 支援)**。 此選項需要 SSMS 17.5 或更新版本。

如需詳細資訊，請參閱 < [SSMS 和 Azure AD 設定 Multi-factor Authentication](sql-database-ssms-mfa-authentication-configure.md)。

> [!NOTE]
> 如果您是在資料庫中的來賓使用者，您也必須提供資料庫的 Azure AD 網域名稱：選取 **選項** > **AD 網域名稱或租用戶識別碼**。 若要在 Azure 入口網站中尋找網域名稱，請選取 [Azure Active Directory] > [自訂網域名稱]。 在 C# 範例程式中，提供網域名稱並非必要。

## <a name="c-code-example"></a>C# 程式碼範例

C# 程式範例依賴 [*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL 組件。

若要安裝此封裝，請在 Visual Studio 中選取 [專案] > [管理 NuGet 封裝]。 搜尋並安裝 **Microsoft.IdentityModel.Clients.ActiveDirectory**。

這是範例C#原始程式碼。

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
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
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
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

這是範例C#測試輸出。

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