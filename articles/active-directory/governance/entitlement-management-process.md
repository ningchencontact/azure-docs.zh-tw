---
title: Azure AD 權利管理中的要求處理和電子郵件通知-Azure Active Directory
description: 瞭解存取套件的要求程式，以及在 Azure Active Directory 權利管理中傳送電子郵件通知的時間。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34d2c69cc808552a3b0c604804f3cd2597b379b
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200042"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Azure AD 權利管理中的要求處理和電子郵件通知

當使用者將要求提交至存取套件時，處理常式就會開始傳遞該存取要求。 Azure AD 權利管理會在過程中發生重要事件時，將電子郵件通知傳送給核准者和要求者。 本文說明要求程式和傳送的電子郵件通知。

## <a name="request-process"></a>要求處理

必須存取存取套件的使用者可以提交存取要求。 視原則的設定而定，要求可能需要核准。 當要求獲得核准時，處理常式會開始將存取封裝中每個資源的存取權指派給該使用者。 下圖顯示進程和不同狀態的總覽：

![核准流程圖表](./media/entitlement-management-process/request-process.png)

| 狀況 | 描述 |
| --- | --- |
| 呈交 | 使用者提交要求。 |
| 等待核准 | 如果存取套件的原則需要核准，則要求會移至 [待核准]。 |
| 已過期 | 如果沒有核准者核准要求超時內的要求，要求就會過期。 若要再試一次，使用者將必須重新提交其要求。 |
| 拒絕 | 核准者拒絕要求。 |
| 已核准 | 核准者核准要求。 |
| 傳遞 | 使用者尚未**獲**指派存取封裝中所有資源的存取權。 如果這是外部使用者，則使用者可能尚未存取資原始目錄並接受同意提示。 |
| 已傳遞 | 使用者已獲指派存取封裝中所有資源的存取權。 |
| 存取擴充 | 如果原則中允許延伸模組，則使用者會擴充指派。 |
| 存取已過期 | 使用者對存取套件的存取權已過期。 若要再次取得存取權，使用者必須提交要求。 |

## <a name="email-notifications"></a>電子郵件通知

如果您是核准者，當您需要核准存取要求以及完成存取要求時，就會傳送電子郵件通知給您。 如果您是要求者，則會傳送電子郵件通知，指出您的要求狀態。

