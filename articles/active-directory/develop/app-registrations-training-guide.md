---
title: 在 Azure 入口網站的訓練指南-Azure 中的應用程式註冊
description: 使用裝置代碼授與，組建無瀏覽器的內嵌驗證流程。
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 118c6ecb16d325a384246a0b3d9e685f6f6f04ee
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870113"
---
# <a name="training-guide-app-registrations-in-the-azure-portal"></a>訓練指南：在 Azure 入口網站中的應用程式註冊  

您可以找到許多增強功能中的新[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗在 Azure 入口網站中。 如果您更熟悉舊版體驗時，使用此訓練指南來協助您開始使用新的體驗。

## <a name="key-changes"></a>重大變更

- 應用程式註冊不侷限於**web 應用程式 /API**或是**原生**應用程式。 您可以使用相同的應用程式註冊的所有這些註冊各自的重新導向 Uri。
- 支援的舊版體驗應用程式登入組織 (Azure AD) 帳戶只。 應用程式註冊為單一租用戶 （支援從目錄中註冊應用程式的只有組織帳戶），無法修改成多租用戶 （支援所有的組織帳戶）。 新體驗可讓您註冊應用程式，可以支援這些兩個選項，以及第三個選項： 所有組織帳戶，以及個人 Microsoft 帳戶。
- 僅當登入 Azure 入口網站使用組織帳戶可使用舊版的體驗。 使用新的體驗，您可以使用未與目錄相關聯的個人 Microsoft 帳戶。

## <a name="list-of-applications"></a>應用程式清單

- 新的應用程式清單會顯示透過舊版的應用程式是否已註冊的應用程式在 Azure 入口網站 （登入 Azure AD 帳戶的應用程式） 以及應用程式的註冊體驗已註冊但[應用程式註冊入口網站](https://apps.dev.microsoft.com/)(在 Azure AD 中簽署的應用程式和個人 Microsoft 帳戶)。
- 新的應用程式清單中沒有**應用程式類型**（因為單一應用程式註冊可以是數種類型） 的資料行且具有兩個額外的資料行：**上建立**資料行和**憑證& 祕密**的已註冊的應用程式的認證會顯示狀態 （目前，將到期或已過期） 的資料行。

## <a name="new-app-registration"></a>新的應用程式註冊

在舊版的體驗中，若要註冊應用程式已要求您提供：**名稱**，**應用程式類型**，以及**登入 URL/重新導向 URI**。 已建立的應用程式是 Azure AD 只單一租用戶應用程式，這表示，只支援從目錄中註冊應用程式的組織帳戶。

在新體驗中，您必須提供**名稱**應用程式，然後選擇**支援的帳戶類型**。 您可以選擇性地提供**重新導向 URI**。 如果您提供的重新導向 URI 時，您必須指定它是 web/公用 （行動和桌面）。 如需詳細資訊，有關如何註冊應用程式使用新的應用程式註冊體驗，請參閱 <<c0> [ 本快速入門](quickstart-register-app.md)。

## <a name="the-legacy-properties-page"></a>舊版的 [內容] 頁面

舊版的體驗有**屬性**頁面沒有新的體驗。 **屬性**刀鋒視窗中有下列欄位：**名稱**，**物件識別碼**，**應用程式識別碼**，**應用程式識別碼 URI**，**標誌**，**首頁 URL****登出 URL**，**服務條款 URL**，**隱私權聲明 URL**，**應用程式類型**，和**多租用戶。**

以下是您可以在其中找到在新版中對等的功能：

- **名稱**，**標誌**，**首頁 URL**，**服務條款 URL**，並**隱私權聲明 URL**現在位於應用程式的**商標**頁面。
- **物件識別碼**並**應用程式 （用戶端） 識別碼**位於**概觀**頁面。
- 所控制的功能**多租用戶**已被取代的舊版體驗中切換**支援的帳戶類型**上**驗證**頁面。 如需有關如何將多租用戶對應至 [支援的帳戶類型] 選項的詳細資訊，請參閱 <<c0> [ 本快速入門](quickstart-modify-supported-accounts.md)。
- **登出 URL**現在**驗證**頁面。
- **應用程式類型**已不再是有效的欄位。 相反地，重新導向 Uri (您可以找到上**驗證**頁面) 判斷支援哪些應用程式類型。
- **應用程式識別碼 URI**現在稱為**應用程式識別碼 URI**您就可以上找到這**公開 API**刀鋒視窗。 在舊版的體驗中，這個屬性是自動註冊，使用下列格式： `https://{tenantdomain}/{appID}` (例如`https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`)。 在新的格式，它會自動產生為`api://{appID}`，但它必須明確地儲存。

## <a name="reply-urlsredirect-urls"></a>回覆 Url/重新導向 Url

在舊版的體驗中，應用程式必須**回覆 Url**頁面。 在新體驗中，回覆 Url 可以找到的應用程式上**驗證**一節。 此外，它們指**重新導向 Uri**。 此外，格式為重新導向 Uri 已變更。 它們是一定要與應用程式類型 （web 或公用） 相關聯。 此外，基於安全性理由，萬用字元和 http:// 配置不支援 (除了 http://localhost)。

## <a name="keyscertificates--secrets"></a>金鑰/憑證與密碼

在舊版的體驗中，應用程式必須**金鑰**頁面。 在新體驗中，它有已重新命名為**憑證與祕密**。 颾魤 ㄛ**公開金鑰**稱為**憑證**並**密碼**稱為**用戶端祕密**。

## <a name="required-permissionsapi-permissions"></a>必要的權限 API 權限

- 在舊版的體驗中，應用程式必須**必要的權限**頁面。 在新體驗中，它有已重新命名為**API 的權限**。
- 當選取的 API 中的舊版體驗，您可以選擇從小型的 Microsoft Api 或透過租用戶中的服務主體的搜尋清單。 在新體驗中，您可以選擇從多個索引標籤：**Microsoft Api**，**我的組織使用的 Api**，或**我的 Api**。 在 搜尋 列**Api 我的組織**租用戶中使用服務主體 索引標籤搜尋。 

   > [!NOTE]
   > 如果您的應用程式未與租用戶相關聯，您就不會看到此索引標籤。 如需如何要求使用的新體驗的權限的詳細資訊，請參閱[本快速入門](quickstart-configure-app-access-web-apis.md)。

- 舊版的體驗有**授與權限**頂端的按鈕**要求的權限**頁面。 在新體驗，還有**授與同意**區段**授與系統管理員同意**的應用程式上的按鈕**API 權限**一節。 此外，有一些差異方式按鈕函式：
   - 在舊版的體驗中，邏輯而變動根據登入的使用者和所要求的權限。 邏輯是：
      - 只有正在要求的使用者可同意的權限登入的使用者不是系統管理員，使用者無法同意要求的權限的使用者。
      - 如果所要求至少一個需要系統管理員同意的權限和登入的使用者不是系統管理員，使用者嘗試將同意授與時，就會收到錯誤。
      - 系統管理員登入的使用者時，所有要求的權限已授與系統管理員同意。
   - 在新體驗中，只有系統管理員可以授與同意。 選取系統管理員的時機**授與系統管理員同意** 按鈕，系統管理員同意授與所有要求的權限。

## <a name="deleting-an-app-registration"></a>刪除應用程式註冊

在舊版的體驗中，必須是單一租用戶刪除應用程式。 多租用戶應用程式已停用 [刪除] 按鈕。 在新體驗中，在任何狀態，才能刪除應用程式，但您必須確認該動作。 如需有關如何刪除應用程式註冊的詳細資訊，請參閱[本快速入門](quickstart-remove-app.md)。

## <a name="application-manifest"></a>應用程式資訊清單

傳統和新型體驗使用不同版本的資訊清單編輯器中的 JSON 格式。 如需詳細資訊，請參閱[應用程式資訊清單](reference-app-manifest.md)。

## <a name="new-ui"></a>新的 UI

沒有新的 UI 可能先前只能透過設定資訊清單編輯器或 API，或不存在的屬性。

- **隱含授與流程**(oauth2AllowImplicitFlow) 都位於**驗證**頁面。 不同於在舊版的體驗，您可以啟用**存取權杖**或是**識別碼權杖**，或兩者。
- **此 API 所定義的範圍**(oauth2Permissions) 和**授權用戶端應用程式**(preAuthorizedApplications) 可以透過設定**公開 API**頁面。 如需如何設定應用程式是 web API，並公開 （expose） 的權限/範圍的詳細資訊，請參閱[本快速入門](quickstart-configure-app-expose-web-apis.md)。
- **發行者網域**(這會顯示給使用者，在[應用程式的同意提示](application-consent-experience.md)) 都位於**商標設定 刀鋒視窗**頁面。 如需如何設定 「 發行者 」 網域的詳細資訊，請參閱[此操作說明](howto-configure-publisher-domain.md)。

## <a name="limitations"></a>限制

新的體驗具有下列限制：

- 新體驗目前無法使用。 Azure AD B2C 租用戶中
- 用戶端祕密 （應用程式密碼） 的格式不同，舊版的體驗，並中斷 CLI。
- 在 UI 中不支援變更為支援的帳戶值。 您需要使用應用程式資訊清單，除非您 Azure AD 單一租用戶和多租用戶之間切換。
