---
title: 使用受控識別進行驗證-Azure Logic Apps
description: 使用受控識別來存取其他 Azure Active Directory 租使用者中的資源，而不需以認證或秘密進行登入
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: fdc5340c9affa7137815577af842aa8b43a552a8
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72799560"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>使用 Azure Logic Apps 中的受控識別來驗證對 Azure 資源的存取

若要存取其他 Azure Active Directory （Azure AD）租使用者中的資源，並在不登入的情況下驗證您的身分識別，您的邏輯應用程式可以使用系統指派的[受控識別](../active-directory/managed-identities-azure-resources/overview.md)（先前稱為受控服務識別或 MSI），而不是認證或秘密。 Azure 會為您管理此身分識別，並協助保護您的認證，因為您不需要提供或輪替使用祕密。 本文說明如何在您的邏輯應用程式中設定及使用系統指派的受控識別。

如需詳細資訊，請參閱下列主題：

* [支援受控識別的觸發程式和動作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [支援使用受控識別進行 Azure AD 驗證的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)
* [輸出呼叫上支援的驗證類型](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [邏輯應用程式的受控識別限制](../logic-apps/logic-apps-limits-and-config.md#managed-identity)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶，或如果沒有訂用帳戶，您可以[免費註冊 Azure 帳戶](https://azure.microsoft.com/free/)。 受控識別和您想要存取的目標 Azure 資源都需要使用相同的 Azure 訂用帳戶。

* [Azure AD 系統管理員許可權](../active-directory/users-groups-roles/directory-assign-admin-roles.md)，可在與目標資源相同的 Azure AD 租使用者中，將角色指派給受控識別。 若要為受控識別授與 Azure 資源的存取權，您必須在目標資源上新增該身分識別的角色。

* 您想要存取的目標 Azure 資源

* 使用支援受控識別之[觸發程式和動作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)的邏輯應用程式

<a name="system-assigned"></a>

## <a name="enable-system-assigned-identity"></a>啟用系統指派的身分識別

不同于使用者指派的身分識別，您不需要手動建立系統指派的身分識別。 若要設定邏輯應用程式的系統指派身分識別，以下是您可以使用的選項：

* [Azure 入口網站](#azure-portal-system-logic-app)
* [Azure 資源管理員範本](#template-system-logic-app)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

<a name="azure-portal-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-portal"></a>在 Azure 入口網站中啟用系統指派的身分識別

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [**設定**] 下，選取 [身分**識別** > **系統指派**]。 在 [**狀態**] 底下 **，選取**[ > **儲存** > **是]** 。

   ![啟用系統指派的身分識別](./media/create-managed-service-identity/turn-on-system-assigned-identity.png)

   您的邏輯應用程式現在可以使用系統指派的身分識別，它會向 Azure Active Directory 註冊，並以物件識別碼表示。

   ![系統指派之身分識別的物件識別碼](./media/create-managed-service-identity/object-id.png)

   | 屬性 | Value | 描述 |
   |----------|-------|-------------|
   | **物件識別碼** | <*identity-resource-ID*> | 全域唯一識別碼（GUID），代表您的 Azure AD 租使用者中邏輯應用程式的系統指派身分識別 |
   ||||

1. 現在遵循[授與資源身分識別存取權的步驟](#access-other-resources)。

<a name="template-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>在 Azure Resource Manager 範本中啟用系統指派的身分識別

若要自動建立和部署 Azure 資源（例如邏輯應用程式），您可以使用[Azure Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)。 若要在範本中為您的邏輯應用程式啟用系統指派的受控識別，請將 `identity` 物件和 `type` 子屬性新增至範本中邏輯應用程式的資源定義，例如：

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

當 Azure 建立您的邏輯應用程式資源定義時，`identity` 物件會取得下列其他屬性：

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| 屬性（JSON） | Value | 描述 |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | 受控識別之服務主體物件的全域唯一識別碼（GUID），代表 Azure AD 租使用者中的邏輯應用程式。 此 GUID 有時候會顯示為「物件識別碼」或 `objectID`。 |
| `tenantId` | <*Azure-AD-tenant-ID*> | 全域唯一識別碼（GUID），代表邏輯應用程式現在是成員的 Azure AD 租使用者。 在 Azure AD 租用戶中，服務主體會有與邏輯應用程式執行個體相同的名稱。 |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>授與資源的身分識別存取權

設定邏輯應用程式的受控識別之後，您可以將該身分[識別存取權授與其他 Azure 資源](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)。 接著，您可以使用該身分識別進行驗證。

1. 在  [Azure 入口網站](https://portal.azure.com)中，移至您想要讓受控識別擁有存取權的 Azure 資源。

1. 從資源的功能表中，選取 [**存取控制（IAM）** ] > [**角色指派**]，您可以在其中檢查該資源目前的角色指派。 在工具列上，選取 [**新增**] > [**新增角色指派**]。

   ![選取 [新增] > [新增角色指派]](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > 如果 [**新增角色指派**] 選項已停用，您很可能沒有許可權。 如需可讓您管理資源角色之許可權的詳細資訊，請參閱[Azure Active Directory 中的系統管理員角色許可權](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

1. 在 [**新增角色指派**] 底下，選取一個**角色**，讓您的身分識別具有目標資源的必要存取權。

   在本主題的範例中，您的身分識別需要[可存取 Azure 儲存體容器中之 blob 的角色](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)：

   ![選取 [儲存體 Blob 資料參與者] 角色](./media/create-managed-service-identity/assign-role.png)

1. 在 [存取權指派對象為] 方塊中，選取 [Azure AD 使用者、群組或服務主體]。

   ![針對系統指派的身分識別選取存取權](./media/create-managed-service-identity/assign-access-system.png)

1. 在 [**選取**] 方塊中，尋找並選取您的邏輯應用程式。

   ![針對系統指派的身分識別選取邏輯應用程式](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. 完成時，選取 [儲存]。

   目標資源的 [角色指派] 清單現在會顯示選取的受控識別和角色。

   ![已將受控識別和角色新增至目標資源](./media/create-managed-service-identity/added-roles-for-identities.png)

1. 現在請遵循下列步驟，在支援受控識別的觸發程式或動作中，[使用身分識別來驗證存取權](#authenticate-access-with-identity)。

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>使用受控識別來驗證存取權

[為邏輯應用程式啟用受控識別](#azure-portal-system-logic-app)，並將該身分[識別存取權授與目標資源](#access-other-resources)之後，您就可以在[支援受控識別的觸發程式和動作](logic-apps-securing-a-logic-app.md#managed-identity-authentication)中使用該身分識別。

> [!IMPORTANT]
> 如果您有想要使用系統指派身分識別的 Azure 函式，請先[啟用 azure 函式的驗證功能](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)。

這些步驟示範如何透過 Azure 入口網站，使用受控識別搭配觸發程式或動作。 若要在觸發程式或動作的基礎 JSON 定義中指定受控識別，請參閱[受控識別驗證](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)。

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 如果您還沒有這麼做，請新增[支援受控](logic-apps-securing-a-logic-app.md#managed-identity-authentication)識別的觸發程式或動作。

   例如，假設您想要在 Azure 儲存體帳戶中的 Blob 上執行[快照集 Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)作業，而您先前已設定身分識別的存取權，但[Azure Blob 儲存體連接器](/connectors/azureblob/)目前不提供這項作業。 相反地，您可以使用[HTTP 動作](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)來執行作業或任何其他[Blob 服務 REST API 作業](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)。 針對驗證，HTTP 動作可以使用您為邏輯應用程式啟用的系統指派身分識別。 HTTP 動作也會使用這些屬性來指定您想要存取的資源：

   * [ **URI** ] 屬性會指定用來存取目標 Azure 資源的端點 URL。 此 URI 語法通常包含 Azure 資源或服務的[資源識別碼](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

   * Header**屬性會**指定您需要包含在要求中的任何標頭值，例如您想要在目標資源上執行之作業的 API 版本。

   * [**查詢**] 屬性會指定您需要包含在要求中的任何查詢參數，例如特定作業的參數或必要時的特定 API 版本。

   因此，若要執行[快照集 Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)作業，HTTP 動作會指定下列屬性：

   * **Method**：指定 `PUT` 作業。

   * **URI**：指定 Azure 全域（公用）環境中 Azure Blob 儲存體檔案的資源識別碼，並使用此語法：

     `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}`

   * **標頭**：指定 `x-ms-blob-type` 做為快照集 Blob 作業 `2019-02-02` 的 `BlockBlob` 和 `x-ms-version`。 如需詳細資訊，請參閱[要求標頭-](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) Azure 儲存體服務的快照集 Blob 和[版本控制](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services)。

   * **查詢**：指定 `comp` 做為查詢參數名稱，並 `snapshot` 做為參數值。

   以下是範例 HTTP 動作，其中顯示所有的屬性值：

   ![新增 HTTP 動作以存取 Azure 資源](./media/create-managed-service-identity/http-action-example.png)

   如需所有可用 Azure REST API 作業的詳細資訊，請參閱[Azure REST API 參考](https://docs.microsoft.com/rest/api/azure/)。

1. 從 [驗證] 清單中，選取 [受控識別]。 如果 [ [**驗證**] 屬性受到支援但已](logic-apps-securing-a-logic-app.md#add-authentication-outbound)隱藏，請開啟 [**加入新的參數**] 清單，然後選取 [**驗證**]。

   > [!NOTE]
   > 並非所有的觸發程式和動作都可讓您選取驗證類型。 如需詳細資訊，請參閱[將驗證新增至輸出呼叫](logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

   ![在 [驗證] 屬性中，選取 [受控識別]](./media/create-managed-service-identity/select-managed-identity.png)

1. 在您選取 [**受控識別**] 之後，某些觸發程式和動作的 [**物件**] 屬性就會出現。 如果 [**物件**] 屬性受到支援但隱藏，請開啟 [**加入新的參數**] 清單，然後選取 [**物件**]。

1. 請確定您將 [**物件**] 值設定為目標資源或服務的資源識別碼。 否則，根據預設，**物件**屬性會使用 `https://management.azure.com/` 資源識別碼，也就是 Azure Resource Manager 的資源識別碼。

   > [!IMPORTANT]
   > 請確定目標資源識別碼*完全符合*AZURE ACTIVE DIRECTORY （AD）所預期的值，包括任何必要的尾端斜線。 例如，所有 Azure Blob 儲存體帳戶的資源識別碼都需要尾端斜線。 不過，特定儲存體帳戶的資源識別碼不需要尾端斜線。 檢查[支援 Azure AD 之 Azure 服務的資源識別碼](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

   這個範例會將**物件**屬性設定為 `https://storage.azure.com/`，讓用於驗證的存取權杖對所有儲存體帳戶有效。 不過，您也可以指定特定儲存體帳戶的根服務 URL `https://fabrikamstorageaccount.blob.core.windows.net`。

   ![在「物件」屬性中指定目標資源識別碼](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   如需使用 Azure 儲存體的 Azure AD 授權存取的詳細資訊，請參閱下列主題：

   * [使用 Azure Active Directory 授權存取 Azure blob 和佇列](../storage/common/storage-auth-aad.md)
   * [使用 Azure Active Directory 授權 Azure 儲存體的存取權](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

<a name="remove-identity"></a>

## <a name="remove-system-assigned-identity"></a>移除系統指派的身分識別

若要停止為您的邏輯應用程式使用系統指派的身分識別，您有下列選項：

* [Azure 入口網站](#azure-portal-disable)
* [Azure 資源管理員範本](#template-disable)
* [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/remove-azroleassignment)
* [Azure CLI](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-delete)

如果您刪除邏輯應用程式，Azure 會自動從 Azure AD 移除受控識別。

<a name="azure-portal-disable"></a>

### <a name="remove-system-assigned-identity-in-the-azure-portal"></a>在 Azure 入口網站中移除系統指派的身分識別

在 Azure 入口網站中，從您的[邏輯應用程式](#disable-identity-logic-app)移除系統指派的身分識別，以及[從您的目標資源](#disable-identity-target-resource)存取該身分識別。

<a name="disable-identity-logic-app"></a>

#### <a name="remove-system-assigned-identity-from-logic-app"></a>從邏輯應用程式移除系統指派的身分識別

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [**設定**] 下，選取 [身分**識別** > **系統指派**]。 在 **狀態** 底下，選取 **關閉** > **儲存** > **是**。

   ![停止使用系統指派的身分識別](./media/create-managed-service-identity/turn-off-system-assigned-identity.png)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>從資源移除身分識別存取

1. 在[Azure 入口網站](https://portal.azure.com)中，移至您想要移除受控身分識別存取權的目標 Azure 資源。

1. 從目標資源的功能表中，選取 [**存取控制（IAM）** ]。 在工具列底下，選取 [**角色指派**]。

1. 在 [角色] 清單中，選取您想要移除的受控識別。 在工具列上，選取 [**移除**]。

   > [!TIP]
   > 如果停用 [**移除**] 選項，您很可能沒有許可權。 如需可讓您管理資源角色之許可權的詳細資訊，請參閱[Azure Active Directory 中的系統管理員角色許可權](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

受控識別現在已移除，且不再具有目標資源的存取權。

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>停用 Azure Resource Manager 範本中的受控識別

如果您使用 Azure Resource Manager 範本來啟用邏輯應用程式的系統管理身分識別，請將 `identity` 物件的 `type` 子屬性設定為 `None`。 此動作也會從 Azure AD 刪除系統管理身分識別的主體識別碼。

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>後續步驟

* [保護 Azure Logic Apps 中的存取和資料](../logic-apps/logic-apps-securing-a-logic-app.md)
