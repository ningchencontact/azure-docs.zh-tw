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
ms.openlocfilehash: d256faa42161e276e165f95c944b9f58ac4a8927
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297406"
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
| 索引鍵名稱 | `service` |
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

| 所建立 | 連結類型 | 連結路徑 | 描述 |
|------------|-----------|-----------|-------------|
| 服務 | 寄件者 連結 | `/messages/devicebound` | C2D 訊息為目標的裝置會傳送到此連結服務。 透過此連結來傳送的訊息有他們`To`屬性設定為 目標裝置的接收者連結路徑： 亦即， `/devices/<deviceID>/messages/devicebound`。 |
| 服務 | 接收者連結 | `/messages/serviceBound/feedback` | 來自裝置接收到此連結服務的完成、 拒絕，並放棄意見反應訊息。 如需詳細的意見反應訊息的詳細資訊，請參閱[此處](./iot-hub-devguide-messages-c2d.md#message-feedback)。 |

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
根據預設，IoT 中樞會儲存內建的事件中樞擷取的裝置遙測訊息。 您服務用戶端可以使用 AMQP 通訊協定來接收儲存的事件。

基於此目的，服務用戶端第一次必須連接到 IoT 中樞端點，並接收到內建的事件中樞的重新導向位址。 服務用戶端接著會使用提供的位址連接至內建的事件中樞。

在每個步驟中，用戶端必須提供下列幾項資訊：
* 有效的服務認證 （服務 SAS 權杖）。
* 想要擷取來自訊息取用者群組資料分割的完整格式的路徑。 指定的取用者群組與分割區識別碼，路徑具有下列格式： `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (預設的取用者群組是`$Default`)。
* 選用篩選述詞來指定資料分割中起點 （可以採用的順序號碼、 位移或已加入佇列的時間戳記格式）。

使用下列程式碼片段[uAMQP 在 Python 中的程式庫](https://github.com/Azure/azure-uamqp-python)來示範上述步驟。

```python
import json
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified using endpiont_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI
def set_endpoint_filter(uri, endpoint_filter=''):
  source_uri = uamqp.address.Source(uri)
  source_uri.set_filter(endpoint_filter)
  return source_uri

receive_client = uamqp.ReceiveClient(set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
  batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
  # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
  receive_client.close()

  sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(redirect.address, policy_name, access_key)
  receive_client = uamqp.ReceiveClient(set_endpoint_filter(redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
  print('*** received a message ***')
  print(''.join(msg.get_data()))
  print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
  print('\t: ' + str(msg.annotations['x-opt-offset']))
  print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

指定的裝置識別碼，IoT 中樞會使用裝置識別碼的雜湊來判斷哪一個資料分割儲存在其訊息。 上述程式碼片段會示範從單一事件的接收這類資料分割。 不過，要注意的是，一般的應用程式通常需要擷取所有事件中樞資料分割中儲存的事件。


## <a name="device-client"></a>裝置用戶端

### <a name="connection-and-authenticating-to-iot-hub-device-client"></a>連線並向 IoT 中樞 （裝置用戶端）
若要連接到 IoT 中樞使用 AMQP，裝置可以使用[宣告型安全性 (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc)或是[簡單驗證及安全性階層 (SASL) 驗證](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)。

下列資訊是必要的裝置用戶端：

| 資訊 | Value | 
|-------------|--------------|
| IoT 中樞主機名稱 | `<iot-hub-name>.azure-devices.net` |
| 存取金鑰 | 與裝置相關聯的主要或次要金鑰 |
| 共用存取簽章 | 短期的 SAS，以下列格式： `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}` (可以找到程式碼來產生這個簽章[這裡](./iot-hub-devguide-security.md#security-token-structure))。


使用下列程式碼片段[uAMQP 在 Python 中的程式庫](https://github.com/Azure/azure-uamqp-python)來連線到 IoT 中樞，透過寄件者 連結。

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(hostname=hostname, device_id=device_id), access_key, None)

operation = '<operation-link-name>' # e.g., '/devices/{device_id}/messages/devicebound'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

裝置作業支援下列的連結路徑：

| 所建立 | 連結類型 | 連結路徑 | 描述 |
|------------|-----------|-----------|-------------|
| 裝置 | 接收者連結 | `/devices/<deviceID>/messages/devicebound` | 此連結會接收 C2D 訊息為目標的裝置，每個目的地裝置。 |
| 裝置 | 寄件者 連結 | `/devices/<deviceID>messages/events` | 從裝置傳送 D2C 訊息會透過此連結傳送。 |
| 裝置 | 寄件者 連結 | `/messages/serviceBound/feedback` | C2D 訊息意見反應傳送到此連結服務的裝置。 |


### <a name="receive-c2d-commands-device-client"></a>接收 C2D 命令 （裝置用戶端）
C2D 命令傳送至裝置到達`/devices/<deviceID>/messages/devicebound`連結。 裝置可以在批次中接收這些訊息，並視需要使用訊息資料內容、 訊息屬性、 註解或應用程式屬性的訊息中。

使用下列程式碼片段[uAMQP 在 Python 中的程式庫](https://github.com/Azure/azure-uamqp-python)裝置接收 C2D 訊息。

```python
# ... 
# Create a receive client for the C2D receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
  batch = receive_client.receive_message_batch(max_batch_size=5)
  for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))

    # Property 'to' is set to: '/devices/device1/messages/devicebound',
    print('\tto:                     ' + str(msg.properties.to))

    # Property 'message_id' is set to value provided by the service
    print('\tmessage_id:             ' + str(msg.properties.message_id))

    # Other properties are present if they were provided by the service
    print('\tcreation_time:          ' + str(msg.properties.creation_time))
    print('\tcorrelation_id:         ' + str(msg.properties.correlation_id))
    print('\tcontent_type:           ' + str(msg.properties.content_type))
    print('\treply_to_group_id:      ' + str(msg.properties.reply_to_group_id))
    print('\tsubject:                ' + str(msg.properties.subject))
    print('\tuser_id:                ' + str(msg.properties.user_id))
    print('\tgroup_sequence:         ' + str(msg.properties.group_sequence))
    print('\tcontent_encoding:       ' + str(msg.properties.content_encoding))
    print('\treply_to:               ' + str(msg.properties.reply_to))
    print('\tabsolute_expiry_time:   ' + str(msg.properties.absolute_expiry_time))
    print('\tgroup_id:               ' + str(msg.properties.group_id))

    # Message sequence number in the built-in Event hub
    print('\tx-opt-sequence-number:  ' + str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>傳送遙測訊息 （裝置用戶端）
遙測訊息也會透過 AMQP 從裝置傳送。 裝置可以選擇性地提供應用程式屬性的字典或各種訊息屬性，例如訊息識別碼。

使用下列程式碼片段[uAMQP 在 Python 中的程式庫](https://github.com/Azure/azure-uamqp-python)從裝置傳送 D2C 訊息。


```python
# ... 
# Create a send client for the D2C send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>其他注意事項
* AMQP 連線可能中斷網路小毛病或 （在程式碼產生） 的驗證權杖的到期日到期。 服務用戶端必須處理這些情況下，並重新建立連線和連結，如有需要。 驗證權杖到期的情況下，用戶端還必須主動可以更新其到期日，以避免連線卸除前的語彙基元。
* 在某些情況下，您的用戶端必須能夠正確處理連結重新導向。 請參閱您的 AMQP 用戶端文件如何處理這項作業。

## <a name="next-steps"></a>後續步驟

若要深入了解 AMQP 通訊協定，請參閱[AMQP v1.0 規格](http://www.amqp.org/sites/amqp.org/files/amqp.pdf)。

若要深入了解 IoT 中樞傳訊，請參閱：

* [雲端到裝置訊息](./iot-hub-devguide-messages-c2d.md)
* [支援其他通訊協定](iot-hub-protocol-gateway.md)
* [支援 MQTT 通訊協定](./iot-hub-mqtt-support.md)
