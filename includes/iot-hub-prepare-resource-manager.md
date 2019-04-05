---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 145fc6bf9780f390d6f137b7e24a1f52f312a1a7
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2019
ms.locfileid: "59052002"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>准备对 Azure Resource Manager 请求进行身份验证
您必須使用 [Azure Resource Manager][lnk-authenticate-arm]搭配 Azure Active Directory (AD) 來驗證所有針對資源執行的作業。 最簡單的設定方式是使用 PowerShell 或 Azure CLI。

請先安裝 [Azure PowerShell Cmdlet][lnk-powershell-install] 再繼續。

下列步驟示範如何使用 PowerShell 設定 AD 應用程式的密碼驗證。 您可以在標準 PowerShell 工作階段中執行這些命令。

1. 使用下列命令來登入您的 Azure 訂用帳戶：

    ```powershell
    Connect-AzAccount
    ```

1. 如果您有多個 Azure 訂用帳戶，則登入 Azure 即會為您授與和您認證相關聯之所有 Azure 訂用帳戶的存取權。 使用下列命令來列出可供您使用的 Azure 訂用帳戶：

    ```powershell
    Get-AzSubscription
    ```

    使用下列命令，來選取您想要用來執行命令以管理 IoT 中樞的訂用帳戶。 您可以使用來自上一個命令之輸出內的訂用帳戶名稱或識別碼︰

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. 记下 **TenantId** 和 **SubscriptionId**。 您稍後需要這些資訊。
3. 使用以下命令并替换占位符，以创建新的 Azure Active Directory 应用程序：
   
   * **{Display name}：** 應用程式的顯示名稱，如 **MySampleApp**
   * **{Home page URL}:** 這類應用程式首頁的 URL **http:\/mysampleapp/主**。 此 URL 不需要指向實際的應用程式。
   * **{Application identifier}：** 這類的唯一識別碼**http:\//mysampleapp**。 此 URL 不需要指向實際的應用程式。
   * **{Password}：** 您用來驗證應用程式的密碼。
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. 請記下建立之應用程式的 **ApplicationId** 。 您稍後需要此資訊。
5. 使用下列命令，並將 **{MyApplicationId}** 取代為上一個步驟的 **ApplicationId**，藉此建立新的服務主體：
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. 使用下列命令，並將 **{MyApplicationId}** 取代為 **ApplicationId**，藉此設定角色指派。
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

您已建立可從自訂 C# 應用程式驗證的 Azure AD 應用程式。 在本教學課程後續的內容當中，您將需要以下各值：

* TenantId
* SubscriptionId
* ApplicationId
* 密碼

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/azurerm/install-Az-ps
