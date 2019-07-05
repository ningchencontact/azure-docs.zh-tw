---
title: 大規模使用 「 Azure IoT 中樞 」 (CLI) 的自動裝置管理 |Microsoft Docs
description: 使用 Azure IoT 中樞自動的裝置管理來將設定指派給多個 IoT 裝置
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: chrisgre
ms.openlocfilehash: d57dbbdd7614d09d52fef0f613c43d4ca1d08136
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485854"
---
# <a name="automatic-iot-device-management-at-scale-using-the-azure-cli"></a>自動大規模使用 Azure CLI 的 IoT 裝置管理

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT 中樞中的自動裝置管理會自動執行許多管理大型裝置艦隊的重複性和複雜工作。 使用自動裝置管理，您可以為目標的一組裝置根據其屬性、 定義所需的設定，然後讓它們進入範圍時，更新裝置的 IoT 中樞。 此更新是使用_自動裝置組態_，它可讓您彙總完成和合規性、 控制代碼合併和衝突，並導入的分段式方法中的組態。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

自動裝置管理運作方式，是一組裝置對應項更新所需屬性和報告摘要為基礎的裝置對應項報告屬性。  它引進新的類別與呼叫的 JSON 文件*組態*，有三個部分：

* **目標條件**會定義要更新的裝置對應項範圍。 目標條件會以查詢的方式指定於裝置對應項標籤和/或報告屬性上。

* **目標內容**會定義要在目標裝置對應項中新增或更新的所需屬性。 內容包含了一個路徑，連往所要變更屬性的區段。

* **計量**會定義各種設定狀態 (例如 **Success**、**Progress** 及 **Error**) 的摘要計數。 自訂計量會以查詢的形式指定於裝置對應項報告屬性上。  系統計量是預設計量，用來計算對應項更新的狀態，例如為目標的裝置對應項的數目和已成功更新的對應項的數目。

自動裝置組態執行組態建立後，很快就在第一次，然後在五分鐘的時間間隔。 計量查詢執行每次執行時自動裝置組態。

## <a name="cli-prerequisites"></a>CLI 先決條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-using-cli.md)。 
* 您環境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 您的 Azure CLI 版本至少必須是 2.0.24 或更新版本。 使用 `az –-version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。 
* [適用於 Azure CLI 的 IoT 擴充功能](https://github.com/Azure/azure-iot-cli-extension) \(英文\)。

## <a name="implement-device-twins-to-configure-devices"></a>實作裝置對應項來設定裝置

自動裝置設定需要使用裝置對應項，以同步處理雲端和裝置之間的狀態。  如需使用裝置對應項的指引，請參閱[了解和使用 IoT 中樞的裝置對應項](iot-hub-devguide-device-twins.md)。

## <a name="identify-devices-using-tags"></a>使用標記識別裝置

您建立的組態之前，您必須指定您想要影響的裝置。 Azure IoT 中樞會使用裝置對應項中的標籤來識別裝置。 每個裝置都可以有多個標記，而您可以利用任何對您解決方案有意義的方式來定義它們。 比方說，如果您管理在不同位置的裝置，請將下列標記新增至裝置對應項：

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>定義目標內容和計量

內容的目標和度量的查詢會指定為 JSON 文件，說明裝置對應項所需的屬性集和報告的屬性，來測量。  若要使用 Azure CLI 建立自動裝置組態，請在本機上將目標內容和計量儲存為 .txt 檔案。 當您執行命令，以將組態套用到您的裝置時，您可以使用後續小節中的檔案路徑。

以下是基本目標內容範例：

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

以下是計量查詢的範例：

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

## <a name="create-a-configuration"></a>建立設定

您可以建立由目標內容和計量組成的組態，來設定目標裝置。 

使用以下命令建立組態：

```cli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** - 將會在 IoT 中樞建立的組態名稱。 為設定指定唯一的名稱，最長為 128 個小寫字母。 避免空格和下列無效字元：`& ^ [ ] { } \ | " < > /`。

* --**labels** - 新增標籤以協助追蹤您的組態。 標籤是成對的「名稱, 值」組合，可描述您的部署。 例如，`HostPlatform, Linux` 或 `Version, 3.0.1`。

