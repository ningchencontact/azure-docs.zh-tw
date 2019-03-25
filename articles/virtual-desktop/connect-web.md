---
title: 使用 Windows 虛擬桌面預覽 web 用戶端連線-Azure
description: 如何連線至 Windows 虛擬桌面預覽 HTML5 web 用戶端。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 231dc2b777ce7fa37f445215863a55e93562e411
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403743"
---
# <a name="connect-with-the-web-client"></a>使用 web 用戶端連線

Web 用戶端可讓您存取您的 Windows 虛擬桌面預覽資源從網頁瀏覽器，而不需要冗長的安裝程序。

>[!NOTE]
>Web 用戶端目前沒有支援的行動裝置作業系統。

## <a name="supported-operating-systems-and-browsers"></a>支援的作業系統和瀏覽器

任何支援 HTML5 的瀏覽器應該可行，我們正式支援下列作業系統和瀏覽器。

| [瀏覽器]           | 支援的 OS                     | 注意               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    |  Windows                          |                     |
| Internet Explorer |  Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows、 macOS、 Linux            | 55 或更新版本 |
| Google Chrome     | Windows、 macOS、 Linux、 Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>存取遠端資源摘要

在瀏覽器中，瀏覽至[Windows 的虛擬桌面 web 用戶端](https://rdweb.wvd.microsoft.com/webclient)並使用您的使用者帳戶登入。

>[!NOTE]
>如果您已經登入使用的是不同於您想要用於 Windows 的虛擬桌面的 Azure AD 帳戶，您可能需要登出或使用私用瀏覽器視窗。

登入之後，您現在應該會看到一份資源清單。 您可以啟動資源，方法是選取它們，就像一般的應用程式中一樣**的所有資源** 索引標籤。
