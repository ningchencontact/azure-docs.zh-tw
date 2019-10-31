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
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a1ce3b2cb72a7b57e556e09264cb5bd421eda0f
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173752"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Azure AD 權利管理中的要求處理和電子郵件通知

當使用者將要求提交至存取套件時，就會啟動處理常式來傳遞該要求。 Azure AD 權利管理也會在過程中發生重要事件時，將電子郵件通知傳送給核准者和要求者。

本文說明要求程式，以及傳送的電子郵件通知。

## <a name="request-process"></a>要求處理

必須存取存取套件的使用者可以提交存取要求。 視原則的設定而定，要求可能需要核准。 當要求獲得核准時，處理常式會開始將存取封裝中每個資源的存取權指派給該使用者。 下圖顯示進程和不同狀態的總覽。

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

如果您是核准者，當您需要核准存取要求以及完成存取要求時，就會傳送電子郵件通知給您。 如果您是要求者，則會傳送電子郵件通知，指出您的要求狀態。 下圖顯示這些電子郵件通知的傳送時間。

![權利管理電子郵件處理常式](./media/entitlement-management-process/email-notifications.png)

下表提供每個電子郵件通知的詳細資訊。

| # | 電子郵件主旨 | 傳送時 | 傳送至 |
| --- | --- | --- | --- |
| 1 | 必要動作：從 [要求者 *]* 到 *[存取套件]* 的 [*日期]* 審查存取要求 | 當要求者提交存取套件的要求時 | 所有核准者 |
| 2 | 必要動作：從 [要求者 *]* 到 *[存取套件]* 的 [*日期]* 審查存取要求 | 核准要求超時前的 X 天 | 所有核准者 |
| 3 | 狀態通知： [*要求者]* 對 *[存取套件]* 的存取要求已過期 | 當核准者未核准或拒絕要求持續時間內的存取要求時 | 申請者 |
| 4 | 狀態通知： [要求者 *]* 對 *[存取套件]* 的存取要求已完成 | 當第一位核准者核准或拒絕存取要求時 | 所有核准者 |
| 5 | 您已被拒絕存取 *[存取套件]* | 當要求者遭到拒絕存取封裝的存取權時 | 申請者 |
| 6 | 您現在可以存取 *[存取套件]*  | 當要求者已被授與存取套件中每個資源的存取權時 | 申請者 |
| 7 | 您對 *[存取套件]* 的存取于 X 天后到期 | 在要求者存取存取套件的前 X 天到期 | 申請者 |
| 8 | 您對 *[存取套件]* 的存取已過期 | 當要求者存取存取套件過期時 | 申請者 |

### <a name="access-request-emails"></a>存取要求電子郵件

當要求者針對設定為需要核准的存取套件提交存取要求時，在原則中設定的所有核准者都會收到電子郵件通知，其中包含要求的詳細資料。 詳細資料包括要求者的名稱、組織、存取開始和結束日期（如有提供）、商業理由、提交要求的時間，以及要求何時到期。 此電子郵件包含一個連結，可讓核准者核准或拒絕存取要求。 以下是在要求者提交存取要求時，傳送給核准者的電子郵件通知範例。

![審查存取要求電子郵件](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>核准或拒絕的電子郵件

當要求者的存取要求已核准且可供存取，或其存取要求遭到拒絕時，會通知要求者。 當核准者收到要求者所提交的存取要求時，他們可以核准或拒絕存取要求。 核准者必須為他們的決策增加商業理由。

當存取要求獲得核准時，權利管理會開始將存取套件中每個資源的存取權授與給要求者的程式。 在要求者授與存取封裝中每個資源的存取權之後，系統會傳送電子郵件通知給要求者，告知要求者已核准其存取要求，而且他們現在可以存取存取封裝。 以下是將存取套件授與存取權時，傳送給要求者的電子郵件通知範例。

拒絕存取要求時，會傳送電子郵件通知給要求者。 以下是在拒絕存取要求時，傳送給要求者的電子郵件通知範例。

### <a name="expired-access-request-emails"></a>過期的存取要求電子郵件

當要求者的存取權過期時，會通知要求者。 當要求者提交存取要求時，要求會有過期的要求持續時間。 如果沒有可提交核准/拒絕決策的核准者，則要求會繼續維持在擱置中的核准狀態。 當要求達到其設定的到期時間時，要求就會過期，而且核准者無法再核准或拒絕。 在此情況下，要求會進入已過期狀態。 已過期的要求無法再核准或拒絕。 系統會傳送電子郵件通知給要求者，指出其存取要求已過期，而且需要重新提交存取要求。 以下是當使用者的存取要求過期時，傳送給要求者的電子郵件通知範例。

![過期的存取要求電子郵件](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>後續步驟

- [要求存取權套件](entitlement-management-request-access.md)
- [核准或拒絕存取要求](entitlement-management-request-approve.md)
