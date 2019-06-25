---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 53ccd439429dc7df311e086d79f7234e26fd8b0e
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302336"
---
> [!IMPORTANT]
> 儲存體帳戶金鑰很類似儲存體帳戶的根密碼。 請務必小心保護您的帳戶金鑰。 請避免轉發給其他使用者、進行硬式編碼，或將它儲存在其他人可以存取的純文字當中。 如果您認為帳戶金鑰可能遭到破解，請使用 Azure 入口網站重新產生帳戶金鑰。
>
> SAS (共用存取簽章) 權杖就像帳戶存取金鑰一樣，非常需要受到保護。 提供細微性時，SAS 會為用戶端授與存取儲存體帳戶中資源的權限，而且不應該公開共用。 若基於疑難排解因素而需要共用，請考慮使用任意記錄檔的編校版本，或從記錄檔中刪除 SAS 權杖 (如果有的話)，並確定螢幕擷取畫面也不會包含 SAS 資訊。
