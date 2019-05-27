---
title: 使用 Internet Explorer (Microsoft Authentication Library for JavaScript) |Azure
description: 了解如何使用 Microsoft Authentication Library for JavaScript (MSAL.js) 與 Internet Explorer 瀏覽器。
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cf8c84120f4c90d3943cfc31ffbf9aafcec0ba3
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873911"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>MSAL.js Internet Explorer 與 Microsoft Edge 瀏覽器上的已知的問題

Microsoft Authentication Library for JavaScript (MSAL.js) 會產生[JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) ，讓它可以在 Internet Explorer 中執行。 有，不過，應該知道的一些事項。

## <a name="run-an-app-in-internet-explorer"></a>在 Internet Explorer 中執行的應用程式
如果您想要使用 MSAL.js 可以在 Internet Explorer 中執行的應用程式中，您必須將參考加入至承諾 polyfill 之前參考 MSAL.js 指令碼。

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

這是因為 Internet Explorer 不以原生方式支援 JavaScript 的承諾。

## <a name="debugging-an-application-running-in-internet-explorer"></a>偵錯在 Internet Explorer 中執行的應用程式

### <a name="running-in-production"></a>在生產環境中執行
您應用程式部署到實際執行環境 （例如在 Azure Web 應用程式） 通常會正常運作，提供使用者已接受快顯功能表。 我們可以測試它與 Internet Explorer 11。

### <a name="running-locally"></a>在本機執行
如果您想要執行和本機偵錯在 Internet Explorer 中執行的應用程式，您需要留意下列注意事項 (假設您想要執行您的應用程式*http://localhost:1234*):

- Internet Explorer 具有名為 「 受保護的模式 」，這是為了避免 MSAL.js 正常運作的安全性機制。 之間的徵兆，您登入後的頁面可以重新導向至 http://localhost:1234/null。

- 若要執行及偵錯您的應用程式在本機，您必須停用此 「 受保護的模式 」。 這個項目：

    1. 按一下 Internet Explorer**工具**（齒輪圖示）。
    1. 選取 [**網際網路選項**，然後**安全性**] 索引標籤。
    1. 按一下 **網際網路**區域，然後取消核取**啟用受保護模式 （需要重新啟動 Internet Explorer）**。 Internet Explorer 會警告您的電腦不再受到保護。 按一下 [確定]。
    1. 重新啟動 Internet Explorer。
    1. 執行和偵錯您的應用程式。

完成之後，還原的 Internet Explorer 安全性設定。  選取 **設定** -> **網際網路選項** -> **安全性** -> **重設為預設層級的所有區域**.

## <a name="next-steps"></a>後續步驟
深入了解[時使用 MSAL.js Internet Explorer 中的已知問題](msal-js-use-ie-browser.md)。