---
title: 快速入門 - 設定使用 Azure Active Directory B2C 登入傳統型應用程式 | Microsoft Docs
description: 執行範例 ASP.NET 傳統型應用程式，而該應用程式使用 Azure Active Directory B2C 來提供帳戶登入。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 94e7972530afee15937b13ae35239a64d9bc986e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190535"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>快速入門：設定使用 Azure Active Directory B2C 登入傳統型應用程式 

Azure Active Directory (Azure AD) B2C 提供雲端身分識別管理，使您的應用程式、企業和客戶受到保護。 Azure AD B2C 可讓您的應用程式使用開放式標準通訊協定，向社交帳戶和企業帳戶進行驗證。 本快速入門中，您會使用 Windows Presentation Foundation (WPF) 傳統型應用程式，透過社交識別提供者登入，並呼叫受 Azure AD B2C 保護的 Web API。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

- [Visual Studio 2019](https://www.visualstudio.com/downloads/)，其中包含 **ASP.NET 和 Web 部署**工作負載。 
- Facebook、Google、Microsoft 或 Twitter 社交帳戶。
- [下載 zip 檔案](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip)，或從 GitHub 複製範例 Web 應用程式。

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>在 Visual Studio 中執行應用程式

1. 在範例應用程式專案資料夾中，於 Visual Studio 中開啟 **active-directory-b2c-wpf.sln** 解決方案。
2. 按 **F5** 鍵來進行應用程式偵錯。

## <a name="sign-in-using-your-account"></a>使用您的帳戶登入

1. 按一下 [登入] 以開始「註冊或登入」工作流程。

    ![範例應用程式](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

    此範例支援數個註冊選項。 這些選項包括使用社交識別提供者或使用電子郵件地址建立本機帳戶。 在本快速入門中，請使用 Facebook、Google、Microsoft 或 Twitter 的社交身分識別提供者帳戶。 


2. Azure AD B2C 會針對範例 Web 應用程式，呈現 Wingtip Toys 虛構品牌的自訂登入頁面。 若要使用社交識別提供者註冊，按一下您想要使用之識別提供者的按鈕。 

    ![登入或註冊提供者](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

    您可使用您的社交帳戶認證進行驗證 (登入)，以及授權應用程式讀取您的社交帳戶資訊。 透過授與存取權，應用程式可以從社交帳戶擷取設定檔資訊，例如您的名稱與縣/市。 

2. 完成識別提供者的登入程序。

    您的新帳戶設定檔詳細資料會預先填入來自您社交帳戶的資訊。

## <a name="edit-your-profile"></a>編輯您的設定檔

Azure AD B2C 提供讓使用者更新其設定檔的功能。 範例 Web 應用程式會對工作流程使用 Azure AD B2C 編輯設定檔使用者流程。 

1. 在應用程式功能表列中，按一下 [編輯設定檔] 以編輯您建立的設定檔。

    ![編輯設定檔](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

2. 選擇與您建立的帳戶相關聯的識別提供者。 例如，如果建立您的帳戶時使用 Twitter 作為識別提供者，請選擇 Twitter 以修改相關聯的設定檔詳細資料。

3. 變更您的 [顯示名稱] 或 [縣/市]，然後按一下 [繼續]。

    新的存取權杖會顯示在 [權杖資訊] 文字方塊。 如果您想要檢查對設定檔所作的變更，請將存取權杖複製並貼到權杖解碼器 https://jwt.ms 。

## <a name="access-a-protected-api-resource"></a>存取受保護的 API 資源

按一下 [呼叫 API] 向受保護的資源提出要求。 

    ![Call API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

    The application includes the Azure AD access token in the request to the protected web API resource. The web API sends back the display name contained in the access token.

您已成功使用 Azure AD B2C 使用者帳戶，對受 Azure AD B2C 保護的 Web API 提出授權呼叫。

## <a name="clean-up-resources"></a>清除資源

如果您想要嘗試其他 Azure AD B2C 快速入門或教學課程，可以使用 Azure AD B2C 租用戶。 不再需要時，您可以[刪除您的 Azure AD B2C 租用戶](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用範例傳統型應用程式： 

* 使用自訂登入頁面登入
* 透過社交識別提供者登入
* 建立 Azure AD B2C 帳戶
* 呼叫受 Azure AD B2C 保護的 Web API

開始建立您自己的 Azure AD B2C 租用戶。 

> [!div class="nextstepaction"]
> [在 Azure 入口網站中建立 Azure Active Directory B2C 租用戶](tutorial-create-tenant.md)
