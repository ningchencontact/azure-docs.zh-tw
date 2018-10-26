---
title: 如何設定 Azure Functions 執行階段目標版本
description: Azure Functions 支援多個執行階段版本。 了解如何指定 Azure 中裝載之函式應用程式的執行階段版本。
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: glenga
ms.openlocfilehash: 6d89746c0a2d4642e5025789d352803195c0a3b9
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886802"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>如何設定 Azure Functions 執行階段目標版本

函式應用程式可在特定版本的 Azure Functions 執行階段中執行。 主要版本有兩種：[1.x 和 2.x](functions-versions.md)。 根據預設，所建立的函式應用程式會使用 2.x 版的執行階段。 本文說明如何在 Azure 中設定要在您選擇的版本上執行的函式應用程式。 如需如何為特定版本設定本機開發環境的相關資訊，請參閱[在本機進行 Azure Functions 的程式碼編寫和測試](functions-run-local.md)。

> [!NOTE]
> 若函式應用程式具有一或多個函式，就無法變更其執行階段版本。 請使用 Azure 入口網站來變更執行階段版本。

## <a name="automatic-and-manual-version-updates"></a>自動和手動版本更新

Functions 可讓您使用函式應用程式中的 `FUNCTIONS_EXTENSION_VERSION` 應用程式設定，鎖定執行階段的特定版本。 函式應用程式會保留在指定的主要版本上，直到您明確選擇移至新版本為止。

如果您只指定主要版本 ("~2" 代表 2.x 或 "~1" 代表 1.x)，當新版本推出時，函數應用程式會自動更新到執行階段的新次要版本。 新的次要版本不會導入重大變更。 如果您指定次要版本 (例如 "2.0.12345")，則函式應用程式會釘選到該特定版本，直到您明確地變更它。

有新版本公開發行時，入口網站會提示您向上移至該版本。 移至新版本之後，您隨時可以使用 `FUNCTIONS_EXTENSION_VERSION` 應用程式設定移回舊版。

變更執行階段版本會導致函式應用程式重新啟動。

您可以在 `FUNCTIONS_EXTENSION_VERSION` 應用程式設定中可設定以啟用自動更新的值，目前 "~1" 代表 1.x 執行階段而 "~2" 代表 2.x。

## <a name="view-and-update-the-current-runtime-version"></a>檢視並更新目前的執行階段版本

請使用下列程序，檢視函式應用程式目前使用的執行階段版本。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至函式應用程式。

1. 在 [設定的功能] 下，選擇 [函式應用程式設定]。

    ![選取函數應用程式設定](./media/set-runtime-version/add-update-app-setting.png)

1. 在 [函數應用程式設定] 索引標籤上，找到 [執行階段版本]。 請注意特定的執行階段版本和要求的主要版本。 在下列範例中，版本設定為 `~2`。

   ![選取函數應用程式設定](./media/set-runtime-version/function-app-view-version.png)

1. 若要將函式應用程式釘選至 1.x 版執行階段，請在 [執行階段版本] 下方選擇 [~1]。 應用程式中有函式時，此開關會停用。

1. 如果您已變更執行階段版本，請回到 [概觀] 索引標籤，然後選擇 [重新啟動] 來重新啟動應用程式。  函式應用程式會重新開始在 1.x 版執行階段上執行，且當您建立函式時，所使用的會是 1.x 版範本。

## <a name="view-and-update-the-runtime-version-using-azure-cli"></a>使用 Azure CLI 檢視並更新執行階段版本

您也可以從 Azure CLI 檢視並設定 `FUNCTIONS_EXTENSION_VERSION`。

>[!NOTE]
>執行階段版本可能會影響其他設定，因此請在入口網站中變更版本。 當您變更執行階段版本時，入口網站會自動進行其他所需的更新，例如 Node.js 版本和執行階段堆疊。  

在 Azure CLI 中，使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) 命令檢視目前的執行階段版本。

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

您可以使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) 命令，在函式應用程式中更新 `FUNCTIONS_EXTENSION_VERSION` 設定。

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
