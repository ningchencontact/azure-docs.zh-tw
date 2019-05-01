---
title: JavaScript 和頁面合約版本-Azure Active Directory B2C |Microsoft Docs
description: 了解如何啟用 JavaScript，並在 Azure Active Directory B2C 中使用頁面合約版本。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 91b4b621fc3dcedb52f88372fbfac222a744dbd1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570619"
---
# <a name="javascript-and-page-contract-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的 JavaScript 和頁面合約版本

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C 提供一組使用者流程和自訂原則中的使用者介面項目包含 HTML、 CSS 和 JavaScript 的封裝內容。 若要啟用您的應用程式的 JavaScript，您必須新增項目您[自訂原則](active-directory-b2c-overview-custom.md)或啟用使用者流程的入口網站中，選取頁面合約，並使用[b2clogin.com](b2clogin.md)在要求中。

如果您想要啟用[JavaScript](javascript-samples.md)用戶端程式碼中，您會想要確定您根據您的 JavaScript 項目是固定不變。 否則任何變更可能會非預期的行為造成使用者頁面。 若要避免這些問題，您可以強制頁面合約的使用，並指定頁面合約版本。 這樣可確保您已根據您的 JavaScript 的所有內容定義為不可變。 即使您不想啟用 JavaScript，您可以指定頁面合約版本，為您的網頁。

## <a name="user-flows"></a>使用者流程

在使用者流程屬性中，您可以啟用 JavaScript，這也會強制使用頁面合約。 然後，您可以按照下一節中的說明設定頁面合約版本。

![啟用 JavaScript 設定](media/user-flow-javascript-overview/javascript-settings.png)

無論是否在使用者流程的屬性中啟用 JavaScript，都可以為使用者流程頁面指定頁面合約版本。 開啟使用者流程，然後選取 [頁面配置]。 在 [配置名稱] 下，選取使用者流程頁面，然後選擇**頁面合約版本**。

![啟用 JavaScript 設定](media/user-flow-javascript-overview/page-contract-version.png)

## <a name="custom-policies"></a>自訂原則

若要在自訂原則中啟用 JavaScript，您將新增**ScriptExecution**項目**RelyingParty**自訂原則檔案中的項目。 如需詳細資訊，請參閱 <<c0> [ 使用 Azure Active Directory B2C 中的 JavaScript 範例](javascript-samples.md)。

不論是否在您的自訂原則中啟用 JavaScript，您可以指定頁面合約版本，為您的網頁。 如需指定頁面合約的詳細資訊，請參閱[選取 Azure Active Directory B2C 中的頁面合約使用自訂原則](page-contract.md)。

## <a name="next-steps"></a>後續步驟

請參閱[適用於 Azure Active Directory B2C 中的 JavaScript 範例](javascript-samples.md)。
