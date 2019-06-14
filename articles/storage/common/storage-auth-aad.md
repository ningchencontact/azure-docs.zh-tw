---
title: 驗證對 Azure blob 和佇列使用 Azure Active Directory 的存取 |Microsoft Docs
description: 驗證對 Azure blob 和佇列使用 Azure Active Directory 的存取。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 66051bd0f8be349f748c72218d538bba273be8f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147260"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>驗證存取 Azure blob 和佇列使用 Azure Active Directory

Azure 儲存體支援使用 Azure Active Directory (Azure AD) 對 Blob 和佇列服務進行驗證和授權。 您可以藉由 Azure AD，使用角色型存取控制 (RBAC) 來授與存取權給使用者、群組或應用程式服務主體。 

使用 Azure AD 認證來驗證使用者或應用程式，可提供比其他授權方法更高的安全性，也更容易使用。 雖然您可以繼續使用共用金鑰授權於應用程式，但使用 Azure AD 就不需要將帳戶存取金鑰和程式碼一起儲存。 您也可以繼續使用共用存取簽章 (SAS) 將細部存取權授與儲存體帳戶中的資源，但 Azure AD 提供類似功能，卻不必管理 SAS 權杖或擔心需要撤銷遭盜用的 SAS。 Microsoft 建議您盡可能針對 Azure 儲存體應用程式使用 Azure AD 驗證。

驗證和授權使用 Azure AD 認證可供所有一般用途和 Blob 儲存體帳戶的所有公用區域和國家/地區雲端。 只有儲存體帳戶使用 Azure Resource Manager 部署模型支援建立 Azure AD 授權。

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>適用於 blob 和佇列的 Azure AD 的概觀

當安全性主體 （使用者、 群組或應用程式） 會嘗試存取的 blob 或佇列資源時，要求必須授權，除非它是可供匿名存取 blob。 與 Azure AD 資源的存取權是兩步驟程序。 首先，已驗證的安全性主體的識別，並傳回 OAuth 2.0 權杖。 接下來，此權杖會做為要求的一部分傳遞至 Blob 或佇列服務，而且服務用來授與指定的資源存取權。

驗證步驟會需要應用程式要求在執行階段的 OAuth 2.0 存取權杖。 如果應用程式從在 Azure 的實體，例如 Azure VM、 虛擬機器擴展集或 Azure Functions 應用程式內執行，它可以使用[受控身分識別](../../active-directory/managed-identities-azure-resources/overview.md)來存取 blob 或佇列。 若要了解如何向 Azure Blob 或佇列服務提出的受管理的身分識別要求的授權，請參閱[驗證適用於 Azure 資源的存取權的 blob 和佇列與 Azure Active Directory 與受管理的身分識別](storage-auth-aad-msi.md)。

授權步驟需要一或多個 RBAC 角色會指派給安全性主體。 Azure 儲存體提供 RBAC 角色，其中包含的 blob 和佇列資料的權限的一般設定。 指派給安全性主體的角色會決定將擁有主體的權限。 若要深入了解 Azure 儲存體指派 RBAC 角色，請參閱[儲存體的資料，使用 RBAC 管理存取權限](storage-auth-aad-rbac.md)。

原生應用程式和 Azure Blob 或佇列服務提出要求的 web 應用程式也可以使用 Azure AD 進行驗證。 若要了解如何要求存取權杖，並使用它來授權要求的 blob 或佇列的資料，請參閱[使用從 Azure 儲存體應用程式的 Azure AD 進行驗證](storage-auth-aad-app.md)。

## <a name="assigning-rbac-roles-for-access-rights"></a>將存取權的 RBAC 角色指派

Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 儲存體定義一組內建的 RBAC 角色，其中包含用來存取 blob 和佇列資料的權限的一組通用。 您也可以定義自訂的角色，以存取 blob 和佇列資料。

