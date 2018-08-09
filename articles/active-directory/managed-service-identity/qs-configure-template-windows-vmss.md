---
title: 使用範本在 Azure 虛擬機器擴展集上設定受控服務識別
description: 使用 Azure Resource Manager 範本在 Azure VMSS 上設定受控服務識別的逐步指示。
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
ms.openlocfilehash: bee75bcefb370382825c6867ea504e14102aa107
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628278"
---
# <a name="configure-managed-service-identity-on-virtual-machine-scale-using-a-template"></a>使用範本在虛擬機器擴展集上設定受控服務識別

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

在 Azure Active Directory 中，「受控服務身分識別」會提供自動受控身分給 Azure 服務。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 Azure Resource Manager 部署範本，在 Azure 虛擬機器擴展集上執行以下受控服務識別作業：
- 在 Azure 虛擬機器擴展集上啟用和停用系統指派的識別
- 在 Azure 虛擬機器擴展集上新增和移除使用者指派的識別

## <a name="prerequisites"></a>必要條件

- 如果您不熟悉受控服務識別，請參閱[概觀](overview.md)一節。 **請務必檢閱[系統指派和使用者指派身分識別之間的差異](overview.md#how-does-it-work)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 若要執行本文中的管理作業，您的帳戶需要下列角色指派：
    - [虛擬機器參與者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)，可建立虛擬機器擴展集，並從虛擬機器擴展集啟用和移除系統和/或使用者指派的受控識別。
    - [受控識別參與者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色，可建立使用者指派的識別。
    - [受控識別操作員](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色，可為虛擬機器擴展集指派和移除使用者指派的識別。

## <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

如同 Azure 入口網站和指令碼，[Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) 範本可讓您部署由 Azure 資源群組所定義的最新或已修改資源。 範本編輯和部署有幾個選項可用 (在本機和入口網站)，包括：

   - 使用 [Azure Marketplace 所提供的自訂範本](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)，可讓您從頭開始建立範本，或根據現有的常用範本或[快速入門範本](https://azure.microsoft.com/documentation/templates/)來建立範本。
   - 衍生自現有的資源群組，方法是從[原始部署](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history)，或從[部署的目前狀態](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)匯出範本。
   - 使用本機 [JSON 編輯器 (例如 VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)，然後使用 PowerShell 或 CLI 上傳和部署。
   - 使用 Visual Studio 的 [Azure 資源群組專案](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)來建立和部署範本。  

不論您選擇的選項，在初始部署和重新部署期間，範本的語法都相同。 在新的或現有 VM 上啟用受控服務識別，是以相同的方式來完成。 此外，根據預設，Azure Resource Manager 會對部署採取[累加式更新](../../azure-resource-manager/deployment-modes.md)。

## <a name="system-assigned-identity"></a>系統指派的身分識別

在本節中，您將使用 Azure Resource Manager 範本來啟用和停用系統指派的身分識別。

### <a name="enable-system-assigned-identity-during-creation-the-creation-of-or-an-existing-azure-virtual-machine-scale-set"></a>在 Azure 虛擬機器擴展集建立期間，或是針對現有的 Azure 虛擬機器擴展集啟用系統指派的識別

1. 將範本載入編輯器之後，找出＜`resources`一節中您感興趣的 `Microsoft.Compute/virtualMachineScaleSets` 資源。 根據您所使用的編輯器，以及您是編輯新的或現有部署的範本而定，您的畫面與下列螢幕擷取畫面可能看起來稍有不同。
   
   ![範本的螢幕擷取畫面 - 尋找虛擬機器](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. 若要啟用系統指派的身分識別，請在與 `"type": "Microsoft.Compute/virtualMachineScaleSets"` 屬性相同的層級新增 `"identity"` 屬性。 使用下列語法：

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (選擇性) 將虛擬機器擴展集的受控服務識別擴充新增為 `extensionsProfile` 元素。 此步驟是選擇性的，因為您也可以使用 Azure Instance Metadata Service (IMDS) 身分識別以擷取權杖。  使用下列語法：

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

   ![更新之後範本的螢幕擷取畫面](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>從 Azure 虛擬機器擴展集移除系統指派身分識別

如果您有不再需要受控服務識別的虛擬機器擴展集：

1. 無論您是在本機登入 Azure 或透過 Azure 入口網站登入，都請使用與包含虛擬機器擴展集的 Azure 訂用帳戶相關聯的帳戶。

2. 在[編輯器](#azure-resource-manager-templates)中載入範本，然後在 `resources` 區段找出想要的 `Microsoft.Compute/virtualMachineScaleSets` 資源。 若您的虛擬機器擴展集只有系統指派的識別，您可以將識別類型變更為 `None` 予以停用。  若您的虛擬機器擴展集同時具有系統與使用者指派的識別，請從識別類型中移除 `SystemAssigned`，並保留 `UserAssigned`，以及使用者指派識別中的 `identityIds` 陣列。  下列範例示範如何從沒有使用者指派識別的虛擬機器擴展集中移除系統指派的識別：
   
   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-identity"></a>使用者指派的身分識別

在本節中，您會使用 Azure Resource Manager 範本，將使用者指派的身分識別指派至 Azure VMSS。

> [!Note]
> 若要使用 Azure Resource Manager 範本建立使用者指派的身分識別，請參閱[建立使用者指派的身分識別](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity)。

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>將使用者指派的身分識別指派給 Azure VMSS

1. 在 `resources` 元素之下，新增下列項目，以將使用者指派的身分識別指派至您的 VMSS。  請務必將 `<USERASSIGNEDIDENTITY>` 取代為您建立的使用者指派身分識別名稱。

   > [!Important]
   > 下列範例所示的 `<USERASSIGNEDIDENTITYNAME>` 值必須儲存於變數中。  此外，目前支援將使用者指派的身分識別指派給 Resource Manager 範本中的虛擬機器，針對這類實作，API 版本必須符合下列範例中的版本。 

    ```json
    {
        "name": "[variables('vmssName')]",
        "apiVersion": "2017-03-30",
        "location": "[parameters(Location')]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
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

### <a name="remove-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>從 Azure 虛擬機器擴展集移除使用者指派的識別

如果您有不再需要受控服務識別的虛擬機器擴展集：

1. 無論您是在本機登入 Azure 或透過 Azure 入口網站登入，都請使用與包含虛擬機器擴展集的 Azure 訂用帳戶相關聯的帳戶。

2. 在[編輯器](#azure-resource-manager-templates)中載入範本，然後在 `resources` 區段找出想要的 `Microsoft.Compute/virtualMachineScaleSets` 資源。 若您的虛擬機器擴展集只有使用者指派的識別，您可以將識別類型變更為 `None` 予以停用。  如果您的虛擬機器擴展集同時具有系統和使用者指派的識別，而且想要保留系統指派的識別，請從識別類型中移除 `UserAssigned`，以及使用者指派識別中的 `identityIds` 陣列。
    
   若要從虛擬機器擴展集移除單一使用者指派的識別，請從 `identityIds` 陣列中移除它。
   
   下列範例示範如何從沒有系統指派識別的虛擬機器擴展集中移除所有使用者指派的識別：
   
   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="next-steps"></a>後續步驟

- 如需有關受控服務識別的更廣泛觀點，請參閱[受控服務識別概觀](overview.md)。