* --**content** - 要設為對應項所需屬性的目標內容內嵌 JSON 或檔案路徑。 

* --**hub-name** - 要在其中建立組態的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶

* --**target-condition** - 輸入目標條件來判斷這個組態會將哪些裝置設為目標。 條件會以裝置對應項標籤或裝置對應項所需屬性為基礎，且應符合運算式格式。 例如，`tags.environment='test'` 或 `properties.desired.devicemodel='4000x'`。 

* --**priority** - 正整數。 在兩個以上組態目標為相同裝置的事件中，將會套用具有優先順序最高數值的組態。

* --**metrics** - 計量查詢的檔案路徑。 計量會提供裝置可能會回報之後套用的設定內容的各種狀態的摘要的計數。 例如，您可以建立擱置設定變更的計量、錯誤的計量，以及成功設定變更的計量。 

## <a name="monitor-a-configuration"></a>監視設定

使用下列命令來顯示組態的內容︰

```cli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** - 存在於 IoT 中樞的組態名稱。

* --**hub-name** - 組態存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需訂用帳戶

在命令視窗中檢查組態。 **metrics** 屬性會列出每個中樞評估的每個計量計數：

* **targetedCount** - 系統計量，指定 IoT 中樞中符合目標條件的裝置對應項數目。

* **appliedCount** - 系統計量，指定已套用目標內容的裝置數目。

* **自訂計量**-您已定義任何計量是使用者計量。

您可以使用下列命令，顯示每個計量的裝置識別碼或物件清單：

```cli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** - 存在於 IoT 中樞的部署名稱。

* --**metric-id** - 您想要在其中查看裝置識別碼清單的計量名稱，例如 `appliedCount`。

* --**hub-name** - 部署存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶。

* --**metric-type** - 計量類型可以是 `system` 或 `user`。  系統計量是 `targetedCount` 和 `appliedCount`。 其他所有計量是使用者計量。

## <a name="modify-a-configuration"></a>修改設定

當您修改設定時，所做的變更會立即複寫到所有目標裝置。 

如果您更新目標條件，就會發生下列更新：

* 如果裝置對應項不符合舊的目標條件，但符合新的目標條件，而且此設定為該裝置對應項的最高優先順序設定，則會將此設定套用到該裝置對應項。 

* 如果裝置對應項不再符合目標條件，系統會將設定中的設定移除，並以優先順序次高的設定來修改該裝置對應項。 

* 如果目前執行此設定的裝置對應項不再符合目標條件，也不符合任何其他設定的目標條件，則系統會將設定中的設定移除，且不會對該對應項進行任何其他變更。 

使用以下命令更新組態：

```cli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** - 存在於 IoT 中樞的組態名稱。

* --**hub-name** - 組態存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶。

* --**set** - 更新組態中的屬性。 您可以更新下列屬性：

    * targetCondition - 例如 `targetCondition=tags.location.state='Oregon'`

    * 標籤 

    * 優先順序

## <a name="delete-a-configuration"></a>刪除設定

當您刪除設定時，任何裝置對應項都會採用其優先順序次高的設定。 如果裝置對應項不符合任何其他設定的目標條件，則不會套用任何其他設定。 

使用以下命令刪除組態：

```cli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```
* --**config-id** - 存在於 IoT 中樞的組態名稱。

* --**hub-name** - 組態存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何設定和監視大規模的 IoT 裝置。 遵循下列連結以深入了解如何管理 Azure IoT 中樞：

* [管理大量的 IoT 中樞裝置身分識別](iot-hub-bulk-identity-mgmt.md)
* [IoT 中樞計量](iot-hub-metrics.md)
* [作業監視](iot-hub-operations-monitoring.md)

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南](iot-hub-devguide.md)
* [使用 Azure IoT Edge 將 AI 部署到 Edge 裝置](../iot-edge/tutorial-simulate-device-linux.md)

若要探索使用 IoT 中樞裝置佈建服務進行 Just-In-Time 自動佈建，請參閱： 

* [Azure IoT 中樞裝置佈建服務](/azure/iot-dps)
