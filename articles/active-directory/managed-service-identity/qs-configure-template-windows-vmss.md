---
title: "使用範本在 Azure 虛擬機器擴展集上設定 MSI"
description: "使用 Azure Resource Manager 範本在 Azure VMSS 上設定「受控服務身分識別 (MSI)」的逐步指示。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 7f65c2ce53e30aa8682a9ee798af9001b4f210dc
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>使用範本設定虛擬機器受控服務識別 (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

受控服務識別 (MSI) 在 Azure Active Directory (Azure AD) 中為 Azure 服務提供自動管理的身分識別。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 Azure Resource Manager 部署範本啟用和移除 Azure 虛擬機器擴展集的 MSI。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set-or-an-existing-azure-virtual-machine-scale-set"></a>在 Azure 虛擬機器擴展集建立期間或在現有的 Azure 虛擬機器擴展集上啟用 MSI

如同 Azure 入口網站和指令碼，Azure Resource Manager 範本可讓您部署由 Azure 資源群組所定義之新的或已修改的資源。 範本編輯和部署有幾個選項可用 (在本機和入口網站)，包括：

   - 使用 [Azure Marketplace 所提供的自訂範本](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)，可讓您從頭開始建立範本，或根據現有的常用範本或[快速入門範本](https://azure.microsoft.com/documentation/templates/)來建立範本。
   - 衍生自現有的資源群組，方法是從[原始部署](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history)，或從[部署的目前狀態](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)匯出範本。
   - 使用本機 [JSON 編輯器 (例如 VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)，然後使用 PowerShell 或 CLI 上傳和部署。
   - 使用 Visual Studio 的 [Azure 資源群組專案](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)來建立和部署範本。  

不論您選擇的選項，在初始部署和重新部署期間，範本的語法都相同。 在新的或現有的 Azure 虛擬機器擴展集上是以相同方式啟用 MSI。 此外，根據預設，Azure Resource Manager 會對部署採取[累加式更新](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)：

1. 無論您是在本機登入 Azure 或透過 Azure 入口網站登入，都請使用與包含虛擬機器擴展集的 Azure 訂用帳戶相關聯的帳戶。

2. 將範本載入編輯器中之後，找出＜`resources`一節中您感興趣的 `Microsoft.Compute/virtualMachineScaleSets` 資源。 根據您所使用的編輯器，以及您是編輯新的或現有部署的範本而定，您的畫面與下列螢幕擷取畫面可能看起來稍有不同。
   
   ![範本的螢幕擷取畫面 - 尋找虛擬機器](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

3. 在與 `"type": "Microsoft.Compute/virtualMachineScaleSets"` 屬性相同的層級上新增 `"identity"` 屬性。 使用下列語法：

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. 然後將虛擬機器擴展集的 MSI 擴充功能新增為 `extensionsProfile` 元素。 使用下列語法：

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

5. 完成之後，您的範本看起來應該如下所示：

   ![更新之後範本的螢幕擷取畫面](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>從 Azure 虛擬機器擴展集中移除 MSI

如果您的虛擬機器擴展集不再需要 MSI：

1. 無論您是在本機登入 Azure 或透過 Azure 入口網站登入，都請使用與包含虛擬機器擴展集的 Azure 訂用帳戶相關聯的帳戶。

2. 移除在前一節中所新增的兩個元素：虛擬機器擴展集的 `"identity"` 和 `"extensionsProfile"` 屬性。

## <a name="next-steps"></a>後續步驟

- 如需 MSI 更廣泛的觀點，請參閱[受控服務識別概觀](overview.md)。

