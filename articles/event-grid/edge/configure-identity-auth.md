---
title: 設定身分識別-Azure Event Grid IoT Edge |Microsoft Docs
description: 設定事件方格模組的身分識別
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 336b6157128468169264d6ffa9564da4d9338aae
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992440"
---
# <a name="configure-identity-for-the-event-grid-module"></a>設定事件方格模組的身分識別

本文提供事件方格模組可能身分識別設定的範例。 根據預設，事件方格模組會顯示其身分識別憑證，如 IoT 安全性守護程式所設定。 當事件方格模組在傳遞事件時，會在其撥出電話上呈現身分識別憑證。 事件方格事件的「訂閱者」可以選擇驗證它確實是在接受事件之前傳送事件的事件方格模組。

如需所有可能的設定，請參閱[安全性和驗證](security-authentication.md)指南。

## <a name="always-present-identity-certificate"></a>一律呈現身分識別憑證
以下是一律在撥出電話上呈現身分識別憑證的範例設定。 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>不要出示身分識別憑證
以下是未在撥出電話上呈現身分識別憑證的範例設定。 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=false"
  ]
}
 ```
