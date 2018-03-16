---
title: "如何設定 Azure Functions 執行階段目標版本"
description: "Azure Functions 支援多個執行階段版本。 了解如何指定 Azure 中裝載之函式應用程式的執行階段版本。"
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 6fc84642050f4b7acfa2e3c5b4518135d6a97171
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>如何設定 Azure Functions 執行階段目標版本

函式應用程式可在特定版本的 Azure Functions 執行階段中執行。 主要版本有兩種：[1.x 和 2.x](functions-versions.md)。 本文說明如何在 Azure 中設定要在您選擇的版本上執行的函式應用程式。 如需如何為特定版本設定本機開發環境的相關資訊，請參閱[在本機進行 Azure Functions 的程式碼編寫和測試](functions-run-local.md)。

>[!IMPORTANT]   
> Azure Functions 執行階段 2.0 為預覽版本，而且目前不支援所有 Azure Functions 功能。 如需詳細資訊，請參閱 [Azure Functions 執行階段版本概觀](functions-versions.md)。

## <a name="automatic-and-manual-version-updates"></a>自動和手動版本更新

Functions 可讓您使用函式應用程式中的 `FUNCTIONS_EXTENSION_VERSION` 應用程式設定，鎖定執行階段的特定版本。 函式應用程式會保留在指定的主要版本上，直到您明確選擇移至新版本為止。

如果您僅指定主要版本 (1.x 的 "~1" 或 2.x 的「搶鮮版 (Beta)」)，則函式應用程式會在有可用的更新時自動更新至執行階段新的次要版本。 新的次要版本不會導入重大變更。 如果您指定次要版本 (例如 "1.0.11360")，則函式應用程式會保留在該版本上，直到您明確加以變更為止。 

有新版本公開發行時，入口網站會提示您向上移至該版本。 移至新版本之後，您隨時可以使用 `FUNCTIONS_EXTENSION_VERSION` 應用程式設定移回舊版。

變更執行階段版本會導致函式應用程式重新啟動。

目前可在 `FUNCTIONS_EXTENSION_VERSION` 應用程式設定中設定用以啟用自動更新的值，分別是 1.x 執行階段的 "~1" 和 2.x 的「搶鮮版 (Beta)」。

## <a name="view-the-current-runtime-version"></a>檢視目前的執行階段版本

請使用下列程序，檢視函式應用程式目前使用的執行階段版本。 

1. 在 [Azure 入口網站](https://portal.azure.com)中巡覽至函式應用程式，然後在 [設定的功能] 下選擇 [函式應用程式設定]。 

    ![選取函數應用程式設定](./media/functions-versions/add-update-app-setting.png)

2. 在 [函數應用程式設定] 索引標籤上，找到 [執行階段版本]。 請注意特定的執行階段版本和要求的主要版本。 在下列範例中，FUNCTIONS\_EXTENSION\_VERSION 應用程式設定設為 `~1`。
 
   ![選取函數應用程式設定](./media/functions-versions/function-app-view-version.png)

## <a name="target-a-version-using-the-portal"></a>使用入口網站鎖定某個版本

當您需要鎖定目前主要版本或 2.0 版以外的版本時，請設定 `FUNCTIONS_EXTENSION_VERSION` 應用程式設定。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的函數應用程式，並在 [設定的功能] 下選擇 [應用程式設定]。

    ![選取函數應用程式設定](./media/functions-versions/add-update-app-setting1a.png)

2. 在 [應用程式設定] 索引標籤中尋找 `FUNCTIONS_EXTENSION_VERSION` 設定，並將值變更為 1.x 執行階段的有效版本，或 2.0 版的 `beta`。 含主要版本的波狀符號表示使用該主要版本的最新版本 (例如，"~1")。 

    ![設定函數應用程式設定](./media/functions-versions/add-update-app-setting2.png)

3. 按一下 [儲存] 來儲存應用程式設定更新。 

## <a name="target-a-version-using-azure-cli"></a>使用 Azure CLI 鎖定某個版本

 您也可以從 Azure CLI 設定 `FUNCTIONS_EXTENSION_VERSION`。 使用 Azure CLI，以 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) 命令在函式應用程式中更新應用程式設定。

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
在此程式碼中，以您的函式應用程式名稱取代 `<function_app>`。 還要以函式應用程式的資源群組名稱取代 `<my_resource_group>`。 以 1.x 執行階段的有效版本或 2.0 版的 `beta` 取代 `<version>`。 

您可以選擇上述程式碼範例中的 [試試看]，從 [Azure Cloud Shell](../cloud-shell/overview.md) 執行此命令。 在執行 [az login](/cli/azure/reference-index#az_login) 登入之後，您也可以使用[本機 Azure CLI](/cli/azure/install-azure-cli) 來執行此命令。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在本機開發環境中鎖定 2.0 執行階段](functions-run-local.md)

> [!div class="nextstepaction"]
> [請參閱執行階段版本的版本資訊](https://github.com/Azure/azure-webjobs-sdk-script/releases)