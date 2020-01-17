---
title: 如何設定適用于 Linux 的 OpenSSL
titleSuffix: Azure Cognitive Services
description: 瞭解如何設定適用于 Linux 的 OpenSSL。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: cadf31dede8ee81323076013d00b9431f597bda6
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156483"
---
# <a name="configure-openssl-for-linux"></a>設定適用于 Linux 的 OpenSSL

在1.9.0 之前使用任何語音 SDK 版本時， [OpenSSL](https://www.openssl.org)會以動態方式設定為主機系統版本。 在較新版本的語音 SDK 中，OpenSSL （版本[1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)）會以靜態方式連結至語音 sdk 的核心程式庫。

## <a name="troubleshoot-connectivity"></a>針對連線問題進行疑難排解

如果使用1.9.0 版本的語音 SDK 時發生連線失敗，請確定 `ssl/certs` 目錄存在於 `/usr/lib` directory 中，也就是在 Linux 檔案系統中找到。 如果 `ssl/certs` 目錄*不存在*，請使用下列命令來檢查您系統中安裝 OpenSSL 的位置：

```bash
which openssl
```

然後，找出 OpenSSL `certs` 目錄，並將該目錄的內容複寫到 `/usr/lib/ssl/certs` 目錄。 接下來，再試一次，查看連線問題是否已解決。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [關於語音 SDK](speech-sdk.md)