---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 23587e617c62cfe4aa24256330c5f7673dd1c674
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684399"
---
您的裝置已為您在 Azure 中的資料當做目的地儲存體帳戶相關聯。 儲存體帳戶的存取權由訂用帳戶和兩個 512 位元儲存體控制存取該儲存體帳戶相關聯的金鑰。

資料方塊的邊緣裝置來存取儲存體帳戶時，其中一個金鑰用於驗證。 其他金鑰會被保留，讓您可以定期輪替金鑰。

基於安全性理由，許多資料中心需要金鑰輪替。 建議您按照這些最佳作法進行金鑰輪替：

- 儲存體帳戶金鑰很類似儲存體帳戶的根密碼。 謹慎保護您的帳戶金鑰。 不要散發給其他使用者的密碼、 硬式程式碼，或將它儲存在其他人可以存取的純文字。
- [重新產生帳戶金鑰](../articles/storage/common/storage-account-manage.md#regenerate-access-keys)使用 Azure 入口網站中，如果您認為它可能已遭入侵。
- Azure 系統管理員應該使用 Azure 入口網站的 [儲存體] 區段來直接存取儲存體帳戶，以定期變更或重新產生主要或次要金鑰。