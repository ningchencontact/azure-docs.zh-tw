---
title: Configure function app settings in Azure
description: 了解如何設定 Azure Functions 應用程式設定。
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230581"
---
# <a name="manage-your-function-app"></a>Manage your function app 

在 Azure Functions 中，函數應用程式會提供個別函數的執行內容。 函數應用程式行為會套用至指定之函數應用程式所裝載的所有函數。 All functions in a function app must be of the same [language](supported-languages.md). 

Individual functions in a function app are deployed together and are scaled together. All functions in the same function app share resources, per instance, as the function app scales. 

Connection strings, environment variables, and other application settings are defined separately for each function app. Any data that must be shared between function apps should be stored externally in a persisted store.

This article describes how to configure and manage your function apps. 

> [!TIP]  
> Many configuration options can also be managed by using the [Azure CLI]. 

## <a name="get-started-in-the-azure-portal"></a>在 Azure 入口網站中開始使用

若要開始，請移至 [Azure 入口網站]，然後登入您的 Azure 帳戶。 在入口網站頂端的搜尋列中，輸入函數應用程式的名稱，然後從清單中選取它。 選取函數應用程式之後，您會看到下列頁面：

![Azure 入口網站中的函數應用程式概觀](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

You can navigate to everything you need to manage your function app from the overview page, in particular the **[Application settings](#settings)** and **[Platform features](#platform-features)** .

## <a name="settings"></a>應用程式設定

The **Application Settings** tab maintains settings that are used by your function app. These settings are stored encrypted, and you must select **Show values** to see the values in the portal. You can also access application settings by using the Azure CLI.

### <a name="portal"></a>入口網站

To add a setting in the portal, select **New application setting** and add the new key-value pair.

![Function app settings in the Azure portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

The [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) command returns the existing application settings, as in the following example:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

The [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) command adds or updates an application setting. The following example creates a setting with a key named `CUSTOM_FUNCTION_APP_SETTING` and a value of `12345`:


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Use application settings

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

When you develop a function app locally, you must maintain local copies of these values in the local.settings.json project file. To learn more, see [Local settings file](functions-run-local.md#local-settings-file).

## <a name="platform-features"></a>Platform features

![函數應用程式平台功能索引標籤。](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

函數應用程式是在 Azure App Service 平台中執行並由此平台維護。 因此，您的函數應用程式可以存取 Azure 核心虛擬主機平台的大多數功能。 [平台功能] 索引標籤可供您存取許多可在函數應用程式中使用的 App Service 平台功能。 

> [!NOTE]
> 當函數應用程式在「取用」主控方案上執行時，並非所有 App Service 功能都可供使用。

The rest of this article focuses on the following App Service features in the Azure portal that are useful for Functions:

+ [App Service 編輯器](#editor)
+ [Console](#console)
+ [進階工具 (Kudu)](#kudu)
+ [部署選項](#deployment)
+ [CORS](#cors)
+ [驗證](#auth)

如需有關如何使用 App Service 設定的詳細資訊，請參閱[設定 Azure App Service 設定](../app-service/configure-common.md)。

### <a name="editor"></a>App Service 編輯器

![App Service 編輯器](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

App Service 編輯器是一個進階的入口網站內編輯器，可供您用來修改 JSON 組態檔和類似的程式碼檔案。 選擇此選項會啟動一個含有基本編輯器的個別瀏覽器索引標籤。 這可讓您與 Git 存放庫整合、執行程式碼和進行偵錯，以及修改函數應用程式設定。 This editor provides an enhanced development environment for your functions compared with the built-in function editor.  

We recommend that you consider developing your functions on your local computer. When you develop locally and publish to Azure, your project files are read-only in the portal. To learn more, see [Code and test Azure Functions locally](functions-develop-local.md).

### <a name="console"></a>主控台

![函數應用程式主控台](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

當您偏好從命令列與函數應用程式進行互動時，入口網站內主控台是一個理想的開發人員工具。 常用命令包含目錄和檔案建立與瀏覽，以及執行批次檔和指令碼。 

When developing locally, we recommend using the [Azure Functions Core Tools](functions-run-local.md) and the [Azure CLI].

### <a name="kudu"></a>進階工具 (Kudu)

![設定 Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

App Service 的進階工具 (也稱為 Kudu) 可讓您存取函數應用程式的進階系統管理功能。 從 Kudu，您可以管理系統資訊、應用程式設定、環境變數、網站擴充功能、HTTP 標頭，以及伺服器變數。 您也可以透過瀏覽至函數應用程式的 SCM 端點 (例如 `https://<myfunctionapp>.scm.azurewebsites.net/`) 來啟動 **Kudu** 


### <a name="deployment"></a>Deployment Center

When you use a source control solution to develop and maintain your functions code, Deployment Center lets you build and deploy from source control. Your project is built and deployed to Azure when you make updates. For more information, see [Deployment technologies in Azure Functions](functions-deployment-technologies.md).

### <a name="cors"></a>Cross-origin resource sharing

To prevent malicious code execution on the client, modern browsers block requests from web applications to resources running in a separate domain. [Cross-origin resource sharing (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) lets an `Access-Control-Allow-Origin` header declare which origins are allowed to call endpoints on your function app.

#### <a name="portal"></a>入口網站

When you configure the **Allowed origins** list for your function app, the `Access-Control-Allow-Origin` header is automatically added to all responses from HTTP endpoints in your function app. 

![Configure function app's CORS list](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

When the wildcard (`*`) is used, all other domains are ignored. 

Use the [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) command to add a domain to the allowed origins list. The following example adds the contoso.com domain:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Use the [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) command to list the current allowed origins.

### <a name="auth"></a>驗證

![設定函數應用程式的驗證](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

當函數使用 HTTP 觸發程序時，您可以要求呼叫必須先經過驗證。 App Service supports Azure Active Directory authentication and sign-in with social providers, such as Facebook, Microsoft, and Twitter. 如需設定特定驗證提供者的詳細資訊，請參閱 [Azure App Service 驗證概觀](../app-service/overview-authentication-authorization.md)。 


## <a name="next-steps"></a>後續步驟

+ [設定 Azure App Service 設定](../app-service/configure-common.md)
+ [Azure Functions 的持續部署](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure 入口網站]: https://portal.azure.com