RBAC 角色指派給 Azure AD 安全性主體時，Azure 授與存取這些資源的安全性主體。 存取權的範圍可以包括訂用帳戶、資源群組、儲存體帳戶或個別的容器或佇列層級。 Azure AD 安全性主體，可能是使用者、 群組、 應用程式的服務主體，或[受管理的 Azure 資源的識別](../../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>適用於 blob 和佇列的內建 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

若要了解如何將內建的 RBAC 角色指派給安全性主體，請參閱下列文章：

- [Azure blob 和佇列資料使用 RBAC 在 Azure 入口網站中授與存取權](storage-auth-aad-rbac-portal.md)
- [授與存取權與使用 Azure CLI 的 RBAC 的 Azure blob 和佇列資料](storage-auth-aad-rbac-cli.md)
- [使用 RBAC，使用 PowerShell 的 Azure blob 和佇列資料的授與存取權](storage-auth-aad-rbac-powershell.md)

如需有關如何為 Azure 儲存體定義內建角色的詳細資訊，請參閱[了解角色定義](../../role-based-access-control/role-definitions.md#management-and-data-operations-preview)。 建立自訂的 RBAC 角色的詳細資訊，請參閱[建立 azure 角色型存取控制的自訂角色](../../role-based-access-control/custom-roles.md)。

### <a name="access-permissions-for-data-operations"></a>資料作業的存取權限

如需呼叫特定的 Blob 或佇列服務作業所需的權限的詳細資訊，請參閱[呼叫 blob 和佇列資料作業的權限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。

## <a name="resource-scope"></a>資源範圍

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>使用 Azure AD 帳戶存取資料

存取 blob 或佇列的資料，透過 Azure 入口網站、 PowerShell 或 Azure CLI 可授權，使用使用者的 Azure AD 帳戶，或使用的帳戶存取金鑰 （共用金鑰驗證）。

### <a name="data-access-from-the-azure-portal"></a>從 Azure 入口網站的資料存取

Azure 入口網站可以使用您的 Azure AD 帳戶或帳戶存取金鑰來存取 Azure 儲存體帳戶中的 blob 和佇列資料。 使用 Azure 入口網站的授權配置取決於指派給您的 RBAC 角色。

當您嘗試存取 blob 或佇列的資料時，Azure 入口網站會先檢查是否您已獲指派 RBAC 角色**Microsoft.Storage/storageAccounts/listkeys/action**。 如果您已獲指派的角色與此動作，在 Azure 入口網站會使用帳戶金鑰來存取 blob 和佇列資料，透過共用金鑰驗證。 如果您有尚未指派的角色與此動作，然後在 Azure 入口網站會嘗試使用您的 Azure AD 帳戶來存取資料。

若要存取 blob 或佇列使用您的 Azure AD 帳戶在 Azure 入口網站中的資料，您需要存取 blob 和佇列資料的權限，您也需要瀏覽 Azure 入口網站中的儲存體帳戶資源的權限。 Azure 儲存體所提供的內建角色授與對 blob 和佇列資源的存取權，但他們不授與儲存體帳戶資源的權限。 基於這個理由，存取入口網站也需要 Azure Resource Manager 角色的指派這類[讀取器](../../role-based-access-control/built-in-roles.md#reader)範圍設定為或更高的儲存體帳戶的層級的角色。 **讀取器**角色會授與的限制最嚴格的權限，但另一個 Azure Resource Manager 角色，授與儲存體帳戶管理資源的存取權也可以接受。 若要深入了解如何將權限指派給使用者的 Azure AD 帳戶的 Azure 入口網站中的資料存取，請參閱[授與存取 Azure blob 和佇列資料使用 RBAC 在 Azure 入口網站中](storage-auth-aad-rbac-portal.md)。

Azure 入口網站會指出哪些授權配置正在使用中當您瀏覽至容器或佇列。 如需有關在入口網站中的資料存取的詳細資訊，請參閱[使用 Azure 入口網站來存取 blob 或佇列資料](storage-access-blobs-queues-portal.md)。

### <a name="data-access-from-powershell-or-azure-cli"></a>從 PowerShell 或 Azure CLI 進行資料存取

Azure CLI 和 PowerShell 支援登入 Azure AD 認證。 登入之後，您的工作階段會在這些認證下執行。 若要進一步了解，請參閱[執行 Azure CLI 或 PowerShell 命令與 Azure AD 認證來存取 blob 或佇列資料](storage-auth-aad-script.md)。

## <a name="azure-ad-authentication-over-smb-for-azure-files"></a>Azure AD 驗證透過 SMB 的 Azure 檔案

「Azure 檔案服務」只有針對已加入網域的 VM 才支援透過 SMB 進行 Azure AD 驗證 (預覽)。 若要深入了解 Azure 檔案，使用透過 SMB 的 Azure AD，請參閱[透過 SMB 的概觀的 Azure Active Directory 驗證，Azure 檔案 （預覽）](../files/storage-files-active-directory-overview.md)。

## <a name="next-steps"></a>後續步驟

- [驗證適用於 Azure 資源的存取權的 blob 和佇列與 Azure Active Directory 與受管理的身分識別](storage-auth-aad-msi.md)
- [從應用程式中使用 Azure Active Directory 進行驗證以存取 Blob 和佇列](storage-auth-aad-app.md)
- [Azure 儲存體支援 Azure Active directory 型存取控制已正式推出](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
