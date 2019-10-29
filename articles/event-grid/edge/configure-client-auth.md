---
title: 設定連入呼叫的用戶端驗證-Azure Event Grid IoT Edge |Microsoft Docs
description: 在 IoT Edge 上設定事件方格所公開的 API 通訊協定。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: b5456130e89bf77e2c2ba41880323e38f6b27f4c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992505"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>設定連入呼叫的用戶端驗證

本指南提供事件方格模組的可能用戶端驗證設定範例。 事件方格模組支援兩種類型的用戶端驗證：-

* 共用存取簽章（SAS）金鑰型
* 以憑證為基礎

如需所有可能的設定，請參閱[安全性和驗證](security-authentication.md)指南。

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>啟用以憑證為基礎的用戶端驗證，沒有自我簽署憑證

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>啟用以憑證為基礎的用戶端驗證，允許自我簽署憑證

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>請只在測試環境中，將屬性**輸入： clientAuth： clientCert： allowUnknownCA**設定為**true** ，因為您通常會使用自我簽署憑證。 對於生產工作負載，建議您將此屬性設定為**false** ，並將憑證從憑證授權單位單位（CA）。

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>啟用以憑證為基礎和以 sas 為基礎的用戶端驗證

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=true",
    "inbound:clientAuth:sasKeys:key1=<some-secret1-here>",
    "inbound:clientAuth:sasKeys:key2=<some-secret2-here>",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>以 SAS 金鑰為基礎的用戶端驗證可讓非 IoT edge 模組執行管理和執行時間作業，前提是 API 埠可以在 IoT Edge 網路外部存取。
