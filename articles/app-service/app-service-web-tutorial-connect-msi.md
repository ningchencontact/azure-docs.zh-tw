---
title: 使用受控識別保護 SQL Database 連線 - Azure App Service | Microsoft Docs
description: 了解如何使用受控識別，讓資料庫連線更加安全，以及如何將此受控服務識別套用到其他 Azure 服務。
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 31535642526c608ad0ae29e5c0e3c93368e184ad
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481021"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>教學課程：使用受控識別保護來自 App Service 的 Azure SQL Database 連線

[App Service](overview.md) 可在 Azure 中提供可高度擴充、自我修復的 Web 主控服務。 它也為您的應用程式提供[受控識別](overview-managed-identity.md)，這是用於保護 [Azure SQL Database](/azure/sql-database/) 和其他 Azure 服務存取權的周全解決方案。 App Service 中的受控識別可藉由從應用程式刪除祕密 (例如連接字串中的認證)，讓應用程式更加安全。 在本教學課程中，您會將受控識別新增至您在[教學課程：在 Azure 中搭配 SQL Database 來建置 ASP.NET 應用程式](app-service-web-tutorial-dotnet-sqldatabase.md)中建立的範例 ASP.NET Web 應用程式。 當您完成時，範例應用程式不需要使用者名稱和密碼，即可安全地連線到 SQL Database。

> [!NOTE]
> 目前只有 .NET Framework 4.7.2 和以上版本支援此案例。 [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) 支援此案例，但是尚未包含在 App Service 的預設映像中。 
>

您會了解如何：

> [!div class="checklist"]
> * 啟用受控身分識別
> * 將 SQL Database 存取權授與受控識別
> * 設定 Entity Framework 以將 Azure AD 驗證用於 SQL Database
> * 使用 Azure AD 驗證從 Visual Studio 連線至 SQL Database

