---
title: 稽核和報告 Azure Active Directory B2B 共同作業使用者 | Microsoft Docs
description: Azure Active Directory B2B 共同作業中的來賓使用者屬性是可設定的
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: fbe1cfdcfb1b5ec295748c3c77030df45323ee54
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434319"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>稽核和報告 B2B 共同作業使用者
使用來賓使用者時，您具有與成員使用者相似的稽核功能。 

## <a name="access-reviews"></a>存取權檢閱
您可以使用存取權檢閱，以定期確認來賓使用者是否仍需要資源的存取權。 在 [管理] > [組織關係] 下的 [Azure Active Directory] 中提供了 [存取權檢閱] 功能。 (您還可以從 Azure 入口網站中的 [所有服務] 搜尋「存取權檢閱」。)若要了解如何使用存取權檢閱，請參閱[使用 Azure AD 存取權檢閱來管理來賓存取權](../governance/manage-guest-access-with-access-reviews.md)。

## <a name="audit-logs"></a>稽核記錄

Azure AD 稽核記錄可提供系統和使用者活動的記錄，包括來賓使用者所起始的活動。 若要存取稽核記錄，請在 [Azure Active Directory] 中的 [監視] 下，選取 [稽核記錄]。 以下範例是受邀者 Sam Oogle 的邀請與兌換記錄：

![稽核記錄檔](./media/auditing-and-reporting/audit-log.png)

您可以深入每個事件以取得詳細資料。 例如，讓我們看看接受詳細資料。

![活動詳細資料](./media/auditing-and-reporting/activity-details.png)

您也可以從 Azure AD 匯出這些記錄檔，並使用您慣用的報告工具來取得自訂報告。

### <a name="next-steps"></a>後續步驟

- [B2B 共同作業使用者屬性](user-properties.md)

