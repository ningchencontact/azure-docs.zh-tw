---
title: 條件式存取-結合的安全性資訊-Azure Active Directory
description: 建立自訂的條件式存取原則, 以要求安全性資訊註冊的信任位置
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: f54ad6de21f05c76ca021e172a041563e3d688a8
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576559"
---
# <a name="conditional-access-require-trusted-location-for-mfa-registration"></a>條件式存取:MFA 註冊需要信任的位置

在條件式存取原則中, 您現在可以透過使用者動作來保護使用者註冊 Azure 多重要素驗證和自助式密碼重設的時間和方式。 此預覽功能適用于已啟用[合併註冊預覽](../authentication/concept-registration-mfa-sspr-combined.md)的組織。 您可以在組織中啟用這項功能, 讓使用者註冊 Azure 多重要素驗證, 並從中央位置 (例如, 在 HR 上架期間受信任的網路位置) SSPR。 如需有關如何在條件式存取中建立信任位置的詳細資訊, 請參閱 <<c0>條件式存取中的位置條件 Azure Active Directory一文。

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>建立要求從信任位置註冊的原則

下列原則適用于所有選取的使用者, 其嘗試使用合併的註冊體驗進行註冊, 並封鎖存取, 除非它們是從標示為受信任的網路的位置進行連接。

1. 在 [ **Azure 入口網站**中, 流覽至**Azure Active Directory**  > **條件式存取**]。
1. 選取 [新增原則]。
1. 在 [名稱] 中, 輸入此原則的名稱。 例如,**在受信任的網路上註冊結合的安全性資訊**。
1. 在 [**指派**] 底下, 按一下 [**使用者和群組**], 然後選取您想要套用此原則的使用者和群組。

   > [!WARNING]
   > 使用者必須啟用[合併的註冊預覽](../authentication/howto-registration-mfa-sspr-combined.md)。

1. 在 [**雲端應用程式或動作**] 底下, 選取 [**使用者動作**], 然後核取 **[註冊安全性資訊 (預覽)** ]
1. 在 [**條件** > **位置**] 底下。
   1. 設定 **[是]** 。
   1. 包含**任何位置**。
   1. 排除**所有信任的位置**。
   1. 按一下 [位置] 分頁上的 [**完成**]。
   1. 按一下 [條件] 分頁上的 [**完成**]。
1. 在 [**存取控制** > ] **[授**與] 底下。
   1. 按一下 [**封鎖存取**]。
   1. 然後按一下 [選取]。
1. 將 [**啟用原則**] 設定為 [**開啟**]。
1. 接著，按一下 [建立]。

## <a name="next-steps"></a>後續步驟

[條件式存取的一般原則](concept-conditional-access-policy-common.md)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)
