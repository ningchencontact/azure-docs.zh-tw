---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8c2dc41fde9387da291b6e4a6c8a6220ae62b514
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174584"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>登入使用者並從 JavaScript 單頁應用程式 (SPA) 呼叫 Microsoft 圖形 API

此指南示範 JavaScript 單頁應用程式 (SPA) 如何登入個人公司及學校帳戶，取得存取權杖，以及呼叫 Microsoft 圖形 API 或需要來自 Microsoft 身分識別平台端點之存取權杖的其他 API。

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南產生之範例應用程式的運作方式

![顯示此教學課程所產生的範例應用程式如何運作](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>相關資訊

此指南建立的範例應用程式可讓 JavaScript SPA 查詢 Microsoft 圖形 API，或查詢可接受來自 Microsoft 身分識別平台端點之權杖的 Web API。 針對這個案例，在使用者登入之後，系統會透過授權標頭要求一個存取權杖，並將其新增到 HTTP 要求。 權杖取得和更新作業是由 Microsoft Authentication Library (MSAL) 處理。

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>程式庫

本指南使用下列程式庫：

|程式庫|說明|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library for JavaScript 預覽|

> [!NOTE]
> *msal.js* 的目標是「Microsoft 身分識別平台」  端點 - 它能讓個人、學校及公司帳戶以登入和取得權杖。 「Microsoft 身分識別平台」  有[一些限制](../articles/active-directory/develop/active-directory-v2-limitations.md)。
> 若要了解 v1.0 與 v2.0 端點之間的差異，請參閱[端點比較指南](../articles/active-directory/develop/azure-ad-endpoint-comparison.md)。

<!--end-collapse-->
