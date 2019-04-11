---
title: 使用藍圖資源鎖定保護新資源
description: 了解如何使用 Azure 藍圖資源鎖定的「唯讀」和「請勿刪除」來保護新部署的資源。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d315fb5fe3ce7844946e6a9405a9a5f6a0be8b9d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272269"
---
# <a name="protect-new-resources-with-azure-blueprints-resource-locks"></a>使用 Azure 藍圖資源鎖定保護新資源

Azure 藍圖[資源鎖定](../concepts/resource-locking.md)能夠防止新部署的資源遭人竄改，即使是具有「擁有者」角色的帳戶也無法修改資源。 您可以從藍圖定義中，將這項保護措施新增至由 Resource Manager 範本成品建立的資源。

本文涵蓋下列步驟：

> [!div class="checklist"]
> - 建立新的藍圖定義
> - 將藍圖定義標記為**已發佈**
> - 將藍圖定義指派給現有的訂用帳戶
> - 檢查新資源群組
> - 取消指派藍圖來移除鎖定

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="create-new-blueprint-definition"></a>建立新的藍圖定義

首先，建立新的藍圖定義。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 在左側的 [快速入門] 頁面上，選取 [建立藍圖] **下方的 [建立]** 按鈕。

1. 在頁面頂端尋找 [空白藍圖] 藍圖範例，然後選取 [從空白藍圖開始]。

1. 輸入藍圖範例的 [基本資料]：

   - **藍圖名稱**：為您的藍本範例副本提供名稱。 在此教學課程中，我們使用的名稱是 _locked-storageaccount_。
   - **藍圖描述**：描述藍圖定義。 請使用「在已部署的資源上測試藍圖資源鎖定」。
   - **定義位置**：使用省略符號，然後選取要儲存您藍圖定義的管理群組或訂用帳戶。

1. 在頁面頂端選取 [成品]索引標籤，或在頁面底部選取 **[下一步：成品]**。

1. 新增訂用帳戶的資源群組：選取 [訂用帳戶] 底下的 [+ 新增成品...] 資料列。
   [成品類型] 請選取 [資源群組]。 將 [成品顯示名稱] 設定為 **RGtoLock**。
   將 [資源群組名稱] 和 [位置] 欄位留白，但是請務必勾選每個屬性的核取方塊，將其設定為**動態參數**。 按一下 [新增] 將此成品新增至藍圖。

