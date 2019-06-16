---
title: 在 Azure Service Fabric Linux 叢集上設定加密憑證並將秘密加密 | Microsoft Docs
description: 了解如何設定加密憑證，並在 Linux 叢集上將祕密加密。
services: service-fabric
documentationcenter: .net
author: shsha
manager: ''
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: 9589d6ea69a2293d592a9e63f2b726f1a620bb9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126982"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>設定加密憑證，並在 Linux 叢集上將祕密加密
本文書名如何設定加密憑證，並在 Linux 叢集上將祕密加密。 針對 Windows 叢集，請參閱[設定加密憑證，並在 Windows 叢集上將祕密加密][secret-management-windows-specific-link]。

## <a name="obtain-a-data-encipherment-certificate"></a>取得資料加密憑證
資料編密憑證只會用於服務的 Settings.xml 中[參數][parameters-link]，以及服務的 ServiceManifest.xml 中[環境變數][environment-variables-link]的加密與解密。 它不是用來驗證或簽署密碼文字。 憑證必須符合下列要求：

* 憑證必須包含私密金鑰。
* 憑證的金鑰使用法必須包含資料編密 (10)，而且不應該包含伺服器驗證或用戶端驗證。

  例如，下列命令可用於使用 OpenSSL 產生所需的憑證：
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>在叢集中安裝憑證
該憑證必須安裝在叢集中的每個節點上 (於 `/var/lib/sfcerts` 下)。 服務執行所在的使用者帳戶 (預設為 sfuser) **應該具有已安裝的憑證的讀取權限** (也就是目前的範例 `/var/lib/sfcerts/TestCert.pem`)。

## <a name="encrypt-secrets"></a>將秘密加密
下列程式碼片段可以用來加密祕密。 此程式碼片段只會將值加密；並**不會**簽署密碼文字。 **您必須使用**安裝在叢集中相同的編密憑證，以產生密碼值的加密文字。

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
產生的 Base-64 編碼字串輸入至 encrypted.txt 同時包含密碼的加密文字，以及用來對其加密的憑證相關資訊。 您可以使用 OpenSSL 解密來驗證其有效性。
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>後續步驟
了解如何[指定應用程式中已加密的祕密][secret-management-specify-encrypted-secrets-link]。

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
