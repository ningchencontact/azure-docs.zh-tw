---
title: 登入自訂開發應用程式的問題 | Microsoft Docs
description: 會造成您無法登入您使用 Azure AD 所開發應用程式的常見錯誤
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 2dade35b05a07b649282ae00bb6fee354adcd195
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52845467"
---
# <a name="problems-signing-in-to-an-custom-developed-application"></a>登入自訂開發應用程式的問題

有數種錯誤會導致您無法登入應用程式。 會遇到此問題的最大原因是應用程式設定錯誤。

## <a name="errors-related-to--misconfigured-apps"></a>與應用程式設定錯誤相關的錯誤

* 確認入口網站中的兩種組態符合您應用程式中的組態。 特別是，比較用戶端/應用程識別碼、回覆 URL、用戶端密碼/金鑰，及應用程式識別碼 URI。

* 將您正以程式碼要求存取的資源，與在 [必要的資源] 索引標籤中設定的權限比較，以確定您只要求所設定的資源。

* 請參閱 [Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) 中任何類似的錯誤或問題。

## <a name="next-steps"></a>後續步驟

[Azure AD 開發人員指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[整合應用程式與 Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications>)<br>

[Azure AD v2.0 交集應用程式中的同意及權限](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
