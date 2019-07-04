---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448642"
---
在適當的 SSL 憑證可確保您要到正確的伺服器傳送加密的資訊。 除了加密，憑證也用來驗證。 您可以上傳您自己受信任的 SSL 憑證，透過裝置的 PowerShell 介面。

1. [連線到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用`Set-HcsCertificate`cmdlet 來上傳的憑證。 出現提示時，提供下列參數：

   - `CertificateFilePath` -包含中的憑證檔案的共用路徑 *.pfx*格式。
   - `CertificatePassword` -用來保護憑證的密碼。
   - `Credentials` -存取的共用，包含憑證的使用者名稱。 提供當系統提示您的網路共用的密碼。

     下列範例會示範這個指令程式的使用方式：

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

