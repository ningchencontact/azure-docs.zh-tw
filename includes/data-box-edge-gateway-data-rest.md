---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8a6ceee0f7db4b7b368dce1a28c8edc0b2b11180
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754252"
---
對於待用資料：

- BitLocker XTS-AES AES 256 位元加密來保護本機資料。
- 資料儲存在共用的存取權會受到限制。

    - 存取共用資料的 SMB 用戶端需要與共用相關聯的使用者認證。 建立共用時，會定義這些認證。
    - 建立共用時，會新增要存取共用的 NFS 用戶端的 IP 位址。