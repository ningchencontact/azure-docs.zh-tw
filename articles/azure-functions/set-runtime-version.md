---
title: 如何設定 Azure Functions 執行階段目標版本
description: Azure Functions 支援多個執行階段版本。 了解如何指定 Azure 中裝載之函式應用程式的執行階段版本。
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 6f93ac7bcbd25c1b120cfeecae9dd4353524855f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230310"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>如何設定 Azure Functions 執行階段目標版本

函式應用程式可在特定版本的 Azure Functions 執行階段中執行。 There are two major versions: [1.x and 2.x](functions-versions.md), with version 3.x in preview. 根據預設，所建立的函式應用程式會使用 2.x 版的執行階段。 本文說明如何在 Azure 中設定要在您選擇的版本上執行的函式應用程式。 如需如何為特定版本設定本機開發環境的相關資訊，請參閱[在本機進行 Azure Functions 的程式碼編寫和測試](functions-run-local.md)。

## <a name="automatic-and-manual-version-updates"></a>自動和手動版本更新

Azure Functions lets you target a specific version of the runtime by using the `FUNCTIONS_EXTENSION_VERSION` application setting in a function app. 函式應用程式會保留在指定的主要版本上，直到您明確選擇移至新版本為止。

If you specify only the major version, the function app is automatically updated to new minor versions of the runtime when they become available. 新的次要版本不會導入重大變更。 如果您指定次要版本 (例如 "2.0.12345")，則函式應用程式會釘選到該特定版本，直到您明確地變更它。

> [!NOTE]
> If you pin to a specific version of Azure Functions, and then try to publish to Azure using Visual Studio, a dialog window will pop up prompting you to update to the latest version or cancel the publish. To avoid this, add the `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` property in your `.csproj` file.

有新版本公開發行時，入口網站會提示您向上移至該版本。 移至新版本之後，您隨時可以使用 `FUNCTIONS_EXTENSION_VERSION` 應用程式設定移回舊版。

The following table shows the `FUNCTIONS_EXTENSION_VERSION` values for each major version to enable automatic updates:

| 主要版本 | `FUNCTIONS_EXTENSION_VERSION` value |
| ------------- | ----------------------------------- |
| 3.x (preview) | `~3` |
| 2.x  | `~2` |
| 1.x | `~1` |

變更執行階段版本會導致函式應用程式重新啟動。

## <a name="view-and-update-the-current-runtime-version"></a>檢視並更新目前的執行階段版本

You can change the runtime version used by your function app. Because of the potential of breaking changes, you can only change the runtime version before you have created any functions in your function app. 

> [!IMPORTANT]
> 雖然執行階段版本取決於 `FUNCTIONS_EXTENSION_VERSION` 設定，但您應該在 Azure 入口網站中進行這項變更，而不是直接變更該設定。 這是因為入口網站會驗證您的變更，並視需要進行其他相關變更。

### <a name="from-the-azure-portal"></a>從 Azure 入口網站

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Using the Azure portal, you can't change the runtime version for a function app that already contains functions.

### <a name="view-and-update-the-runtime-version-using-azure-cli"></a>從 Azure CLI

您也可以從 Azure CLI 檢視並設定 `FUNCTIONS_EXTENSION_VERSION`。

>[!NOTE]
>執行階段版本可能會影響其他設定，因此請在入口網站中變更版本。 當您變更執行階段版本時，入口網站會自動進行其他所需的更新，例如 Node.js 版本和執行階段堆疊。  

在 Azure CLI 中，使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 命令檢視目前的執行階段版本。

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

在此程式碼中，以您的函式應用程式名稱取代 `<function_app>`。 還要以函式應用程式的資源群組名稱取代 `<my_resource_group>`。 

您會在下列輸出中看到 `FUNCTIONS_EXTENSION_VERSION`，為求清楚，已將輸出截斷：

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

您可以使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 命令，在函式應用程式中更新 `FUNCTIONS_EXTENSION_VERSION` 設定。

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

以函式應用程式的名稱取代 `<function_app>`。 還要以函式應用程式的資源群組名稱取代 `<my_resource_group>`。 此外，將 `<version>` 取代為有效的 1.x 執行階段版本取代為 `~2` (代表 2.x 版本)。

您可以選擇上述程式碼範例中的 [試試看]，從 [Azure Cloud Shell](../cloud-shell/overview.md) 執行此命令。 在執行 [az login](/cli/azure/reference-index#az-login) 登入之後，您也可以使用[本機 Azure CLI](/cli/azure/install-azure-cli) 來執行此命令。



## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在本機開發環境中鎖定 2.0 執行階段](functions-run-local.md)

> [!div class="nextstepaction"]
> [請參閱執行階段版本的版本資訊](https://github.com/Azure/azure-webjobs-sdk-script/releases)