下圖顯示將這些電子郵件通知傳送給核准者或要求者的時機。 參考 [[電子郵件通知] 資料表](entitlement-management-process.md#email-notifications-table)，尋找圖表中顯示之電子郵件通知的對應號碼。

### <a name="primary-approvers-and-alternate-approvers"></a>主要核准者和替代核准者
下圖顯示主要核准者和替代核准者的體驗，以及在要求過程中收到的電子郵件通知：

![主要和替代的核准者程式流程](./media/entitlement-management-process/primary-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>要求者
下圖顯示要求者的體驗，以及在要求過程中所收到的電子郵件通知：

![要求者程式流程](./media/entitlement-management-process/requestor-approval-and-expiration-request-flow.png)

### <a name="email-notifications-table"></a>電子郵件通知資料表
下表提供每個電子郵件通知的詳細資訊。 若要管理這些電子郵件，您可以使用規則。 例如，您可以在 Outlook 中建立規則，將電子郵件移至資料夾（如果主旨包含此表格中的單字）：

| # | 電子郵件主旨 | 傳送時 | 傳送至 |
| --- | --- | --- | --- |
| 1 | 必要動作：依 *[日期]* 核准或拒絕轉送的要求 | 此電子郵件將會傳送給第1階段的替代核准者（在要求呈報之後）以採取動作。 | 第1階段的替代核准者 |
| 2 | 必要動作：依 *[日期]* 核准或拒絕要求 | 這封電子郵件會傳送給第1階段主要核准者，如果已停用擴大，則採取動作。 | 第1階段主要核准者 |
| 3 | 提醒：為 [要求者 *]* 核准或拒絕 *[date]* 要求 | 此提醒電子郵件將會傳送給第1階段主要核准者，如果已停用呈報，則只有在尚未採取動作時才會採取動作。 | 第1階段主要核准者 |
| 4 | 以 [ *time]* 于 *[date]* 核准或拒絕要求 | 這封電子郵件會傳送給第1階段主要核准者（如果已啟用呈報）以採取動作。 | 第1階段主要核准者 |
| 5 | 必要動作提醒： [要求者 *]* 的 *[日期]* 核准或拒絕要求 | 此提醒電子郵件將會傳送給第1階段的主要核准者（如果已啟用擴大），只有在尚未採取動作時才會採取動作。 | 第1階段主要核准者 |
| 6 | *[Access_package]* 的要求已過期 | 這封電子郵件會在要求過期後，傳送給第1階段的主要核准者和/或第1階段的替代核准者。 | 第1階段主要核准者，第1階段的替代核准者 |
| 7 | 已核准 [要求者 *]* 至 *[access_package]* | 這封電子郵件會在要求完成時，傳送至第1階段的主要核准者和/或第1階段的替代核准者。 | 第1階段主要核准者，第1階段的替代核准者 |
| 8 | 已核准 [要求者 *]* 至 *[access_package]* | 只有在第1階段核准時，才會將此電子郵件傳送至階段1的主要核准者和/或第1階段的替代核准者。 | 第1階段主要核准者，第1階段的替代核准者 |
| 9 | 拒絕要求至 *[access_package]* | 只有在拒絕要求時，才會將此電子郵件傳送給要求者 | 申請者 |
| 10 | 您的要求已過期 *[access_package]* | 這封電子郵件會在要求過期後，于單一階段或多階段要求的第1階段結束時傳送給要求者。 | 申請者 |
| 18 | 您現在可以存取 *[access_package]* | 這封電子郵件將會傳送給使用者，以開始使用他們的存取權。 | 申請者 |
| 19 | 延伸 [ *access_package]* 的存取權（依 *[日期]）* | 這封電子郵件將會傳送給使用者，他們的存取權才會過期。 | 申請者 |
| 20 | 已結束存取 *[access_package]* | 這封電子郵件會在其存取權過期後傳送給使用者。 | 申請者 |

### <a name="access-request-emails"></a>存取要求電子郵件

當要求者針對設定為需要核准的存取套件提交存取要求時，新增至該原則的所有核准者都會收到電子郵件通知，其中包含要求的詳細資料。 詳細資料包括要求者的名稱、組織、存取開始和結束日期（如果有提供）、商業理由、提交要求的時間，以及要求何時到期。

此電子郵件包含連結核准者可以按一下前往 Myaccess，以核准或拒絕存取要求。 以下是在要求者提交存取要求時，傳送給核准者的電子郵件通知範例：

![核准存取封裝電子郵件的要求](./media/entitlement-management-shared/approver-request-email.png)

主要核准者也會傳送電子郵件通知，並提醒您採取動作並進行要求的決策。 以下是通知主要核准者收到的電子郵件範例，以提醒他們採取行動：

![提醒存取要求電子郵件](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approver-request-emails"></a>替代核准者要求電子郵件

如果已啟用轉送至替代核准者，則根據轉送原則，如果要求仍處於擱置狀態，則會轉送要求。 替代核准者將會收到通知電子郵件，以核准或拒絕要求。 以下是替代核准者收到通知的電子郵件範例：

![替代核准者要求電子郵件](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

同時，主要核准者和替代核准者都可以核准或拒絕要求。

### <a name="approved-or-denied-emails"></a>核准或拒絕的電子郵件

當要求者的存取要求已核准且可供存取，或其存取要求遭到拒絕時，會通知要求者。 當核准者收到要求者所提交的存取要求時，他們可以核准或拒絕存取要求。 核准者必須為他們的決策增加商業理由。 以下是核准要求之後，傳送給主要或替代核准者的電子郵件範例：

![審查存取要求電子郵件](./media/entitlement-management-process/approver-request-email-approved.png)

當存取要求獲得核准，並布建其存取權時，系統會將電子郵件通知傳送給要求者，他們現在可以存取存取套件。 以下是將存取套件授與存取權時，傳送給要求者的電子郵件通知範例：

![過期的存取要求電子郵件](./media/entitlement-management-process/requestor-email-approved.png)

拒絕存取要求時，會傳送電子郵件通知給要求者。 以下是在拒絕存取要求時，傳送給要求者的電子郵件通知範例：

![要求者要求拒絕電子郵件](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="expired-access-request-emails"></a>過期的存取要求電子郵件

如果沒有核准者核准或拒絕要求，存取要求可能會過期。 

當要求達到其設定的到期日且過期時，核准者就無法再核准或拒絕此要求。 以下是傳送給所有主要和其他核准者之通知的電子郵件範例：

 ![核准者過期的存取要求電子郵件](./media/entitlement-management-process/approver-request-email-expired.png)

 電子郵件通知也會傳送給要求者，通知他們其存取要求已過期，而且需要重新提交存取要求。 以下是當使用者的存取要求過期時，傳送給要求者的電子郵件通知範例：

![要求者過期的存取要求電子郵件](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>後續步驟

- [要求存取權套件](entitlement-management-request-access.md)
- [核准或拒絕存取要求](entitlement-management-request-approve.md)
