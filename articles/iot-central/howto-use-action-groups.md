---
title: 從 Azure IoT Central 的規則執行多個動作 |Microsoft Docs
description: 從單一 IoT Central 規則執行多個動作，並建立可重複使用的群組，您可以從多個規則執行的動作。
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 857d747fa691d1ec2b386d5931a7edea08b7e609
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60517256"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>若要從一或多個規則執行的多個動作群組

*這篇文章適用於產生器和系統管理員。*

在 Azure IoT Central，您會建立規則，以符合條件時執行動作。 規則是以裝置遙測資料或事件為基礎。 例如，您可以在裝置的溫度超出臨界值時通知操作員。 這篇文章說明如何使用[Azure 監視器](../azure-monitor/overview.md)*動作群組*IoT Central 規則附加多個動作。 您可以附加多個規則動作群組。 [動作群組](../azure-monitor/platform/action-groups.md)是 Azure 訂用帳戶的擁有者所定義的通知喜好設定的集合。

## <a name="prerequisites"></a>必要條件

- 隨用隨付應用程式
- Azure 帳戶和訂用帳戶來建立和管理 Azure 監視器的動作群組

## <a name="create-action-groups"></a>建立動作群組

您可以[建立和管理 Azure 入口網站中的動作群組](../azure-monitor/platform/action-groups.md)或使用[Azure Resource Manager 範本](../azure-monitor/platform/action-groups-create-resource-manager-template.md)。

動作群組可以：

- 傳送通知，例如電子郵件、 簡訊，或撥打語音電話。
- 執行動作，例如呼叫 webhook。

下列螢幕擷取畫面顯示，傳送電子郵件和 SMS 通知或呼叫 webhook 動作群組：

![動作群組](media/howto-use-action-groups/actiongroup.png)

若要使用動作群組中的 IoT Central 的規則，動作群組必須位於相同的 Azure 訂用帳戶與 IoT Central 應用程式。

## <a name="use-an-action-group"></a>使用動作群組

若要在您的 IoT Central 應用程式中使用動作群組，請先建立遙測或事件的規則。 當您將動作新增至規則時，選取**Azure 監視器的動作群組**:

![選擇動作](media/howto-use-action-groups/chooseaction.png)

選擇您的 Azure 訂用帳戶動作群組：

![選擇 動作群組](media/howto-use-action-groups/chooseactiongroup.png)

選取 [ **儲存**]。 動作群組現在會出現在清單中的規則觸發時執行的動作：

![儲存動作群組](media/howto-use-action-groups/savedactiongroup.png)

下表摘要說明傳送到支援的動作類型的資訊：

| 動作類型 | 輸出格式 |
| ----------- | -------------- |
| 電子郵件       | 標準的 IoT 中心電子郵件範本 |
| sms         | Azure IoT Central 警示: ${applicationName}-"${ruleName}"在"${deviceName}"${triggerDate} ${triggerTime} 觸發 |
| 語音       | Azure 的 I.O.T Central 警示： 應用程式 ${applicationName} 中的規則"${ruleName}"裝置"${deviceName}"${triggerDate} {triggerTime}，在觸發 |
| Webhook     | {"schemaId 」:「 AzureIoTCentralRuleWebhook"，"data": {[規則的 webhook 承載](#payload)}} |

下列文字是來自動作群組的範例 SMS 訊息：

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a> 下列 JSON 顯示 webhook 動作承載範例：

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>後續步驟

既然您已了解如何使用動作群組與規則，建議的下一個步驟是了解如何[管理您的裝置](howto-manage-devices.md)。
