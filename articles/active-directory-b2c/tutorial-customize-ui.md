---
title: 教學課程 - 在 Azure Active Directory B2C 中自訂應用程式的使用者介面 | Microsoft Docs
description: 了解如何使用 Azure 入口網站在 Azure Active Directory B2C 中自訂應用程式的使用者介面。
services: B2C
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ee6d7735a2983f642eff82a7dabe036af100e60e
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622664"
---
# <a name="tutorial-customize-the-user-interface-of-your-applications-in-azure-active-directory-b2c"></a>教學課程：在 Azure Active Directory B2C 中自訂應用程式的使用者介面

針對較常見的使用者體驗，例如註冊、登入和設定檔編輯等，您可以使用 Azure Active Directory (Azure AD) B2C 中的[內建原則](active-directory-b2c-reference-policies.md)。 本教學課程中的資訊可協助您了解如何使用自己的 HTML 和 CSS 檔案，為這些體驗[自訂使用者介面 (UI)](customize-ui-overview.md)。

在本文中，您將了解：

> [!div class="checklist"]
> * 建立 UI 自訂檔案
> * 建立將使用這些檔案的註冊與登入原則
> * 測試自訂的 UI

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

如果您尚未建立自己的 [Azure AD B2C 租用戶](tutorial-create-tenant.md)，請立即建立一個。 如果您已在先前的教學課程中建立租用戶，則可以使用現有的租用戶。

## <a name="create-customization-files"></a>建立自訂檔案

您將建立 Azure 儲存體帳戶和容器，然後將基本的 HTML 和 CSS 檔案放入容器中。

### <a name="create-a-storage-account"></a>建立儲存體帳戶

雖然您可以用很多方式來儲存檔案，但在此教學課程中，您會將這些檔案儲存在 [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)。

1. 請確定您使用的目錄中有您的 Azure 訂用帳戶。 在上方功能表中選取 [目錄和訂用帳戶篩選]，然後選擇包含您訂用帳戶的目錄。 此目錄不同於包含您 Azure B2C 租用戶的目錄。

    ![切換至訂用帳戶目錄](./media/tutorial-customize-ui/switch-directories.png)

2. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [儲存體帳戶]。 
3. 選取 [新增] 。
4. 在 [資源群組] 下，選取 [新建]，然後輸入新資源群組的名稱並按一下 [確認]。
5. 輸入儲存體帳戶的名稱。 您選擇的名稱在 Azure 中必須是唯一的、必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。
6. 選取儲存體帳戶的位置，或接受預設位置。 
7. 接受所有其他預設值，然後選取 [檢閱 + 建立]，並按一下 [建立]。
8. 建立儲存體帳戶之後，請選取 [移至資源]。

### <a name="create-a-container"></a>建立容器

1. 在儲存體帳戶的 [概觀] 頁面中，選取 [Blob]。
2. 選取 [容器]，輸入容器的名稱，然後選擇 [Blob (僅限 Blob 的匿名讀取存取)\]，並按一下 [確定]。

### <a name="enable-cors"></a>啟用 CORS

 瀏覽器中的 Azure AD B2C 程式碼會使用最新且標準的方法，從原則中的指定 URL 載入自訂內容。 跨來源資源共用 (CORS) 可允許從其他網域要求網頁上受限制的資源。

1. 在功能表中，選取 [CORS]。
2. 針對 [允許的來源]，輸入 `your-tenant-name.b2clogin.com`。 將 `your-tenant-name` 取代為您的 Azure AD B2C 租用戶名稱。 例如： `fabrikam.b2clogin.com`。
3. 針對 [允許的方法]，選取 `GET` 和 `OPTIONS`。
4. 針對 [允許的標頭]，輸入星號 (*)。
5. 針對 [公開的標頭]，輸入星號 (*)。
6. 針對 [最大壽命]，輸入 200。

    ![啟用 CORS](./media/tutorial-customize-ui/enable-cors.png)

5. 按一下 [檔案] 。

### <a name="create-the-customization-files"></a>建立自訂檔案

若要自訂註冊體驗的 UI，您可以從建立簡單的 HTML 和 CSS 檔案開始。 您可以透過任何方式設定 HTML，但其中必須有識別碼為 `api` 的 **div** 元素。 例如： `<div id="api"></div>`。 當頁面顯示時，Azure AD B2C 會將元素插入 `api` 容器。

