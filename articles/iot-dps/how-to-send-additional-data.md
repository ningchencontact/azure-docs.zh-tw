---
title: 如何在裝置與 Azure 裝置布建服務之間傳輸其他資料
description: 本檔說明如何在裝置與 DPS 之間傳輸其他資料
author: menchi
ms.author: menchi
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 90402dd6fbe19811b5bb6d5ac0fbdd984b71fd33
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123176"
---
# <a name="how-to-transfer-additional-data-between-device-and-dps"></a>如何在裝置與 DPS 之間傳輸其他資料
DPS 有時候需要從裝置取得更多資料，才能適當地將它們佈建到正確的 IoT 中樞，而且該資料需要由裝置提供。 相反地，DPS 可以將資料傳回給裝置，以協助用戶端邏輯。 

## <a name="when-to-use-it"></a>使用時機
這項功能可用來做為[自訂配置](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)的增強功能。 例如，您想要在沒有人為介入的情況下，根據裝置型號來配置您的裝置。 在此情況下，您將使用[自訂配置](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)。 您可以設定裝置，在[註冊裝置呼叫](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)中報告模型資訊。 DPS 會將裝置的資訊傳遞至自訂配置 webhook。 而您的函式可以決定當此裝置收到裝置型號資訊時，會前往哪一個 IoT 中樞。 同樣地，如果 Webhook 希望傳回一些資料給裝置，它會在 Webhook 回應中以字串的形式將資料傳遞回去。  

## <a name="device-sends-data-to-dps"></a>裝置將資料傳送至 DPS
當您的裝置將[註冊裝置呼叫](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)傳送至 DPS 時，可以增強註冊呼叫以接受本文中的其他欄位。 本文看起來如下所示： 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “data”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS 將資料傳回到裝置
如果自訂配置原則 webhook 想要將某些資料傳回給裝置，它會將資料以字串形式傳入 webhook 回應中。 這項變更是在下面的 returnData 一節中。 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "returnData": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK 支援
這項功能適用于 C、 C#、JAVA 和 node.js[用戶端 sdk](https://docs.microsoft.com/azure/iot-dps/)。  

## <a name="next-steps"></a>後續步驟
* 針對 Azure IoT 中樞和 Azure IoT 中樞裝置佈建服務，使用 [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) \(英文\) 進行開發
