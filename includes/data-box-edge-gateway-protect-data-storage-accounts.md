---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174623"
---
您的裝置已為您在 Azure 中的資料當做目的地儲存體帳戶相關聯。 儲存體帳戶的存取權由訂用帳戶和兩個 512 位元儲存體控制存取該儲存體帳戶相關聯的金鑰。

資料方塊的邊緣裝置來存取儲存體帳戶時，其中一個金鑰用於驗證。 另一個索引鍵會被保留，因此您可以定期輪替金鑰。

基於安全性理由，許多資料中心需要金鑰輪替。 建議您按照這些最佳作法進行金鑰輪替：

- 儲存體帳戶金鑰很類似儲存體帳戶的根密碼。 謹慎保護您的帳戶金鑰。 不要散發給其他使用者的密碼、 硬式程式碼，或將它儲存在其他人可以存取的純文字。
- [重新產生帳戶金鑰](../articles/storage/common/storage-account-manage.md#regenerate-access-keys)透過 Azure 入口網站，如果您認為可能外洩。
- 您的 Azure 系統管理員應定期變更或重新產生主要或次要金鑰使用 Azure 入口網站的 [儲存體] 區段來直接存取儲存體帳戶。