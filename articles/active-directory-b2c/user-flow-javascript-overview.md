---
title: Azure Active Directory B2C 中使用者流程的 JavaScript 和頁面合約版本 | Microsoft Docs
description: 了解如何啟用 JavaScript 並使用頁面合約版本來自訂 Azure Active Directory B2C 中的使用者流程。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5102755c9e830f43fa92e8546e5125960e0a2f9a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401549"
---
# <a name="about-using-javascript-and-page-contract-versions-in-a-user-flow"></a>關於在使用者流程中使用 JavaScript 和頁面合約版本

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C 為使用者流程中的使用者介面元素提供一組包含 HTML、 CSS 和 JavaScript 的封裝內容。 如果您打算在使用者流程中啟用 [JavaScript](javascript-samples.md) 用戶端程式碼，那麼您需要確定根據 JavaScript 的元素是固定不變。 否則，任何變更都可能導致使用者流程頁面出現非預期的行為。 若要避免這些問題，您可以強制頁面合約用於使用者流程，並指定頁面合約版本。 如此一來，可確保您根據 JavaScript 的所有內容定義都是不可變的。 即使您不打算為使用者流程啟用 JavaScript，也可以為使用者流程頁面指定頁面合約版本。

> [!NOTE]
> 本文討論使用者流程的 JavaScript，但是當您使用[自訂原則](page-contract.md)時，您也可以使用 JavaScript，並選取頁面合約版本。

## <a name="enable-javascript"></a>啟用 JavaScript

在使用者流程屬性中，您可以啟用 JavaScript，這也會強制使用頁面合約。 然後，您可以按照下一節中的說明設定頁面合約版本。

![啟用 JavaScript 設定](media/user-flow-javascript-overview/javascript-settings.PNG)

## <a name="specify-a-page-contract-version"></a>指定頁面合約版本

無論是否在使用者流程的屬性中啟用 JavaScript，都可以為使用者流程頁面指定頁面合約版本。 開啟使用者流程，然後選取 [頁面配置]。 在 [配置名稱] 下，選取使用者流程頁面，然後選擇**頁面合約版本**。

![啟用 JavaScript 設定](media/user-flow-javascript-overview/page-contract-version.PNG)

## <a name="next-steps"></a>後續步驟
請參閱[適用於 Azure Active Directory B2C 中的 JavaScript 範例](javascript-samples.md)。
