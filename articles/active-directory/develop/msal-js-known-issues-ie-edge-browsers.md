---
title: 已知問題的瀏覽器 (Microsoft Authentication Library for JavaScript) |Azure
description: 深入了解已知問題時使用的 Microsoft Authentication Library for JavaScript (MSAL.js) 搭配 Internet Explorer 與 Microsoft Edge 瀏覽器。
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
ms.openlocfilehash: c57ed956ec50c8bac26720a27894c07353928336
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873896"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>MSAL.js Internet Explorer 與 Microsoft Edge 瀏覽器上的已知的問題

## <a name="issues-due-to-security-zones"></a>因為安全性區域的問題
我們與 IE 和 Microsoft Edge 中的驗證問題相關的多個報表 (之後的更新*Microsoft Edge 瀏覽器版本，以 40.15063.0.0*)。 我們會追蹤這些，而且有通知 Microsoft Edge 小組。 而解決方法適用於 Microsoft Edge，以下是經常發生的問題和可能的因應措施可以實作的描述。

### <a name="cause"></a>原因
大部分的這些問題的原因如下所示。 工作階段存放區和本機儲存體分割的 Microsoft Edge 瀏覽器安全性區域。 在此特定版本的 Microsoft Edge 中，跨越多個區域，重新導向的應用程式時的工作階段存放區和本機存放區會清除。 具體來說，在一般的瀏覽器導覽中，清除工作階段存放區，且工作階段和本機儲存體已清除在瀏覽器的 InPrivate 模式中。 MSAL.js 將特定狀態儲存在工作階段存放區，並依賴在驗證流程期間檢查此狀態。 清除工作階段存放區時，此狀態會遺失，也因此會導致中斷的體驗。

### <a name="issues"></a>問題

- **無限重新導向迴圈和頁面重新載入在驗證期間**。 當使用者登入 Microsoft Edge 上的應用程式時，它們會被重新導向回從 AAD 登入頁面，並會陷入無限重新導向迴圈導致重複的頁面重新載入。 這通常會伴隨`invalid_state`工作階段存放區中的錯誤。

- **無限取得權杖的迴圈和 AADSTS50058 錯誤**。 在 Microsoft Edge 上執行的應用程式嘗試取得資源的權杖，應用程式可能堵塞在取得權杖以及下列的錯誤從 AAD 中呼叫您的網路追蹤的無限迴圈中：

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **不會關閉快顯視窗，或使用透過快顯視窗登入進行驗證時停滯**。 因為 MSAL.js 失去的控制代碼，透過快顯視窗，在 Microsoft Edge 或 IE(InPrivate)，驗證輸入認證並登入之後，如果在導覽中，牽涉到跨越多個安全性區域的多個網域時不關閉快顯視窗快顯視窗中。  

    以下是在 Microsoft Edge 問題追蹤器中的這些問題的連結：  
    - [Bug 13861050](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861050/)
    - [Bug 13861663](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861663/)

### <a name="update-fix-available-in-msaljs-023"></a>更新：MSAL.js 0.2.3 中可用的修正程式
修正針對驗證重新導向迴圈問題發行[MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)。 啟用旗標`storeAuthStateInCookie`MSAL.js 組態，才能利用此修正程式中。 依預設會將此旗標設定為 false。

當`storeAuthStateInCookie`啟用旗標、 MSAL.js 將用來儲存所需的驗證的驗證流程的要求狀態的瀏覽器 cookie。

> [!NOTE]
> 此修正程式尚無法使用 msal angular 和 msal angularjs 包裝函式。 此修正無法解決的問題與快顯視窗。

使用下列因應措施。

#### <a name="other-workarounds"></a>其他因應措施
請務必測試您的問題採用這些解決方法之前發生只會針對特定版本的 Microsoft Edge 瀏覽器並在其他瀏覽器上的運作。  
1. 若要解決這些問題的第一個步驟，請確認應用程式定義域，並使它們屬於相同的安全性區域，將會新增為信任的網站，在瀏覽器的安全性設定的任何其他站台所涉及的驗證流程重新導向。
若要這樣做，請依照下列步驟執行：
    - 開啟**Internet Explorer** ，然後按一下**設定**（齒輪圖示） 在右上角
    - 選取**網際網路選項**
    - 選取 [**安全性**] 索引標籤
    - 底下**信任的網站**選項中，按一下 **站台**按鈕，然後在對話方塊中，開啟 新增 Url。

2. 如所述，因為只有工作階段中儲存體清除期間定期瀏覽之前，您可以設定 MSAL.js 改為使用本機儲存體。 這可以設定為`cacheLocation`初始化 MSAL 時的組態參數。

請注意，這不會解決 InPrivate 瀏覽工作階段和本機存放區會清除後的問題。

## <a name="issues-due-to-popup-blockers"></a>由於快顯封鎖程式的問題

在 IE 或 Microsoft Edge 中，例如多重要素驗證期間發生的第二個快顯視窗時，已封鎖快顯時，有一些情況。 您會收到警示，以允許快顯視窗中，一次，或永遠瀏覽器中。 如果您選擇允許，瀏覽器會自動開啟快顯視窗，並傳回`null`處理它。 如此一來，程式庫並沒有視窗的控制代碼，而且沒有任何方法以關閉快顯視窗。 它會提示您允許快顯視窗，因為它不會自動開啟快顯視窗時相同的問題不會在 Chrome 中。

作為**因應措施**，開發人員就必須容許 IE 和 Microsoft Edge 中的快顯功能表，然後再開始使用其應用程式，若要避免這個問題。

## <a name="next-steps"></a>後續步驟
深入了解[Internet Explorer 中的使用 MSAL.js](msal-js-use-ie-browser.md)。
