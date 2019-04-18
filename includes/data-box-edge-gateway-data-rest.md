---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: d8570c0be6b64d4e289575ce3f3f1721c4a65074
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684406"
---
針對資料待用：

- 針對資料待用，BitLocker XTS AES-256 加密來保護本機資料。
- 位於 共用資料的存取權的共用時受到限制。

    - 存取共用資料的 SMB 用戶端，它們需要與共用相關聯的使用者認證。 這些認證會在共用建立時定義。
    - 存取共用的 NFS 用戶端，用戶端的 IP 位址需要在建立共用時加入。