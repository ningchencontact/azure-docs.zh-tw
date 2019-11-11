---
title: Azure 入口網站應用程式註冊訓練指南（舊版）-Azure
description: Microsoft 身分識別平臺中的新應用程式註冊體驗簡介。
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
ms.openlocfilehash: bbdb6a2e17ab867b4938e94ae5a20a95cc352daa
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905390"
---
# <a name="training-guide-app-registrations-in-the-azure-portal-legacy"></a>訓練指南： Azure 入口網站中的應用程式註冊（舊版）

您可以在 Azure 入口網站的新[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗中找到眾多改良功能。 如果您更熟悉舊版體驗，請使用此訓練指南協助您開始使用新的體驗。

在 Azure Active Directory 中，這裡所述的新應用程式註冊體驗已正式推出（GA）。 在 Azure Active Directory B2C （Azure AD B2C）中，這項體驗目前為預覽狀態。

## <a name="key-changes"></a>金鑰變更

- 應用程式註冊不限於**web 應用程式/API**或**原生**應用程式。 藉由註冊個別的重新導向 Uri，您就可以使用相同的應用程式註冊。
- 舊版體驗僅支援登入組織（Azure AD）帳戶的應用程式。 應用程式已註冊為單一租使用者（僅支援來自已註冊應用程式的目錄中的組織帳戶），而且可以修改為多租使用者（支援所有組織帳戶）。 新的體驗可讓您註冊可以同時支援這兩個選項的應用程式，以及第三個選項：所有組織帳戶，以及個人 Microsoft 帳戶。
- 只有在使用組織帳戶登入 Azure 入口網站時，才可以使用舊版體驗。 有了新的體驗，您可以使用未與目錄相關聯的個人 Microsoft 帳戶。

## <a name="list-of-applications"></a>應用程式清單

- [新增應用程式] 清單會顯示已透過 Azure 入口網站中的繼承應用程式註冊體驗（登入 Azure AD 帳戶），以及應用[程式註冊入口網站](https://apps.dev.microsoft.com/)（登入的應用程式）登錄的應用程式Azure AD 和個人 Microsoft 帳戶）。
- 新的應用程式清單沒有 [**應用程式類型**] 資料行（因為單一應用程式註冊可以是數種類型），而且有兩個額外的資料行： [在資料行**上建立**] 和 [**憑證 & 秘密**] 資料行顯示狀態（目前、即將到期或已過期）已在應用程式上註冊的認證。

## <a name="new-app-registration"></a>新增應用程式註冊

在舊版體驗中，若要註冊應用程式，您必須提供：**名稱**、**應用程式類型**和登**入 URL/重新導向 URI**。 所建立的應用程式只 Azure AD 單一租使用者應用程式，這表示它們只支援從應用程式註冊所在目錄中的組織帳戶。

在新的體驗中，您必須提供應用程式的**名稱**，並選擇**支援的帳戶類型**。 您可以選擇性地提供重新**導向 URI**。 如果您提供 [重新導向 URI]，則必須指定它是否為 web/公用（行動和桌面）。 如需如何使用新的應用程式註冊體驗來註冊應用程式的詳細資訊，請參閱[使用 Microsoft 身分識別平臺註冊應用程式](quickstart-register-app.md)。 如 Azure AD B2C，請參閱[在 Azure Active Directory B2C 中註冊應用程式](../../active-directory-b2c/tutorial-register-applications.md)。

## <a name="the-legacy-properties-page"></a>舊版屬性頁面

舊版體驗具有新體驗沒有的**屬性**頁面。 [**屬性**] 分頁具有下欄欄位：**名稱**、**物件識別碼**、**應用程式識別碼**、**應用程式識別碼 URI**、**標誌**、**首頁 url**、**登出 url**、**服務條款 URL**、**隱私權聲明URL**、**應用程式類型**和**多租使用者。**

您可以在這裡找到新體驗中的對等功能：

- [**名稱**]、[**標誌**]、[**首頁 url**]、[**服務條款 url**] 和 [**隱私權聲明 url** ] 現在位於應用程式的 [**商標**] 頁面上。
- **物件識別碼**和**應用程式（用戶端）識別碼**位於 [**總覽**] 頁面上。
- 舊版體驗中的**多租**使用者切換所控制的功能，已由 [**驗證**] 頁面上**受支援的帳戶類型**所取代。 如需多租使用者如何對應至支援的帳戶類型選項的詳細資訊，請參閱[此快速入門](quickstart-modify-supported-accounts.md)。
- [**登出 URL** ] 現在位於 [**驗證**] 頁面上。
- **應用程式類型**不再是有效的欄位。 相反地，重新導向 Uri （您可以在 [**驗證**] 頁面上找到）會決定支援哪些應用程式類型。
- **APP ID uri**現在稱為**應用程式識別碼 uri** ，您可以在 [**公開 API** ] 分頁上找到此識別碼。 在舊版體驗中，已使用下列格式自動註冊此屬性： `https://{tenantdomain}/{appID}` （例如 `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`）。 在新的體驗中，它會自動產生為 `api://{appID}`，但必須加以明確儲存。 在 Azure AD B2C 租使用者中，仍然會使用 `https://{tenantdomain}/{appID}` 格式。

## <a name="reply-urlsredirect-urls"></a>回復 Url/重新導向 Url

在舊版體驗中，應用程式有 [**回復 url** ] 頁面。 在新體驗中，可以在應用程式的**驗證**區段中找到回復 url。 此外，它們也稱為「重新**導向 uri**」。 此外，重新導向 Uri 的格式也已變更。 它們必須與應用程式類型（web 或公用）相關聯。 此外，基於安全性理由，不支援萬用字元和 HTTP://配置（但 http://localhost)除外。

## <a name="keyscertificates--secrets"></a>金鑰/憑證 & 秘密

在舊版體驗中，應用程式有 [**金鑰**] 頁面。 在新的體驗中，已將其重新命名為 **& 秘密的憑證**。 此外，**公開金鑰**稱為「**憑證**」，而「**密碼**」則稱為「**用戶端**密碼」。

## <a name="required-permissionsapi-permissions"></a>必要許可權/API 許可權

- 在舊版體驗中，應用程式具有 [**必要許可權**] 頁面。 在新的體驗中，它已重新命名為**API 許可權**。
- 選取舊版體驗中的 API 時，您可以從小型的 Microsoft Api 清單中選擇，或在租使用者中搜尋服務主體。 在新體驗中，您可以從多個索引標籤中選擇： **Microsoft api**、**我的組織使用的 Api**，或**我的 api**。 **我組織的 api**上的搜尋列會使用索引標籤搜尋租使用者中的服務主體。

   > [!NOTE]
   > 如果您的應用程式未與租使用者建立關聯，您將不會看到此索引標籤。 如需如何使用新的體驗來要求許可權的詳細資訊，請參閱[此快速入門](quickstart-configure-app-access-web-apis.md)。

- 舊版體驗具有 [**要求的許可權**] 頁面頂端的 **[授與許可權**] 按鈕。 在新的體驗中，有一個 **[授與同意**] 區段，其中包含應用程式的 [ **API 許可權**] 區段上的 **[授與管理員同意**] 按鈕。 此外，按鈕的運作方式有一些差異：
   - 在舊版體驗中，邏輯會根據登入的使用者和所要求的許可權而有所不同。 邏輯是：
      - 如果只要求使用者同意的許可權，而且登入的使用者不是系統管理員，則使用者可以將要求的許可權授與使用者同意。
      - 如果至少有一個許可權需要系統管理員同意，而且登入的使用者不是系統管理員，則在嘗試授與同意時，使用者會收到錯誤。
      - 如果登入的使用者是系統管理員，則會針對所有要求的許可權授與系統管理員同意。
   - 在新的體驗中，只有系統管理員可以授與同意。 當系統管理員選取 [**授與系統管理員同意**] 按鈕時，系統管理員同意會授與所有要求的許可權。

## <a name="deleting-an-app-registration"></a>刪除應用程式註冊

在舊版體驗中，應用程式必須是要刪除的單一租使用者。 多租使用者應用程式的 [刪除] 按鈕已停用。 在新的體驗中，應用程式可以在任何狀態下刪除，但是您必須確認該動作。 如需有關刪除應用程式註冊的詳細資訊，請參閱[此快速入門](quickstart-remove-app.md)。

## <a name="application-manifest"></a>應用程式資訊清單

舊版和新體驗會在資訊清單編輯器中使用不同版本的 JSON 格式。 如需詳細資訊，請參閱[應用程式資訊清單](reference-app-manifest.md)。

## <a name="new-ui"></a>新增 UI

先前只能使用資訊清單編輯器或 API 來設定屬性的新 UI，或不存在。

- 您可以在 [**驗證**] 頁面上找到**隱含授與流程**（oauth2AllowImplicitFlow）。 與舊版體驗不同的是，您可以啟用**存取權杖**或**識別碼權杖**，或兩者。
- **此 API 定義的範圍**（oauth2Permissions）和**授權的用戶端應用程式**（preAuthorizedApplications）可以透過 [**公開 API** ] 頁面來設定。 如需有關如何將應用程式設定為 Web API 並公開許可權/範圍的詳細資訊，請參閱[此快速入門](quickstart-configure-app-expose-web-apis.md)。
- **發行者網域**（向使用者顯示在[應用程式的同意提示](application-consent-experience.md)中）可以在 [**商標**] 分頁頁面上找到。 如需有關如何設定發行者網域的詳細資訊，請參閱[此](howto-configure-publisher-domain.md)作法。

## <a name="limitations"></a>限制

新的體驗有下列限制：

- 用戶端秘密（應用程式密碼）的格式與舊版體驗不同，可能會中斷 CLI。
- 在 UI 中不支援變更所支援帳戶的值。 您必須使用應用程式資訊清單，除非您要在 Azure AD 單一租使用者和多租使用者之間切換。