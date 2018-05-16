---
title: 保護您的物聯網 (IoT) 部署 | Microsoft Docs
description: 本文將詳細說明如何保護您的 IoT 部署
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 95c23341-16b0-4954-b3f2-d2e82ab7b367
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: c14d2082854fe88df9d1139c619b061699e443aa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
[!INCLUDE [iot-secure-your-deployment](../../includes/iot-secure-your-deployment.md)]

## <a name="iot-solution-accelerator-cipher-suites"></a>IoT 解決方案加速器的加密套件

IoT 解決方案加速器會依照此順序支援下列加密套件。

| 加密套件 | 長度 |
| --- | --- |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 (0xc028) ECDH secp384r1 (相等於 7680 位元 RSA) FS |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0xc027) ECDH secp256r1 (相等於 3072 位元 RSA) FS |128 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014) ECDH secp384r1 (相等於 7680 位元 RSA) FS |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013) ECDH secp256r1 (相等於 3072 位元 RSA) FS |128 |
| TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 (0x9c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA256 (0x3d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0x3c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x35) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x2f) |128 |
| TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0xa) |112 |

## <a name="see-also"></a>另請參閱
您也可以探索 IoT 解決方案加速器的一些其他特性與功能：

* [預測性維護解決方案加速器概觀][lnk-predictive-overview]
* [IoT 解決方案加速器的常見問題集][lnk-faq]

您可以在 IoT 中樞開發人員指南的[控制 IoT 中樞的存取權][lnk-devguide-security]中閱讀關於 IoT 中樞安全性的資訊。

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-devguide-security]: ../iot-hub/iot-hub-devguide-security.md
