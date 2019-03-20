---
title: 使用 Azure 入口網站來管理 Azure 資源管理員群組 |Microsoft Docs
description: 使用 Azure 门户管理 Azure 资源管理器组。
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
ms.openlocfilehash: cbbc710c9408c84c601d7b9eb9560641b5313a05
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090928"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>使用 Azure 门户管理 Azure 资源管理器资源组

了解如何将 [Azure 门户](https://portal.azure.com)与 [Azure 资源管理器](resource-group-overview.md)配合使用来管理 Azure 资源组。 若要管理 Azure 资源，请参阅[使用 Azure 门户管理 Azure 资源](./manage-resources-portal.md)。

有关资源组管理的其他文章：

- [使用 Azure CLI 管理 Azure 资源组](./manage-resources-cli.md)
- [使用 Azure PowerShell 管理 Azure 资源组](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>什么是资源组

資源群組是存放 Azure 方案相關資源的容器。 资源组可以包含解决方案的所有资源，也可以只包含想要作为组来管理的资源。 您可決定如何根據對組織最有利的方式，將資源配置到資源群組。 一般而言，會新增共用相同生命週期的資源到相同資源群組，因此您可以以群組為單位輕鬆地部署、更新、刪除它們。

資源群組會儲存資源相關中繼資料。 因此，當您指定資源群組的位置時，您便是指定中繼資料的儲存位置。 基於相容性理由，您可能需要確保您的資料存放在特定區域中。

資源群組會儲存資源相關中繼資料。 指定资源组的位置时，也会指定元数据的存储位置。

## <a name="create-resource-groups"></a>建立資源群組

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 选择“资源组”

    ![新增資源群組](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. 選取 [新增] 。
4. 輸入下列值：

   - 訂用帳戶：選取 Azure 訂用帳戶。 
   - **资源组**：输入新的资源组名称。 
   - **區域**：選取 Azure 位置，例如**美國中部**。

     ![建立資源群組](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. 选择“查看 + 创建”
6. 選取 [建立] 。 创建资源组需要几秒钟的时间。
7. 在顶部菜单中选择“刷新”以刷新资源组列表，然后选择新建的资源组将其打开。 或者，选择顶部的“通知”（钟形图标），然后选择“转到资源组”打开新建的资源组

    ![转到资源组](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>列出資源群組

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 若要列出资源组，请选择“资源组”

    ![瀏覽資源群組](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. 若要自定义显示的资源组信息，请选择“编辑列”。 以下屏幕截图显示了可添加到画面中的附加列：

## <a name="open-resource-groups"></a>打开资源组

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [資源群組]。
3. 选择要打开的资源组。

## <a name="delete-resource-groups"></a>删除资源组

1. 打开要删除的资源组。  参阅[打开资源组](#open-resource-groups)。
2. 選取 [刪除資源群組]。

    ![删除 Azure 资源组](./media/manage-resource-groups-portal/delete-group.png)

若要详细了解 Azure 资源管理器如何控制资源的删除，请参阅 [Azure 资源管理器资源组的删除](./resource-group-delete.md)。

## <a name="deploy-resources-to-a-resource-group"></a>将资源部署到资源组

创建资源管理器模板后，可以使用 Azure 门户部署 Azure 资源。 有关如何创建模板，请参阅[快速入门：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](./resource-manager-quickstart-create-templates-use-the-portal.md)。 有关如何使用门户部署模板，请参阅[使用资源管理器模板和 Azure 门户部署资源](resource-group-template-deploy-portal.md)。

## <a name="move-to-another-resource-group-or-subscription"></a>移到另一个资源组或订阅

可将组中的资源移到另一个资源组。 如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](resource-group-move-resources.md)。

## <a name="lock-resource-groups"></a>锁定资源组

锁定可以防止组织中的其他用户意外删除或修改关键资源，例如 Azure 订阅、资源组或资源。 

1. 打开要删除的资源组。  参阅[打开资源组](#open-resource-groups)。
2. 在左窗格中选择“锁定”。
3. 若要将锁添加到资源组，请选择“添加”。
4. 输入“锁定名”、“锁类型”和“备注”。 锁类型包括“只读”和“删除”。

    ![锁定 Azure 资源组](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

有关详细信息，请参阅[锁定资源以防止意外更改](./resource-group-lock-resources.md)。

## <a name="tag-resource-groups"></a>标记资源组

您可以將標籤套用至資源群組和資源，以便以邏輯方式組織您的資產。 有关信息，请参阅[使用标记组织 Azure 资源](./resource-group-using-tags.md#portal)。

## <a name="export-resource-groups-to-templates"></a>将资源组导出到模板

成功设置资源组后，可能需要查看资源组的资源管理器模板。 匯出此範本有兩個優點︰

- 由于模板包含整个基础结构，因此将来可以自动完成解决方案的部署。
- 通过查看代表解决方案的 JavaScript 对象表示法 (JSON)，了解模板语法。

有兩種方式可以匯出範本：

- 可以导出用于部署的实际模板。 匯出的範本包含與原始範本完全相同的所有參數和變數。 如果您透過入口網站部署資源，而且想要知道範本如何建立這些資源，則這種方法十分有用。 此範本立即可用。 
- 可以导出已生成的表示资源组当前状态的模板。 匯出的範本不是以任何用於部署的範本為基礎。 反而，它所建立的範本是資源群組的「快照集」或「備份」。 匯出的範本會有許多硬式編碼值，但數量可能不如您通常會定義的參數數量。 使用此選項來將資源重新部署至相同的資源群組。 若要對其他資源群組使用此範本，您可能必須大幅修改它。

### <a name="export-templates-from-deployment-history"></a>从部署历史记录导出模板

此方法导出特定部署的模板。 如果已在门户中更改了资源，或者在多个部署中添加/删除了资源，请参阅[从资源组导出模板](#export-templates-from-resource-groups)。

1. 打开要删除的资源组。  参阅[打开资源组](#open-resource-groups)。
2. 在左窗格中选择“部署”，或者选择“部署”下的链接。  以下屏幕截图显示**有 4 项操作成功**，因为其中显示了四个具有不同部署名称的独立部署。 你可能会看到**有 1 项操作成功**。

    ![Azure 资源组 - 导出模板](./media/manage-resource-groups-portal/manage-resource-groups-export-templates-deployment-history.png)

3. 从列表中选择一个部署。
4. 在左窗格中选择“模板”。 Resource Manager 會為您擷取下列六個檔案：

   - **範本** - 用於定義解決方案之基礎結構的範本。 當您透過入口網站建立儲存體帳戶時，Resource Manager 會使用範本來部署它，並且儲存該範本供日後參考。
   - **參數** - 您可以在部署期間用來傳入值的參數檔案。 它會包含您在第一次部署期間所提供的值。 當您重新部署範本時，即可變更所有這些值。
   - **CLI** - 您可以為部署範本而使用的 Azure CLI 指令碼檔案。
   - **PowerShell** - 您可以為了部署範本而使用的 Azure PowerShell 指令碼檔案。
   - **.NET** - 您可以為了部署範本而使用的 .NET 類別。
   - **Ruby** - 您可以為了部署範本而使用的 Ruby 類別。

     根據預設，入口網站會顯示範本。

5. 选择“下载”，将模板导出到本地计算机。

    ![Azure 资源组 - 导出模板](./media/manage-resource-groups-portal/manage-resource-groups-export-templates-deployment-history-download.png)

<a name="export-templates-from-resource-groups"></a>
### <a name="export-templates-from-resource-groups"></a>从资源组导出模板

如果在门户中更改了资源，或者在多个部署中添加/删除了资源，则从部署历史记录中检索模板不能反映资源组的当前状态。 本節說明您如何匯出反映資源群組目前狀態的範本。 其目的在作為資源群組的快照集，以供您用來重新部署至相同的資源群組。 若要對其他解決方案使用所匯出的範本，您必須大幅修改它。

1. 打开要删除的资源组。  参阅[打开资源组](#open-resource-groups)。
2. 在左窗格中选择“自动化脚本”。 Resource Manager 會為您擷取下列六個檔案：

   - **範本** - 用於定義解決方案之基礎結構的範本。 當您透過入口網站建立儲存體帳戶時，Resource Manager 會使用範本來部署它，並且儲存該範本供日後參考。
   - **參數** - 您可以在部署期間用來傳入值的參數檔案。 它會包含您在第一次部署期間所提供的值。 當您重新部署範本時，即可變更所有這些值。
   - **CLI** - 您可以為部署範本而使用的 Azure CLI 指令碼檔案。
   - **PowerShell** - 您可以為了部署範本而使用的 Azure PowerShell 指令碼檔案。
   - **.NET** - 您可以為了部署範本而使用的 .NET 類別。
   - **Ruby** - 您可以為了部署範本而使用的 Ruby 類別。

     根據預設，入口網站會顯示範本。
3. 选择“下载”，将模板导出到本地计算机。

导出的某些模板需要经过一些编辑才能使用。 若要了解如何开发模板，请参阅[分步教程](/azure/azure-resource-manager/)。

### <a name="export-template-before-deploying"></a>在部署之前导出模板

可以使用门户定义资源。  在部署资源之前，可以查看和导出模板。 如需指示，請參閱[快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](./resource-manager-quickstart-create-templates-use-the-portal.md)。

### <a name="fix-export-issues"></a>修正匯出問題

並非所有的資源類型都支援匯出範本功能。 從資源群組 (而非部署歷程記錄) 匯出時，您只會看到匯出問題。 如果上一次部署精確地表示資源群組的目前狀態，您應該從部署歷程記錄 (而非資源群組) 匯出範本。 只有在變更未定義於單一範本中的資源群組時，才能從資源群組匯出。

若要解決匯出問題，請手動將遺漏的資源新增回您的範本。 此錯誤訊息包含無法匯出的資源類型。 在[範本參考](/azure/templates/)中尋找該資源類型。 例如，若要手動新增虛擬網路閘道，請參閱 [Microsoft.Network/virtualNetworkGateways 範本參考](/azure/templates/microsoft.network/virtualnetworkgateways)。 範本參考會提供用來將資源新增至範本的 JSON 給您。

取得 JSON 格式的資源後，您必須取得資源值。 在資源類型的 REST API 中使用 GET 作業，即可看到資源的值。 例如，若要取得虛擬網路閘道的值，請參閱[虛擬網路閘道 - Get](/rest/api/network-gateway/virtualnetworkgateways/get)。

## <a name="manage-access-to-resource-groups"></a>管理对资源组的访问

[角色型存取控制 (RBAC)](../role-based-access-control/overview.md) 是您對 Azure 中的資源存取進行管理的機制。 如需詳細資料，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>後續步驟

- 若要了解 Azure 资源管理器，请参阅 [Azure 资源管理器概述](./resource-group-overview.md)。
- 若要了解资源管理器模板语法，请参阅[了解 Azure 资源管理器模板的结构和语法](./resource-group-authoring-templates.md)。
- 若要了解如何开发模板，请参阅[分步教程](/azure/azure-resource-manager/)。
- 若要查看 Azure 资源管理器模板架构，请参阅[模板参考](/azure/templates/)。