1. 在資源群組下新增範本：選取 [+ 新增成品...] 資料列，位於 **RGtoLock** 項目底下。 [成品類型] 請選擇「Azure Resource Manager 範本 」，並將 [成品顯示名稱] 設為「StorageAccount」，將 [描述] 留白。 在編輯器方塊的 [範本] 索引標籤中，貼上以下的 Resource Manager 範本。 貼上範本之後，請選取 [新增]，以將此成品新增至藍圖。

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "storageAccountType": {
               "type": "string",
               "defaultValue": "Standard_LRS",
               "allowedValues": [
                   "Standard_LRS",
                   "Standard_GRS",
                   "Standard_ZRS",
                   "Premium_LRS"
               ],
               "metadata": {
                   "description": "Storage Account type"
               }
           }
       },
       "variables": {
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
           "apiVersion": "2018-07-01",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "StorageV2",
           "properties": {}
       }],
       "outputs": {
           "storageAccountName": {
               "type": "string",
               "value": "[variables('storageAccountName')]"
           }
       }
   }
   ```

1. 選取頁面底部的 [儲存草稿]。

此步驟會在選取的管理群組或訂用帳戶中建立藍圖定義。

出現**成功儲存藍圖定義**的入口網站通知後，請移至下一個步驟。

## <a name="publish-the-blueprint-definition"></a>發佈藍圖定義

藍圖定義現在已建立您的環境中。 這會以**草稿**模式建立，而且必須先**發佈**，才能進行指派和部署。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 選取左側的 [藍圖定義] 頁面。 使用篩選來尋找 locked-storageaccount 藍圖定義，然後將其選取。

1. 選取頁面頂端的 [發佈藍圖]。 在右側的新窗格中，提供 _1.0_ 作為 [版本]。 如果您稍後會進行修改，此屬性十分實用。 請提供**變更附註**，例如「為鎖定藍圖部署資源發佈的第一版」。 然後選取頁面底端的 [發佈]。

此步驟可讓您將藍圖指派給訂用帳戶。 發佈之後，仍可進行變更。 進行其他變更時，需要以新的 [版本] 值來發佈，如此才能追蹤相同藍圖定義的各版本差異。

出現**成功發佈藍圖定義**的入口網站通知後，請移至下一個步驟。

## <a name="assign-the-blueprint-definition"></a>指派藍圖定義

成功**發佈**藍圖定義後，可以將藍圖定義指派給其所在管理群組中的訂用帳戶。 此步驟用於提供參數，以建立每個專屬的藍圖定義部署。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 選取左側的 [藍圖定義] 頁面。 使用篩選來尋找 locked-storageaccount 藍圖定義，然後將其選取。

1. 選取藍圖定義頁面頂端的 [指派藍圖]。

1. 提供用於指派藍圖的參數值：

   - 基本概念

     - **訂用帳戶**：在儲存您藍圖定義的管理群組中，選取一個或多個訂用帳戶。 如果您選取多個訂用帳戶，輸入的參數就會用來為每個訂用帳戶建立指派。
     - **指派名稱**：名稱會根據藍圖定義名稱預先填入。 我們想要用此指派來表示鎖定新資源群組，因此將指派名稱變更為 _assignment-locked-storageaccount-TestingBPLocks_。
     - **位置**：選取要在其中建立受控識別的區域。 Azure 藍圖會使用此受控識別來部署指派的藍圖之中的所有成品。 若要深入了解，請參閱[適用於 Azure 資源的受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)。
       在本教學課程中，我們選取 [美國東部 2]。
     - **藍圖定義版本**：選取藍圖定義的**已發佈**版本：_1.0_。

   - 鎖定指派

     選取 [唯讀] 藍圖鎖定模式。 如需詳細資訊，請參閱[藍圖資源鎖定](../concepts/resource-locking.md)。

   - 受控識別

     保留預設的 [系統指派] 選項。 如需詳細資訊，請參閱[受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)。

   - 構件參數

     本節中定義的參數會套用至其定義所屬的成品。 這些參數是[動態參數](../concepts/parameters.md#dynamic-parameters)，因為定義於藍圖指派期間。 針對 [值] 資料行中定義的內容，為每個成品設定參數值。

     |成品名稱|成品類型|參數名稱|值|說明|
     |-|-|-|-|-|
     |RGtoLock 資源群組|資源群組|Name|TestingBPLocks|定義要套用藍圖鎖定的新資源群組名稱。|
     |RGtoLock 資源群組|資源群組|位置|美國西部 2|定義要套用藍圖鎖定的新資源群組位置。|
     |StorageAccount|Resource Manager 範本|storageAccountType (StorageAccount)|Standard_GRS|選取儲存體 SKU。 預設值是 _Standard_LRS_。|

1. 輸入所有參數後，選取頁面底部的 [指派]。

此步驟會部署已定義的資源，並設定所選的 [鎖定指派]。 套用藍圖鎖定可能需要 30 分鐘的時間。

出現**成功指派藍圖定義**的入口網站通知後，請移至下一個步驟。

## <a name="inspect-resources-deployed-by-the-assignment"></a>檢查指派所部署的資源

指派已建立 TestingBPLocks 資源群組，並且由 Resource Manager 範本成品部署儲存體帳戶。 指派詳細資料頁面會顯示新的資源群組和選取的鎖定狀態。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 選取左側的 [指派的藍圖] 頁面。 使用篩選來尋找 assignment-locked-storageaccount-TestingBPLocks 藍圖指派，然後將其選取。

   在這個頁面中，我們可以看到指派已成功建立，以及資源已以新的藍圖鎖定狀態來部署。 如果更新指派，則 [指派作業] 下拉式清單會顯示每個定義版本的部署詳細資料。 按一下資源群組即可直接開啟屬性頁面。

1. 選取 [TestingBPLocks] 資源群組。

1. 選取左側的 [存取控制 (IAM)] 頁面，然後選取 [角色指派] 索引標籤。

   我們會在此看到 assignment-locked-storageaccount-TestingBPLocks 藍圖指派具有「擁有者」角色，因為這是用來部署和鎖定資源群組的角色。

1. 選取 [拒絕指派] 索引標籤。

   藍圖指派已在部署的資源群組上建立[拒絕指派](../../../role-based-access-control/deny-assignments.md)，進而強制執行「唯讀」鎖定模式。 針對在 [角色指派] 索引標籤上具有適當權限的人員，拒絕指派可阻止他們採取特定動作。 拒絕指派會影響「所有主體」。

   如需從拒絕指派中排除主體的相關資訊，請參閱[藍圖資源鎖定](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment)。

1. 選取 [拒絕指派]，然後選取左側的 [拒絕的權限] 頁面。

   拒絕指派會阻止所有使用 **\*** 的作業和**動作**設定，但允許透過 **NotActions** 排除 **\*/read** 來允許讀取存取。

1. 從 Azure 入口網站的階層連結中，選取 [TestingBPLocks - 存取控制 (IAM)]。 然後選取左側的 [概觀] 頁面，然後選取 [刪除資源群組] 按鈕。 輸入 TestingBPLocks 名稱以確認刪除，然後選取窗格底部的 [刪除]。

   **無法刪除 TestingBPLocks 資源群組**的入口網站通知會隨即出現。 該錯誤表示，雖然您的帳戶有權刪除資源群組，但藍圖指派拒絕了您的存取。 別忘了，我們已在藍圖指派期間選取「唯讀」藍圖鎖定模式。 藍圖鎖定會阻止具有權限的帳戶 (甚至是「擁有者」權限) 刪除資源。 如需詳細資訊，請參閱[藍圖資源鎖定](../concepts/resource-locking.md)。

這些步驟說明部署資源現在已受到藍圖鎖定的保護，可防止意外的刪除動作，甚至是具有權限的帳戶也無法刪除資源。

## <a name="unassign-the-blueprint"></a>取消指派藍圖

最後一個步驟是要移除藍圖定義的指派。 移除指派並不會移除觸及的成品。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 選取左側的 [指派的藍圖] 頁面。 使用篩選來尋找 assignment-locked-storageaccount-TestingBPLocks 藍圖指派，然後將其選取。

1. 選取頁面頂端的 [取消指派藍圖] 按鈕。 閱讀確認對話方塊中的警告，然後選取 [確定]。

   移除藍圖指派後，藍圖鎖定也會一併移除。 具有權限的帳戶可再次刪除已建立的資源。

1. 從 Azure 功能表選取 [資源群組]，然後選取 [TestingBPLocks]。

1. 選取左側的 [存取控制 (IAM)] 頁面，然後選取 [角色指派] 索引標籤。

資源群組的安全性會顯示藍圖指派不再具有「擁有者」存取權。

出現**成功移除藍圖指派**的入口網站通知後，請移至下一個步驟。

## <a name="clean-up-resources"></a>清除資源

完成本教學課程後，請刪除下列資源：

- TestingBPLocks 資源群組
- locked-storageaccount 藍圖定義

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。