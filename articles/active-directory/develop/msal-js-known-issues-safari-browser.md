---
title: 已知問題的瀏覽器 (Microsoft Authentication Library for JavaScript) |Azure
description: 深入了解已知問題，使用 Microsoft Authentication Library for JavaScript (MSAL.js) 時使用 Safari 瀏覽器。
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb89b1ef4dbbef234fba3152d7f85bbadfbdc64a
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873881"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>MSAL.js 使用 Safari 瀏覽器上的已知的問題 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>無訊息更新權杖上的 Safari 12 和 ITP 2.0

包含的版本 12 的 Apple iOS 和 MacOS 10.14 operating systems [Safari 12 瀏覽器](https://developer.apple.com/safari/whats-new/)。 基於安全性和隱私權的資訊，包括 Safari 12[智慧型追蹤預防 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/)。 基本上，這會導致卸除所設定的第三方 cookie 的瀏覽器。 ITP 2.0 也會將為第三方 cookie 的身分識別提供者所設定的 cookie。

### <a name="impact-on-msaljs"></a>MSAL.js 影響

MSAL.js 使用隱藏的 Iframe 來執行無訊息的權杖取得和更新的一部分`acquireTokenSilent`呼叫。 無訊息的權杖要求需要能夠存取由 Azure AD 所設定的 cookie 驗證的使用者工作階段在 Iframe 上。 防止存取這些 cookie ITP 2.0 MSAL.js 無法以無訊息方式取得和更新權杖，而且這會導致`acquireTokenSilent`失敗。

沒有此問題的解決方案的這個時候，我們會評估與標準社群的選項。

### <a name="work-around"></a>因應措施

根據預設 ITP 設定已啟用 Safari 瀏覽器上。 您可以瀏覽至連線，停用此設定**喜好設定** -> **隱私權**核取**防止跨站台追蹤**選項。

![safari 設定](./media/msal-js-known-issue-safari-browser/safari.png)

您必須處理`acquireTokenSilent`失敗與互動式取得權杖的呼叫，會提示使用者登入。
若要避免重複的登入，您可以實作一個方法是將處理`acquireTokenSilent`失敗，並提供使用者一個選項來進行互動式的呼叫之前，先停用在 Safari 中的 [ITP] 設定。 一旦停用此設定之後，後續的無訊息權杖更新作業應該會成功。
