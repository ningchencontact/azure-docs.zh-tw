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
ms.openlocfilehash: fbc5ca4d433be65d43ae535703cc7f765dda0a1e
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292669"
---
> [!NOTE]
> - 適用於 Blob 和佇列的 Azure AD 驗證預覽版僅供用於非生產環境。 生產環境的服務等級協定 (SLA) 目前無法使用。 如果您的案例尚未支援 Azure AD 驗證，請繼續在應用程式中使用您的共用金鑰授權或 SAS 權杖。
>
> - 在預覽期間，RBAC 角色指派可能需要五分鐘的時間傳播。
>
> - 若要以 OAuth 權杖為 Blob 和佇列作業授權，您必須使用 HTTPS。
>
> - Azure 入口網站現在支援在預覽版本中使用 Azure AD 認證來讀取和寫入 Blob 與佇列資料。
> 
> - [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)目前使用儲存體帳戶金鑰來存取 Blob 和佇列資料。
>
> - 「Azure 檔案服務」只有針對已加入網域的 VM 才支援透過 SMB 進行 Azure AD 驗證 (預覽)。 若要了解如何針對「Azure 檔案服務」使用透過 SMB 的 Azure AD，請參閱[針對 Azure 檔案透過 SMB 進行 Azure Active Directory 驗證 (預覽) 的概觀](../articles/storage/files/storage-files-active-directory-overview.md)。



