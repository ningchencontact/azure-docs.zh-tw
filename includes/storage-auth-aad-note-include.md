---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 50217829be7aff3bcdbf417f19fff9f1fd7c6901
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372658"
---
> [!NOTE]
> - RBAC 角色指派可能需要兩分鐘的時間傳播。
>
> 若要以 OAuth 權杖為 Blob 和佇列作業授權，您必須使用 HTTPS。
>
> - Azure 入口網站現在支援在預覽版本中使用 Azure AD 認證來讀取和寫入 Blob 與佇列資料。 若要存取 Azure 入口網站中的 blob 和佇列資料，使用者必須具有 Azure Resource Manager**讀取器**角色時，除了 blob 或佇列的存取權的適當角色。 如需詳細資訊，請參閱 <<c0> [ 授與存取權的 Azure 容器和 Azure 入口網站中的使用 RBAC 佇列](../articles/storage/common/storage-auth-aad-rbac.md)。 
> 
> - [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)目前使用儲存體帳戶金鑰來存取 Blob 和佇列資料。 如果無法使用索引鍵，Azure AD 授權將用於存取 blob。 Azure AD 授權目前不支援佇列。 
>
> - 「Azure 檔案服務」只有針對已加入網域的 VM 才支援透過 SMB 進行 Azure AD 驗證 (預覽)。 若要了解如何針對「Azure 檔案服務」使用透過 SMB 的 Azure AD，請參閱[針對 Azure 檔案透過 SMB 進行 Azure Active Directory 驗證 (預覽) 的概觀](../articles/storage/files/storage-files-active-directory-overview.md)。