---
title: 從 Azure PowerShell 管理 IoT Central | Microsoft Docs
description: 從 Azure PowerShell 管理 IoT Central。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 85afb69a7656e96e2f7163ded5cb23ac1b2560ea
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/05/2019
ms.locfileid: "70380975"
---
# <a name="manage-iot-central-from-azure-powershell"></a>從 Azure PowerShell 管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

您可以使用[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)來管理應用程式，而不是在[Azure IoT Central 應用程式管理員](https://aka.ms/iotcentral)網站上建立和管理 IoT Central 應用程式。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您偏好在本機電腦上執行 Azure PowerShell，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps) \(英文\)。 當您在本機上執行 Azure PowerShell 時，在嘗試本文中所述的 Cmdlet 之前，請先使用 **Connect-AzAccount** Cmdlet 來登入 Azure。

## <a name="install-the-iot-central-module"></a>安裝 IoT Central 模組

執行下列命令來檢查已安裝在您 PowerShell 環境中的 [IoT Central 模組](https://docs.microsoft.com/powershell/module/az.iotcentral/) \(英文\)：

```powershell
Get-InstalledModule -name Az.I*
```

如果已安裝模組的清單沒有包含 **Az.IotCentral**，請執行下列命令：

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>建立應用程式

使用 [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) \(英文\) Cmdlet 來在您的 Azure 訂用帳戶中建立 IoT Central 應用程式。 例如:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

該指令碼會先為應用程式在美國東部區域中建立資源群組。 下表說明搭配 **New-AzIotCentralApp** 命令使用的參數：

|參數         |描述 |
|------------------|------------|
|ResourceGroupName |包含應用程式的資源群組。 此資源群組必須已經存在於您的訂用帳戶中。 |
|Location |根據預設，此 Cmdlet 會使用來自資源群組的位置。 目前您可以在**美國東部**、**美國西部**、**北歐**或**西歐**地區建立 IoT Central 應用程式。 |
|Name              |應用程式在 Azure 入口網站中的名稱。 |
|子網域         |應用程式 URL 中的子網域。 在範例中，應用程式 URL 是 https://mysubdomain.azureiotcentral.com 。 |
|SKU               |目前唯一的值是 **S1** (標準層)。 請參閱 [Azure IoT Central 價格](https://azure.microsoft.com/pricing/details/iot-central/)。 |
|範本          | 要使用的應用程式範本。 如需詳細資訊，請參閱下列表格： |
|顯示名稱       |在 UI 中顯示的應用程式名稱。 |

**應用程式範本**

|範本名稱  |描述 |
|---------------|------------|
|iotc-default@1.0.0 |為您建立空的應用程式，以填入您自己的裝置範本和裝置。 |
|iotc-demo@1.0.0    |建立一個應用程式，其中包含已為冷飲自動販賣機建立的裝置範本。 使用此範本來開始探索 Azure IoT 中心。 |
|iotc-devkit-sample@1.0.0 |使用您可使用的裝置範本來建立應用程式，以連線 MXChip 或 Raspberry Pi 裝置。 如果您是以其中任一裝置進行試驗的裝置開發人員，請使用此範本。 |

> [!NOTE]
> **預覽應用程式**範本目前僅**適用于北歐和** **美國中部**區域。

## <a name="view-your-iot-central-applications"></a>檢視 IoT Central 應用程式

使用 [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) \(英文\) Cmdlet 來列出您的 IoT Central 應用程式並檢視中繼資料。

## <a name="modify-an-application"></a>修改應用程式

使用 [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) \(英文\) Cmdlet 來更新 IoT Central 應用程式的中繼資料。 例如，若要變更應用程式的顯示名稱：

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>移除應用程式

使用 [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) \(英文\) Cmdlet 來刪除 IoT Central 應用程式。 例如:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>後續步驟

您現在已了解如何從 Azure PowerShell 管理 Azure IoT Central 應用程式，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [管理您的應用程式](howto-administer.md)
