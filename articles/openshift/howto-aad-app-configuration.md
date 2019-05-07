---
title: 建立 Azure AD 應用程式註冊和使用者針對 Azure Red Hat OpenShift |Microsoft Docs
description: 了解如何建立服務主體、 產生用戶端密碼和驗證回呼 URL，以及用於測試 Microsoft Azure 的 Red Hat OpenShift 叢集上的應用程式建立新的 Active Directory 使用者。
author: tylermsft
ms.author: twhitney
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/06/2019
ms.openlocfilehash: de3f3c30848d26ea399bcccc29a6149a149f6a55
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078516"
---
# <a name="create-an-azure-ad-app-registration-and-user-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 來建立 Azure AD 應用程式註冊和使用者

Microsoft Azure 的 Red Hat OpenShift 需要代表您的叢集執行工作的權限。 如果您的組織還沒有 Azure Active Directory (Azure AD) 應用程式註冊為服務主體使用，請遵循下列指示來建立一個。

本主題最後指示如何建立新的 Azure AD 使用者，您將需要存取 Azure 的 Red Hat OpenShift 叢集上執行的應用程式。

如果您尚未建立 Azure AD 租用戶，請依照下列中的指示[建立 Azure AD 租用戶的 Azure Red Hat OpenShift](howto-create-tenant.md)再繼續進行這些指示。

## <a name="create-a-new-app-registration"></a>建立新的應用程式註冊

想要使用 Azure AD 功能的應用程式必須先註冊 Azure AD 租用戶中。 此登錄程序牽涉到提供 Azure AD 應用程式，例如應用程式所在的 URL、 使用者驗證之後傳送回覆的 URL、 識別應用程式，並依此類推的 URI 的詳細資料。

