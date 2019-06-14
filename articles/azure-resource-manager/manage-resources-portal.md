---
title: 使用 Azure 入口網站管理 Azure 資源 |Microsoft Docs
description: 使用 Azure 入口網站和 Azure Resource Manager 來管理您的資源。
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 20bf38b87ce29f8506a5611ecd25cf38f6d4ed61
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60550640"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>使用 Azure 入口網站管理 Azure 資源

了解如何使用[Azure 入口網站](https://portal.azure.com)具有[Azure Resource Manager](resource-group-overview.md)來管理您的 Azure 資源。 如需管理資源群組，請參閱[使用 Azure 入口網站管理 Azure 資源群組](./manage-resource-groups-portal.md)。

關於管理資源的其他文章：

- [使用 Azure CLI 管理 Azure 資源](./manage-resources-cli.md)
- [使用 Azure PowerShell 管理 Azure 資源](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>將資源部署至資源群組

建立 Resource Manager 範本之後，您可以使用 Azure 入口網站來部署您的 Azure 資源。 建立範本，請參閱[快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](./resource-manager-quickstart-create-templates-use-the-portal.md)。 部署範本，使用入口網站，請參閱[使用 Resource Manager 範本與 Azure 入口網站部署資源](resource-group-template-deploy-portal.md)。

## <a name="open-resources"></a>開啟資源

由 Azure 服務和資源群組，會組織 azure 資源。 下列程序示範如何開啟儲存體帳戶名**mystorage0207**。 虛擬機器都位於名的資源群組**mystorage0207rg**。

若要開啟 服務類型的資源：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左窗格中，選取 Azure 服務。 在此情況下，**儲存體帳戶**。  如果您沒有看到列出的服務，請選取**所有服務**，然後選取 服務類型。

    ![在入口網站中開啟 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. 選取您想要開啟的資源。

    ![在入口網站中開啟 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    儲存體帳戶看起來像：

    ![在入口網站中開啟 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

若要開啟資源的資源群組：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左窗格中，選取**資源群組**列出的資源群組內。
3. 選取您想要開啟的資源。 

## <a name="manage-resources"></a>管理資源

在入口網站中檢視資源時，您會看到用於管理特定資源的選項。

![管理 Azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

螢幕擷取畫面顯示 Azure 虛擬機器的管理選項。 您可以執行作業，例如啟動、 重新啟動和停止虛擬機器。

## <a name="delete-resources"></a>刪除資源

1. 在入口網站中開啟資源。 如需步驟，請參閱[開啟資源](#open-resources)。
2. 選取 [刪除]  。 下列螢幕擷取畫面顯示虛擬機器的管理選項。

    ![刪除 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. 輸入要確認刪除，然後選取 資源名稱**刪除**。

如需 Azure Resource Manager 如何排序資源的刪除作業的詳細資訊，請參閱[Azure Resource Manager 資源群組刪除](./resource-group-delete.md)。

## <a name="move-resources"></a>移動資源

1. 在入口網站中開啟資源。 如需步驟，請參閱[開啟資源](#open-resources)。
2. 選取 [移動]  。 下列螢幕擷取畫面顯示儲存體帳戶的管理選項。

    ![移動 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. 選取 **移至另一個資源群組**或是**Moeve 到另一個訂用帳戶**根據您的需求。

如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](resource-group-move-resources.md)。

## <a name="lock-resources"></a>鎖定資源

鎖定可防止不小心刪除或修改重要資源，例如 Azure 訂用帳戶、 資源群組或資源組織中的其他使用者。 

1. 在入口網站中開啟資源。 如需步驟，請參閱[開啟資源](#open-resources)。
2. 選取 **鎖定**。 下列螢幕擷取畫面顯示儲存體帳戶的管理選項。

    ![鎖定 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. 選取 **新增**，然後指定鎖定屬性。

如需詳細資訊，請參閱[使用 Azure Resource Manager 來鎖定資源](resource-group-lock-resources.md)。

## <a name="tag-resources"></a>標記資源

標記以邏輯方式組織您的資源群組和資源的協助。 

1. 在入口網站中開啟資源。 如需步驟，請參閱[開啟資源](#open-resources)。
2. 選取 [標記]  。 下列螢幕擷取畫面顯示儲存體帳戶的管理選項。

    ![標記 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. 指定標記的屬性，然後按**儲存**。

如需資訊，請參閱[使用標記來組織您的 Azure 資源](./resource-group-using-tags.md#portal)。

## <a name="monitor-resources"></a>監視資源

當您開啟資源時，入口網站會顯示預設圖形和資料表，以便監視該資源類型。 下列螢幕擷取畫面顯示虛擬機器的圖形：

![監視 azure 資源](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

您可以選取要釘選到儀表板圖表圖右上角的釘選圖示。 若要了解如何使用儀表板，請參閱 [Creating and sharing dashboards in the Azure portal (在 Azure 入口網站建立和共用儀表板)](../azure-portal/azure-portal-dashboards.md)。

## <a name="manage-access-to-resources"></a>管理對資源的存取

[角色型存取控制 (RBAC)](../role-based-access-control/overview.md) 是您對 Azure 中的資源存取進行管理的機制。 如需詳細資料，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure Resource Manager，請參閱[Azure Resource Manager 概觀](./resource-group-overview.md)。
- 若要深入了解 Resource Manager 範本語法，請參閱[了解的結構和 Azure Resource Manager 範本的語法](./resource-group-authoring-templates.md)。
- 若要了解如何開發的範本，請參閱[逐步教學課程](/azure/azure-resource-manager/)。
- 若要檢視 Azure Resource Manager 範本結構描述，請參閱[範本參考](/azure/templates/)。