---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f77310ebf10812c1bf15a2555520f46c10c8c182
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165757"
---
<!--alkohli 02/21/2017 cloud appliance security-->

使用 StorSimple 雲端設備時，請留意下列安全性考量：

* 雲端設備會透過您的 Microsoft Azure 訂用帳戶受到保護。 這表示，如果您使用的是雲端設備，而您的 Azure 訂用帳戶遭到洩漏，則儲存在雲端設備上的資料也會受到影響。
* 用來對儲存在 StorSimple 中的資料加密的憑證公開金鑰，會安全地在 Azure 入口網站中供人使用，而私密金鑰則會保留在 StorSimple 雲端設備中。 在 StorSimple 雲端設備上，公開和私密金鑰都會儲存於 Azure 中。
* 雲端設備會裝載於 Microsoft Azure 資料中心。

