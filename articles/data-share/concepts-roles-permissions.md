---
title: 角色和 Azure 的資料共用 Preview 的需求
description: 角色和 Azure 的資料共用 Preview 的需求
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: a5d70b9aa611b4f939cb46b5d25655edd818cb35
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807528"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>角色和 Azure 的資料共用 Preview 的需求

這篇文章描述的共用資料使用 Azure 的資料共用 Preview 也接受和接收資料，使用 Azure 的資料共用 Preview 所需的角色。 

## <a name="roles-and-requirements"></a>角色和需求

若要共用，或接收資料，使用 Azure 的資料共用，您用以登入 Azure 的使用者帳戶必須是能夠授與共用資料的儲存體帳戶，您要共用的資料，或接收資料的權限。 這通常是存在於的權限**擁有者**角色或指派 Microsoft.Authorization/role 指派/寫入權限的自訂角色。 

若要共用，或至 Azure 儲存體帳戶或從中接收資料，您必須是儲存體帳戶的擁有者。 即使您已建立的儲存體帳戶，這不會自動授與您的儲存體帳戶的擁有權。 若要將在您自己新增至您的 Azure 儲存體帳戶的擁有者角色，請遵循下列步驟。

1. 瀏覽至 Azure 入口網站中的儲存體帳戶
1. 選取**存取控制 (IAM)**
1. 按一下 [新增] 
1. 在您自己新增為擁有者

若要檢視您在訂用帳戶中擁有的權限，請在 Azure 入口網站中選取右上角的使用者名稱，然後選取 [權限]  。 如果您有多個訂用帳戶的存取權，請選取適當的訂用帳戶。 

## <a name="resource-provider-registration"></a>資源提供者註冊 

當接受 Azure 的資料共用邀請，您必須以手動方式註冊的 Microsoft.DataShare 資源提供者訂用帳戶。 遵循下列步驟來註冊到您 Azure 訂用帳戶的 Microsoft.DataShare 資源提供者。 

1. 在 Azure 入口網站中，瀏覽至**訂用帳戶**
1. 選取您用於 Azure 的資料共用的訂用帳戶
1. 按一下**資源提供者**
1. Microsoft.DataShare 搜尋
1. 按一下 [註冊] 

## <a name="next-steps"></a>後續步驟

- 深入了解 Azure 中的角色 - [了解角色定義](../role-based-access-control/role-definitions.md)

