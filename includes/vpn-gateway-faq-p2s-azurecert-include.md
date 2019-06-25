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
ms.openlocfilehash: e2e91dc91cf0fbe6827808785a4c3cc25b06542b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173799"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>是否可以使用自己的內部 PKI 根 CA 進行點對站台連線？

是。 先前只能使用自我簽署的根憑證。 您仍然可以上傳 20 個根憑證。

### <a name="what-tools-can-i-use-to-create-certificates"></a>我可以使用哪些工具來建立憑證？

您可以使用 Enterprise PKI 解決方案 (您的內部 PKI)、Azure PowerShell、MakeCert 和 OpenSSL。

### <a name="certsettings"></a>是否有憑證設定及參數的指示？

* **內部 PKI/Enterprise PKI 解決方案：** 步驟請參閱 <<c0> [ 產生憑證](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)。

* **Azure PowerShell：** 請參閱[Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md)文章的步驟。

* **MakeCert:** 請參閱[MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)文章的步驟。

* **OpenSSL：** 

    * 匯出憑證時，請務必將根憑證轉換為 Base64。

    * 針對用戶端憑證：

      * 建立私密金鑰時，請將長度指定為 4096。
      * 建立憑證時，針對 -extensions  參數，請指定 usr_cert  。