> [!NOTE]
>Azure AD 驗證與內部部署 Active Directory (AD DS) 中的[整合式 Windows 驗證](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10))_不同_。 AD DS 和 Azure AD 使用完全不同的驗證通訊協定。 如需詳細資訊，請參閱 [Azure AD Domain Services 文件](https://docs.microsoft.com/azure/active-directory-domain-services/)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

本文會從您結束[教學課程：在 Azure 中搭配 SQL Database 來建置 ASP.NET 應用程式](app-service-web-tutorial-dotnet-sqldatabase.md)的地方繼續下去。 如果您還沒這麼做，請先依照該教學課程進行。 或者，您可以使用 SQL Database 針對自己的 ASP.NET 應用程式調整步驟。

若要使用 SQL Database 作為後端對您的應用程式進行偵錯，請確定您已[允許從您的電腦進行用戶端連線](app-service-web-tutorial-dotnet-sqldatabase.md#allow-client-connection-from-your-computer)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-azure-ad-user-access-to-database"></a>為 Azure AD 使用者授與資料庫的存取權

首先，將 Azure AD 使用者指派為 SQL Database 伺服器的 Active Directory 管理員，以啟用對 SQL Database 的 Azure AD 驗證。 此使用者與您用來註冊 Azure 訂用帳戶的 Microsoft 帳戶不同。 這必須是您已在 Azure AD 中建立、匯入、同步處理或受邀加入的使用者。 若要進一步了解允許的 Azure AD 使用者，請參閱 [SQL Database 中的 Azure AD 功能和限制](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)。 

使用 [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) 尋找 Azure AD 使用者的物件識別碼，並取代 *\<user-principal-name>* 。 結果會儲存到變數中。

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> 若要在 Azure AD 中查看所有使用者主體名稱的清單，請執行 `az ad user list --query [].userPrincipalName`。
>

在 Cloud Shell 中使用 [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) 命令，將此 Azure AD 使用者新增為 Active Directory 管理員。 在下列命令中，取代 *\<server-name>* 。

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

如需關於新增 Active Directory 管理員的詳細資訊，請參閱[為 Azure SQL Database 伺服器佈建 Azure Active Directory 管理員](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>設定 Visual Studio

若要啟用在 Visual Studio 中的開發和偵錯，請從功能表中選取 [檔案]   > [帳戶設定]  ，然後按一下 [新增帳戶]  ，以在 Visual Studio 中新增您的 Azure AD 使用者。

若要設定 Azure 服務驗證的 Azure AD 使用者，請從功能表中選取 [工具]   > [選項]  ，然後選取 [Azure 服務驗證]   > [帳戶選取]  。 選取您新增的 Azure AD 使用者，然後按一下 [確定]  。

現在您已可開始將 SQL Database 作為後端，使用 Azure AD 驗證開發和偵錯您的應用程式。

## <a name="modify-aspnet-project"></a>修改 ASP.NET 專案

在 Visual Studio 中，開啟套件管理員主控台，並新增 NuGet 套件 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)：

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.2.0
```

在 Web.config  中，從檔案頂端開始處理，並進行下列變更：

- 在 `<configSections>` 中，新增下列區段宣告：

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- 在右側 `</configSections>` 標記下方，為 `<SqlAuthenticationProviders>` 新增下列 XML 指令碼。

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- 尋找名為 `MyDbConnection` 的連接字串，並將其 `connectionString` 值取代為 `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`。 將 _\<server-name>_ 和 _\<db-name>_ 取代為您的伺服器名稱和資料庫名稱。

輸入 `Ctrl+F5` 以再次執行應用程式。 瀏覽器中的相同 CRUD 應用程式此時會使用 Azure AD 驗證直接連線至 Azure SQL Database。 此設定可讓您執行資料庫移轉。 後續當您將變更部署到 App Service 時，相同的設定將在應用程式的受控識別下運作。

## <a name="use-managed-identity-connectivity"></a>使用受控識別連線

接著，您將設定 App Service 應用程式，以使用系統指派的受控識別連線至 SQL Database。

### <a name="enable-managed-identity-on-app"></a>啟用應用程式上的受控識別

若要啟用 Azure 應用程式的受控識別，請在 Cloud Shell 中使用 [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) 命令。 在下列命令中，取代 *\<app-name>* 。

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

輸出的範例如下：

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>將受控識別新增至 Azure AD 群組

若要為此身分識別授與對 SQL Database 的存取權，您必須將其新增至 [Azure AD 群組](../active-directory/fundamentals/active-directory-manage-groups.md)。 在 Cloud Shell 中，將其新增至名為 myAzureSQLDBAccessGroup  的新群組，如下列指令碼所示：

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

如果您想要查看每個命令的完整 JSON 輸出，請拿掉 `--query objectId --output tsv` 參數。

### <a name="grant-permissions-to-azure-ad-group"></a>為 Azure AD 群組授與權限

在 Cloud Shell 中，使用 SQLCMD 命令登入 SQL Database。 請將 _\<server-name>_ 取代為您的 SQL Database 伺服器名稱，將 _\<db-name>_ 取代為您的應用程式使用的資料庫名稱，並將 _\<aad-user-name>_ 和 _\<aad-password>_ 取代為您 Azure AD 使用者的認證。

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

在您所需資料庫的 SQL 提示字元中執行下列命令，以新增 AD 群組，並授與您的應用程式所需的權限。 例如， 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

輸入 `EXIT` 以返回 Cloud Shell 提示字元。

### <a name="modify-connection-string"></a>修改連接字串

請記住，您在 `Web.config` 中所做的相同變更也適用於受控識別，因此您唯一要做的，就是在您的應用程式中移除現有的連接字串，也就是 Visual Studio 在第一次部署您的應用程式時建立的字串。 使用下列命令，但將 *\<app-name>* 取代為您的應用程式名稱。

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>發佈您的變更

現在只剩下將您的變更發佈至 Azure。

在 [方案總管]  中，以滑鼠右鍵按一下 [DotNetAppSqlDb]  專案，然後選取 [發佈]  。

![從方案總管發佈](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

在發佈頁面中，按一下 [發佈]  。 當新的網頁顯示待辦事項清單時，表示應用程式正使用受控識別連線到資料庫。

![Code First 移轉之後的 Azure 應用程式](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

您現在應該能夠如以往一樣編輯待辦事項清單。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>後續步驟

您已了解如何︰

> [!div class="checklist"]
> * 啟用受控身分識別
> * 將 SQL Database 存取權授與受控識別
> * 設定 Entity Framework 以將 Azure AD 驗證用於 SQL Database
> * 使用 Azure AD 驗證從 Visual Studio 連線至 SQL Database

前往下一個教學課程，了解如何將自訂的 DNS 名稱對應至 Web 應用程式。

> [!div class="nextstepaction"]
> [將現有的自訂 DNS 名稱對應至 Azure App Service](app-service-web-tutorial-custom-domain.md)
