---
title: 使用 Azure Active Directory 來驗證 Azure Blob 和佇列的存取權 (預覽) | Microsoft Docs
description: 使用 Azure Active Directory 來驗證 Azure Blob 和佇列的存取權 (預覽)。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/13/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: dcf7d237c8cfbf52a804e428d84fff0bb328c7c8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012097"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory-preview"></a>使用 Azure Active Directory 來驗證 Azure Blob 和佇列的存取權 (預覽)

Azure 儲存體支援使用 Azure Active Directory (Azure AD) 對 Blob 和佇列服務進行驗證和授權。 您可以藉由 Azure AD，使用角色型存取控制 (RBAC) 來授與存取權給使用者、群組或應用程式服務主體。 

使用 Azure AD 認證來驗證使用者或應用程式，可提供比其他授權方法更高的安全性，也更容易使用。 雖然您可以繼續使用共用金鑰授權於應用程式，但使用 Azure AD 就不需要將帳戶存取金鑰和程式碼一起儲存。 您也可以繼續使用共用存取簽章 (SAS) 將細部存取權授與儲存體帳戶中的資源，但 Azure AD 提供類似功能，卻不必管理 SAS 權杖或擔心需要撤銷遭盜用的 SAS。 Microsoft 建議您盡可能針對 Azure 儲存體應用程式使用 Azure AD 驗證。

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="about-the-preview"></a>關於預覽

請留意有關預覽的下列事項：

- 只有預覽版才提供 Blob 和佇列服務的 Azure AD 整合。
- 所有公用區域中的 GPv1、GPv2 及 Blob 儲存體帳戶均提供 Azure AD 整合。 
- 只支援使用 Resource Manager 部署模型建立的儲存體帳戶。 
- 即將推出在 Azure 儲存體分析記錄中支援呼叫者身分識別資訊。
- 目前支援 Azure AD 授與標準儲存體帳戶中資源的存取權。 即將推出支援進階儲存體帳戶中授與分頁 Blob 的存取權。
- Azure 儲存體支援內建與自訂 RBAC 角色。 您可以指定訂用帳戶、資源群組、儲存體帳戶或個別的容器或佇列範圍內的角色。
- 目前支援 Azure AD 整合的 Azure 儲存體用戶端程式庫包括：
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage)
    - [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)
    - Python
        - [Blob、佇列及檔案](https://github.com/Azure/azure-storage-python) \(英文\)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

## <a name="overview-of-azure-ad-for-storage"></a>儲存體的 Azure AD 的概觀

使用 Azure AD 與 Azure 儲存體的整合，第一個步驟就是將適用於儲存體資料的 RBAC 角色指派給服務主體 (使用者、群組或應用程式服務主體) 或 Azure 資源的受控識別。 RBAC 角色包含容器和佇列的一般權限集合。 若要深入了解 Azure 儲存體指派 RBAC 角色，請參閱[管理儲存體的資料，使用 RBAC （預覽） 的存取權限](storage-auth-aad-rbac.md)。

若要使用 Azure AD 授與應用程式中的儲存體資源存取權，您需要從程式碼中要求 OAuth 2.0 存取權杖。 若要了解如何要求存取權杖，並用來授權對 Azure 儲存體的要求，請參閱[從 Azure 儲存體應用程式中使用 Azure AD 進行驗證 (預覽)](storage-auth-aad-app.md)。 如果您使用受控識別，請參閱[使用 Azure 資源的 Azure 受控識別來驗證 Blob 和佇列的存取權 (預覽)](storage-auth-aad-msi.md)。

Azure CLI 和 PowerShell 現在支援以 Azure AD 身分識別登入。 以 Azure AD 身分識別登入之後，工作階段便會在該身分識別下執行。 若要進一步了解，請參閱[使用 Azure AD 身分識別以使用 CLI 或 PowerShell 存取 Azure 儲存體 (預覽)](storage-auth-aad-script.md)。

## <a name="rbac-roles-for-blobs-and-queues"></a>適用於 Blob 和佇列的 RBAC 角色

Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 儲存體會定義一組內建的 RBAC 角色，其中包含一般用來存取容器或佇列的權限集合。 

RBAC 角色指派給 Azure AD 安全性主體時，Azure 授與存取這些資源的安全性主體。 存取權的範圍可以包括訂用帳戶、資源群組、儲存體帳戶或個別的容器或佇列層級。 Azure AD 安全性主體，可能是使用者、 群組、 應用程式的服務主體，或[受管理的 Azure 資源的識別](../../active-directory/managed-identities-azure-resources/overview.md)。 

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

若要了解如何在 Azure 入口網站中的內建角色指派，請參閱[授與存取權的 Azure 容器，使用 RBAC 在 Azure 入口網站 （預覽） 中的佇列](storage-auth-aad-rbac.md)。

### <a name="access-permissions-granted-by-rbac-roles"></a>授與 RBAC 角色的存取權限 

下表摘要說明不同的範圍層級的內建角色授與的存取權限：

|影響範圍|Blob 資料擁有者|Blob 資料參與者|Blob 資料讀者|佇列資料參與者|佇列資料讀者|
|---|---|---|---|---|---|
|訂用帳戶層級|訂用帳戶中所有容器和 Blob 的讀取/寫入存取和 POSIX 存取控制管理|訂用帳戶中所有容器和 Blob 的讀取/寫入存取| 訂用帳戶中所有容器和 Blob 的讀取存取|訂用帳戶中所有佇列的讀取/寫入存取|訂用帳戶中所有佇列的讀取存取|
|資源群組層級|資源群組中所有容器和 Blob 的讀取/寫入存取和 POSIX 存取控制管理|資源群組中所有容器和 Blob 的讀取/寫入存取|資源群組中所有容器和 Blob 的讀取存取|資源群組中所有佇列的讀取/寫入存取|資源群組中所有佇列的讀取存取|
|儲存體帳戶層級|儲存體帳戶中所有容器和 Blob 的讀取/寫入存取和 POSIX 存取控制管理|儲存體帳戶中所有容器和 Blob 的讀取/寫入存取|儲存體帳戶中所有容器和 Blob 的讀取存取|儲存體帳戶中所有佇列的讀取/寫入存取|儲存體帳戶中所有佇列的讀取存取|
|容器/佇列層級|指定的容器及其 Blob 的讀取/寫入存取和 POSIX 存取控制管理。|指定容器和其 Blob 的讀取/寫入存取|指定容器和其 Blob 的讀取存取|指定佇列的讀取/寫入存取|指定佇列的讀取存取|

如需呼叫 Azure 儲存體作業所需權限的詳細資訊，請參閱[呼叫 REST 作業的權限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)。

## <a name="next-steps"></a>後續步驟

如需 Azure Blob 和佇列的 Azure AD 整合詳細資訊，請參閱 Azure 儲存體小組部落格文章[宣布適用於 Azure 儲存體的 Azure AD 驗證預覽](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/) \(英文\)。
