---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 2d641287bcf095f13719667a91b7f61295309b5d
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430352"
---
> [!NOTE]
> - 適用於 Blob 和佇列的 Azure AD 驗證預覽版僅供用於非生產環境。 生產環境的服務等級協定 (SLA) 目前無法使用。 如果您的案例尚未支援 Azure AD 驗證，請繼續在應用程式中使用您的共用金鑰授權或 SAS 權杖。
>
> - 在預覽期間，RBAC 角色指派可能需要五分鐘的時間傳播。
>
> - 在呼叫 Blob 和佇列作業時，您必須使用 HTTPS 來向 Azure AD 進行驗證。
>
> - Azure 入口網站現在支援在預覽版本中使用 Azure AD 認證來讀取和寫入 Blob 資料。
> 
> - Azure 入口網站目前不支援使用 Azure AD 認證來讀取和寫入佇列資料。 佇列資料可透過儲存體帳戶金鑰存取。
>
> - [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)目前使用儲存體帳戶金鑰來存取 Blob 和佇列資料。
>
> - 「Azure 檔案服務」只有針對已加入網域的 VM 才支援透過 SMB 進行 Azure AD 驗證 (預覽)。 若要了解如何針對「Azure 檔案服務」使用透過 SMB 的 Azure AD，請參閱[針對 Azure 檔案透過 SMB 進行 Azure Active Directory 驗證 (預覽) 的概觀](../articles/storage/files/storage-files-active-directory-overview.md)。



