---
title: 使用範本在 Azure 虛擬機器擴展集上設定 MSI
description: 使用 Azure Resource Manager 範本在 Azure VMSS 上設定「受控服務身分識別 (MSI)」的逐步指示。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 9f550af869ccfc44ba4d840f54503ad017cdaf95
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901206"
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>使用範本設定 VMSS 受控服務識別

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

在 Azure Active Directory 中，「受控服務身分識別」會提供自動受控身分給 Azure 服務。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 Azure Resource Manager 部署範本，在 Azure VMSS 上執行以下受控服務識別作業：
- 在 Azure VMSS 上啟用和停用系統指派身分識別
- 在 Azure VMSS 上新增和移除使用者指派的身分識別

## <a name="prerequisites"></a>先決條件

- 如果您不熟悉受控服務識別，請參閱[概觀](overview.md)一節。 **請務必檢閱[系統指派和使用者指派身分識別之間的差異](overview.md#how-does-it-work)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。

## <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

如同 Azure 入口網站和指令碼，[Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) 範本可讓您部署由 Azure 資源群組所定義的最新或已修改資源。 範本編輯和部署有幾個選項可用 (在本機和入口網站)，包括：

   - 使用 [Azure Marketplace 所提供的自訂範本](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)，可讓您從頭開始建立範本，或根據現有的常用範本或[快速入門範本](https://azure.microsoft.com/documentation/templates/)來建立範本。
   - 衍生自現有的資源群組，方法是從[原始部署](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history)，或從[部署的目前狀態](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)匯出範本。
   - 使用本機 [JSON 編輯器 (例如 VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)，然後使用 PowerShell 或 CLI 上傳和部署。
   - 使用 Visual Studio 的 [Azure 資源群組專案](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)來建立和部署範本。  

不論您選擇的選項，在初始部署和重新部署期間，範本的語法都相同。 在新的或現有的虛擬機器上是以相同方式啟用 MSI。 此外，根據預設，Azure Resource Manager 會對部署採取[累加式更新](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)。

## <a name="system-assigned-identity"></a>系統指派的身分識別

在本節中，您將使用 Azure Resource Manager 範本來啟用和停用系統指派的身分識別。

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>在建立 Azure VMSS 時或在現有的 Azure VMSS 上，啟用系統指派的身分識別

1. 將範本載入編輯器之後，找出＜`resources`一節中您感興趣的 `Microsoft.Compute/virtualMachineScaleSets` 資源。 根據您所使用的編輯器，以及您是編輯新的或現有部署的範本而定，您的畫面與下列螢幕擷取畫面可能看起來稍有不同。
   
   ![範本的螢幕擷取畫面 - 尋找虛擬機器](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. 若要啟用系統指派的身分識別，請在與 `"type": "Microsoft.Compute/virtualMachineScaleSets"` 屬性相同的層級新增 `"identity"` 屬性。 使用下列語法：

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (選擇性) 將虛擬機器擴展集的 MSI 擴充功能新增為 `extensionsProfile` 元素。 此步驟是選擇性的，因為您也可以使用 Azure Instance Metadata Service (IMDS) 身分識別以擷取權杖。  使用下列語法：

   >[!NOTE] 
   > 下列範例假設正在部署 Windows 虛擬機器擴展集擴充功能 (`ManagedIdentityExtensionForWindows`)。 您也可以改用 `ManagedIdentityExtensionForLinux`來為 Linux 設定 `"name"` 和 `"type"` 元素。
   >

   ```JSON
   "extensionProfile": {
        "extensions": [
            {
                "name": "MSIWindowsExtension",
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

4. 完成之後，您的範本看起來應該如下所示：

   ![更新之後範本的螢幕擷取畫面](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>從 Azure 虛擬機器擴展集移除系統指派身分識別

> [!NOTE]
> 目前不支援從虛擬機器停用受控服務識別。 但與此同時，您可以在使用系統指派和使用者指派身分識別之間做切換。

如果您有不再需要系統指派身分識別，但是仍然需要使用者指派身分識別的虛擬機器擴展集：

- 將範本載入編輯器，並將身分識別類型變更為 `'UserAssigned'`

## <a name="user-assigned-identity"></a>使用者指派的身分識別

在本節中，您會使用 Azure Resource Manager 範本，將使用者指派的身分識別指派至 Azure VMSS。

> [!Note]
> 若要使用 Azure Resource Manager 範本建立使用者指派的身分識別，請參閱[建立使用者指派的身分識別](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity)。

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>將使用者指派的身分識別指派給 Azure VMSS

1. 在 `resources` 元素之下，新增下列項目，以將使用者指派的身分識別指派至您的 VMSS。  請務必將 `<USERASSIGNEDIDENTITY>` 取代為您建立的使用者指派身分識別名稱。

    ```json
    {
        "name": "[variables('vmssName')]",
        "apiVersion": "2017-03-30",
        "location": "[parameters(Location')]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/<USERASSIGNEDIDENTITY>)']"
            ]
        }

    }
    ```
2. (選擇性) 在 `extensionProfile` 元素下新增下列項目，以將受控身分識別擴充指派至您的 VMSS。 此步驟是選擇性的，因為您也可以使用 Azure Instance Metadata Service (IMDS) 識別端點以擷取權杖。 使用下列語法：
   
    ```JSON
       "extensionProfile": {
            "extensions": [
                {
                    "name": "MSIWindowsExtension",
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
3.  完成之後，您的範本看起來應該如下所示：
   
      ![使用者指派之身分識別的螢幕擷取畫面](./media/qs-configure-template-windows-vmss/qs-configure-template-windows-final.PNG)

## <a name="next-steps"></a>後續步驟

- 如需 MSI 更廣泛的觀點，請參閱[受控服務識別概觀](overview.md)。

