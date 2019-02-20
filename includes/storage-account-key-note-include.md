---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3cfdca99c91dc54a711801d92aa0da91fb9703e4
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246771"
---
> [!IMPORTANT]
> 儲存體帳戶金鑰很類似儲存體帳戶的根密碼。 請務必小心保護您的帳戶金鑰。 請避免轉發給其他使用者、進行硬式編碼，或將它儲存在其他人可以存取的純文字當中。 如果您認為帳戶金鑰可能遭到破解，請使用 Azure 入口網站重新產生帳戶金鑰。
> 
> SAS (共用存取簽章) 權杖就像帳戶存取金鑰一樣，非常需要受到保護。 提供細微性時，SAS 會為用戶端授與存取儲存體帳戶中資源的權限，而且不應該公開共用。 若基於疑難排解因素而需要共用，請考慮使用任意記錄檔的編校版本，或從記錄檔中刪除 SAS 權杖 (如果有的話)，並確定螢幕擷取畫面也不會包含 SAS 資訊。
> 
> Microsoft 建議使用 Azure Active Directory (Azure AD) 驗證 Blob 和佇列儲存體應用程式 (預覽)，藉以盡可能增強安全性。 如需詳細資訊，請參閱[使用 Azure Active Directory 來驗證 Azure Blob 和佇列的存取權 (預覽)](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)。
