---
title: 使用 Azure 入口網站匯出 Azure Resource Manager 範本
description: 您可以使用 Azure 入口網站，從您的訂用帳戶中的資源匯出 Azure Resource Manager 範本。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: tomfitz
ms.openlocfilehash: e482bf99013b9bec9dfbf64c4e8ad5a8a43ff540
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296313"
---
# <a name="single-and-multi-resource-export-to-template-in-azure-portal"></a>單一和多重資源匯出至 Azure 入口網站中的範本

若要協助您建立 Azure Resource Manager 範本，您可以從現有資源匯出範本。 匯出的範本可協助您了解的 JSON 語法和部署資源的屬性。 若要自動化未來的部署，開始匯出的範本並修改您的案例。

Resource Manager 可讓您選擇匯出至範本的一或多個資源。 您可以專注於完全在範本中所需的資源。

這篇文章會示範如何匯出範本，透過入口網站。 您也可以使用[Azure CLI](manage-resource-groups-cli.md#export-resource-groups-to-templates)， [Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates)，或[REST API](/rest/api/resources/resourcegroups/exporttemplate)。

## <a name="choose-the-right-export-option"></a>選擇正確的匯出選項

有兩種方式可以匯出範本：

* **從資源群組或資源匯出**。 此選項會產生新的範本，從現有的資源。 匯出的範本是資源群組的目前狀態 「 快照集 」。 您可以匯出整個資源群組或該資源群組內的特定資源。

* **部署之前，或從歷程記錄匯出**。 此選項會擷取用於部署範本的完全相同複本。

視您選擇的選項，匯出的範本會有不同的品質。

| 從資源群組或資源 | 部署之前，或從歷程記錄 |
| --------------------- | ----------------- |
| 範本快照集的資源的目前狀態。 它包含您在部署後所做的任何手動變更。 | 範本只會在部署時顯示資源的狀態。 任何手動變更您之後部署不會包含在內。 |
| 您可以從 要匯出的資源群組，以選取哪些資源。 | 會包含特定部署的所有資源。 您無法挑選這些資源的子集，或新增已新增在不同時間的資源。 |
| 範本包含的資源，包括一些屬性，您通常不會在部署期間設定的所有屬性。 您可能想要移除或重複使用範本之前清除這些屬性。 | 範本包含只針對部署所需的屬性。 範本是已準備好可使用。 |
| 範本可能不包含所有您需要以重複使用的參數。 大部分的屬性值會硬式編碼在範本中。 若要重新部署的範本，在其他環境中，您需要加入參數，增加的能力，以設定的資源。 | 範本包含可讓您輕鬆地重新部署在不同環境中的參數。 |

從資源群組或資源，匯出範本時：

* 您需要擷取資源的原始部署之後所做的變更。
* 您想要選取哪些資源會匯出。

匯出的範本部署之前，或從歷程記錄中，當：

* 您想要輕鬆重複範本。
* 您不需要包含在原始部署之後，您所做的變更。

## <a name="export-template-from-resource-group"></a>從資源群組匯出範本

若要從資源群組匯出一或多個資源：

1. 選取包含您想要匯出的資源的資源群組。

1. 若要匯出的資源群組中的所有資源，請選取所有然後**都匯出範本**。 **匯出範本**選項才會啟用之後，您已選取至少一個資源。

   ![匯出所有的資源](./media/export-template-portal/select-all-resources.png)

1. 若要挑選特定資源的匯出，請選取那些資源旁邊的核取方塊。 然後，選取**匯出範本**。

   ![選取要匯出資源](./media/export-template-portal/select-resources.png)

1. 匯出的範本會顯示，並可供下載。

   ![顯示範本](./media/export-template-portal/show-template.png)

## <a name="export-template-from-resource"></a>從資源匯出範本

若要匯出一個資源：

1. 選取包含您想要匯出的資源的資源群組。

1. 選取要匯出的資源。

   ![選取資源](./media/export-template-portal/select-link-resource.png)

1. 該資源，請選取**匯出範本**的左窗格中。

   ![匯出資源](./media/export-template-portal/export-single-resource.png)

1. 匯出的範本會顯示，並可供下載。 範本只包含單一資源。

## <a name="export-template-before-deployment"></a>匯出範本，再進行部署

1. 選取您想要部署的 Azure 服務。

1. 填入新的服務值。

1. 之後通過驗證，而開始部署之前，請選取**下載自動化的範本**。

   ![下載範本](./media/export-template-portal/download-before-deployment.png)

1. 此範本會顯示，並可供下載。

   ![顯示範本](./media/export-template-portal/show-template-before-deployment.png)

## <a name="export-template-after-deployment"></a>在部署後的 匯出範本

您可以匯出的範本，用來部署現有的資源。 您所取得的範本是完全部署所用的。

1. 選取您想要匯出的資源群組。

1. 選取下方的連結**部署**。

   ![選取部署歷程記錄](./media/export-template-portal/select-deployment-history.png)

1. 您可以選取其中一個部署從部署歷程記錄。

   ![選取部署](./media/export-template-portal/select-details.png)

1. 選取 **範本**。 用於此部署的範本會顯示，並可供下載。

   ![選取範本](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>後續步驟

- 了解如何使用匯出範本[Azure CLI](manage-resource-groups-cli.md#export-resource-groups-to-templates)， [Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates)，或[REST API](/rest/api/resources/resourcegroups/exporttemplate)。
- 若要深入了解 Resource Manager 範本語法，請參閱[了解的結構和 Azure Resource Manager 範本的語法](./resource-group-authoring-templates.md)。
- 若要了解如何開發的範本，請參閱[逐步教學課程](/azure/azure-resource-manager/)。
- 若要檢視 Azure Resource Manager 範本結構描述，請參閱[範本參考](/azure/templates/)。