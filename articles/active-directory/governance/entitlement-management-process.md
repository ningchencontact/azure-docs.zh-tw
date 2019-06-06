---
title: 要求 Azure AD 權限管理 （預覽）-Azure Active Directory 中的程序和電子郵件通知
description: 深入了解要求程序存取套件，並在 Azure Active Directory 權限管理 （預覽） 中傳送電子郵件通知。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: aede5e315141251026867f7028ebf989d44da4d5
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473041"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>要求 Azure AD 權限管理 （預覽） 中的程序和電子郵件通知

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

當使用者提交要求以存取封裝時，處理程序會啟動來傳遞該要求。 Azure AD 權限管理也傳送電子郵件通知給核准者及要求者程序期間所發生的索引鍵的事件時。

此文章說明要求程序，以及傳送電子郵件通知。

## <a name="request-process"></a>要求程序

需要存取套件的存取權的使用者可以提交存取要求。 根據原則的設定，要求可能需要核准。 要求獲准後，處理程序會開始將使用者存取權指派給存取封裝中的每個資源。 下圖顯示程序，以及不同的狀態概觀。

![核准程序流程圖](./media/entitlement-management-process/request-process.png)

| State | 描述 |
| --- | --- |
| 提交 | 使用者提交要求。 |
| 等待核准 | 如果存取封裝的原則需要核准，要求將移至擱置的核准。 |
| 已過期 | 如果任何核准者不核准要求核准的要求逾時內時，要求就會到期。 若要再試一次，使用者必須重新提交要求。 |
| 拒絕 | 核准者拒絕要求。 |
| 已核准 | 核准者核准要求。 |
| 傳遞 | 使用者擁有**不**已指派的存取權存取封裝中的所有資源。 如果這是外部使用者，使用者有尚未存取的資源目錄，並接受權限提示字元。 |
| 已傳遞 | 使用者已獲得存取存取封裝中的所有資源。 |
| 擴充的存取 | 如果延伸模組可以在原則中，使用者會延長指派。 |
| 存取過期 | 存取封裝之使用者的權限已過期。 若要取得存取同樣地，使用者必須提交要求。 |

## <a name="email-notifications"></a>電子郵件通知

如果您是核准者，您就會傳送電子郵件通知，以及存取要求已完成時您需要核准的存取要求。 如果您是要求者，您就會傳送電子郵件通知，指出要求的狀態。 下圖顯示當這些電子郵件通知會傳送。

![權利管理電子郵件的程序](./media/entitlement-management-process/email-notifications.png)

下表提供有關每個這些電子郵件通知的進一步詳細資訊。

| # | 電子郵件主旨 | 當傳送 | 傳送至 |
| --- | --- | --- | --- |
| 1 | 必要的動作：檢閱存取要求來自 *[要求者]* 要 *[存取套件]* 由 *[date]* | 當要求者提交要求，以存取封裝 | 所有核准者 |
| 2 | 必要的動作：檢閱存取要求來自 *[要求者]* 要 *[存取套件]* 由 *[date]* | X 天之前的核准要求逾時 | 所有核准者 |
| 3 | 狀態通知： *[要求者]* 的存取要求 *[存取套件]* 已過期 | 當不核准或拒絕要求的持續時間內的存取要求核准者 | 要求者 |
| 4 | 狀態通知： *[要求者]* 存取要求 *[存取套件]* 已完成 | 當第一個核准者核准或拒絕存取要求 | 所有核准者 |
| 5 | 您已被拒絕存取 *[存取套件]* | 當要求者已被拒絕存取套件的存取權 | 要求者 |
| 6 | 您現在可以存取 *[存取套件]*  | 當要求者已授與存取封裝中的每個資源的存取權 | 要求者 |
| 7 | 您的存取權 *[存取套件]* 於 X 天後過期 | 存取封裝的要求者的權限前的 X 天到期 | 要求者 |
| 8 | 您的存取權 *[存取套件]* 已過期 | 要求者的權限存取套件的到期時 | 要求者 |

### <a name="access-request-emails"></a>存取要求的電子郵件

當要求者提交存取要求設定為需要核准存取封裝時，原則中設定的所有核准者會收到電子郵件通知的要求詳細資料。 詳細資料包括要求者的名稱、 組織、 存取開始和結束日期，如果提供業務正當理由，當已提交的要求，並要求的到期時間。 電子郵件會包含核准者可以在其中核准或拒絕存取要求的連結。 以下是要求者提交存取要求時，會傳送給核准者的範例電子郵件通知。

![檢閱存取要求的電子郵件](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>核准或拒絕的電子郵件

當他們存取要求核准並且可供存取，或其存取要求遭到拒絕時，系統會通知要求者。 當核准者收到所要求者提交存取要求時，它們可以核准或拒絕存取要求。 核准者必須將他們的決策的業務理由。

存取要求獲准後，權利管理就會啟動程序中存取套件的資源的每個要求者存取權授與。 要求者已授與存取封裝中的每個資源的存取權之後，會傳送電子郵件通知給要求者已核准存取要求，以及他們已擁有存取套件的存取權。 以下是它們會授與存取套件的存取權時，會傳送給要求者的範例電子郵件通知。

當遭拒的存取要求時，會將電子郵件通知傳送給要求者。 以下是其存取要求遭到拒絕時，會傳送給要求者的範例電子郵件通知。

### <a name="expired-access-request-emails"></a>過期存取要求的電子郵件

當他們存取要求已過期時，會收到通知要求者。 當要求者提交存取要求時，要求就會包含在它到期之前要求持續時間。 如果不有任何核准者提交核准/拒絕的決策，要求會繼續維持在待決的核准狀態。 當要求到達其已設定的到期期間時，要求到期，並可以不再是核准或拒絕核准者。 在此情況下，要求會進入過期狀態。 過期的要求可不會再核准或拒絕。 電子郵件通知會傳送給要求者，其存取要求已過期，而它們需要重新提交存取要求。 以下是其存取要求已過期時，會傳送給要求者的範例電子郵件通知。

![過期存取要求的電子郵件](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>後續步驟

- [要求存取套件的存取權](entitlement-management-request-access.md)
- [核准或拒絕存取要求](entitlement-management-request-approve.md)
