---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: d7a9923d5bd9e357bcd75fae6e0a7d1bcd437a53
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64732648"
---
在適當的 SSL 憑證可確保您要到正確的伺服器傳送加密的資訊。 除了加密，憑證也用來驗證。 您可以上傳您自己受信任的 SSL 憑證，透過裝置的 PowerShell 介面。

1. [連線到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用`Set-HcsCertificate`cmdlet 來上傳的憑證。 出現提示時，提供下列參數：

   - `CertificateFilePath` -包含中的憑證檔案的共用路徑 *.pfx*格式。
   - `CertificatePassword` -用來保護憑證的密碼。
   - `Credentials` -使用者名稱和密碼來存取共用，其中包含的憑證。

     下列範例會示範這個指令程式的使用方式：

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username/Password"
     ```

