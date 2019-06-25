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
ms.openlocfilehash: c304c9b7fe02e3396d49aee0b70576071d9fac92
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055376"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>使用 AMQP 通訊協定與 IoT 中樞通訊

Azure IoT 中樞支援[OASIS 進階訊息佇列通訊協定 (AMQP) 1.0 版](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)來提供各種由透過面向裝置和服務面向端點的功能。 本文件說明 AMQP 用戶端連線至 IoT 中樞，以使用 IoT 中樞功能的使用。

## <a name="service-client"></a>服務用戶端

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>連接及驗證 IoT 中樞 （服務用戶端）
若要連接到 IoT 中樞使用 AMQP，可以使用用戶端[宣告型安全性 (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc)或是[簡單驗證及安全性階層 (SASL) 驗證](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)。

下列資訊是服務用戶端需要：

| 資訊 | 值 | 
|-------------|--------------|
| IoT 中樞主機名稱 | `<iot-hub-name>.azure-devices.net` |
| 索引鍵名稱 | `service` |
| 存取金鑰 | 與服務相關聯的主要或次要金鑰 |
| 共用存取簽章 | 以下列格式的短期的共用的存取簽章： `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`。 若要取得的程式碼產生這個簽章，請參閱[控制 IoT 中樞的存取權](./iot-hub-devguide-security.md#security-token-structure)。

下列程式碼片段會使用[uAMQP 在 Python 中的程式庫](https://github.com/Azure/azure-uamqp-python)連接到 IoT 中樞透過寄件者 連結。

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

### <a name="invoke-cloud-to-device-messages-service-client"></a>叫用的雲端到裝置訊息 （服務用戶端）
若要深入了解服務與 IoT 中樞之間，以及裝置與 IoT 中樞之間的雲端到裝置訊息交換，請參閱[從您的 IoT 中樞傳送雲端到裝置訊息](iot-hub-devguide-messages-c2d.md)。 服務用戶端會使用兩個連結來傳送訊息和先前接收意見反應，並從裝置傳送訊息下, 表中所述：

| 所建立 | 連結類型 | 連結路徑 | 描述 |
|------------|-----------|-----------|-------------|
| 服務 | 寄件者 連結 | `/messages/devicebound` | 目的地為裝置的雲端到裝置訊息傳送到此連結服務。 透過此連結來傳送的訊息有他們`To`屬性設定為 目標裝置的接收者連結路徑， `/devices/<deviceID>/messages/devicebound`。 |
| 服務 | 接收者連結 | `/messages/serviceBound/feedback` | 來自裝置接收到此連結服務的完成、 拒絕，並放棄意見反應訊息。 如需詳細的意見反應訊息的詳細資訊，請參閱[從 IoT 中樞傳送雲端到裝置訊息](./iot-hub-devguide-messages-c2d.md#message-feedback)。 |

下列程式碼片段示範如何建立雲端到裝置訊息，並將它傳送至裝置中，使用[uAMQP 在 Python 中的程式庫](https://github.com/Azure/azure-uamqp-python)。

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

若要接收意見反應，服務用戶端會建立接收器的連結。 下列程式碼片段示範如何使用以建立連結[uAMQP 在 Python 中的程式庫](https://github.com/Azure/azure-uamqp-python):

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
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

雲端到裝置意見反應訊息前面的程式碼所示，有的內容類型*application/vnd.microsoft.iothub.feedback.json*。 您可以使用訊息的 JSON 主體中的屬性，來推斷原始訊息的傳遞狀態：
* 索引鍵`statusCode`的意見反應中主體會包含下列值之一：*成功*，*過期*， *DeliveryCountExceeded*，*拒絕*，或*清除*。
* 索引鍵`deviceId`的意見反應中主體具有目標裝置的識別碼。
* 索引鍵`originalMessageId`的意見反應中主體會包含原始服務所傳送的雲端到裝置訊息的識別碼。 若要將雲端到裝置訊息的意見反應的相互關聯，您可以使用此傳遞狀態。

### <a name="receive-telemetry-messages-service-client"></a>接收遙測訊息 （服務用戶端）

根據預設，IoT 中樞會內建的事件中樞儲存內嵌的裝置的遙測訊息。 您服務用戶端可以使用 AMQP 通訊協定來接收儲存的事件。

基於此目的，服務用戶端第一次必須連接到 IoT 中樞端點與接收的內建的事件中樞的重新導向位址。 服務用戶端然後會使用提供的位址來連線到內建的事件中樞。

在每個步驟中，用戶端必須提供下列幾項資訊：
* 有效的服務認證 （服務共用的存取簽章語彙基元）。
* 想要擷取來自訊息取用者群組分割完整格式的路徑。 指定的取用者群組與分割區識別碼，路徑具有下列格式： `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (預設的取用者群組是`$Default`)。
* 選用篩選述詞來指定資料分割中的起始點。 順序號碼、 位移或已加入佇列的時間戳記的形式可以是這個述詞。

下列程式碼片段會使用[uAMQP 在 Python 中的程式庫](https://github.com/Azure/azure-uamqp-python)來示範上述的步驟：

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
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

指定的裝置識別碼，IoT 中樞會使用裝置識別碼的雜湊，來決定要儲存其訊息的哪一個資料分割。 上面的程式碼片段示範如何從單一收到事件這類資料分割。 不過，要請您注意的是一般的應用程式通常必須擷取所有事件中樞資料分割中儲存的事件。


## <a name="device-client"></a>裝置用戶端

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>連接及驗證 IoT 中樞 （裝置用戶端）
若要使用 AMQP 連接到 IoT 中樞，裝置可以使用[宣告型安全性 (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc)或是[簡單驗證及安全性階層 (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)驗證。

下列資訊是必要的裝置用戶端：

| 資訊 | 值 | 
|-------------|--------------|
| IoT 中樞主機名稱 | `<iot-hub-name>.azure-devices.net` |
| 存取金鑰 | 與裝置相關聯的主要或次要金鑰 |
| 共用存取簽章 | 以下列格式的短期的共用的存取簽章： `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`。 若要取得的程式碼產生這個簽章，請參閱[控制 IoT 中樞的存取權](./iot-hub-devguide-security.md#security-token-structure)。


下列程式碼片段會使用[uAMQP 在 Python 中的程式庫](https://github.com/Azure/azure-uamqp-python)連接到 IoT 中樞透過寄件者 連結。

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
| 裝置 | 接收者連結 | `/devices/<deviceID>/messages/devicebound` | 此連結上所接收目的地為裝置的雲端到裝置訊息，每個目的地裝置。 |
| 裝置 | 寄件者 連結 | `/devices/<deviceID>messages/events` | 從裝置傳送裝置到雲端訊息會透過此連結傳送。 |
| 裝置 | 寄件者 連結 | `/messages/serviceBound/feedback` | 透過此連結傳送給服務的裝置的雲端到裝置訊息意見反應。 |


### <a name="receive-cloud-to-device-commands-device-client"></a>接收雲端到裝置命令 （裝置用戶端）
傳送至裝置的雲端到裝置命令到達`/devices/<deviceID>/messages/devicebound`連結。 裝置可以在批次中接收這些訊息，並視需要使用訊息資料內容、 訊息屬性、 註解或應用程式屬性的訊息中。

下列程式碼片段會使用[uAMQP 在 Python 中的程式庫](https://github.com/Azure/azure-uamqp-python)) 以接收雲端到裝置訊息的裝置。

```python
# ... 
# Create a receive client for the cloud-to-device receive link on the device
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

    # Message sequence number in the built-in event hub
    print('\tx-opt-sequence-number:  ' + str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>傳送遙測訊息 （裝置用戶端）
您也可以使用 AMQP，從裝置傳送遙測訊息。 裝置可以選擇性地提供應用程式屬性的字典或各種訊息屬性，例如訊息識別碼。

下列程式碼片段會使用[uAMQP 在 Python 中的程式庫](https://github.com/Azure/azure-uamqp-python)從裝置傳送裝置到雲端訊息。


```python
# ... 
# Create a send client for the device-to-cloud send link on the device
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
* AMQP 連線可能會因為網路問題或驗證到期而中斷與語彙基元 （產生程式碼中）。 服務用戶端必須處理這些情況下，並重新建立連線和連結，如有需要。 如果驗證權杖過期時，用戶端可以避免連接下拉式主動更新在其訂閱到期前的語彙基元。
* 有時會對您的用戶端能夠正確處理連結重新導向。 若要了解這類作業，請參閱您的 AMQP 用戶端文件。

## <a name="next-steps"></a>後續步驟

若要深入了解 AMQP 通訊協定，請參閱[AMQP v1.0 規格](http://www.amqp.org/sites/amqp.org/files/amqp.pdf)。

若要深入了解 IoT 中樞傳訊，請參閱：

* [雲端到裝置訊息](./iot-hub-devguide-messages-c2d.md)
* [支援其他通訊協定](iot-hub-protocol-gateway.md)
* [訊息佇列遙測傳輸 (MQTT) 通訊協定的支援](./iot-hub-mqtt-support.md)
