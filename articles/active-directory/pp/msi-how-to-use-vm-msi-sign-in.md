---
title: "如何使用 Azure 虛擬機器受控服務識別來登入"
description: "使用 Azure 虛擬機器 MSI 服務主體來進行指令碼用戶端登入和資源存取的逐步指示與範例。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/05/2018
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c5f71d27a9e07cc6d6a260b809e91aaa2a50270c
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="sign-in-using-a-vm-user-assigned-managed-service-identity-msi"></a>使用 VM，受管理服務身分識別 (MSI) 指派使用者登入。

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]這篇文章提供 CLI 指令碼範例使用指派給使用者的 MSI 服務主體和指引重要主題，例如錯誤處理登入。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

若要在本文中使用 Azure CLI 範例，請務必安裝最新版[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

> [!IMPORTANT]
> - 本文中的所有範例指令碼都假設命令列用戶端在已啟用 MSI 的虛擬機器上執行。 在 Azure 入口網站中使用虛擬機器「連線」功能，從遠端連線到您的虛擬機器。 如需有關啟用 VM 上的 MSI 的詳細資訊，請參閱[設定 VM 管理服務身分識別 (MSI) 使用 Azure CLI](msi-qs-configure-cli-windows-vm.md)，或其中一個變數 （使用 PowerShell、 入口網站、 範本或 Azure SDK） 的發行項。 
> - 若要避免錯誤登入和資源存取期間，MSI 必須至少指定 「 讀取 」 存取在適當的範圍 (VM 或更高版本) 允許在 VM 上的 Azure 資源管理員作業。 請參閱[指派的管理服務身分識別 (MSI) 存取資源，使用 Azure CLI](msi-howto-assign-access-cli.md)如需詳細資訊。

## <a name="overview"></a>概觀

MSI 會提供[服務主體](~/articles/active-directory/develop/active-directory-dev-glossary.md#service-principal-object)，這是在虛擬機器上[啟用 MSI 時所建立](msi-overview.md#how-does-it-work)。 服務主體可以獲得存取權的 Azure 資源，並做為識別指令碼/命令列用戶端使用的登入和資源的存取。 傳統上，若要以受保護資源本身的身分識別來存取該資源，指令碼用戶端需要：  

   - 向 Azure AD 註冊並獲得同意成為機密/Web 用戶端應用程式
   - 在其服務主體下登入，並使用應用程式的認證 (這可能會內嵌於指令碼中)

透過 MSI，您的指令碼用戶端不再需要進行這其中的任何一項，因為它可以在 MSI 服務主體下登入。 

## <a name="azure-cli"></a>Azure CLI

下列指令碼示範如何：

1. 登入 Azure AD 在指派給使用者的 MSI 的服務主體。  
2. 呼叫 Azure 資源管理員，並取得 VM 的 Azure 區域位置。 CLI 會自動為您管理權杖的擷取/使用。 務必以取代您的 VM 名稱`<VM NAME>`，並指派使用者給 MSI 的資源 id `<MSI ID>`。 此 MSI 的資源識別碼會傳入`id`屬性指派給使用者的 MSI 建立期間 (請參閱[設定 VM，使用 Azure CLI 使用者指派受管理服務身分識別 (MSI)](msi-qs-configure-cli-windows-vm.md)的範例`az identity create`命令).

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

下列回應可能表示，MSI 尚未正確設定：

- CLI：MSI：無法從 'http://localhost:50342/oauth2/token' 擷取權杖，包含 HTTPConnectionPool(主機='localhost'，連接埠=50342) 錯誤 

如果您收到這些錯誤的其中一個，請返回 [Azure 入口網站](https://portal.azure.com)中的 Azure 虛擬機器，並且：

- 移至 [設定] 頁面，並確定「受控服務識別」已設定為「是」。
- 移至 [延伸模組] 頁面，並確定已成功部署 MSI 延伸模組。

如果是不正確，您可能需要一次，重新指派至您的資源 MSI 或疑難排解部署失敗。 請參閱[設定 VM 管理服務身分識別 (MSI) 使用 Azure CLI](msi-qs-configure-cli-windows-vm.md)如果您需要協助以 VM 組態。

## <a name="next-steps"></a>後續步驟

- 若要啟用 Azure VM 上的 MSI，請參閱[設定 VM 管理服務身分識別 (MSI) 使用 Azure CLI](msi-qs-configure-cli-windows-vm.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。








