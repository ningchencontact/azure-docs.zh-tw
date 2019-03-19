---
title: 將公司或學校帳戶新增至 Microsoft Authenticator 應用程式 - Azure Active Directory | Microsoft Docs
description: 如何將公司和學校帳戶新增至 Microsoft Authenticator 應用程式以進行雙因素驗證。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231f3c44344f4119bab6e1efb5546e6fded0c784
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960599"
---
# <a name="add-your-work-or-school-account"></a>新增公司或學校帳戶
如果您的組織使用雙因素驗證，您可以設定公司或學校帳戶，使其使用 Microsoft Authenticator 應用程式作為其中一種驗證方法。

>[!Important]
>您必須先下載並安裝 Microsoft Authenticator 應用程式，才可新增帳戶。 如果尚未這麼做，請依照[下載並安裝應用程式](user-help-auth-app-download-install.md)文章中的步驟。

## <a name="add-your-work-or-school-account"></a>新增公司或學校帳戶

1. 在您的電腦上，移至[其他安全性驗證](https://aka.ms/mfasetup)頁面。

    >[!Note]
    >如果您未看到 [其他安全性驗證] 頁面，有可能是您的系統管理員已開啟安全性資訊 (預覽) 體驗。 若是如此，您即應依照[將安全性資訊設定為使用驗證器應用程式](security-info-setup-auth-app.md)一節中的指示操作。 若非如此，您必須連絡組織的技術支援人員以尋求協助。 如需安全性資訊的詳細資訊，請參閱[安全性資訊 （預覽） 概觀](user-help-security-info-overview.md)。

2. 核取 [驗證器應用程式] 旁的方塊，然後選取 [設定]。

    [設定行動應用程式] 頁面隨即出現。
    
    ![提供 QR 代碼的畫面](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. 開啟 Microsoft Authenticator 應用程式、從右上角的 [自訂和控制] 圖示中選取 [新增帳戶]，然後選取 [公司或學校帳戶]。

4. 使用裝置相機掃描電腦的 [設定行動應用程式] 畫面中顯示的 QR 代碼，然後選擇 [完成]。

    >[!Note]
    >如果您的相機無法擷取 QR 代碼，您可以將您的帳戶資訊手動新增至 Microsoft Authenticator 應用程式，以進行雙因素驗證。 如需詳細資訊及其操作方式，請參閱[手動新增帳戶](user-help-auth-app-add-account-manual.md)。

5. 在您的裝置上檢閱應用程式的 [帳戶] 畫面，以確定您的帳戶正確無誤，並且有相關聯的六位數驗證碼。 為了提高安全性，驗證碼每隔 30 秒就會變更，避免多次使用同一個驗證碼。

    ![帳戶畫面](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>後續步驟

- 將您的帳戶新增至應用程式之後，您即可在裝置上使用 Authenticator 應用程式登入。 如需詳細資訊，請參閱[使用應用程式登入](user-help-auth-app-sign-in.md)。

- 對於執行 iOS 的裝置，您也可以將您的帳戶認證和相關應用程式設定 (例如您的帳戶順序) 備份到雲端。 如需更多資訊，請參閱[使用 Microsoft Authenticator 應用程式備份和復原](user-help-auth-app-backup-recovery.md)。
