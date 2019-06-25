---
title: 使用 Azure 入口網站來管理 Azure 資源管理員群組 |Microsoft Docs
description: 使用 Azure 入口網站來管理您的 Azure Resource Manager 群組。
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: bc3c1a05c64edea260bd177dd7eaefc003db5310
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296296"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>使用 Azure 入口網站來管理 Azure Resource Manager 資源群組

了解如何使用[Azure 入口網站](https://portal.azure.com)具有[Azure Resource Manager](resource-group-overview.md)來管理您的 Azure 資源群組。 如需管理 Azure 資源，請參閱[使用 Azure 入口網站管理 Azure 資源](./manage-resources-portal.md)。

關於管理資源群組的其他文章：

- [使用 Azure CLI 管理 Azure 資源群組](./manage-resources-cli.md)
- [使用 Azure PowerShell 管理 Azure 資源群組](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>什麼是資源群組

資源群組是存放 Azure 方案相關資源的容器。 資源群組可以包含方案的所有資源，或只包含您要以群組方式管理的資源。 您可決定如何根據對組織最有利的方式，將資源配置到資源群組。 一般而言，會新增共用相同生命週期的資源到相同資源群組，因此您可以以群組為單位輕鬆地部署、更新、刪除它們。

資源群組會儲存資源相關中繼資料。 因此，當您指定資源群組的位置時，您便是指定中繼資料的儲存位置。 基於相容性理由，您可能需要確保您的資料存放在特定區域中。

資源群組會儲存資源相關中繼資料。 當您指定的資源群組的位置時，您指定儲存中繼資料。

## <a name="create-resource-groups"></a>建立資源群組

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取**資源群組**

    ![新增資源群組](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. 選取 [新增]  。
4. 輸入下列值：

   - 訂用帳戶  ：選取 Azure 訂用帳戶。 
   - **资源组**：輸入新的資源群組名稱。 
   - **區域**：選取 Azure 位置，例如**美國中部**。

     ![建立資源群組](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. 選取**檢閱 + 建立**
6. 選取 [建立]  。 需要幾秒鐘才會建立資源群組。
7. 選取 **重新整理**從上方的功能表，以重新整理 資源群組 清單中，然後選取 新建立的資源群組，以開啟它。 或選取**通知**（鈴鐺圖示） 從上方，然後選取**移至資源群組**以開啟新建立的資源群組

    ![移至資源群組](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>列出資源群組

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 若要列出資源群組，請選取**資源群組**

    ![瀏覽資源群組](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. 若要自訂針對資源群組顯示的資訊，請選取**編輯資料行**。 下列螢幕擷取畫面顯示新增的資料行，您無法新增到顯示：

## <a name="open-resource-groups"></a>開啟資源群組

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [資源群組]  。
3. 選取您想要開啟的資源群組。

## <a name="delete-resource-groups"></a>刪除資源群組

1. 開啟您想要刪除的資源群組。  請參閱[開啟 資源群組](#open-resource-groups)。
2. 選取 [刪除資源群組]  。

    ![刪除 azure 資源群組](./media/manage-resource-groups-portal/delete-group.png)

如需 Azure Resource Manager 如何排序資源的刪除作業的詳細資訊，請參閱[Azure Resource Manager 資源群組刪除](./resource-group-delete.md)。

## <a name="deploy-resources-to-a-resource-group"></a>將資源部署至資源群組

建立 Resource Manager 範本之後，您可以使用 Azure 入口網站來部署您的 Azure 資源。 建立範本，請參閱[快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](./resource-manager-quickstart-create-templates-use-the-portal.md)。 部署範本，使用入口網站，請參閱[使用 Resource Manager 範本與 Azure 入口網站部署資源](resource-group-template-deploy-portal.md)。

## <a name="move-to-another-resource-group-or-subscription"></a>移至另一個資源群組或訂用帳戶

您可以移動的資源群組中，另一個資源群組。 如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](resource-group-move-resources.md)。

## <a name="lock-resource-groups"></a>鎖定資源群組

鎖定可防止不小心刪除或修改重要資源，例如 Azure 訂用帳戶、 資源群組或資源組織中的其他使用者。 

1. 開啟您想要刪除的資源群組。  請參閱[開啟 資源群組](#open-resource-groups)。
2. 在左窗格中，選取**鎖定**。
3. 若要加入的資源群組中的鎖定，請選取**新增**。
4. 請輸入**鎖定名稱**，**鎖定類型**，並**備忘稿**。 鎖定類型包括**唯讀**，並**刪除**。

    ![鎖定 azure 資源群組](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

如需詳細資訊，請參閱 <<c0> [ 鎖定資源以防止非預期的變更](./resource-group-lock-resources.md)。

## <a name="tag-resource-groups"></a>標記資源群組

您可以將標籤套用至資源群組和資源，以便以邏輯方式組織您的資產。 如需資訊，請參閱[使用標記來組織您的 Azure 資源](./resource-group-using-tags.md#portal)。

## <a name="export-resource-groups-to-templates"></a>將資源群組匯出範本

匯出範本的相關資訊，請參閱[單一和多重資源匯出至範本-入口網站](export-template-portal.md)。

## <a name="manage-access-to-resource-groups"></a>管理資源群組的存取權

[角色型存取控制 (RBAC)](../role-based-access-control/overview.md) 是您對 Azure 中的資源存取進行管理的機制。 如需詳細資料，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure Resource Manager，請參閱[Azure Resource Manager 概觀](./resource-group-overview.md)。
- 若要深入了解 Resource Manager 範本語法，請參閱[了解的結構和 Azure Resource Manager 範本的語法](./resource-group-authoring-templates.md)。
- 若要了解如何開發的範本，請參閱[逐步教學課程](/azure/azure-resource-manager/)。
- 若要檢視 Azure Resource Manager 範本結構描述，請參閱[範本參考](/azure/templates/)。