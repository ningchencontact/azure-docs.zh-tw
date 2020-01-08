---
title: 條件式存取-封鎖舊版驗證-Azure Active Directory
description: 建立自訂的條件式存取原則，以封鎖舊版驗證通訊協定
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c40993df8033b9dbc49c81e8db2f9f01c6de37d9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424925"
---
# <a name="conditional-access-block-legacy-authentication"></a>條件式存取：封鎖舊版驗證

由於與舊版驗證通訊協定相關聯的風險增加，Microsoft 建議組織使用這些通訊協定封鎖驗證要求，並要求進行新式驗證。

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

下列步驟將協助建立條件式存取原則，以封鎖舊版驗證要求。

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入**Azure 入口網站**。
1. 流覽至**Azure Active Directory** > **安全性** > **條件式存取**。
1. 選取 [新增原則]。
1. 提供您的原則名稱。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [**指派**] 底下，選取 [**使用者和群組**]
   1. 在 [**包含**] 底下，選取 [**所有使用者**]。
   1. 在 [**排除**] 底下，選取 [**使用者和群組**]，然後選擇必須維持使用舊版驗證之功能的任何帳戶。 
   1. 選取 [完成]。
1. 在 [**雲端應用程式或動作**] 底下，選取 [**所有雲端應用程式**]
   1. 選取 [完成]。
1. 在 **條件** > **用戶端應用程式（預覽）** 底下，將**設定 設為** **是**
   1. 僅查看行動裝置**應用程式和桌面用戶端** > **其他用戶端**的方塊。
   1. 選取 [完成]。
1. 在 [**存取控制** > 授與] 底下，選取 **[** **封鎖存取**]。
   1. 選取 [選取]。
1. 確認您的設定，並將 [**啟用原則**] 設為 [**開啟**]。
1. 選取 [**建立**] 以建立以啟用您的原則。

## <a name="next-steps"></a>後續步驟

[條件式存取的一般原則](concept-conditional-access-policy-common.md)

[使用條件式存取僅限報告模式判斷影響](howto-conditional-access-report-only.md)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)
