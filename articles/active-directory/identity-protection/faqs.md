---
title: Azure Active Directory Identity Protection 常見問題集 | Microsoft Docs
description: 關於 Azure AD Identity Protection 的常見問題集
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d36b513b6835088b5120072906667cd30af505d
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025734"
---
# <a name="azure-active-directory-identity-protection-faq"></a>Azure Active Directory Identity Protection 常見問題集

本文包含有關 Azure Active Directory (Azure AD) Identity Protection.常見問題的解答。 如需詳細資訊，請參閱 [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)。 

## <a name="why-do-some-risk-detections-have-closed-system-status"></a>為什麼部分風險偵測具有「已關閉（系統）」狀態？

**答：** Identity Protection 偵測到這些風險偵測，並于稍後關閉，因為事件不再被視為有風險。 這些事件不會計入使用者的風險層級。 

---

## <a name="do-i-need-to-be-a-global-admin-to-use-identity-protection-in-the-azure-portal"></a>是否需要成為全域管理員，才能在 Azure 入口網站中使用 Identity Protection？
**答：** 資料分割 成為安全性讀取者、安全性系統管理員或全域管理員都能使用 Identity Protection。

---

## <a name="how-do-i-get-identity-protection"></a>要如何取得 Identity Protection？

**答：** 如需此問題的解答，請參閱[開始使用 Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md)。

---

## <a name="how-can-i-sort-users-in-users-flagged-for-risk"></a>如何排序 [標幟為有風險的使用者] 中的使用者？

**答：** 按一下 [已**標示為風險的使用者**] 頁面頂端的 [**下載**]，下載標示為風險的使用者報告。 接著，您便可以根據可用的欄位 (包括 [上次更新時間 (UTC)]) 來排序所下載的資料。

---
