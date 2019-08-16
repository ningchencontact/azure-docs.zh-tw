---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 93f6bc8533218af7f0e6dcd1c5f7be6fe8c00e29
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520820"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>我可以使用自己的內部 PKI 根 CA 來產生點對站連線的憑證嗎？

是的。 先前只能使用自我簽署的根憑證。 您仍然可以上傳 20 個根憑證。

### <a name="can-i-use-certificates-from-azure-key-vault"></a>我可以使用來自 Azure Key Vault 的憑證嗎？

資料分割

### <a name="what-tools-can-i-use-to-create-certificates"></a>我可以使用哪些工具來建立憑證？

您可以使用 Enterprise PKI 解決方案 (您的內部 PKI)、Azure PowerShell、MakeCert 和 OpenSSL。

### <a name="certsettings"></a>是否有憑證設定及參數的指示？

* **內部 PKI/企業 PKI 解決方案:** 請參閱[產生憑證](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)的步驟。

* **Azure PowerShell：** 如需步驟, 請參閱[Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md)一文。

* **MakeCert**如需步驟, 請參閱[MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)一文。

* **OpenSSL：** 

    * 匯出憑證時，請務必將根憑證轉換為 Base64。

    * 針對用戶端憑證：

      * 建立私密金鑰時，請將長度指定為 4096。
      * 建立憑證時，針對 -extensions 參數，請指定 usr_cert。
