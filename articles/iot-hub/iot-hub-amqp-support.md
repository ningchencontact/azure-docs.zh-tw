---
title: 了解 Azure IoT 中樞的 AMQP 支援 |Microsoft Docs
description: 開發人員指南-支援裝置連接到 IoT 中樞使用 AMQP 通訊協定的裝置對向及面向服務的端點。 包含內建 Azure IoT 裝置 Sdk 中的 AMQP 支援的相關資訊。
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: rezas
ms.openlocfilehash: 703e2c842fb42bad8aa112d84c516a29c2327378
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473502"
---
# <a name="communicate-with-your-iot-hub-using-the-amqp-protocol"></a>IoT 中樞使用 AMQP 通訊協定與通訊

IoT 中樞支援[AMQP 1.0 版](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)來提供各種由透過面向裝置和服務面向端點的功能。 本文件說明為了使用 IoT 中樞功能連線到 IoT 中樞的 AMQP 用戶端使用。

## <a name="service-client"></a>服務用戶端

### <a name="connection-and-authenticating-to-iot-hub-service-client"></a>連線並向 IoT 中樞 （服務用戶端）
若要連接到 IoT 中樞使用 AMQP，用戶端可以使用[宣告型安全性 (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc)或是[簡單驗證及安全性階層 (SASL) 驗證](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)。

下列資訊是服務用戶端需要：

| 資訊 | Value | 
|-------------|--------------|
| IoT 中樞主機名稱 | `<iot-hub-name>.azure-devices.net` |
| 金鑰名稱 | `service` |
| 存取金鑰 | 與服務相關聯的主要或次要金鑰 |
| 共用存取簽章 | 短期的 SAS，以下列格式： `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` (可以找到程式碼來產生這個簽章[這裡](./iot-hub-devguide-security.md#security-token-structure))。


使用下列程式碼片段[uAMQP 在 Python 中的程式庫](https://github.com/Azure/azure-uamqp-python)來連線到 IoT 中樞，透過寄件者 連結。

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>' # e.g., '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoking-cloud-to-device-messages-service-client"></a>叫用的雲端到裝置訊息 （服務用戶端）
描述服務與 IoT 中樞之間，以及裝置與 IoT 中樞之間的雲端到裝置訊息交換[此處](iot-hub-devguide-messages-c2d.md)。 服務用戶端會使用兩個連結，如下所述來傳送訊息，並從裝置收到先前傳送的訊息的意見反應。

| 建立者: | 連結類型 | 連結路徑 | 說明 |
|------------|-----------|-----------|-------------|
| 服務 | 寄件者 連結 | `/messages/devicebound` | C2D 訊息為目標的裝置會傳送到此連結服務。 透過此連結來傳送的訊息有他們`To`屬性設定為 目標裝置的接收者連結路徑： 亦即， `/devices/<deviceID>/messages/devicebound`。 |
| 服務 | 接收者連結 | `/messages/serviceBound/feedback` | 來自裝置接收到此連結服務的完成、 拒絕，並放棄意見反應訊息。 請參閱[此處](./iot-hub-devguide-messages-c2d.md#message-feedback)的意見反應訊息的詳細資訊。 |

下列程式碼片段示範如何建立 C2D 訊息，並將它傳送至裝置，使用[uAMQP 在 Python 中的程式庫](https://github.com/Azure/azure-uamqp-python)。

```python
import uuid
# Create a message and set message property 'To' to the devicebound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message using the send client created and connected IoT Hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if not needed
send_client.close()
```

若要接收意見反應，服務用戶端會建立接收器的連結。 下列程式碼片段示範如何使用執行此動作[uAMQP 在 Python 中的程式庫](https://github.com/Azure/azure-uamqp-python)。

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Recreate the URI using the feedback path above and authenticate
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
  print('received a message')
  # Check content_type in message property to identify feedback messages coming from device
  if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
    msg_body_raw = msg.get_data()
    msg_body_str = ''.join(msg_body_raw)
    msg_body = json.loads(msg_body_str)
    print(json.dumps(msg_body, indent=2))
    print('******************')
    for feedback in msg_body:
      print('feedback received')
      print('\tstatusCode: ' + str(feedback['statusCode']))
      print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
      print('\tdeviceId: ' + str(feedback['deviceId']))
      print
  else:
    print('unknown message:', msg.properties.content_type)
```

C2D 意見反應訊息如上所示，有的內容類型`application/vnd.microsoft.iothub.feedback.json`而且其 JSON 主體中的屬性可用來推斷原始訊息的傳遞狀態：
* 索引鍵`statusCode`意見反應中主體具有其中一個值： `['Success', 'Expired', 'DeliveryCountExceeded', 'Rejected', 'Purged']`。
* 索引鍵`deviceId`意見反應中主體具有目標裝置的識別碼。
* 索引鍵`originalMessageId`意見反應中主體會包含服務所傳送的原始 C2D 訊息的識別碼。 這可用來將意見反應給 C2D 訊息相互關聯。

### <a name="receive-telemetry-messages-service-client"></a>接收遙測訊息 （服務用戶端）


### <a name="additional-notes"></a>其他附註
* AMQP 連線可能中斷網路小毛病或 （在程式碼產生） 的驗證權杖的到期日到期。 服務用戶端必須處理這些情況下，並重新建立連線和連結，如有需要。 驗證權杖到期的情況下，用戶端還必須主動可以更新其到期日，以避免連線卸除前的語彙基元。
* 在某些情況下，您的用戶端必須能夠正確處理連結重新導向。 請參閱您的 AMQP 用戶端文件上如何執行這項操作。

### <a name="receive-cloud-to-device-messages-device-and-module-client"></a>接收雲端到裝置訊息 （裝置和模組用戶端）
在裝置端上使用 AMQP 連結如下所示：

| 建立者: | 連結類型 | 連結路徑 | 說明 |
|------------|-----------|-----------|-------------|
| 裝置 | 接收者連結 | `/devices/<deviceID>/messages/devicebound` | 此連結會接收 C2D 訊息為目標的裝置，每個目的地裝置。 |
| 裝置 | 寄件者 連結 | `/messages/serviceBound/feedback` | C2D 訊息意見反應傳送到此連結服務的裝置。 |
| 模組 | 接收者連結 | `/devices/<deviceID>/modules/<moduleID>/messages/devicebound` | 每個目的地模組 C2D 訊息流向模組接收到此連結。 |
| 模組 | 寄件者 連結 | `/messages/serviceBound/feedback` | C2D 訊息意見反應傳送至由模組透過此連結服務。 |


## <a name="next-steps"></a>後續步驟

若要深入了解 AMQP 通訊協定，請參閱[AMQP v1.0 規格](http://www.amqp.org/sites/amqp.org/files/amqp.pdf)。

若要深入了解 IoT 中樞傳訊，請參閱：

* [雲端到裝置訊息](./iot-hub-devguide-messages-c2d.md)
* [支援其他通訊協定](iot-hub-protocol-gateway.md)
* [支援 MQTT 通訊協定](./iot-hub-mqtt-support.md)
