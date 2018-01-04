---
title: "使用 Azure AD 驗證搭配 REST 存取 Azure 媒體服務 API | Microsoft Docs"
description: "了解如何使用 Azure Active Directory 驗證搭配 REST 存取 Azure 媒體服務 API。"
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/26/2017
ms.author: willzhan;juliako;johndeu
ms.openlocfilehash: ed78d6c6d4c695b841dbfbf917cd1681adc44ee7
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2018
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>使用 Azure AD 驗證搭配 REST 存取 Azure 媒體服務 API

使用 Azure AD 驗證搭配 Azure 媒體服務時，您可以下列其中一種方式進行驗證：

- **使用者驗證**可驗證使用應用程式與 Azure 媒體服務資源互動的人員。 互動式應用程式應該會先提示使用者輸入認證。 例如，授權的使用者用來監控編碼工作或即時串流的管理主控台應用程式。 
- **服務主體驗證**會驗證服務。 通常使用這種驗證方法的應用程式有執行精靈服務、中介層服務或排程的工作的應用程式：例如，Web 應用程式、函數應用程式、邏輯應用程式、API 或微服務。

    本教學課程會示範如何使用 Azure AD**服務主體**存取 AMS REST API 的驗證。 

    > [!NOTE]
    > **服務主體**是建議的最佳作法，對於大部分的應用程式連接到 Azure Media Services。 

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 從 Azure 入口網站取得的驗證資訊
> * 取得使用郵差的存取權杖
> * 測試**資產**應用程式開發介面使用的存取權杖


