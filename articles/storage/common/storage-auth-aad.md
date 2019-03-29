---
title: 驗證對 Azure blob 和佇列使用 Azure Active Directory 的存取 |Microsoft Docs
description: 驗證對 Azure blob 和佇列使用 Azure Active Directory 的存取。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/28/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e72400a759b976b1a2a6864b2fa7d7d91e16c62f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619283"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>驗證存取 Azure blob 和佇列使用 Azure Active Directory

Azure 儲存體支援使用 Azure Active Directory (Azure AD) 對 Blob 和佇列服務進行驗證和授權。 您可以藉由 Azure AD，使用角色型存取控制 (RBAC) 來授與存取權給使用者、群組或應用程式服務主體。 

使用 Azure AD 認證來驗證使用者或應用程式，可提供比其他授權方法更高的安全性，也更容易使用。 雖然您可以繼續使用共用金鑰授權於應用程式，但使用 Azure AD 就不需要將帳戶存取金鑰和程式碼一起儲存。 您也可以繼續使用共用存取簽章 (SAS) 將細部存取權授與儲存體帳戶中的資源，但 Azure AD 提供類似功能，卻不必管理 SAS 權杖或擔心需要撤銷遭盜用的 SAS。 Microsoft 建議您盡可能針對 Azure 儲存體應用程式使用 Azure AD 驗證。

驗證和授權使用 Azure AD 認證是適用於所有的一般用途 v2、 一般用途 v1 和在所有公用區域的 Blob 儲存體帳戶。 只有儲存體帳戶使用 Azure Resource Manager 部署模型支援建立 Azure AD 授權。

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>適用於 blob 和佇列的 Azure AD 的概觀

使用 Azure AD 與 Azure 儲存體的整合，第一個步驟就是將適用於儲存體資料的 RBAC 角色指派給服務主體 (使用者、群組或應用程式服務主體) 或 Azure 資源的受控識別。 RBAC 角色包含容器和佇列的一般權限集合。 若要深入了解 Azure 儲存體指派 RBAC 角色，請參閱[儲存體的資料，使用 RBAC 管理存取權限](storage-auth-aad-rbac.md)。

若要使用 Azure AD 授與應用程式中的儲存體資源存取權，您需要從程式碼中要求 OAuth 2.0 存取權杖。 若要了解如何要求存取權杖，並使用它來授權要求到 Azure 儲存體，請參閱[使用從 Azure 儲存體應用程式的 Azure AD 進行驗證](storage-auth-aad-app.md)。 如果您使用受控身分識別，請參閱[適用於 Azure 資源管理身分識別的驗證的存取權的 blob 和佇列與 Azure](storage-auth-aad-msi.md)。

Azure CLI 和 PowerShell 現在支援以 Azure AD 身分識別登入。 您使用 Azure AD 身分識別登入之後，您的工作階段會在該識別之下執行。 若要進一步了解，請參閱[使用 Azure AD 身分識別存取 Azure 儲存體，使用 CLI 或 PowerShell](storage-auth-aad-script.md)。

## <a name="rbac-roles-for-blobs-and-queues"></a>適用於 Blob 和佇列的 RBAC 角色

Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 儲存體定義一組內建的 RBAC 角色，其中包含用來存取 blob 和佇列資料的權限的一組通用。 您也可以定義自訂的角色，以存取 blob 和佇列資料。

将 RBAC 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 存取權的範圍可以包括訂用帳戶、資源群組、儲存體帳戶或個別的容器或佇列層級。 Azure AD 安全性主體，可能是使用者、 群組、 應用程式的服務主體，或[受管理的 Azure 資源的識別](../../active-directory/managed-identities-azure-resources/overview.md)。

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

若要了解如何指派內建的 RBAC 的 Azure 儲存體資源，請參閱下列主題之一：

- [Azure blob 和佇列資料使用 RBAC 在 Azure 入口網站中授與存取權](storage-auth-aad-rbac-portal.md)
- [授與存取權與使用 Azure CLI 的 RBAC 的 Azure blob 和佇列資料](storage-auth-aad-rbac-cli.md)
- [使用 RBAC，使用 PowerShell 的 Azure blob 和佇列資料的授與存取權](storage-auth-aad-rbac-powershell.md)

### <a name="access-permissions-granted-by-rbac-roles"></a>通过 RBAC 角色授予的访问权限 

如需呼叫 Azure 儲存體作業所需權限的詳細資訊，請參閱[呼叫 REST 作業的權限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)。

## <a name="resource-scope"></a>資源範圍

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="next-steps"></a>後續步驟

- [Azure 儲存體支援 Azure Active directory 型存取控制已正式推出](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
- [從應用程式中使用 Azure Active Directory 進行驗證以存取 Blob 和佇列](storage-auth-aad-app.md)
- [驗證適用於 Azure 資源的存取權的 blob 和佇列與受管理的身分識別](storage-auth-aad-msi.md)
- 「Azure 檔案服務」只有針對已加入網域的 VM 才支援透過 SMB 進行 Azure AD 驗證 (預覽)。 若要了解如何針對「Azure 檔案服務」使用透過 SMB 的 Azure AD，請參閱[針對 Azure 檔案透過 SMB 進行 Azure Active Directory 驗證 (預覽) 的概觀](../files/storage-files-active-directory-overview.md)。