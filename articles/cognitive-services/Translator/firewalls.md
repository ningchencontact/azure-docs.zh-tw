---
title: 防火牆後翻譯 - Translator Text API
titlesuffix: Azure Cognitive Services
description: 使用 Translator Text API IP 進行防火牆後翻譯。
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: v-jansko
ms.openlocfilehash: 1e598594f53bc94d3a740d2bd5cdd100456131fa
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884727"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>如何使用 Translator Text API IP 進行防火牆後翻譯

Translator Text API 可以在使用網域名稱或 IP 篩選的防火牆後方進行翻譯。 網域名稱篩選是慣用的方法。 我們**不建議**從已篩選 IP 的防火牆後方執行 Microsoft Translator。 安裝程式未來可能會中斷，恕不另行通知。 

## <a name="translator-ip-addresses"></a>Translator IP 位址
api.cognitive.microsofttranslator.com 的 IP 位址 - Microsoft Translator Text API (2018 年 11 月 20 日生效)：

* **亞太地區：** 40.90.139.163、104.44.89.44
* **歐洲︰** 40.90.138.4、40.90.141.99
* **北美洲：** 40.90.139.36、40.90.139.2


## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [在 Translator API 呼叫中進行 IP 防火牆後翻譯](reference/v3-0-translate.md)
