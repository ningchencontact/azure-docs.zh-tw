---
title: 更新 Azure 應用程式閘道憑證
description: 了解如何更新與應用程式閘道接聽程式相關聯的憑證。
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/17/2018
ms.author: victorh
ms.openlocfilehash: b125f707e8de17764701e981736a53492e5e756c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356935"
---
# <a name="renew-application-gateway-certificates"></a>更新應用程式閘道憑證

如果您為應用程式閘道設定了 SSL 加密，您在某些時候將必須更新憑證。

您可以使用 Azure 入口網站或 Azure PowerShell 來更新與接聽程式相關聯的憑證：

## <a name="azure-portal"></a>Azure 入口網站

若要從入口網站更新接聽程式憑證，請瀏覽至您的應用程式閘道接聽程式。 按一下有憑證需要更新的接聽程式，然後按一下 [更新或編輯選取的憑證]。

![更新憑證](media/renew-certificate/ssl-cert.png)

上傳新的 PFX 憑證並為其命名，輸入密碼，然後按一下 [儲存]。

## <a name="azure-powershell"></a>Azure PowerShell

若要使用 Azure PowerShell 更新您的憑證，請使用下列 Cmdlet：

```PowerShell
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-azureRmApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password
```

## <a name="next-steps"></a>後續步驟

若要了解如何設定與「Azure 應用程式閘道」搭配運作的「SSL 卸載」，請參閱 [設定 SSL 卸載](application-gateway-ssl-portal.md)
