---
title: 從 macOS 連接到 Windows 虛擬桌面-Azure
description: 如何使用 macOS 用戶端連接到 Windows 虛擬桌面。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: dc980d60c1db684a47c38b3b8efceb08dd618838
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605795"
---
# <a name="connect-with-the-macos-client"></a>連線至 macOS 用戶端

> 適用于： macOS 10.12 或更新版本

>[!NOTE]
> 從 macOS 用戶端存取 Windows 虛擬桌面資源的能力目前已提供預覽。

您可以使用可下載的用戶端，從 macOS 裝置存取 Windows 虛擬桌面資源。 本指南將告訴您如何設定用戶端。

## <a name="install-the-client"></a>安裝用戶端

若要開始使用，請[下載](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) 並在您的 macOS 裝置上安裝用戶端。

## <a name="subscribe-to-a-feed"></a>訂閱摘要

訂閱您的系統管理員提供給您的摘要，以取得可在 macOS 裝置上使用的受控資源清單。

若要訂閱摘要：

1. 在主頁面上選取 [**新增**摘要]，以連線至服務並抓取您的資源。
2. 輸入摘要 URL。 這可以是 URL 或電子郵件地址：
   - 如果您使用 URL，請使用您的系統管理員提供給您的帳戶。 通常會 <https://rdweb.wvd.microsoft.com>URL。
   - 若要使用電子郵件，請輸入您的電子郵件地址。 如果您的系統管理員以這種方式設定伺服器，這會告訴用戶端搜尋與您的電子郵件地址相關聯的 URL。
3. 選取 [**訂閱**]。
4. 出現提示時，使用您的使用者帳戶登入。

登入之後，您應該會看到可用資源的清單。

訂閱摘要之後，摘要的內容將會定期自動更新。 根據系統管理員所做的變更，可能會新增、變更或移除資源。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 macOS 用戶端，請參閱[開始使用 macOS 用戶端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac)檔。
