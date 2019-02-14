---
title: 產生並匯出點對站的憑證：Linux：CLI：Azure | Microsoft Docs
description: 建立自我簽署的根憑證、匯出公開金鑰，以及使用 Linux (strongSwan) CLI 產生用戶端憑證。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/31/2019
ms.author: cherylmc
ms.openlocfilehash: b673be47d4951adab08f04efc56410095f549356
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2019
ms.locfileid: "55564780"
---
# <a name="generate-and-export-certificates"></a>產生並匯出憑證

點對站連線使用憑證進行驗證。 本文說明如何建立自我簽署的根憑證，以及使用 Linux CLI 和 strongSwan 產生用戶端憑證。 如果您要尋找不同的憑證指示，請參閱 [Powershell](vpn-gateway-certificates-point-to-site.md) 或 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 文章。 如需如何使用 GUI (而非使用 CLI) 安裝 strongSwan 的相關資訊，請參閱[用戶端組態](point-to-site-vpn-client-configuration-azure-cert.md#install)一文中的步驟。

## <a name="generate-and-export"></a>產生並匯出
[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>後續步驟

請繼續進行點對站設定，以[建立和安裝 VPN 用戶端組態檔](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)。
