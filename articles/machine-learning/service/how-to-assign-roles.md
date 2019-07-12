---
title: 管理 Azure Machine Learning 工作區中的角色
titleSuffix: Azure Machine Learning service
description: 了解如何存取 Azure 機器學習服務工作區中使用角色型存取控制 (RBAC)。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 4a5723b2ffbd34fd7cf022bf747b4504b3bdac53
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797667"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>管理 Azure Machine Learning 工作區的存取權

在本文中，您將了解如何管理 Azure Machine Learning 工作區的存取權。 [角色型存取控制 (RBAC)](/azure/role-based-access-control/overview)用來管理 Azure 資源的存取權。 Azure Active Directory 中的使用者指派特定的角色，授與資源的存取權。 Azure 提供內建角色和建立自訂角色的能力。

## <a name="default-roles"></a>預設角色

Azure Machine Learning 工作區是 Azure 資源。 如同其他 Azure 資源，建立新的 Azure Machine Learning 工作區時，它是三個預設角色。 您可以將使用者新增至工作區，並將它們指派給其中一個內建的角色。

| Role | 存取層級 |
| --- | --- |
| **讀取者** | 在工作區的唯讀狀態動作。 讀者可以列出及檢視在工作區中的資產，但無法建立或更新這些資產。 |
| **參與者** | 檢視、 建立、 編輯或刪除 （如果適用） 工作區中的資產。 比方說，參與者可以建立實驗、 建立或附加的計算叢集、 提交執行，以及部署 web 服務。 |
| **擁有者** | 完整存取權工作區，包括能夠檢視、 建立、 編輯或刪除 （如果適用） 工作區中的資產。 此外，您可以變更角色指派。 |

> [!IMPORTANT]
> 在 Azure 中的多個層級範圍可以設定角色存取權。 比方說，到工作區的擁有者存取的人可能沒有擁有者存取權包含的工作區的資源群組。 如需詳細資訊，請參閱 <<c0> [ 如何 RBAC 一起運作](/azure/role-based-access-control/overview#how-rbac-works)。

如需有關特定內建角色的詳細資訊，請參閱[適用於 Azure 的內建角色](/azure/role-based-access-control/built-in-roles)。

## <a name="manage-workspace-access"></a>管理工作區的存取

如果您的工作區擁有者，您可以新增和移除工作區的角色。 您也可以指派角色給使用者。 使用下列連結以了解如何管理存取權：
- [Azure 入口網站 UI](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Azure 資源管理員範本](/azure/role-based-access-control/role-assignments-template)

如果您已安裝[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md)，您也可以使用 CLI 命令來指派角色給使用者。

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user`欄位是工作區父訂用帳戶所在的 Azure Active Directory 執行個體中現有使用者的電子郵件地址。 如何使用此命令的範例如下：

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>建立自訂角色

如果內建角色不敷使用，您可以建立自訂角色。 自訂的角色可能會具有讀取、 寫入、 刪除和計算資源的權限，該工作區中。 您可以提供角色在特定的工作區層級、 特定的資源群組層級或特定的訂用帳戶層級。

> [!NOTE]
> 您必須是層級資源的擁有者，才能建立該資源中的自訂角色。

若要建立自訂角色，請先建構，指定的權限和角色領域中的角色定義 JSON 檔案。 下列範例會定義名為 「 資料科學家 「 範圍限定在特定的工作區層級的自訂角色：

`data_scientist_role.json` :
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

您可以變更`AssignableScopes`欄位，以在訂用帳戶層級、 資源群組層級或特定工作區層級設定此自訂角色的範圍。

這個自訂角色可以執行下列動作除外工作區中的所有項目：

- 無法建立或更新的計算資源。
- 它不能刪除的計算資源。
- 它不能加入、 刪除或修改角色指派。
- 它無法刪除工作區。

若要部署這個自訂角色，使用下列 Azure CLI 命令：

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

在部署後，此角色中指定的工作區則會變成可用。 現在您可以新增並指派此角色在 Azure 入口網站中。 或者，您可以指派此角色給使用者使用`az ml workspace share`CLI 命令：

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


如需詳細資訊，請參閱 <<c0> [ 適用於 Azure 資源的自訂角色](/azure/role-based-access-control/custom-roles)。

## <a name="next-steps"></a>後續步驟

- [企業安全性概觀](concept-enterprise-security.md)
- [安全地執行實驗和在虛擬網路內的推斷/分數](how-to-enable-virtual-network.md)
- [教學課程：將模型定型](tutorial-train-models-with-aml.md)
