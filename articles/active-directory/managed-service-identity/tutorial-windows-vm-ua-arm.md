---
title: 使用 Windows VM 使用者指派的受控服務識別來存取 Azure Resource Manager
description: 此教學課程會引導您使用 Windows VM 上使用者指派的受控服務識別，來存取 Azure Resource Manager 的程序。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: daveba
ms.openlocfilehash: 9cc7683b260a9afbe4aee006a22af9c4834c4eb1
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248382"
---
# <a name="tutorial-use-a-user-assigned-managed-service-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>教學課程：使用 Windows VM 上使用者指派的受控服務識別來存取 Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

本教學課程說明如何建立使用者指派的身分識別，並將其指派給 Windows 虛擬機器 (VM)，然後使用該身分識別存取 Azure Resource Manager API。 由 Azure 自動管理受控服務身分識別。 它們會啟用對支援 Azure AD 驗證之服務的驗證，而不需要在程式碼中內嵌認證。 

您會了解如何：

> [!div class="checklist"]
> * 建立 Windows VM 
> * 建立使用者指派的身分識別
> * 將您的使用者指派身分識別指派至 Windows VM
> * 在 Azure Resource Manager 中，將使用者指派的身分識別存取權授與資源群組 
> * 使用使用者指派的身分識別來取得存取權杖，並用其呼叫 Azure Resource Manager 
> * 讀取資源群組的屬性

## <a name="prerequisites"></a>必要條件

- 如果您不熟悉受控服務識別，請參閱[概觀](overview.md)一節。 **請務必檢閱[系統和使用者指派之身分識別其間的差異](overview.md#how-does-it-work)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 若要執行本教學課程中所需的資源建立和角色管理步驟，您的帳戶必須在適當的範圍 (您的訂用帳戶或資源群組) 上具備「擁有者」權限。 如果您需要角色指派的協助，請參閱[使用角色型存取控制來管理 Azure 訂用帳戶資源的存取權](/azure/role-based-access-control/role-assignments-portal)。

如果您選擇在本機安裝和使用 PowerShell，則在執行本教學課程時，您必須使用 Azure PowerShell 模組 5.7 版或更新版本。 執行 `Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Login-AzureRmAccount` 以建立與 Azure 的連線。

## <a name="create-resource-group"></a>建立資源群組

在下列範例中，會在 *EastUS* 區域中建立名為 *myResourceGroupVM* 的資源群組。

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

## <a name="create-virtual-machine"></a>建立虛擬機器

建立資源群組之後，建立 Windows VM。

使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 設定虛擬機器上系統管理員帳戶所需的使用者名稱和密碼：

```azurepowershell-interactive
$cred = Get-Credential
```
使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 建立虛擬機器。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="create-a-user-assigned-identity"></a>建立使用者指派的身分識別

使用者指派的身分識別會建立為獨立的 Azure 資源。 Azure 會使用 [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity)，在您的 Azure AD 租用戶中建立一個可指派給一或多個 Azure 服務執行個體的身分識別。

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

回應會包含所建立使用者指派身分識別的詳細資料，與下列範例類似。 請記下使用者指派身分識別的 `Id` 值，因為後續步驟中會使用該值：

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>將使用者指派的身分識別指派至 Windows VM

使用者指派的身分識別可以由多個 Azure 資源上的用戶端使用。 使用下列命令以將使用者指派的身分識別指派至單一虛擬機器。 針對 `-IdentityID` 參數，請使用前一個步驟中所傳回的 `Id` 屬性。

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-managed-service-identity-access-to-a-resource-group-in-azure-resource-manager"></a>在 Azure Resource Manager 中將使用者指派受控服務識別的存取權授與資源群組 

受控服務識別提供的身分識別可使用您的程式碼來要求存取權杖，以向支援 Azure AD 驗證的資源 API 進行驗證。 在本教學課程中，您的程式碼將存取 Azure Resource Manager API。 

您必須先將身分識別存取權授與 Azure Resource Manager 中的資源，您的程式碼才能存取 API。 在此情況下，是包含 VM 的資源群組。 將 `<SUBSCRIPTION ID>` 的值更新為適用於環境的值。

```azurepowershell-interactive
$spID = (Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

回應會包含已建立的角色指派詳細資料，與下列範例類似：

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>使用 VM 身分識別取得存取權杖，並使用它來呼叫 Resource Manager 

其餘課程要從稍早建立的虛擬機器繼續進行。

1. 在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站

2. 在入口網站中，瀏覽至 [虛擬機器]，並移至您的 Windows 虛擬機器，在 [概觀]中按一下 [連線]。

3. 輸入您建立 Windows VM 時使用的**使用者名稱**和**密碼**。

4. 現在您已經建立虛擬機器的**遠端桌面連線**，請在遠端工作階段中開啟 **PowerShell**。

5. 使用 Powershell 的 `Invoke-WebRequest`，向本機受控服務識別端點提出要求，取得 Azure Resource Manager 的存取權杖。

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=73444643-8088-4d70-9532-c3a0fdc190fz&resource=https://management.azure.com' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>讀取資源群組的屬性

使用在先前步驟中擷取的存取權杖來存取 Azure Resource Manager，以及讀取您已授與使用者指派之身分識別存取權的資源群組屬性。 將 <SUBSCRIPTION ID> 取代為您環境的訂用帳戶識別碼。

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
回應包含特定資源群組資訊，與下列範例類似：

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/TestRG","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已學習如何建立使用者指派的身分識別，並將其連結至 Azure 虛擬機器以存取 Azure Resource Manager API。  若要深入了解 Azure Resource Manager，請參閱：

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)