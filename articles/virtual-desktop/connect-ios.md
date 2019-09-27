---
title: 從 iOS 連接到 Windows 虛擬桌面-Azure
description: 如何使用 iOS 用戶端連接到 Windows 虛擬桌面。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: 764ed4fefd1a3aba1f0b7812fa2965505aa34161
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338695"
---
# <a name="connect-with-the-ios-client"></a>與 iOS 用戶端連線

> 適用于： iOS 8.0 或更新版本。 與 iPhone、iPad 和 iPod touch 相容。

>[!NOTE]
> IOS 用戶端目前仍處於預覽狀態。

您可以使用可下載的用戶端，從您的 iOS 裝置存取 Windows 虛擬桌面資源。 本指南將告訴您如何設定 iOS 用戶端。

## <a name="install-the-ios-beta-client"></a>安裝 iOS Beta 用戶端
若要安裝 iOS 搶鮮版（Beta）用戶端：

1. 在您的 iOS 裝置上安裝[Apple TestFlight](https://apps.apple.com/us/app/testflight/id899247664)應用程式。
2. 在您的 iOS 裝置上，開啟瀏覽器並流覽至[aka.ms/rdiosBeta](https://aka.ms/rdiosbeta)。
3. 在標籤**底下的步驟2：加入 Beta 版**，選取 [**開始測試**]。
4. 當您重新導向至 TestFlight 應用程式時，請選取 [**接受**]，然後選取 [**安裝**]。

## <a name="subscribe-to-a-feed"></a>訂閱摘要

訂閱您的系統管理員所提供的摘要，以取得您可以在 iOS 裝置上存取的受控資源清單。

若要訂閱摘要：

1. 在 [連接中心] 中 **+** ，點擊，然後按 [**新增工作區**]。
2. 在 [摘要**url** ] 欄位中輸入摘要 url。 摘要 URL 可以是 URL 或電子郵件地址。
   - 如果您使用 URL，請使用您的系統管理員提供給您的帳戶。 通常，URL 為<https://rdweb.wvd.microsoft.com>。
   - 若要使用電子郵件，請輸入您的電子郵件地址。 如果您的系統管理員以這種方式設定伺服器，這會告訴用戶端搜尋與您的電子郵件地址相關聯的 URL。
3. 按 **[下一步]** 。
4. 出現提示時，請提供您的認證。
   - 針對 [**使用者名稱**]，為使用者名稱提供存取資源的許可權。
   - 針對 [**密碼**]，提供與使用者名稱相關聯的密碼。
   - 如果您的系統管理員以這種方式設定驗證，可能也會提示您提供其他因素。
5. 按一下 [**儲存**]。

在此之後，連接中心應該會顯示遠端資源。

訂閱摘要之後，摘要的內容將會定期自動更新。 根據系統管理員所做的變更，可能會新增、變更或移除資源。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何使用 iOS Beta 版用戶端，請參閱[開始使用 ios 用戶端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios)檔。
