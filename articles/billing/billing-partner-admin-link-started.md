---
title: 將 Azure 帳戶連結到合作夥伴識別碼 | Microsoft Docs
description: 將合作夥伴識別碼連結到您用來管理客戶資源的使用者帳戶，以追蹤與 Azure 客戶的業務開發情況。
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c38b28a247feb94efd5f4b73e690d30aac9ed73a
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2019
ms.locfileid: "54900233"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>將合作夥伴識別碼連結到您的 Azure 帳戶

身為合作夥伴，您可以追蹤對客戶業務開發的影響力。 您可以將合作夥伴識別碼連結到用來管理客戶資源的帳戶。

此功能目前提供於公開預覽版。

## <a name="get-access-from-your-customer"></a>取得客戶提供的存取權

在您連結合作夥伴識別碼之前，您的客戶必須先透過下列其中一個選項，將其 Azure 資源的存取權提供給您：

- **來賓使用者**：客戶可將您新增為來賓使用者，並指派任何角色型存取控制 (RBAC) 角色。 如需詳細資訊，請參閱[從另一個目錄中新增來賓使用者](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)。

- **目錄帳戶**：客戶可以在自己的目錄中為您建立新使用者，並指派任何 RBAC 角色。

- **服務主體**：客戶可從您的組織在其目錄中新增應用程式或指令碼，並指派任何 RBAC 角色。 應用程式或指令碼的身分識別稱為服務主體。

## <a name="link-to-a-partner-id"></a>連結到合作夥伴識別碼

當您具有客戶資源的存取權時，請使用 Azure 入口網站、PowerShell 或 Azure CLI，將您的 Microsoft 合作夥伴網路識別碼 (MPN ID) 連結到您的使用者識別碼或服務主體。 在每個客戶租用戶中連結合作夥伴識別碼。

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>使用 Azure 入口網站連結到新的合作夥伴識別碼

1. 在 Azure 入口網站中，移至[連結到合作夥伴識別碼](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade)。

2. 登入 Azure 入口網站。

3. 輸入 Microsoft 合作夥伴識別碼。 合作夥伴識別碼是您組織的 [Microsoft 合作夥伴網路](https://partner.microsoft.com/)識別碼。

   ![顯示 [連結到合作夥伴識別碼] 的螢幕擷取畫面](./media/billing-link-partner-id/link-partner-ID.PNG)

4. 若要為另一個客戶連結合作夥伴識別碼，請切換目錄。 在 [切換目錄] 底下，選取您的目錄。

   ![顯示 [切換目錄] 的螢幕擷取畫面](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>使用 PowerShell 連結到新的合作夥伴識別碼

1. 安裝 [AzureRM.ManagementPartner](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner) PowerShell 模組。

2. 以使用者帳戶或服務主體登入客戶的租用戶。 如需詳細資訊，請參閱[使用 PowerShell 登入](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-5.2.0) \(英文\)。
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. 連結到新的合作夥伴識別碼。 合作夥伴識別碼是您組織的 [Microsoft 合作夥伴網路](https://partner.microsoft.com/)識別碼。

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>取得已連結的合作夥伴識別碼
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>更新已連結的合作夥伴識別碼
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>刪除已連結的合作夥伴識別碼
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>使用 Azure CLI 連結到新的合作夥伴識別碼
1. 安裝 Azure CLI 擴充功能。

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ``` 

2. 以使用者帳戶或服務主體登入客戶的租用戶。 如需詳細資訊，請參閱[使用 Azure CLI 登入](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ``` 

3. 連結到新的合作夥伴識別碼。 合作夥伴識別碼是您組織的 [Microsoft 合作夥伴網路](https://partner.microsoft.com/)識別碼。

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>取得已連結的合作夥伴識別碼
```azurecli-interactive
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>更新已連結的合作夥伴識別碼
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>刪除已連結的合作夥伴識別碼
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
``` 

## <a name="next-steps"></a>後續步驟

在 [Microsoft 合作夥伴社群](https://aka.ms/PALdiscussion)中參與討論，以接收更新或傳送意見反應。

## <a name="frequently-asked-questions"></a>常見問題集

**誰可以連結合作夥伴識別碼？**

夥伴組織中負責管理客戶 Azure 資源的任何使用者，都可將合作夥伴識別碼連結到帳戶。

**連結合作夥伴識別碼之後可加以改變嗎？**

是。 連結的合作夥伴識別碼可以變更、新增或移除。

**如果使用者在多個客戶租用戶中具有同一帳戶，將會如何？**

合作夥伴識別碼與帳戶之間的連結必須對個別的客戶租用戶建立。 在每個客戶租用戶中連結合作夥伴識別碼。

**其他合作夥伴或客戶是否可編輯或移除合作夥伴識別碼的連結？**

連結會在使用者帳戶層級產生關聯。 只有您才可編輯或移除合作夥伴識別碼的連結。 客戶和其他合作夥伴無法變更合作夥伴識別碼的連結。 