1. 在  [Azure 入口網站](https://portal.azure.com)，確保您的租用戶，會出現在右上角您的使用者名稱下入口網站的權限：

    ![與租用戶入口網站的螢幕擷取畫面示右上方][tenantcallout]如果顯示錯誤的租用戶，請按一下右上方，您的使用者名稱，然後按一下**切換目錄**，然後選取正確的租用戶從**所有目錄**清單。

2. 開啟[應用程式註冊 刀鋒視窗](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)然後按一下**新的應用程式註冊**。
3. 在 [**建立**] 窗格中，輸入您的應用程式物件的易記名稱。
4. 請確認**應用程式類型**設為*Web 應用程式 /API*。
5. 建立**登入 URL**使用下列模式：

    ```
    https://<cluster-name>.<azure-region>.cloudapp.azure.com
    ```

    上也提供本文中使用的原始碼。 . 上也提供本文中使用的原始碼。 何處`<cluster-name>`是 Azure 的 Red Hat OpenShift 叢集的預期的名稱和`<azure-region>`是[裝載您的 Azure Red Hat OpenShift 叢集的 Azure 區域](supported-resources.md#azure-regions)。 例如，如果您的叢集名稱被`contoso`，和您將建立在`eastus`區域中，您會輸入完整的網域名稱 (FQDN)**登入 URL**會 `https://contoso.eastus.cloudapp.azure.com`

    > [!IMPORTANT]
    > 叢集名稱必須全部小寫，並必須是唯一的 FQDN URL。
    > 請確定您選擇完全不同的名稱，為您的叢集。

    請記下您的叢集名稱和登入 URL，您將需要這些更新版本為存取您的叢集上執行的應用程式。 我們會為叢集名稱`CLUSTER_NAME`，與此登入 URL 作為`FQDN`中[建立 Azure 的 Red Hat OpenShift 叢集](tutorial-create-cluster.md)教學課程。

6. 請確定您**登入 URL**使用綠色的核取的值進行驗證。 (按 Tab 鍵將焦點，共*登入 URL*欄位，然後執行驗證檢查。)
7. 按一下 [**建立**] 按鈕來建立 Azure AD 應用程式物件。
8. 在 **註冊的應用程式**所顯示的頁面，向下複製**應用程式識別碼**。 我們會將此值作為`APPID`中[建立 Azure 的 Red Hat OpenShift 叢集](tutorial-create-cluster.md)教學課程。

    ![應用程式的 [識別碼] 文字方塊的螢幕擷取畫面][appidimage]
    
如需 Azure 應用程式物件的詳細資訊，請參閱[應用程式和 Azure Active Directory 中的服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)。

如需有關建立新的 Azure AD 應用程式，請參閱[註冊應用程式與 Azure Active Directory v1.0 端點](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)。

### <a name="create-a-client-secret"></a>建立用戶端密碼

現在您已準備好產生用戶端祕密，以驗證您的應用程式至 Azure Active Directory。

1. 從**註冊的應用程式**頁面上，按一下**設定**以開啟 已註冊的應用程式設定。
2. 在 **設定**窗格中，按一下 **金鑰**。  **金鑰**窗格隨即出現。
![在入口網站中建立金鑰 頁面的螢幕擷取畫面][createkeyimage]
3. 提供的金鑰**描述**。
4. 設定的值**Expires**，例如*2 年內*。
5. 按一下 **儲存**和索引鍵的值將會出現。
6. 複製金鑰值。 我們會將此值作為`SECRET`中[建立 Azure 的 Red Hat OpenShift 叢集](tutorial-create-cluster.md)教學課程。

### <a name="create-a-reply-url"></a>建立 回覆 URL

Azure AD 會使用應用程式物件*回覆 URL*指定授權應用程式使用時回呼。 如果是 web API 或 web 應用程式中，回覆 URL 會是 Azure AD 會將傳送驗證回應，包括權杖，如果驗證成功的位置。

即使最小的不相符 (尾端斜線遺失，大小寫不同) 會導致權杖發佈作業失敗，而且您將無法登入。

1. 返回至**設定**窗格 (您可以按一下**設定**入口網站頂端階層連結)，然後按一下**回覆 Url**右邊。  **回覆 Url**窗格隨即出現。
2. 在清單中的第一個回覆 URL 會是`FQDN`值中的步驟 6[建立新的應用程式註冊](#create-a-new-app-registration)。 編輯它，並附加`/oauth2callback/Azure%20AD`。  例如，您的回覆 URL 現在看起來應該類似 `https://mycluster.eastus.cloudapp.azure.com/oauth2callback/Azure%20AD`
3. 按一下 **儲存**儲存回覆 URL。

## <a name="create-a-new-active-directory-user"></a>建立新的 Active Directory 使用者

用以登入 Azure 的 Red Hat OpenShift 叢集上執行應用程式的 Active Directory 中建立新的使用者。

1. 移至[使用者-所有使用者](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)刀鋒視窗。
2. 按一下 [+ 新增使用者]。 **使用者**窗格隨即出現。
3. 請輸入**名稱**您想要為此使用者。
4. 建立**使用者名**根據您使用建立租用戶名稱`.onmicrosoft.com`附加在結尾。 例如： `yourUserName@yourTenantName.onmicrosoft.com`。 記下此使用者名稱。 您將需要它來登入您的叢集上使用應用程式。
5. 按一下 **目錄角色**，然後選取**全域系統管理員**，然後按一下  **Ok**窗格的底部。
6. 中的中間**使用者**窗格中，按一下**顯示密碼**和錄製的暫時密碼。 登入第一次之後，系統會提示您重設它。
7. 在窗格的底部，按一下**建立**來建立使用者。

## <a name="resources"></a>資源

* [應用程式與 Azure Active Directory 中的服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [快速入門：使用 Azure Active Directory v1.0 端點註冊 app](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

[appidimage]: ./media/howto-create-tenant/get-app-id.png
[createkeyimage]: ./media/howto-create-tenant/create-key.png
[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="next-steps"></a>後續步驟

如果您已符合所有[Azure Red Hat OpenShift 必要條件](howto-setup-environment.md)，您即可建立您的第一個叢集 ！

請嘗試本教學課程：
> [!div class="nextstepaction"]
> [建立 Azure 的 Red Hat OpenShift 叢集](tutorial-create-cluster.md)
