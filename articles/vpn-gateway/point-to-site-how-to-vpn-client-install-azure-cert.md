---
title: 安裝 P2S 用戶端憑證 | Azure
description: 安裝用於 P2S 憑證驗證的 Mac 或 Windows 用戶端憑證。
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager, azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: cherylmc
ms.openlocfilehash: bf2788fff64ab8b3a5ccf75b8a80f2bd5aba5151
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
ms.locfileid: "30317988"
---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>安裝用於 Azure 點對站憑證驗證連線的用戶端憑證

使用 Azure 點對站憑證驗證來連線到虛擬網路的所有用戶端都必須有用戶端憑證。 本文可協助您安裝使用 P2S 連線至 VNet 時要供驗證使用的用戶端憑證。

## <a name="generate"></a>產生並匯出用戶端憑證

您可以透過使用企業 CA 解決方案所產生的根憑證來產生用戶端憑證，也可以透過自我簽署的根憑證來產生。 如需相關步驟，請參閱 [PowerShell](vpn-gateway-certificates-point-to-site.md) 或 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 指示。 在產生用戶端憑證後，請將它們匯出為 .pfx 檔案。 匯出時請務必包含整個憑證鏈結。

## <a name="installwin"></a>安裝憑證 - Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a> - Mac

只有 Resource Manager 部署模型能支援 Mac VPN 用戶端。 傳統部署模型則不支援。

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>後續步驟

繼續進行點對站組態步驟。

* [Azure 入口網站](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Azure 入口網站 (傳統)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
