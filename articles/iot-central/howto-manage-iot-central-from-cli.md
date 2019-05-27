---
title: 從 Azure CLI 管理 IoT 中心 |Microsoft Docs
description: 從 Azure CLI 來管理 IoT 中心。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/07/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 9e5d842cece316bc9c53e1e8583f40a0f222b91d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "66151964"
---
# <a name="manage-iot-central-from-azure-cli"></a>從 Azure CLI 管理 IoT 中心

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

而不是建立和管理 IoT Central 應用程式，從 IoT Central[應用程式管理員](https://aka.ms/iotcentral)頁面上，您可以使用[Azure CLI](/cli/azure/)來管理您的應用程式。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您想要在本機電腦上執行 Azure CLI，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 當您在本機執行 Azure CLI 時，使用**az 登入**命令來登入 Azure 才能嘗試這篇文章中的命令。

## <a name="create-an-application"></a>建立應用程式

使用[az iotcentral 應用程式建立](/cli/azure/iotcentral/app#az-iotcentral-app-create)您 Azure 訂用帳戶中建立 IoT Central 應用程式的命令。 例如：

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

第一次，這些命令會在美國東部地區的應用程式中建立資源群組。 下表描述搭配使用的參數**az iotcentral 應用程式建立**命令：

| 參數         | 說明 |
| ----------------- | ----------- |
| resource-group    | 包含應用程式的資源群組。 此資源群組必須已經存在於您的訂用帳戶中。 |
| location          | 根據預設，此命令會使用的資源群組的位置。 目前您可以在**美國東部**、**美國西部**、**北歐**或**西歐**地區建立 IoT Central 應用程式。 |
| name              | 應用程式在 Azure 入口網站中的名稱。 |
| 子網域         | 應用程式 URL 中的子網域。 在範例中，應用程式 URL 是 https://mysubdomain.azureiotcentral.com。 |
| SKU               | 目前唯一的值是 **S1** (標準層)。 請參閱 [Azure IoT Central 價格](https://azure.microsoft.com/pricing/details/iot-central/)。 |
| 樣板          | 要使用的應用程式範本。 如需詳細資訊，請參閱下列表格： |
| display-name      | 在 UI 中顯示的應用程式名稱。 |

**應用程式範本**

| 範本名稱            | 說明 |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | 為您建立空的應用程式，以填入您自己的裝置範本和裝置。 |
| iotc-demo@1.0.0          | 建立一個應用程式，其中包含已為冷飲自動販賣機建立的裝置範本。 使用此範本來開始探索 Azure IoT 中心。 |
| iotc-devkit-sample@1.0.0 | 使用您可使用的裝置範本來建立應用程式，以連線 MXChip 或 Raspberry Pi 裝置。 如果您是以其中任一裝置進行試驗的裝置開發人員，請使用此範本。 |

## <a name="view-your-applications"></a>檢視您的應用程式

使用[az iotcentral 應用程式清單](/cli/azure/iotcentral/app#az-iotcentral-app-list)命令來列出您的 IoT Central 應用程式，並檢視中繼資料。

## <a name="modify-an-application"></a>修改應用程式

使用[az iotcentral 應用程式更新](/cli/azure/iotcentral/app#az-iotcentral-app-update)命令來更新 IoT Central 應用程式的中繼資料。 例如，若要變更應用程式的顯示名稱：

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>移除應用程式

使用[az iotcentral 應用程式刪除](/cli/azure/iotcentral/app#az-iotcentral-app-delete)命令來刪除 IoT Central 應用程式。 例如：

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>後續步驟

既然您已了解如何從 Azure CLI 管理 Azure IoT Central 應用程式，以下是建議的下一個步驟：

> [!div class="nextstepaction"]
> [管理您的應用程式](howto-administer.md)
