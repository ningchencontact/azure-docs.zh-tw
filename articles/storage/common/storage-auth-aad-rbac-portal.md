---
title: 使用 Azure 入口網站管理 Azure AD rbac-Azure 儲存體 blob 和佇列資料的存取權限 |Microsoft Docs
description: 在 Azure 门户中使用基于角色的访问控制 (RBAC) 向安全主体分配容器和队列的访问权限。 Azure 存储支持通过 Azure AD 使用内置和自定义的 RBAC 角色进行身份验证。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8214ff821bad8a46eb710c8b9506d337715db103
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61484305"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-in-the-azure-portal"></a>Azure blob 和佇列資料使用 RBAC 在 Azure 入口網站中授與存取權

Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 儲存體定義一組內建的 RBAC 角色，其中包含用來存取 blob 或佇列資料的權限的一組通用。 

将 RBAC 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 存取權的範圍可以包括訂用帳戶、資源群組、儲存體帳戶或個別的容器或佇列層級。 Azure AD 安全性主體，可能是使用者、 群組、 應用程式的服務主體，或[受管理的 Azure 資源的識別](../../active-directory/managed-identities-azure-resources/overview.md)。

本文介绍如何使用 Azure 门户分配 RBAC 角色。 Azure 门户提供一个简单的界面用于分配 RBAC 角色，以及管理对存储资源的访问权限。 也可以使用 Azure 命令行工具或 Azure 存储管理 API 来为 Blob 和队列资源分配 RBAC 角色。 有关存储资源的 RBAC 角色的详细信息，请参阅[使用 Azure Active Directory 验证对 Azure Blob 和队列的访问](storage-auth-aad.md)。 

## <a name="rbac-roles-for-blobs-and-queues"></a>適用於 Blob 和佇列的 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>确定资源范围 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 门户分配 RBAC 角色

確定適當的角色指派範圍之後，瀏覽至該資源在 Azure 入口網站中。 顯示**存取控制 (IAM)** 設定資源，並遵循這些指示來管理角色指派：

1. 指派適當的 Azure 儲存體的 RBAC 角色，授與存取權的 Azure AD 安全性主體。

1. 可将 Azure 资源管理器[读取者](../../role-based-access-control/built-in-roles.md#reader)角色分配给需要通过 Azure 门户使用其 Azure AD 凭据访问容器或队列的用户。 

以下各部分更详细地说明了其中的每个步骤。

### <a name="assign-a-built-in-rbac-role"></a>分配内置的 RBAC 角色

在将角色分配到安全主体之前，请务必考虑所要授予的权限的范围。 查看[确定资源范围](#determine-resource-scope)部分以确定适当的范围。

這裡顯示的程序會對容器範圍指派角色，但是您可以依照相同的步驟，對佇列範圍指派角色： 

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的儲存體帳戶，並顯示帳戶的**概觀**。
1. 在 [服務] 下，選取 [Blob]。 
1. 找出您要為其指派角色的容器，並顯示容器的設定。 
1. 選取 [存取控制 (IAM)]，以顯示容器的存取控制設定。 選取 [角色指派] 索引標籤，以查看角色指派的清單。

    ![显示容器访问控制设置的屏幕截图](media/storage-auth-aad-rbac-portal/portal-access-control-container.png)

1. 按一下 [新增角色指派] 按鈕新增角色。
1. 在“添加角色分配”窗口中，选择要分配的 Azure 存储角色。 然后通过搜索找到要为其分配该角色的安全主体。

    ![顯示如何指派 RBAC 角色的螢幕擷取畫面](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. 按一下 [檔案] 。 您對其指派角色的身分識別會出現在該角色下方。 如下圖所示，新增的使用者現在有權對名為 sample-container 容器讀取資料。

    ![显示已分配到某个角色的用户列表的屏幕截图](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

您可以依照類似的步驟，若要將範圍設定至儲存體帳戶、 資源群組或訂用帳戶的角色指派。

> [!NOTE]
> 身為 Azure 儲存體帳戶擁有者的您，並不會自動收到指派的資料存取權限。 您必須明確地將 Azure 儲存體的 RBAC 角色指派給自己。 您可以在訂用帳戶、資源群組、儲存體帳戶或容器/佇列層級上指派此角色。
> 
> 您無法指派範圍設定至容器或佇列儲存體帳戶是否已啟用階層式命名空間的角色。

### <a name="assign-the-reader-role-for-portal-access"></a>分配“读取者”角色以访问门户

将 Azure 存储的内置或自定义角色分配到某个安全主体时，会向该安全主体授予权限，以便针对存储帐户中的数据执行操作。 内置的“数据读取者”角色提供对容器或队列中的数据的读取权限，而内置的“数据参与者”角色提供对容器或队列的读取、写入和删除权限。 权限范围限定为指定的资源。  

比方說，如果您指派**儲存體 Blob 資料參與者**角色，以使用者層級的容器，名為 Mary **sample container>**、 然後 Mary 授與讀取、 寫入和刪除所有的 blob 中的存取權該容器中。

但是，如果 Mary 希望在 Azure 门户中查看某个 Blob，“存储 Blob 数据参与者”角色本身无法提供足够的权限用于在门户中导航，因此 Mary 无法查看该 Blob。 必须拥有其他 Azure AD 权限才能在门户中导航和查看门户中显示的其他资源。

如果用户需要在 Azure 门户中访问 Blob，请在存储帐户或更高的级别向这些用户分配一个额外的 RBAC 角色：[读取者](../../role-based-access-control/built-in-roles.md#reader)角色。 “读取者”角色是一个 Azure 资源管理器角色，可让用户查看存储帐户资源，但不允许修改这些资源。 该角色不提供对 Azure 存储中的数据的读取权限，而只提供对帐户管理资源的读取权限。

请遵循以下步骤分配“读取者”角色，使用户能够在 Azure 门户中访问 Blob。 在此示例中，分配范围限定为存储帐户：

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的儲存體帳戶。
1. 选择“访问控制(IAM)”以显示存储帐户的访问控制设置。 選取 [角色指派] 索引標籤，以查看角色指派的清單。
1. 在“添加角色分配”窗口中，选择“读取者”角色。 
1. 在“将访问权限分配给”字段中，选择“Azure AD 用户、组或服务主体”。
1. 通过搜索找到要为其分配该角色的安全主体。
1. 保存角色分配。

> [!NOTE]
> 只有必要对需要使用 Azure 门户访问 Blob 或队列的用户分配“读取者”角色。 

## <a name="next-steps"></a>後續步驟

- 有关存储资源的 RBAC 角色的详细信息，请参阅[使用 Azure Active Directory 验证对 Azure Blob 和队列的访问](storage-auth-aad.md)。 
- 若要深入了解 RBAC，請參閱[什麼是角色型存取控制 (RBAC)？](../../role-based-access-control/overview.md)。
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 來指派和管理 RBAC 角色指派，請參閱下列文章：
    - [使用 Azure PowerShell 管理角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-powershell.md)
    - [使用 Azure CLI 管理角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-rest.md)
- 若要深入了解如何從儲存體應用程式內授權容器和佇列的存取權，請參閱[使用 Azure AD 與 Azure 儲存體應用程式](storage-auth-aad-app.md)。
