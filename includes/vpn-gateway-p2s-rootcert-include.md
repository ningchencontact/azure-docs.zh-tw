---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b2a96aad793d956b3ea3de06fba74bcf68e50786
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
您可以使用透過企業解決方案產生的根憑證 (建議)，也可以產生自我簽署憑證。 建立根憑證之後，請將公開憑證資料 (不是私密金鑰) 匯出為 Base-64 編碼的 X.509.cer 檔案，並將公開憑證資料上傳至 Azure。

* **企業憑證：**如果您是使用企業解決方案，則可以使用現有的憑證鏈結。 取得您想要使用的根憑證 .cer 檔案。
* **自我簽署根憑證：**如果您未使用企業憑證解決方案，則必須建立自我簽署的根憑證。 您務必遵循以下其中一篇 P2S 憑證文章中的步驟。 否則，您所建立的憑證將無法與 P2S 連線相容，而且用戶端會在嘗試連線時收到連線錯誤訊息。 您可以使用 Azure PowerShell、MakeCert 或 OpenSSL。 所提供文章中的步驟都會產生相容的憑證：

  * [Windows 10 PowerShell 指示](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md)：這些指示需要 Windows 10 和 PowerShell，以產生憑證。 從根憑證產生的用戶端憑證可以安裝於任何支援的 P2S 用戶端上。
  * [MakeCert 指示](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)：如果您無法存取 Windows 10 電腦來產生憑證，則可使用 MakeCert。 MakeCert 已被取代，但您仍可使用 MakeCert 來產生憑證。 從根憑證產生的用戶端憑證可以安裝於任何支援的 P2S 用戶端上。
