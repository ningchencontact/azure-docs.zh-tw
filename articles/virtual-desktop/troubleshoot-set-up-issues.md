---
title: Windows 虛擬桌面租使用者和主機集區建立-Azure
description: 如何在 Windows 虛擬桌面租使用者環境的安裝期間疑難排解和解決租使用者和主機集區的問題。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: v-chjenk
ms.openlocfilehash: 9e58c3bfc0e74f9aac61085608a843954b0923c0
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249565"
---
# <a name="tenant-and-host-pool-creation"></a>建立租用戶和主機集區

本文涵蓋 Windows 虛擬桌面租使用者和相關工作階段主機集區基礎結構的初始設定期間的問題。

## <a name="provide-feedback"></a>提供意見反應

我們目前不採用 Windows 虛擬桌面處於預覽狀態時的支援案例。 請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>取得 Windows 10 企業版多會話映射

若要使用 Windows 10 企業版的多會話映射, 請移至 Azure Marketplace,   > 選取 [開始使用] [**Microsoft Windows 10** > 和  [Windows 10 企業版] [虛擬桌面預覽版, 版本 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)]。

![為虛擬桌面預覽版選取 Windows 10 企業版 (版本 1809) 的螢幕擷取畫面。](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>正在建立 Windows 虛擬桌面租使用者

本節涵蓋建立 Windows 虛擬桌面租使用者時可能發生的問題。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Error:使用者未獲授權, 無法查詢管理服務

![PowerShell 視窗的螢幕擷取畫面, 其中的使用者未獲授權查詢管理服務。](media/UserNotAuthorizedNewTenant.png)

原始錯誤的範例:

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

**原因：** 已登入的使用者未獲指派其 Azure Active Directory 中的 TenantCreator 角色。

**補丁**依照[將 TenantCreator 應用程式角色指派給 Azure Active Directory 租使用者中的使用者](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant)中的指示進行。 依照指示進行之後, 您將會有已指派給 TenantCreator 角色的使用者。

![已指派 TenantCreator 角色的螢幕擷取畫面。](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>建立 Windows 虛擬桌面工作階段主機 Vm

您可以透過數種方式建立工作階段主機 Vm, 但遠端桌面服務/Windows 虛擬桌面小組僅支援與 Azure Resource Manager 範本相關的 VM 布建問題。 Azure Resource Manager 範本可在[Azure Marketplace](https://azuremarketplace.microsoft.com/)和[GitHub](https://github.com/)中取得。

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>使用 Windows 虛擬桌面的問題–布建主機集區 Azure Marketplace 供應專案

[Windows 虛擬桌面-布建主機集區] 範本可從 Azure Marketplace 取得。

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Error:使用 GitHub 的連結時, 會顯示「建立免費帳戶」訊息

![建立免費帳戶的螢幕擷取畫面。](media/be615904ace9832754f0669de28abd94.png)

**原因 1：** 用來登入 Azure 的帳戶中沒有使用中的訂閱, 或使用的帳戶沒有許可權可查看訂閱。

**修正 1:** 使用具有參與者存取權的帳戶 (至少) 登入工作階段主機 Vm 即將部署的訂用帳戶。

**原因 2：** 使用的訂用帳戶是 Microsoft Cloud 服務提供者 (CSP) 租使用者的一部分。

**修正 2:** 移至 GitHub 位置以**建立和布建新的 Windows 虛擬桌面主機集**區, 並遵循下列指示:

1. 以滑鼠右鍵按一下 [**部署至 Azure** ], 然後選取 [**複製連結位址**]。
2. 開啟 [**記事本**] 並貼上連結。
3. 在 # 字元之前, 插入 CSP 終端客戶租使用者名稱。
4. 在瀏覽器中開啟新的連結, Azure 入口網站將會載入範本。

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure Resource Manager 範本和 PowerShell Desired State Configuration (DSC) 錯誤

請遵循這些指示, 針對 Azure Resource Manager 範本和 PowerShell DSC 的不成功部署進行疑難排解。

1. 使用[Azure Resource Manager 的 View 部署作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations), 檢查部署中的錯誤。
2. 如果部署中沒有任何錯誤, 請使用 [[查看活動記錄] 來審查資源的動作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit), 以查看活動記錄中的錯誤。
3. 一旦識別出錯誤, 請使用錯誤訊息和針對[常見 Azure 部署 Azure Resource Manager 錯誤進行疑難排解](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors)中的資源, 以解決問題。
4. 刪除先前部署期間所建立的任何資源, 然後再次嘗試部署範本。

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Error:您的部署失敗 ...\<. 主機名稱 >/joindomain

![您的部署失敗螢幕擷取畫面。](media/e72df4d5c05d390620e07f0d7328d50f.png)

原始錯誤的範例:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**原因 1：** 提供用來將 Vm 加入網域的認證不正確。

**修正 1:** 請參閱「不正確的認證」錯誤, 讓 Vm 未加入[工作階段主機 VM](troubleshoot-vm-configuration.md)設定中的網域。

**原因 2：** 功能變數名稱無法解析。

**修正 2:** 請參閱「功能變數名稱無法解析」錯誤, 讓 Vm 未加入[工作階段主機 VM](troubleshoot-vm-configuration.md)設定中的網域。


### <a name="error-your-deployment-failedunauthorized"></a>Error:您的部署失敗。 .\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**原因：** 您所使用的訂用帳戶是無法存取客戶嘗試部署之區域中所需功能的類型。 例如, MSDN、免費或教育訂閱可能會顯示此錯誤。

**補丁**將您的訂用帳戶類型或區域變更為可存取所需功能的地區。

### <a name="error-vmextensionprovisioningerror"></a>Error:VMExtensionProvisioningError

![因為終端機布建狀態失敗, 所以部署的螢幕擷取畫面失敗。](media/7aaf15615309c18a984673be73ac969a.png)

**原因 1：** Windows 虛擬桌面環境發生暫時性錯誤。

**原因 2：** 連接發生暫時性錯誤。

**補丁**使用 PowerShell 登入, 確認 Windows 虛擬桌面環境狀況良好。 在[使用 PowerShell 建立主機集](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)區中手動完成 VM 註冊。

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Error:不允許指定的系統管理員使用者名稱

![部署的螢幕擷取畫面失敗, 其中不允許系統管理員指定。](media/f2b3d3700e9517463ef88fa41875bac9.png)

原始錯誤的範例:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**原因：** 提供的密碼包含禁止的子字串 (管理員、系統管理員、根)。

**補丁**更新使用者名稱或使用不同的使用者。

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Error:VM 在處理延伸模組時回報失敗

![在您的部署中, 以終端機布建狀態完成的資源作業的螢幕擷取畫面失敗。](media/49c4a1836a55d91cd65125cf227f411f.png)

原始錯誤的範例:

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

**原因：** PowerShell DSC 擴充功能無法取得 VM 上的系統管理員存取權。

**補丁**確認使用者名稱和密碼具有虛擬機器的系統管理存取權, 然後再次執行 Azure Resource Manager 範本。

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Error:DeploymentFailed-PowerShell DSC 設定 ' FirstSessionHost ' 已完成, 但發生錯誤

![因為 PowerShell DSC 設定 ' FirstSessionHost ' 已完成但發生錯誤, 所以部署的螢幕擷取畫面失敗。](media/64870370bcbe1286906f34cf0a8646ab.png)

原始錯誤的範例:

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

**原因：** PowerShell DSC 擴充功能無法取得 VM 上的系統管理員存取權。

**補丁**確認提供的使用者名稱和密碼具有虛擬機器的系統管理存取權, 然後再次執行 Azure Resource Manager 範本。

### <a name="error-deploymentfailed--invalidresourcereference"></a>Error:DeploymentFailed – InvalidResourceReference

原始錯誤的範例:

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

**原因：** 資源組名的一部分會用於範本所建立的特定資源。 由於名稱符合現有的資源, 因此範本可能會從不同的群組中選取現有的資源。

**補丁**執行 Azure Resource Manager 範本來部署工作階段主機 Vm 時, 請將前兩個字元設為訂用帳戶資源組名的唯一名稱。

### <a name="error-deploymentfailed--invalidresourcereference"></a>Error:DeploymentFailed – InvalidResourceReference

原始錯誤的範例:

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

**原因：** 此錯誤是因為使用 Azure Resource Manager 範本建立的 NIC 與 VNET 中已有的另一個 NIC 具有相同的名稱。

**補丁**請使用不同的主機首碼。

### <a name="error-deploymentfailed--error-downloading"></a>Error:DeploymentFailed –下載錯誤

原始錯誤的範例:

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

**原因：** 此錯誤是因為靜態路由、防火牆規則或 NSG 封鎖下載與 Azure Resource Manager 範本系結的 zip 檔案。

**補丁**移除封鎖靜態路由、防火牆規則或 NSG。 (選擇性) 在文字編輯器中開啟 Azure Resource Manager 範本 json 檔案, 接受 zip 檔案的連結, 並將資源下載到允許的位置。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Error:使用者未獲授權, 無法查詢管理服務

原始錯誤的範例:

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

**原因：** 指定的 Windows 虛擬桌面租使用者管理員不具有有效的角色指派。

**補丁**建立 Windows 虛擬桌面租使用者的使用者必須登入 Windows 虛擬桌面 PowerShell, 並將角色指派指派給嘗試的使用者。 如果您正在執行 GitHub Azure Resource Manager 範本參數, 請使用 PowerShell 命令來遵循下列指示:

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Error:使用者需要 Azure 多重要素驗證 (MFA)

![因缺少多重要素驗證 (MFA) 而導致部署失敗的螢幕擷取畫面](media/MFARequiredError.png)

原始錯誤的範例:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**原因：** 指定的 Windows 虛擬桌面租使用者管理員需要 Azure 多重要素驗證 (MFA) 才能登入。

**補丁**依照[教學課程: 下列步驟所示, 為您的 Windows 虛擬桌面租使用者建立服務主體並為其指派角色。使用 PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)建立服務主體和角色指派。 確認您可以使用服務主體登入 Windows 虛擬桌面之後, 請根據您所使用的方法, 重新執行 Azure Marketplace 供應專案或 GitHub Azure Resource Manager 範本。 請依照下列指示, 為您的方法輸入正確的參數。

如果您正在執行 Azure Marketplace 供應專案, 請提供下列參數的值, 以適當地驗證 Windows 虛擬桌面:

- Windows 虛擬桌面租使用者 RDS 擁有者:服務主體
- 應用程式識別碼：您所建立之新服務主體的應用程式識別碼
- 密碼/確認密碼:您為服務主體產生的密碼秘密
- Azure AD 租使用者識別碼:您所建立之服務主體的 Azure AD 租使用者識別碼

如果您正在執行 GitHub Azure Resource Manager 範本, 請提供下列參數的值, 以適當地驗證 Windows 虛擬桌面:

- 租使用者系統管理員使用者主體名稱 (UPN) 或應用程式識別碼:您所建立之新服務主體的應用程式識別碼
- 租使用者系統管理員密碼:您為服務主體產生的密碼秘密
- IsServicePrincipal: **true**
- AadTenantId您所建立之服務主體的 Azure AD 租使用者識別碼

## <a name="next-steps"></a>後續步驟

- 如需疑難排解 Windows 虛擬桌面和擴大追蹤的總覽, 請參閱[疑難排解總覽、意見反應和支援](troubleshoot-set-up-overview.md)。
- 若要在 Windows 虛擬桌面中設定虛擬機器 (VM) 時針對問題進行疑難排解, 請參閱[工作階段主機虛擬機器](troubleshoot-vm-configuration.md)設定。
- 若要疑難排解 Windows 虛擬桌面用戶端連線的問題, 請參閱[遠端桌面用戶端連接](troubleshoot-client-connection.md)。
- 若要針對搭配 Windows 虛擬桌面使用 PowerShell 時的問題進行疑難排解, 請參閱[Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要深入瞭解預覽服務, 請參閱[Windows 桌面預覽環境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)進行疑難排解。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。
