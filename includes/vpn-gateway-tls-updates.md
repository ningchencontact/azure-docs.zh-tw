---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9a17f34333503436d3da340670abdde154e45ef6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38727519"
---
從 2018 年 7 月 1 日起，對 TLS 1.0 和 1.1 的支援將會從 Azure VPN 閘道移除。 VPN 閘道僅支援 TLS 1.2。 若要為您的 Windows 7 和 Windows 8 點對站用戶端 (使用 TLS) 維護 TLS 支援和連線能力，我們建議您安裝下列更新：

•   [可啟用 TLS 的 Microsoft EAP 實作更新](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th)

•   [更新為啟用 TLS 1.1 和 TLS 1.2 作為 WinHTTP 中的預設安全通訊協定](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in)

下列舊版演算法也會在 2018 年 7 月 1 日針對 TLS 取代：

* RC4 (Rivest Cipher 4)
* DES (資料加密演算法)
* 3DES (三重資料加密演算法)
* MD5 (訊息摘要 5)
