---
title: 使用 Azure 入口網站管理 Azure AD rbac-Azure 儲存體 blob 和佇列資料的存取權限 |Microsoft Docs
description: 您可以使用從 Azure 入口網站的角色型存取控制 (RBAC) 來指派存取權給容器和安全性主體的佇列。 Azure 儲存體支援透過 Azure AD 的驗證內建和自訂的 RBAC 角色。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8214ff821bad8a46eb710c8b9506d337715db103
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449928"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-in-the-azure-portal"></a>Azure blob 和佇列資料使用 RBAC 在 Azure 入口網站中授與存取權

Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 儲存體定義一組內建的 RBAC 角色，其中包含用來存取 blob 或佇列資料的權限的一組通用。 

RBAC 角色指派給 Azure AD 安全性主體時，Azure 授與存取這些資源的安全性主體。 存取權的範圍可以包括訂用帳戶、資源群組、儲存體帳戶或個別的容器或佇列層級。 Azure AD 安全性主體，可能是使用者、 群組、 應用程式的服務主體，或[受管理的 Azure 資源的識別](../../active-directory/managed-identities-azure-resources/overview.md)。

本文說明如何使用 Azure 入口網站指派的 RBAC 角色。 Azure 入口網站提供簡單的介面，用於將 RBAC 角色指派和管理儲存體資源的存取權。 您也可以指派使用 Azure 命令列工具或 Azure 儲存體管理 Api 的 blob 和佇列資源的 RBAC 角色。 如需有關儲存體資源的 RBAC 角色的詳細資訊，請參閱[驗證存取 Azure blob 和佇列使用 Azure Active Directory](storage-auth-aad.md)。 

## <a name="rbac-roles-for-blobs-and-queues"></a>適用於 Blob 和佇列的 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>判斷資源範圍 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>將使用 Azure 入口網站的 RBAC 角色指派

確定適當的角色指派範圍之後，瀏覽至該資源在 Azure 入口網站中。 顯示**存取控制 (IAM)** 設定資源，並遵循這些指示來管理角色指派：

1. 指派適當的 Azure 儲存體的 RBAC 角色，授與存取權的 Azure AD 安全性主體。

1. 指派 Azure Resource Manager[讀取器](../../role-based-access-control/built-in-roles.md#reader)角色給使用者，需要存取容器或透過使用其 Azure AD 認證在 Azure 入口網站的佇列。 

下列各節描述每個詳細步驟。

### <a name="assign-a-built-in-rbac-role"></a>將內建的 RBAC 角色指派

您將角色指派給安全性主體之前，請務必考慮您要授與的權限的範圍。 檢閱[判斷資源範圍](#determine-resource-scope)一節以決定適當的範圍內。

這裡顯示的程序會對容器範圍指派角色，但是您可以依照相同的步驟，對佇列範圍指派角色： 

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的儲存體帳戶，並顯示帳戶的**概觀**。
1. 在 [服務] 下，選取 [Blob]。 
1. 找出您要為其指派角色的容器，並顯示容器的設定。 
1. 選取 [存取控制 (IAM)]，以顯示容器的存取控制設定。 選取 [角色指派] 索引標籤，以查看角色指派的清單。

    ![顯示容器的存取控制設定螢幕擷取畫面](media/storage-auth-aad-rbac-portal/portal-access-control-container.png)

1. 按一下 [新增角色指派] 按鈕新增角色。
1. 在 [**新增的角色指派**] 視窗中，選取您想要指派的 Azure 儲存體角色。 然後搜尋以找出您要將該角色指派的安全性主體。

    ![顯示如何指派 RBAC 角色的螢幕擷取畫面](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. 按一下 [檔案] 。 您對其指派角色的身分識別會出現在該角色下方。 如下圖所示，新增的使用者現在有權對名為 sample-container 容器讀取資料。

    ![指派給角色之使用者的螢幕擷取畫面顯示清單](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

您可以依照類似的步驟，若要將範圍設定至儲存體帳戶、 資源群組或訂用帳戶的角色指派。

> [!NOTE]
> 身為 Azure 儲存體帳戶擁有者的您，並不會自動收到指派的資料存取權限。 您必須明確地將 Azure 儲存體的 RBAC 角色指派給自己。 您可以在訂用帳戶、資源群組、儲存體帳戶或容器/佇列層級上指派此角色。
> 
> 您無法指派範圍設定至容器或佇列儲存體帳戶是否已啟用階層式命名空間的角色。

### <a name="assign-the-reader-role-for-portal-access"></a>指派讀取者角色存取入口網站

當您將 Azure 儲存體的內建或自訂的角色指派給安全性主體時，您會授與權限給該安全性主體，執行您的儲存體帳戶中的資料上的作業。 內建**資料讀取器**角色會提供資料中的容器或佇列，同時內建的讀取權限**Data 參與者**角色提供讀取、 寫入和刪除容器的權限或佇列。 權限範圍都限於指定的資源。  

比方說，如果您指派**儲存體 Blob 資料參與者**角色，以使用者層級的容器，名為 Mary **sample container>**、 然後 Mary 授與讀取、 寫入和刪除所有的 blob 中的存取權該容器中。

不過，如果想要在 Azure 入口網站中檢視 blob 的 Mary 則**儲存體 Blob 資料參與者**角色本身將不會提供足夠的權限來瀏覽 blob 入口網站中，才能檢視它。 其他 Azure AD 權限，才能透過入口網站瀏覽和檢視會顯示有的其他資源。

如果您的使用者必須能夠存取 Azure 入口網站中的 blob，然後將它們指派 RBAC 角色，可[讀取器](../../role-based-access-control/built-in-roles.md#reader)角色給這些使用者，在層級的儲存體帳戶或更新版本。 **讀取器**角色是 Azure Resource Manager 角色，以允許使用者檢視儲存體帳戶資源，但無法修改它們。 它不提供 Azure 儲存體中的資料，只針對帳戶管理資源的讀取權限。

請遵循這些步驟，將指派**讀取器**角色，讓使用者可以從 Azure 入口網站存取 blob。 在此範例中，指派的範圍為儲存體帳戶：

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的儲存體帳戶。
1. 選取 **存取控制 (IAM)** 來顯示儲存體帳戶的存取控制設定。 選取 [角色指派] 索引標籤，以查看角色指派的清單。
1. 在 **新增的角色指派**視窗中，選取**讀取器**角色。 
1. 從**存取權指派給**欄位中，選取**Azure AD 使用者、 群組或服務主體**。
1. [搜尋]，以找出您要將角色指派的安全性主體。
1. 儲存角色指派。

> [!NOTE]
> 僅適用於需要存取 blob 或佇列使用 Azure 入口網站的使用者必須指派 「 讀者 」 角色。 

## <a name="next-steps"></a>後續步驟

- 如需有關儲存體資源的 RBAC 角色的詳細資訊，請參閱[驗證存取 Azure blob 和佇列使用 Azure Active Directory](storage-auth-aad.md)。 
- 若要深入了解 RBAC，請參閱[什麼是角色型存取控制 (RBAC)？](../../role-based-access-control/overview.md)。
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 來指派和管理 RBAC 角色指派，請參閱下列文章：
    - [使用 Azure PowerShell 管理角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-powershell.md)
    - [使用 Azure CLI 管理角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-rest.md)
- 若要深入了解如何從儲存體應用程式內授權容器和佇列的存取權，請參閱[使用 Azure AD 與 Azure 儲存體應用程式](storage-auth-aad-app.md)。
