---
title: 要求使用者指派 - Azure AD | Microsoft Docs'
description: 如何要求 Azure 應用程式的使用者指派。
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: ''
ms.assetid: 30b78cba-1e0f-472f-8314-f2250a9b91c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: kgremban
robots: noindex
ms.openlocfilehash: 4519681d9b91383d27c00a992f85b0cb5d74f235
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859457"
---
# <a name="azure-ad-and-applications-require-user-assignment"></a>Azure AD 和應用程式：要求使用者指派
## <a name="requiring-user-assignment"></a>要求使用者指派
1. 使用系統管理員帳戶登入 Azure 入口網站。
2. 在主功能表中，按一下 [所有服務] 項目。
3. 選擇您要為應用程式使用的目錄。
4. 按一下 [企業應用程式] 索引標籤。
5. 從與此目錄相關聯的應用程式清單中選取應用程式。
6. 按一下 [屬性] 索引標籤。
7. 將 [需要使用者指派嗎?] 切換變更為 [是]。
8. 按一下畫面頂端的 [儲存] 按鈕。

現在您需要將使用者及/或群組指派給應用程式。 請參閱[將使用者指派給應用程式](active-directory-applications-guiding-developers-assigning-users.md)和[將群組指派給應用程式](active-directory-applications-guiding-developers-assigning-groups.md)。

## <a name="next-steps"></a>後續步驟
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
