---
title: 將您的安全性訊息傳送至 IoT 的 Azure 資訊安全中心 |Microsoft Docs
description: 瞭解如何使用適用于 IoT 的 Azure 資訊安全中心來傳送安全性訊息。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: c780eea15b9f064d3279c75ac2f967e8b6099ecb
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596214"
---
# <a name="send-security-messages-sdk"></a>傳送安全性訊息 SDK

本操作指南說明當您選擇收集和傳送裝置安全性訊息, 而不使用 IoT 代理程式的 Azure 資訊安全中心時, 適用于 IoT 服務功能的 Azure 資訊安全中心, 並說明如何執行此動作。  

在本指南中，您將了解如何： 
> [!div class="checklist"]
> * 使用 C# 的傳送安全性訊息 API
> * 使用 C 的傳送安全性訊息 API

## <a name="azure-security-center-for-iot-capabilities"></a>IoT 功能的 Azure 資訊安全中心

只要傳送的資料符合[IoT 架構的 Azure 資訊安全中心](https://aka.ms/iot-security-schemas), 而且訊息已設定為安全性訊息, IoT 的 Azure 資訊安全中心就可以處理及分析任何類型的安全性訊息資料。

## <a name="security-message"></a>安全性訊息

IoT 的 Azure 資訊安全中心會使用下列準則來定義安全性訊息:
- 如果訊息是透過 Azure IoT C/C# SDK 所傳送
- 如果訊息符合[安全性訊息架構](https://aka.ms/iot-security-schemas)
- 如果訊息在傳送之前已設定為安全性訊息

每個安全性訊息都包含寄件者的元`AgentId`資料`AgentVersion` `MessageSchemaVersion` , 例如、和安全性事件清單。
架構會定義安全性訊息 (包括事件種類) 的有效和必要屬性。

>[!Note]
> 所傳送的訊息若不符合結構描述，則會遭到忽略。 請務必先確認結構描述再起始資料的傳送，因為系統目前並不會儲存遭到忽略的訊息。 

>[!Note]
> 未將使用 Azure IoT C/C# SDK 設定為安全性訊息的已傳送訊息, 將不會路由傳送至 IoT 管線的 Azure 資訊安全中心

## <a name="valid-message-example"></a>有效的訊息範例

下列範例顯示有效的安全性訊息物件。 此範例包含訊息中繼資料和一個`ProcessCreate`安全性事件。

一旦設定為安全性訊息並傳送之後, IoT 的 Azure 資訊安全中心就會處理此訊息。

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>傳送安全性訊息 

使用[Azure C# iot 裝置 Sdk](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview)或[azure iot C 裝置 Sdk](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview), 在不使用 IoT 代理程式的 Azure 資訊安全中心的情況下傳送安全性訊息。

若要從您的裝置傳送裝置資料以供 IoT Azure 資訊安全中心處理, 請使用下列其中一個 Api 將訊息標示為正確路由至 IoT 處理管線的 Azure 資訊安全中心。 

所有傳送的資料 (即使以正確的標頭標記) 也必須符合[IoT 訊息架構的 Azure 資訊安全中心](https://aka.ms/iot-security-schemas)。 

### <a name="send-security-message-api"></a>傳送安全性訊息 API

**傳送安全性訊息** API 目前適用於 C 和C#。  

#### <a name="c-api"></a>C# API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="c-api"></a>C API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {
 
    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
 
    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);
 
    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }
 
cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }
 
    return success;
}
 
static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }
 
    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

## <a name="next-steps"></a>後續步驟
- 閱讀 IoT 服務的 Azure 資訊安全中心[總覽](overview.md)
- 深入瞭解 IoT[架構](architecture.md)的 Azure 資訊安全中心
- 啟用[服務](quickstart-onboard-iot-hub.md)
- 閱讀[常見問題集](resources-frequently-asked-questions.md)
- 了解如何存取[未經處理的安全性資料](how-to-security-data-access.md)
- 了解[建議](concept-recommendations.md)
- 了解[警示](concept-security-alerts.md)