1. 在本機資料夾中建立下列檔案，並確定您已將 `your-storage-account` 變更為儲存體帳戶名稱，以及將 `your-container` 變更為您建立的容器名稱。 例如： `https://store1.blob.core.windows.net/b2c/style.css`。

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>  
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    您可以透過任何方式設計頁面，但您建立的任何 HTML 自訂檔案都必須有 **api** div 元素。 

3. 將檔案儲存為 custom-ui.html。
4. 建立下列簡單的 CSS，以將所有項目 (包括 Azure AD B2C 插入的項目) 放到註冊或登入頁面的中間。

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center; 
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center; 
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. 將檔案儲存為 style.css。

### <a name="upload-the-customization-files"></a>上傳自訂檔案

在本教學課程中，您會將建立的檔案儲存在儲存體帳戶中，讓 Azure AD B2C 可以存取這些檔案。

1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [儲存體帳戶]。
2. 選取您建立的儲存體帳戶、選取 [Blob]，然後選取您建立的容器。
3. 選取 [上傳]，瀏覽至 [custom-ui.html] 檔案並加以選取，然後再按一下 [上傳]。

    ![上傳自訂檔案](./media/tutorial-customize-ui/upload-blob.png)

4. 複製已上傳檔案的 URL，以便稍後在教學課程中使用。
5. 對 style.css 檔案重複步驟 3 和 4。

## <a name="create-a-sign-up-and-sign-in-policy"></a>建立註冊與登入原則

若要完成本教學課程中的步驟，您需要在 Azure AD B2C 中建立測試應用程式及註冊或登入原則。 您可以將此教學課程中所述的原則套用到其他使用者體驗，例如設定檔編輯。

### <a name="create-an-azure-ad-b2c-application"></a>建立 Azure AD B2C 應用程式

與 Azure AD B2C 的通訊會透過您在租用戶中建立的應用程式進行。 下列步驟會建立應用程式，此應用程式會將傳回到 [https://jwt.ms](https://jwt.ms) 的授權權杖重新導向。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [應用程式]，然後選取 [新增]。
5. 輸入應用程式的名稱，例如 testapp1。
6. 針對 [Web 應用程式 / Web API] ，選取 `Yes`，然後y在 [回覆 URL] 欄位輸入 `https://jwt.ms`。
7. 按一下頁面底部的 [新增] 。

### <a name="create-the-policy"></a>建立原則

若要測試您的自訂檔案，您可以建立內建的註冊或登入原則，來使用您先前建立的應用程式。

1. 在 Azure AD B2C 租用戶中，選取 [註冊或登入原則]，然後按一下 [新增]。
2. 輸入原則的名稱。 例如：signup_signin。 建立原則時，前置詞 B2C_1 會自動加到名稱中。
3. 選取 [識別提供者]，為本機帳戶設定 [電子郵件註冊]，然後按一下 [確定]。
4. 選取 [註冊屬性]，選擇註冊期間要向客戶收集的屬性。 例如，設定 [國家/區域]、[顯示名稱] 和 [郵遞區號]，然後按一下 [確定]。
5. 選取 [應用程式宣告]，選擇成功註冊或登入後，您要在授權權杖中傳回給應用程式的宣告。 例如，選取 [顯示名稱]、[識別提供者]、[郵遞區號]、[使用者是新的] 和 [使用者的物件識別碼]，然後按一下 [確定]。
6. 選取 [頁面 UI 自訂]、選取 [統一註冊或登入頁面]，然後針對 [使用自訂頁面] 按一下 [是]。
7. 在 [自訂頁面 URI] 中，輸入您先前記下的 custom-ui.html 檔案 URL，然後按一下 [確定]。
8. 按一下頁面底部的 [新增] 。

## <a name="test-the-policy"></a>測試原則

1. 在 Azure AD B2C 租用戶中，選取 [註冊或登入原則]，然後選取您建立的原則。 例如：B2C_1_signup_signin。
2. 請確定 [選取應用程式] 中已選取您建立的應用程式，然後按一下 [立即執行]。

    ![執行註冊或登入原則](./media/tutorial-customize-ui/signup-signin.png)

    您應該會看到類似下列範例的頁面，而頁面上的置中元素會以您所建立的 CSS 檔案為基礎：

    ![原則結果](./media/tutorial-customize-ui/run-now.png) 

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 建立 UI 自訂檔案
> * 建立將使用這些檔案的註冊與登入原則
> * 測試自訂的 UI

> [!div class="nextstepaction"]
> [Azure Active Directory B2C 中的語言自訂](active-directory-b2c-reference-language-customization.md)