> [!IMPORTANT]
> 目前，媒體服務支援 Azure 存取控制服務驗證模型。 不過，存取控制驗證將在 2018 年 6 月 1 日被取代。 建議您儘速移轉至 Azure AD 驗證模型。

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 。
- [建立 Azure 媒體服務帳戶使用 Azure 入口網站](media-services-portal-create-account.md)。
- 檢閱[存取 Azure 媒體服務應用程式開發介面使用 AAD 驗證概觀](media-services-use-aad-auth-to-access-ams-api.md)發行項。
- 安裝[郵差](https://www.getpostman.com/)執行本文中所顯示的 REST Api 的 REST 用戶端。 

    在本教學課程中，我們會在**郵差**但任何其他工具會適用。 其他替代方式為： **Visual Studio Code**使用 REST 外掛程式或**Telerik Fiddler**。 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>從 Azure 入口網站取得的驗證資訊

### <a name="overview"></a>概觀

若要存取 Media Services API，您需要收集下列資料點。

|設定|範例|說明|
|---|-------|-----|
|Azure Active Directory 租用戶網域|microsoft.onmicrosoft.com|Azure AD 做為安全權杖服務 (STS) 端點會建立使用下列格式： https://login.microsoftonline.com/ {your-aad-tenant-name.onmicrosoft.com}/oauth2/token。 Azure AD 會發出 JWT 才能存取資源 （存取權杖）。|
|REST API 端點|https://amshelloworld.restv2.westus.media.azure.net/api/|您的應用程式中發出之所有媒體服務 REST API 呼叫，都是針對此端點。|
|用戶端識別碼 （應用程式識別碼）|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Azure AD 應用程式 （用戶端） 識別碼。 用戶端識別碼，才能取得存取權杖。 |
|用戶端密碼|+ mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq + Dbim0 =|Azure AD 應用程式金鑰 （用戶端密碼）。 用戶端密碼，才能取得存取權杖。|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>從 Azure 入口網站取得 AAD 驗證資訊

若要取得的資訊，請遵循下列步驟：

1. 登入 [Azure 入口網站](http://portal.azure.com)。
2. 瀏覽至您 AMS 執行個體。
3. 選取**API 存取**。
4. 按一下**連接到 Azure Media Services API，與服務主體**。

    ![API 存取權](./media/connect-with-rest/connect-with-rest01.png)

5. 選取現有**Azure AD 應用程式**或建立新的連線 （如下所示）。

    > [!NOTE]
    > 對於 Azure 媒體 REST 要求成功，必須呼叫使用者**參與者**或**擁有者**Media Services 帳戶，嘗試存取的角色。 如果您收到例外狀況指出 「 遠端伺服器傳回錯誤: (401) 未授權，「 請參閱[存取控制](media-services-use-aad-auth-to-access-ams-api.md#access-control)。

    如果您要建立新的 AD 應用程式，請遵循下列步驟：
    
    1. 按**建立新**。
    2. 輸入的名稱。
    3. 按**新建**一次。
    4. 按下 [儲存] 。

    ![API 存取權](./media/connect-with-rest/new-app.png)

    新的應用程式會顯示在頁面中。

6. 取得**用戶端識別碼**（應用程式識別碼）。
    
    1. 選取的應用程式。
    2. 取得**用戶端識別碼**從右側視窗。 

    ![API 存取權](./media/connect-with-rest/existing-client-id.png).

7.  取得應用程式的**金鑰**（用戶端密碼）。 

    1. 按一下**管理應用程式**按鈕 (請注意，用戶端識別碼資訊低於**應用程式識別碼**)。 
    2. 按**金鑰**。
    
        ![API 存取權](./media/connect-with-rest/manage-app.png)
    3. 產生應用程式金鑰 （用戶端密碼） 來填入**描述**和**EXPIRES**按**儲存**。
    
        一次**儲存**按下按鈕時，索引鍵的值會出現。 離開刀鋒視窗之前複製的索引鍵的值。

    ![API 存取權](./media/connect-with-rest/connect-with-rest03.png)

您可以加入您的 web.config 或 app.config 檔案，以更新您的程式碼中使用的 AD 連接參數的值。

> [!IMPORTANT]
> **用戶端金鑰**是重要的密碼和應該正確保護的金鑰保存庫或加密在生產環境中。

## <a name="get-the-access-token-using-postman"></a>取得使用郵差的存取權杖

本節示範如何使用**郵差**執行 REST API 會傳回 JWT Bearer 權杖 （存取權杖）。 若要呼叫任何媒體服務 REST API，您需要加入 「 授權 」 標頭的呼叫，並加入的值"承載*your_access_token*」 至每個呼叫 （如本教學課程的下一節所示）。 

1. 開啟**郵差**。
2. 選取 [POST] 。
3. 輸入 URL，其中包含您的租用戶名稱使用下列格式： 租用戶名稱應以結尾**。 onmicrosoft.com**和 URL 應以結尾**oauth2權杖/**: 

    https://login.microsoftonline.com/ {your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. 選取**標頭** 索引標籤。
5. 輸入**標頭**使用 「 索引鍵/值 」 資料格的資訊。 

    ![資料格](./media/connect-with-rest/headers-data-grid.png)

    或者，按一下**大量編輯**郵差視窗右邊的連結並貼上下列程式碼。

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. 按**主體** 索引標籤。
7. 輸入使用 （取代用戶端識別碼和密碼值） 時，「 索引鍵/值 」 資料格的主體資訊。 

    ![資料格](./media/connect-with-rest/data-grid.png)

    或者，按一下**大量編輯** 右邊的 郵差視窗和 貼上下列主體 （取代用戶端識別碼和密碼值）：

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your AAD Application}
        client_secret:{Your client secret that you got from your AAD Application's Keys}
        resource:https://rest.media.azure.net

8. 按 [傳送]。

    ![取得權杖](./media/connect-with-rest/connect-with-rest04.png)

傳回的回應包含**存取權杖**您要用來存取任何 AMS Api。

## <a name="test-the-assets-api-using-the-access-token"></a>測試**資產**應用程式開發介面使用的存取權杖

本節示範如何存取**資產**API 使用**郵差**。

1. 開啟**郵差**。
2. 選取 [GET] 。
3. 貼上 REST API 端點 (例如，https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. 選取**授權** 索引標籤。 
5. 選取**持有人權杖**。
6. 貼上一節中所建立的語彙基元。

    ![取得權杖](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > 郵差 UX 可能 Mac 與電腦之間不同。 如果 Mac 版本中並沒有"Bearer Token"選項**驗證**區段的下拉式清單中，您應該加入**授權**Mac 用戶端上手動的標頭。

   ![授權標頭](./media/connect-with-rest/auth-header.png)

7. 選取**標頭**。
5. 按一下**大量編輯**右邊郵差視窗的連結。
6. 貼上下列標頭：

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. 按 [傳送]。

傳回的回應會包含在您的帳戶中的資產。

## <a name="next-steps"></a>後續步驟

* 在此範例程式碼再試一次[以 Azure Media Services 存取的 Azure AD 驗證： 同時透過 REST API](https://github.com/willzhan/WAMSRESTSoln)
* [上傳檔案的.NET](media-services-dotnet-upload-files.md)
