---
title: "如何使用 Azure 虛擬機器受控服務識別來登入"
description: "使用 Azure 虛擬機器 MSI 服務主體來進行指令碼用戶端登入和資源存取的逐步指示與範例。"
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
ms.date: 01/05/2018
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c5c1be01947dba8b7f4ef8aa54aa6aedfb191d32
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/03/2018
---
# <a name="sign-in-using-a-vm-user-assigned-managed-service-identity-msi"></a>使用 VM 使用者指派的受控服務識別 (MSI) 進行登入

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]
本文提供使用使用者指派的 MSI 服務主體進行登入的 CLI 指令碼範例，以及錯誤處理等重要主題的指引。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

若要使用本文中的 Azure CLI 範例，請務必安裝最新版的 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

> [!IMPORTANT]
> - 本文中的所有範例指令碼都假設命令列用戶端在已啟用 MSI 的虛擬機器上執行。 在 Azure 入口網站中使用虛擬機器「連線」功能，從遠端連線到您的虛擬機器。 如需有關在虛擬機器上啟用 MSI 的詳細資訊，請參閱[使用 Azure CLI 設定虛擬機器受控服務識別 (MSI)](msi-qs-configure-cli-windows-vm.md)，或其中一篇變化文章 (使用 PowerShell、入口網站、範本或 Azure SDK)。 
> - 若要避免登入和資源存取期間發生錯誤，MSI 必須至少取得適當範圍 (該虛擬機器或更高層級) 的「讀者」存取權，以允許在虛擬機器上進行 Azure Resource Manager 作業。 如需詳細資訊，請參閱[使用 Azure CLI 將受控服務識別 (MSI) 存取權指派給資源](msi-howto-assign-access-cli.md)。

## <a name="overview"></a>概觀

MSI 會提供[服務主體](~/articles/active-directory/develop/active-directory-dev-glossary.md#service-principal-object)，這是在虛擬機器上[啟用 MSI 時所建立](msi-overview.md#how-does-it-work)。 服務主體可以獲得 Azure 資源的存取權，並可讓指令碼/命令列用戶端用來作為登入及存取資源時的身分識別。 傳統上，若要以受保護資源本身的身分識別來存取該資源，指令碼用戶端需要：  

   - 向 Azure AD 註冊並獲得同意成為機密/Web 用戶端應用程式
   - 在其服務主體下登入，並使用應用程式的認證 (這可能會內嵌於指令碼中)

透過 MSI，您的指令碼用戶端不再需要進行這其中的任何一項，因為它可以在 MSI 服務主體下登入。 

## <a name="azure-cli"></a>Azure CLI

下列指令碼示範如何：

1. 在使用者指派的 MSI 服務主體下登入 Azure AD。  
2. 呼叫 Azure Resource Manager 並取得虛擬機器的 Azure 區域位置。 CLI 會自動為您管理權杖的擷取/使用。 務必以您的 VM 名稱取代 `<VM NAME>`，並以使用者指派的 MSI 資源識別碼取代 `<MSI ID>`。 在建立使用者指派的 MSI 期間，MSI 資源識別碼會在 `id` 屬性中傳回 (請參閱[使用 Azure CLI 為虛擬機器設定使用者指派的受控服務識別 (MSI)](msi-qs-configure-cli-windows-vm.md)，以取得 `az identity create` 命令的範例)。

    ```azurecli
    az login --msi –u <MSI ID>
   
    vmLocation=$(az resource list -n <VM NAME> --query [*].location --out tsv)
    echo The VM region location is $vmLocation
    ```

    範例回應：
   
    ```bash
    user@vmLinux:~$ az login --msi -u /subscriptions/80c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName
    [
      {
        "environmentName": "AzureCloud",
        "id": "90c696ff-5efa-4909-a64d-z1b616f423bl",
        "isDefault": true,
        "name": "MSIResource-/subscriptions/90c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName@50342",
        "state": "Enabled",
        "tenantId": "933a8f0e-ec41-4e69-8ad8-971zc4b533ll",
        "user": {
          "name": "userAssignedIdentity",
          "type": "servicePrincipal"
        }
      }
    ]  
    user@vmLinux:~$ vmLocation=$(az resource list -n vmLinux --query [*].location --out tsv)
    user@vmLinux:~$ echo The VM region location is $vmLocation
    The VM region location is westcentralus
    ```

## <a name="resource-ids-for-azure-services"></a>Azure 服務的資源識別碼

關於支援 Azure AD 且經過 MSI 測試的資源及其各自資源識別碼的清單，請參閱[支援 Azure AD 驗證的 Azure 服務](msi-overview.md#azure-services-that-support-azure-ad-authentication)。

## <a name="error-handling-guidance"></a>錯誤處理指引 

下列回應可能表示 MSI 未正確設定：

- CLI：MSI：無法從 'http://localhost:50342/oauth2/token' 擷取權杖，包含 HTTPConnectionPool(主機='localhost'，連接埠=50342) 錯誤 

如果您收到這些錯誤的其中一個，請返回 [Azure 入口網站](https://portal.azure.com)中的 Azure 虛擬機器，並且：

- 移至 [設定] 頁面，並確定「受控服務識別」已設定為「是」。
- 移至 [延伸模組] 頁面，並確定已成功部署 MSI 延伸模組。

如果其中一者不正確，您可能需要再度將 MSI 重新指派給您的資源，或針對部署失敗進行疑難排解。 如果您需要設定虛擬機器的協助，請參閱[使用 Azure CLI 設定虛擬機器受控服務識別 (MSI)](msi-qs-configure-cli-windows-vm.md)。

## <a name="next-steps"></a>後續步驟

- 若要在 Azure VM 上啟用 MSI，請參閱[使用 Azure CLI 設定虛擬機器受控服務識別 (MSI)](msi-qs-configure-cli-windows-vm.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。








