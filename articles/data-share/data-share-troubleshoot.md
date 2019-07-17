---
title: 針對 Azure Data Share Preview 進行疑難排解
description: 了解如何使用 Azure Data Share Preview 進行問題的疑難排解
services: data-share
author: joannapea
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: ef33ff41ce78657709a497860cdbc3acb1bb24ae
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789002"
---
# <a name="troubleshoot-common-issues-in-azure-data-share-preview"></a>針對 Azure Data Share Preview 的常見問題進行疑難排解

本文說明如何針對 Azure Data Share Preview 的常見問題進行疑難排解。 

## <a name="azure-data-share-invitations"></a>Azure Data Share 邀請 

在某些情況下，當新的使用者從已傳送的電子郵件邀請中按一下 [接受邀請]  時，他們可能會看到空白的邀請清單。 

![沒有邀請](media/no-invites.png)

上述錯誤是此服務的已知問題，目前正在處理。 解決方法是遵循下列步驟。 

1. 在 Azure 入口網站中，瀏覽到 [訂用帳戶]  。
1. 選取您用於 Azure Data Share 的訂用帳戶
1. 按一下 [資源提供者] 
1. 搜尋 Microsoft.DataShare
1. 按一下 [註冊] 

您必須擁有 [Azure 參與者 RBAC 角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)才能完成這些步驟。 

> [!IMPORTANT]
> 如果您已接受 Azure Data Share 邀請並結束服務才設定儲存體，請遵循[設定資料集對應](how-to-configure-mapping.md)操作指南所述的指示，來了解如何完成所接收資料共用的設定，並開始接收資料。 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>建立或接收新的 Data Share 時發生錯誤

「錯誤：作業傳回無效的狀態碼 'BadRequest'」

「錯誤：AuthorizationFailed」

「錯誤：儲存體帳戶的角色指派」

![權限錯誤](media/error-write-privilege.png)

如果您在建立新的資料共用或接收新的資料共用時收到任何上述錯誤，原因會是沒有足夠的儲存體帳戶權限。 所需動作為 Microsoft.Authorization/role assignments/write  ，這存在於儲存體擁有者角色，或者也可以指派給自訂角色。 即使您建立了儲存體帳戶，但不代表您就會自動成為儲存體帳戶的擁有者。 請遵循下列步驟來對自己授與儲存體帳戶的擁有者角色。 或者，您也可以建立有此權限的自訂角色，再將自己新增至該角色。  

1. 在 Azure 入口網站中瀏覽至儲存體帳戶
1. 選取 [存取控制 (IAM)] 
1. 按一下 [新增] 
1. 將自己新增為儲存體 Blob 資料擁有者

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。

