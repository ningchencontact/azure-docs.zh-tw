---
title: 如何使用 Azure 範本為 Azure 虛擬機器設定使用者指派的 MSI
description: 使用 Azure Resource Manager 範本，為 Azure 虛擬機器設定使用者指派之受控服務識別 (MSI) 的逐步指示。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e01e4c397e0d0a19280a32fc1e8341b57b47e4eb
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38610378"
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-template"></a>使用 Azure 範本為虛擬機器設定使用者指派的受控服務識別 (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

在 Azure Active Directory 中，「受控服務識別」會提供受控身分識別給 Azure 服務。 您可以使用此身分識別來向支援 Azure AD 驗證的服務進行驗證，而不需要您程式碼中的認證。 

在本文中，您將了解如何使用 Azure Resource Manager 部署範本，為 Azure 虛擬機器啟用和移除使用者指派的 MSI。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>在建立 Azure VM 時，或在現有 VM 上啟用 MSI

如同 Azure 入口網站和指令碼，Azure Resource Manager 範本可讓您部署由 Azure 資源群組所定義之新的或已修改的資源。 範本編輯和部署有幾個選項可用 (在本機和入口網站)，包括：

   - 使用 [Azure Marketplace 所提供的自訂範本](~/articles/azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)，可讓您從頭開始建立範本，或根據現有的常用範本或[快速入門範本](https://azure.microsoft.com/documentation/templates/)來建立範本。
   - 衍生自現有的資源群組，方法是從[原始部署](~/articles/azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history)，或從[部署的目前狀態](~/articles/azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)匯出範本。
   - 使用本機 [JSON 編輯器 (例如 VS Code)](~/articles/azure-resource-manager/resource-manager-create-first-template.md)，然後使用 PowerShell 或 CLI 上傳和部署。
   - 使用 Visual Studio 的 [Azure 資源群組專案](~/articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)來建立和部署範本。  

不論您選擇的選項，在初始部署和重新部署期間，範本的語法都相同。 建立使用者指派的 MSI 並將其指派給新的或現有虛擬機器是以相同的方式進行。 此外，根據預設，Azure Resource Manager 會對部署採取[累加式更新](~/articles/azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)：

1. 無論您是在本機登入 Azure 或透過 Azure 入口網站登入，都使用與包含 MSI 和虛擬機器的 Azure 訂用帳戶相關聯的帳戶。 此外，請確定您的帳戶屬於可授與您訂用帳戶或資源寫入權限的角色 (例如，「擁有者」角色)。

2. 將範本載入編輯器中之後，找出＜`resources`一節中您感興趣的 `Microsoft.Compute/virtualMachines` 資源。 根據您所使用的編輯器，以及您是編輯新的或現有部署的範本而定，您的畫面與下列螢幕擷取畫面可能看起來稍有不同。

   >[!NOTE] 
   > 此範例假設在範本中已定義了 `vmName`、`storageAccountName` 和 `nicName` 這類變數。
   >

   ![範本的螢幕擷取畫面 - 尋找虛擬機器](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. 在與 `"type": "Microsoft.Compute/virtualMachines"` 屬性相同的層級上新增 `"identity"` 屬性。 使用下列語法：

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. 然後，將虛擬機器 MSI 擴充新增為 `resources` 元素。 使用下列語法：

   >[!NOTE] 
   > 下列範例假設正在部署 Windows 虛擬機器擴充 (`ManagedIdentityExtensionForWindows`)。 您也可以改用 `ManagedIdentityExtensionForLinux`來為 Linux 設定 `"name"` 和 `"type"` 元素。
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.ManagedIdentity",
           "type": "ManagedIdentityExtensionForWindows",
           "typeHandlerVersion": "1.0",
           "autoUpgradeMinorVersion": true,
           "settings": {
               "port": 50342
           },
           "protectedSettings": {}
       }
   }
   ```

5. 完成之後，您的範本看起來應該如下所示：

   ![更新之後範本的螢幕擷取畫面](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>從 Azure VM 移除 MSI

如果您的虛擬機器不再需要 MSI：

1. 無論您是在本機登入 Azure 或透過 Azure 入口網站登入，都請使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶。 此外，也請確定您的帳戶屬於在虛擬機器上具有寫入權限的角色 (例如「虛擬機器參與者」的角色)。

2. 移除在前一節中所新增的兩個元素：虛擬機器 的 `"identity"` 屬性和 `"Microsoft.Compute/virtualMachines/extensions"` 資源。

## <a name="related-content"></a>相關內容

- 如需 MSI 更廣泛的觀點，請參閱[受控服務識別概觀](msi-overview.md)。

