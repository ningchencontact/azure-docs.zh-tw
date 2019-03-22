---
title: 使用受控識別進行驗證 - Azure Logic Apps | Microsoft Docs
description: 若要進行驗證而不登入，您可以建立受控識別 (先前稱為「受控服務識別」或 MSI)，讓您的邏輯應用程式可以存取其他 Azure Active Directory (Azure AD) 租用戶中的資源，而不使用認證或祕密
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 046aed64d3551d5c0b6ddae44b925452c01c297a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337574"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>使用 Azure Logic Apps 中的受控識別驗證及存取資源

若要存取其他 Azure Active Directory (Azure AD) 租用戶中的資源並驗證您的身分識別而不登入，您的邏輯應用程式可以使用[受控識別](../active-directory/managed-identities-azure-resources/overview.md) (先前稱為「受控服務識別」或 MSI)，而不使用認證或祕密。 Azure 會為您管理此身分識別，並協助保護您的認證，因為您不需要提供或輪替使用祕密。 本文示範如何為您的邏輯應用程式設定並使用系統指派的受控識別。 如需受控識別的詳細資訊，請參閱[什麼是適用於 Azure 資源的受控識別？](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> 您目前最多可擁有 10 個邏輯應用程式工作流程，而且每個 Azure 訂用帳戶中具有系統指派的受控識別。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶，或如果沒有訂用帳戶，您可以<a href="https://azure.microsoft.com/free/" target="_blank">免費註冊 Azure 帳戶</a>。

* 您要在其中使用系統指派之受控識別的邏輯應用程式。 如果您沒有邏輯應用程式，請參閱[建立第一個邏輯應用程式工作流程](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>啟用受控識別

針對系統指派的受控識別，您不需要手動建立該身分識別。 若要為您的邏輯應用程式設定系統指派的受控識別，您可以使用下列方式： 

* [Azure 入口網站](#azure-portal) 
* [Azure 資源管理員範本](#template) 
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>Azure 入口網站

若要透過 Azure 入口網站為您的邏輯應用程式啟用系統指派的受控識別，請在邏輯應用程式的身分識別設定中開啟 [系統指派] 設定。

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定] 下，選取 [身分識別]。 

1. 在 [系統指派] > [狀態] 下，選擇 [開啟]。 然後，選擇 [儲存] > [是]。

   ![開啟受控識別設定](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   您的邏輯應用程式現在於 Azure Active Directory 中已註冊系統指派的受控識別：

   ![適用於物件識別碼的 GUID](./media/create-managed-service-identity/object-id.png)

   | 屬性 | 值 | 描述 | 
   |----------|-------|-------------| 
   | **物件識別碼** | <*identity-resource-ID*> | 代表 Azure AD 租用戶中邏輯應用程式之系統指派受控識別的全域唯一識別碼 (GUID) | 
   ||| 

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

當您想要自動建立並部署邏輯應用程式之類的 Azure 資源，您可以使用 [Azure Resource Manager 範本](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)。 若要透過範本為您的邏輯應用程式建立系統指派的受控識別，請在部署範本中將 `"identity"` 元素與 `"type"` 屬性新增到您的邏輯應用程式工作流程定義： 

```json
"identity": {
   "type": "SystemAssigned"
}
```

例如︰

```json
{
   "apiVersion": "2016-06-01", 
   "type": "Microsoft.logic/workflows", 
   "name": "[variables('logicappName')]", 
   "location": "[resourceGroup().location]", 
   "identity": { 
      "type": "SystemAssigned" 
   }, 
   "properties": { 
      "definition": { 
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#", 
         "actions": {}, 
         "parameters": {}, 
         "triggers": {}, 
         "contentVersion": "1.0.0.0", 
         "outputs": {} 
   }, 
   "parameters": {}, 
   "dependsOn": [] 
}
```

當 Azure 建立您的邏輯應用程式時，該邏輯應用程式的工作流程定義會包含這些額外的屬性：

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| 屬性 | 值 | 描述 | 
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | 代表 Azure AD 租用戶中的邏輯應用程式，且有時會以「物件識別碼」或 `objectID` 之形式呈現的全域唯一識別碼 (GUID) | 
| tenantId | <*Azure-AD-tenant-ID*> | 代表邏輯應用程式現在已是其成員之 Azure AD 租用戶的全域唯一識別碼 (GUID)。 在 Azure AD 租用戶中，服務主體會有與邏輯應用程式執行個體相同的名稱。 | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>使用受控識別存取資源

當您為邏輯應用程式建立系統指派的受控識別之後，您可以[將該身分識別存取權提供給其他 Azure 資源](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)。 您接著可將該身分識別用於驗證，就像任何其他[服務主體](../active-directory/develop/app-objects-and-service-principals.md)一樣。 

> [!NOTE]
> 系統指派的受控識別與您要指派存取權的資源必須有相同的 Azure 訂用帳戶。

### <a name="assign-access-to-managed-identity"></a>將存取權指派給受控識別

若要針對邏輯應用程式的系統指派受控識別提供另一個 Azure 資源的存取權，請依照下列步驟執行：

1. 在 Azure 入口網站中，移至您想要針對受控識別指派存取權的 Azure 資源。 

1. 從資源的功能表中，選取 [存取控制 (IAM)]，然後選擇 [新增角色指派]。 

   ![新增角色指派](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. 在 [新增角色指派] 下方，選取要用於身分識別的**角色**。 

1. 在 [存取權指派對象為] 屬性中，選取 [Azure AD 使用者、群組或服務主體] (如果尚未選取)。

1. 在 [選取] 方塊中，從邏輯應用程式名稱中的第一個字元開始，輸入邏輯應用程式的名稱。 當您的邏輯應用程式出現時，選取該邏輯應用程式。

   ![使用受控識別選取邏輯應用程式](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. 完成之後，請選擇 [儲存]。

### <a name="authenticate-with-managed-identity-in-logic-app"></a>在邏輯應用程式中使用受控識別進行驗證

當您使用系統指派的受控識別和指派給您想要用於該身分識別之資源的存取權來設定邏輯應用程式之後，您現在可以使用該身分識別進行驗證。 例如，您可以使用 HTTP 動作，讓您的邏輯應用程式可以傳送 HTTP 要求或呼叫該資源。 

1. 在您的邏輯應用程式中，新增 **HTTP** 動作。 

1. 提供該動作的必要詳細資料，例如要求**方法**與您要呼叫之資源的 **URI** 位置。

   例如，假設您是搭配[這些支援 Azure AD 的 Azure 服務之一](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)來使用 Azure Active Directory (Azure AD) 驗證。 
   在 [URI] 方塊中，輸入該 Azure 服務的端點 URL。 
   因此，假設您是使用 Azure Resource Manager，請在 [URI] 屬性中輸入此值：

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version-2016-06-01`

1. 在 HTTP 動作中，選擇 [顯示進階選項]。 

1. 從 [驗證] 清單中，選取 [受控識別]。 在您選取此驗證之後，[Audience] 屬性會顯示，並搭配預設資源識別碼值：

   ![選取 [受控識別]](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > 在 [Audience] 屬性中，資源識別碼值必須完全符合 Azure AD 所預期的值，包括任何必要的結尾斜線。 
   > 您可以在這個[描述支援 Azure AD 之 Azure 服務的表格中](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)找到這些資源識別碼值。 
   > 例如，如果您是使用 Azure Resource Manager 資源識別碼，請確定 URI 具有結尾斜線。

1. 繼續按照您想要的方式建置邏輯應用程式。

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>移除受控識別

若要在您的邏輯應用程式上停用系統指派的受控識別，您可以依照透過 Azure 入口網站、Azure Resource Manager 部署範本或 Azure PowerShell 設定身分識別的類似步驟來停用它。 

當您刪除邏輯應用程式時，Azure 會自動將您邏輯應用程式的系統指派身分識別從 Azure AD 移除。

### <a name="azure-portal"></a>Azure 入口網站

若要透過 Azure 入口網站為您的邏輯應用程式移除系統指派的受控識別，請在邏輯應用程式的身分識別設定中關閉 [系統指派] 設定。

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定] 下，選取 [身分識別]。 

1. 在 [系統指派] > [狀態] 下，選擇 [關閉]。 然後，選擇 [儲存] > [是]。

   ![關閉受控識別設定](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>部署範本

如果您已使用 Azure Resource Manager 部署範本來建立邏輯應用程式的系統指派受控識別，請將 `"identity"` 元素的 `"type"` 屬性設定為 `"None"`。 這個動作也會將主體識別碼從 Azure AD 刪除。 

```json
"identity": {
   "type": "None"
}
```

## <a name="get-support"></a>取得支援

* 如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交或票選功能構想，請造訪 [Logic Apps 使用者意見反應網站](https://aka.ms/logicapps-wish)。