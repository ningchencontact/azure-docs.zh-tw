---
title: Windows 虛擬桌面租用戶和主機建立集區-Azure
description: 如何疑難排解及解決租用戶和主應用程式集區的問題在安裝 Windows 的虛擬桌面的租用戶環境的期間。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 88e843c410a750387ecf58497dec79586e2a59d8
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523337"
---
# <a name="tenant-and-host-pool-creation"></a>建立租用戶和主機集區

本文章涵蓋的 Windows 虛擬桌面租用戶和相關的工作階段主應用程式集區的基礎結構的初始設定期間的問題。

## <a name="provide-feedback"></a>提供意見反應

我們目前不採用 Windows 虛擬桌面處於預覽狀態時的支援案例。 請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>取得 Windows 10 企業版多重工作階段影像

若要使用 Windows 10 企業版多重工作階段映像，請移至 Azure Marketplace 中，選取**開始** > **Microsoft Windows 10** > 和[for Windows 10 企業版虛擬桌面預覽，版本 1809年](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)。

![對於虛擬桌面預覽，版本 1809年選取 Windows 10 企業版的螢幕擷取畫面。](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>建立 Windows 虛擬桌面的租用戶

建立 Windows 虛擬桌面的租用戶時，本節將涵蓋的潛在問題。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Error:使用者未獲授權來查詢管理服務

![在其中使用者未獲授權來查詢管理服務的螢幕擷取畫面的 PowerShell 視窗。](media/UserNotAuthorizedNewTenant.png)

原始錯誤的範例：

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**原因：** 使用者登入的使用者尚未被指派 TenantCreator 角色，其 Azure Active Directory 中。

**修正：** 請依照下列中的指示[TenantCreator 應用程式角色指派給您的 Azure Active Directory 租用戶中的使用者](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant)。 遵循指示進行之後，您必須獲指派 TenantCreator 角色的使用者。

![螢幕擷取畫面的 TenantCreator 角色指派。](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>建立 Windows 虛擬桌面工作階段主機 Vm

工作階段主機的 Vm 可以建立數種方式，但是遠端桌面服務/Windows 虛擬桌面小組只支援 VM 佈建至 Azure Resource Manager 範本的相關問題。 Azure Resource Manager 範本位於[Azure Marketplace](https://azuremarketplace.microsoft.com/)並[GitHub](https://github.com/)。

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>使用 Windows 虛擬桌面 – 佈建的主應用程式集區 Azure Marketplace 供應項目問題

Windows 虛擬桌面 – 佈建的主應用程式集區範本也可以從 Azure Marketplace。

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Error:當使用 GitHub 中的連結，訊息 「 建立免費帳戶 」 隨即出現

![若要建立免費帳戶的螢幕擷取畫面。](media/be615904ace9832754f0669de28abd94.png)

**原因 1：** 沒有使用中的訂用帳戶中用來登入 Azure 帳戶，或使用的帳戶沒有檢視訂用帳戶的權限。

**1 的修正：** 使用具有工作階段主機 Vm 所要部署的 （至少） 的訂用帳戶的參與者存取權的帳戶登入。

**原因 2：** 正在使用的訂用帳戶是 Microsoft 雲端服務提供者 (CSP) 租用戶的一部分。

**2 的修正：** 移至 GitHub 位置**建立及佈建新的 Windows 虛擬桌面主應用程式集區**並遵循這些指示：

1. 以滑鼠右鍵按一下**部署至 Azure** ，然後選取**複製連結位址**。
2. 開啟**記事本**連結並貼上。
3. # 字元前插入 CSP 終端客戶租用戶名稱。
4. 開啟瀏覽器和 Azure 入口網站中新的連結會將範本載入。

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure Resource Manager 範本和 PowerShell Desired State Configuration (DSC) 錯誤

遵循這些指示不成功部署 Azure Resource Manager 範本和 PowerShell DSC 的問題進行疑難排解。

1. 檢閱在部署中使用的錯誤[來檢視部署作業使用 Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。
2. 如果部署中沒有任何錯誤，請檢閱在活動記錄檔中使用的錯誤[檢視活動記錄以稽核對資源的動作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
3. 一旦識別錯誤，將錯誤訊息和中的資源[疑難排解常見的 Azure 部署錯誤與 Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors)來解決問題。
4. 刪除在先前的部署和重試一次部署範本期間建立的任何資源。

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Error:您的部署失敗...<hostname>/joindomain

![您部署無法螢幕擷取畫面。](media/e72df4d5c05d390620e07f0d7328d50f.png)

原始錯誤的範例：

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**原因 1：** 提供將 Vm 加入網域的認證不正確。

**1 的修正：** 如未加入網域中的 Vm，請參閱 「 不正確認證 」 錯誤[工作階段主機 VM 設定](troubleshoot-vm-configuration.md)。

**原因 2：** 未解析的網域名稱。

**2 的修正：** 請參閱 Vm 未加入網域的 「 網域名稱未解析 」 錯誤[工作階段主機 VM 設定](troubleshoot-vm-configuration.md)。

### <a name="error-vmextensionprovisioningerror"></a>Error:VMExtensionProvisioningError

![螢幕擷取畫面的您部署失敗，終端佈建狀態失敗。](media/7aaf15615309c18a984673be73ac969a.png)

**原因 1：** 與 Windows 虛擬桌面環境的暫時性錯誤。

**原因 2：** 與連接的暫時性錯誤。

**修正：** 確認 Windows 虛擬桌面環境狀況良好，使用 PowerShell 登入。 完成 VM 註冊中手動[使用 PowerShell 建立主應用程式集區](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)。

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Error:不允許指定的系統管理員使用者名稱

![中不允許指定的系統管理員的螢幕擷取畫面，您的部署失敗。](media/f2b3d3700e9517463ef88fa41875bac9.png)

原始錯誤的範例：

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**原因：** 提供的密碼包含禁止的子字串，系統管理員、 系統管理員 （根）。

**修正：** 更新使用者名稱，或使用不同的使用者。

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Error:VM 回報在失敗時處理延伸模組

![資源作業已完成，但在您的部署失敗的終端機佈建狀態的螢幕擷取畫面。](media/49c4a1836a55d91cd65125cf227f411f.png)

原始錯誤的範例：

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-hostpool4-preview-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few: 
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource": 
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft. 
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**原因：** PowerShell DSC 延伸模組無法取得 VM 上的系統管理員存取權。

**修正：** 確認使用者名稱和密碼在虛擬機器上具有系統管理權限，並再次執行 Azure Resource Manager 範本。

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Error:DeploymentFailed-PowerShell DSC 設定 'FirstSessionHost' 已完成但發生錯誤

![使用 PowerShell DSC 設定 'FirstSessionHost' 已完成但發生錯誤，失敗部署的螢幕擷取畫面。](media/64870370bcbe1286906f34cf0a8646ab.png)

原始錯誤的範例：

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list 
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**原因：** PowerShell DSC 延伸模組無法取得 VM 上的系統管理員存取權。

**修正：** 確認使用者名稱和密碼提供具有系統管理權限在虛擬機器上，然後再次執行 Azure Resource Manager 範本。

### <a name="error-deploymentfailed--invalidresourcereference"></a>Error:DeploymentFailed – InvalidResourceReference

原始錯誤的範例：

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**原因：** 資源群組名稱的組件用於特定範本所建立的資源。 名稱符合現有的資源，因為範本可能會從不同的群組選取現有的資源。

**修正：** 當執行 Azure Resource Manager 範本來部署工作階段主機 Vm，請前兩個字元唯一的訂用帳戶資源群組名稱。

### <a name="error-deploymentfailed--invalidresourcereference"></a>Error:DeploymentFailed – InvalidResourceReference

原始錯誤的範例：

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**原因：** 此錯誤是因為使用 Azure Resource Manager 範本建立的 NIC 有另一個 NIC 相同的名稱已在 VNET 中。

**修正：** 使用不同的主機前置詞。

### <a name="error-deploymentfailed--error-downloading"></a>Error:DeploymentFailed – 下載時發生錯誤

原始錯誤的範例：

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-hostpool-3-preview-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-private-preview-
1/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**原因：** 此錯誤是因為靜態路由、 防火牆規則或 NSG 封鎖繫結至 Azure Resource Manager 範本的 zip 檔案的下載。

**修正：** 移除靜態路由、 防火牆規則或 NSG 封鎖。 （選擇性） 在文字編輯器中開啟 Azure Resource Manager 範本 json 檔案、 將 zip 檔案的連結和資源下載至允許的位置。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Error:使用者未獲授權來查詢管理服務

原始錯誤的範例：

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00", 
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing 
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s). 
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n 
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**原因：** 指定的 Windows 虛擬桌面租用戶系統管理員沒有有效的角色指派。

**修正：** 建立 Windows 虛擬桌面的租用戶的使用者必須登入 Windows 虛擬桌面的 PowerShell，並將嘗試的使用者指派的角色指派。 如果您執行 GitHub Azure Resource Manager 範本參數，請遵循這些指示使用 PowerShell 命令：

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Error:使用者需要 Azure Multi-factor Authentication (MFA)

![您的部署失敗，因為缺少的 Multi-factor Authentication (MFA) 的螢幕擷取畫面](media/MFARequiredError.png)

原始錯誤的範例：

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**原因：** 指定的 Windows 虛擬桌面租用戶系統管理員需要 Azure Multi-factor Authentication (MFA) 來登入。

**修正：** 建立服務主體並將它指派角色的 Windows 虛擬桌面租用戶中的步驟[教學課程：使用 PowerShell 建立服務主體和角色指派](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)。 在確認之後，您可以使用登入 Windows 虛擬桌面的服務主體，請重新執行 Azure Marketplace 供應項目或 GitHub Azure Resource Manager 範本，您使用的方法而定。 請遵循下列指示來輸入正確的參數，您的方法。

如果您執行 Azure Marketplace 供應項目，提供正確驗證到 Windows 虛擬桌面的下列參數的值：

- Windows 虛擬桌面租用戶 RDS 的擁有者：服務主體
- 應用程式識別碼:建立新的服務主體的應用程式識別
- 密碼/確認密碼：您產生服務主體的密碼祕密
- Azure AD 租用戶識別碼：您所建立的服務主體的 Azure AD 租用戶識別碼

如果您執行 GitHub Azure Resource Manager 範本，請提供正確驗證到 Windows 虛擬桌面的下列參數的值：

- 租用戶系統管理員使用者主體名稱 (UPN) 或應用程式識別碼：建立新的服務主體的應用程式識別
- 租用戶系統管理員密碼：您產生服務主體的密碼祕密
- IsServicePrincipal: **，則為 true**
- AadTenantId:您所建立的服務主體的 Azure AD 租用戶識別碼

## <a name="next-steps"></a>後續步驟

- 如需疑難排解 Windows 的虛擬桌面和擴大追蹤的概觀，請參閱 <<c0> [ 疑難排解概觀、 意見反應和支援](troubleshoot-set-up-overview.md)。
- 若要設定虛擬機器 (VM) 中 Windows 虛擬桌面時疑難排解問題，請參閱[工作階段主機的虛擬機器設定](troubleshoot-vm-configuration.md)。
- 若要疑難排解使用 Windows 的虛擬桌面用戶端連線的問題，請參閱[遠端桌面用戶端連線](troubleshoot-client-connection.md)。
- 若要使用 PowerShell 與 Windows 虛擬桌面時，請疑難排解問題，請參閱[Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要深入了解預覽服務，請參閱[Windows Desktop 預覽環境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。