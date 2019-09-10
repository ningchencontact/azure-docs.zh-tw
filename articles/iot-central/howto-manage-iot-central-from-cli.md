---
title: 從 Azure CLI 管理 IoT Central |Microsoft Docs
description: 從 Azure CLI 管理 IoT Central。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 1ec83541c62d93eee91348531797ecdeb8c9fc6e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873469"
---
# <a name="manage-iot-central-from-azure-cli"></a>從 Azure CLI 管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

您可以使用[Azure CLI](/cli/azure/)來管理應用程式，而不是在[Azure IoT Central 應用程式管理員](https://aka.ms/iotcentral)網站上建立和管理 IoT Central 應用程式。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您想要在本機電腦上執行 Azure CLI，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 當您在本機執行 Azure CLI 時，請使用**az login**命令來登入 Azure，然後再嘗試本文中的命令。

## <a name="create-an-application"></a>建立應用程式

使用[az iotcentral app create](/cli/azure/iotcentral/app#az-iotcentral-app-create)命令，在您的 Azure 訂用帳戶中建立 IoT Central 應用程式。 例如:

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

這些命令會先在「美國東部」區域中為應用程式建立資源群組。 下表描述搭配**az iotcentral app create**命令使用的參數：

| 參數         | 描述 |
| ----------------- | ----------- |
| resource-group    | 包含應用程式的資源群組。 此資源群組必須已經存在於您的訂用帳戶中。 |
| 位置          | 根據預設，此命令會使用來自資源群組的位置。 目前，您可以在「**美國東部**」、「**美國西部**」、「**北歐**」或「**西歐」區域或**「**澳大利亞**地理位置」中建立 IoT Central 應用程式。 |
| name              | 應用程式在 Azure 入口網站中的名稱。 |
| 子域         | 應用程式 URL 中的子網域。 在範例中，應用程式 URL 是 https://mysubdomain.azureiotcentral.com 。 |
| SKU               | 目前唯一的值是 **S1** (標準層)。 請參閱 [Azure IoT Central 價格](https://azure.microsoft.com/pricing/details/iot-central/)。 |
| 樣板          | 要使用的應用程式範本。 如需詳細資訊，請參閱下列表格： |
| display-name      | 在 UI 中顯示的應用程式名稱。 |

**應用程式範本**

| 範本名稱            | 描述 |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | 為您建立空的應用程式，以填入您自己的裝置範本和裝置。 |
| iotc-demo@1.0.0          | 建立一個應用程式，其中包含已為冷飲自動販賣機建立的裝置範本。 使用此範本來開始探索 Azure IoT 中心。 |
| iotc-devkit-sample@1.0.0 | 使用您可使用的裝置範本來建立應用程式，以連線 MXChip 或 Raspberry Pi 裝置。 如果您是以其中任一裝置進行試驗的裝置開發人員，請使用此範本。 |

> [!NOTE]
> **預覽應用程式**範本目前僅**適用于北歐和** **美國中部**區域。

## <a name="view-your-applications"></a>檢視您的應用程式

使用[az iotcentral app list](/cli/azure/iotcentral/app#az-iotcentral-app-list)命令來列出您的 IoT Central 應用程式並查看中繼資料。

## <a name="modify-an-application"></a>修改應用程式

使用[az iotcentral app update](/cli/azure/iotcentral/app#az-iotcentral-app-update)命令來更新 IoT Central 應用程式的中繼資料。 例如，若要變更應用程式的顯示名稱：

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>移除應用程式

使用[az iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete)命令來刪除 IoT Central 應用程式。 例如:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何從 Azure CLI 管理 Azure IoT Central 應用程式，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [管理您的應用程式](howto-administer.md)
