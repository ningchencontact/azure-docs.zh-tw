---
title: 從 Azure VM 受控服務識別使用 Azure AD 進行驗證 (預覽) | Microsoft Docs
description: 從 Azure VM 受控服務識別使用 Azure AD 進行驗證 (預覽)。
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 83d3a2d973604e3b8a709b24cabcb3abba1e304c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659211"
---
# <a name="authenticate-with-azure-ad-from-an-azure-managed-service-identity-preview"></a>從 Azure 受控服務識別使用 Azure AD 進行驗證 (預覽)

Azure 儲存體支援使用[受控服務識別](../../active-directory/managed-service-identity/overview.md)來進行 Azure Active Directory (Azure AD) 驗證。 受控服務識別 (MSI) 會在 Azure Active Directory (Azure AD) 中提供自動管理的身分識別。 您可以使用 MSI，從 Azure 虛擬機器中執行的應用程式、函式應用程式和虛擬機器擴展集等項目向 Azure 儲存體進行驗證。 藉由使用 MSI 並利用 Azure AD 驗證的功能，您可以避免在執行於雲端中的應用程式內儲存認證。  

若要對儲存體容器或佇列的受控服務識別授與權限，您可以對 MSI 指派含有儲存體權限的 RBAC 角色。 如需儲存體中 RBAC 角色的詳細資訊，請參閱[使用 RBAC 管理儲存體資料的存取權限 (預覽)](storage-auth-aad-rbac.md)。 

> [!IMPORTANT]
> 此預覽僅適用於非生產環境。 Azure 儲存體的 Azure AD 整合宣告上市之前，無法使用生產服務等級協定 (SLA)。 如果您的案例尚未支援 Azure AD 整合，請繼續在應用程式中使用您的共用金鑰授權或 SAS 權杖。 如需預覽的詳細資訊，請參閱[使用 Azure Active Directory 來驗證 Azure 儲存體的存取權 (預覽)](storage-auth-aad.md)。
>
> 在預覽期間，RBAC 角色指派可能需要五分鐘的時間傳播。

本文會說明如何使用 MSI 從 Azure VM 向 Azure 儲存體進行驗證。  

## <a name="enable-msi-on-the-vm"></a>在 VM 上啟用 MSI

您必須先在 VM 上啟用 MSI，才可以使用 MSI 從 VM 向 Azure 儲存體進行驗證。 若要了解如何啟用 MSI，請參閱以下其中一篇文章：

- [Azure 入口網站](https://docs.microsoft.com/en-us/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-service-identity/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-service-identity/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 範本](../../active-directory/managed-service-identity/qs-configure-template-windows-vm.md)
- [Azure SDK](../../active-directory/managed-service-identity/qs-configure-sdk-windows-vm.md)

## <a name="get-an-msi-access-token"></a>取得 MSI 存取權杖

若要使用 MSI 進行驗證，應用程式或指令碼必須取得 MSI 存取權杖。 若要了解如何取得存取權杖，請參閱[如何使用 Azure 虛擬機器受控服務識別 (MSI) 來取得權杖](../../active-directory/managed-service-identity/how-to-use-vm-token.md)。

## <a name="net-code-example-create-a-block-blob"></a>.NET 程式碼範例：建立區塊 Blob

此程式碼範例假設您有 MSI 存取權杖。 存取權杖可用來授權受控服務識別建立區塊 Blob。

### <a name="add-references-and-using-statements"></a>新增參考並使用陳述式  

在 Visual Studio 中，安裝 Azure 儲存體用戶端程式庫的預覽版本。 在 [工具] 功能表中，依序選取 [Nuget 套件管理員] 及 [套件管理員主控台]。 將下列命令輸入到主控台：

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

將下列 using 陳述式新增至程式碼：

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-msi-access-token"></a>從 MSI 存取權杖建立認證

若要建立區塊 Blob，請使用預覽套件所提供的 **TokenCredentials** 類別。 建構 **TokenCredentials** 的新執行個體，並傳入您先前取得的 MSI 存取權杖：

```dotnet
// Create storage credentials from your MSI access token.
TokenCredential tokenCredential = new TokenCredential(msiAccessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Azure AD 與 Azure 儲存體的整合需要您使用 HTTPS 來進行 Azure 儲存體作業。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure 儲存體的 RBAC 角色，請參閱[使用 RBAC 管理儲存體資料的存取權限 (預覽)](storage-auth-aad-rbac.md)。
- 若要深入了解如何從儲存體應用程式內授權容器和佇列的存取權，請參閱[使用 Azure AD 與儲存體應用程式](storage-auth-aad-app.md)。
- 若要了解如何以 Azure AD 身分識別登入 Azure CLI 和 PowerShell，請參閱[使用 Azure AD 身分識別透過 CLI 或 PowerShell 來存取 Azure 儲存體 (預覽)](storage-auth-aad-script.md)。
- 如需 Azure Blob 和佇列的 Azure AD 整合詳細資訊，請參閱 Azure 儲存體小組部落格文章，[宣告適用於 Azure 儲存體的 Azure AD 驗證預覽](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)。
