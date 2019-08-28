---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: f3b9f6c27fb8d423350eac5d286c9859ad6fbd37
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70104232"
---
## <a name="about-the-user-delegation-sas-preview"></a>關於使用者委派 SAS (預覽)

您可以使用 Azure AD 認證或帳戶金鑰來保護存取容器或 blob 的 SAS 權杖。 使用 Azure AD 認證保護的 SAS 稱為使用者委派 SAS, 因為用來簽署 SAS 的 OAuth 2.0 權杖會代表使用者要求。

Microsoft 建議您盡可能使用 Azure AD 認證做為安全性最佳作法, 而不是使用帳戶金鑰, 這樣會更容易遭到入侵。 當您的應用程式設計需要共用存取簽章時, 請使用 Azure AD 認證來建立使用者委派 SAS, 以獲得更佳的安全性。 如需使用者委派 SAS 的詳細資訊, 請參閱[建立使用者委派 sas](/rest/api/storageservices/create-user-delegation-sas)。

> [!NOTE]
> 使用者委派 SAS 預覽僅供非生產環境使用。

> [!CAUTION]
> 任何擁有有效 SAS 的用戶端, 都可以存取該 SAS 所允許的儲存體帳戶中的資料。 保護 SAS 不受惡意或非預期的使用是很重要的。 散發 SAS 時請謹慎使用, 並備妥用於撤銷遭入侵 SAS 的計畫。

如需共用存取簽章的詳細資訊, 請參閱[使用共用存取簽章 (SAS) 授與 Azure 儲存體資源的有限存取權](../articles/storage/common/storage-sas-overview.md)。
