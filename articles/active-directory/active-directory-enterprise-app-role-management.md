---
title: 針對 Azure Active Directory 中的企業應用程式設定 SAML 權杖中發出的角色宣告 | Microsoft Docs
description: 了解如何針對 Azure Active Directory 中的企業應用程式設定 SAML 權杖中發出的角色宣告
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 58656e2aa3b052d9bd9aa14edeb6215858d55ea4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="configuring-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>針對 Azure Active Directory 中的企業應用程式設定 SAML 權杖中發出的角色宣告

此功能可讓使用者針對使用 Azure AD 來授權應用程式時所收到回應權杖中的「角色」宣告，自訂宣告類型。

## <a name="prerequisites"></a>先決條件
- 具有目錄設定的 Azure AD 訂用帳戶
- 已啟用單一登入的訂用帳戶
- 您必須設定與您應用程式搭配運作的 SSO

## <a name="when-to-use-this-feature"></a>使用此功能的時機

如果您的應用程式預期在 SAML 回應中傳遞自訂角色，您就需要使用此功能。 此功能可讓您依需求建立多個要從 Azure AD 傳回給應用程式的角色。

## <a name="steps-to-use-this-feature"></a>使用此功能的步驟

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入應用程式的名稱，從結果面板選取您的應用程式，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的應用程式](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. 新增應用程式之後，移至 [屬性] 頁面，然後複製 [物件識別碼]

    ![屬性頁面](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. 在另一個視窗中開啟 [Azure AD Graph 總管](https://developer.microsoft.com/graph/graph-explorer)。

    a. 使用您租用戶的全域管理員/共同管理員認證來登入 [Graph 總管] 網站。

    b. 將版本變更為 [搶鮮版 (Beta)]，然後使用下列查詢，從您的租用戶擷取服務主體清單：
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    如果您使用多個目錄，則應該依照此模式 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph 總管對話方塊](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    c. 從所擷取的服務主體清單中，取得您需要修改的服務主體。 您也可以使用 Ctrl+F，從所有列出的服務主體中搜尋應用程式。 搜尋您從 [屬性] 頁面複製的**物件識別碼**，然後使用下列查詢來移至個別的「服務主體」。
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`。

    d. 從服務主體物件擷取 appRoles 屬性。

    ![Graph 總管對話方塊](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    e. 您需要立即為您的應用程式產生新角色。 您可以從[這裡](https://app.box.com/s/jw6m9p9ehmf4ut5jx8xhcw87cu09ml3y)下載「Azure AD 角色產生器」。

    f. 開啟「Azure AD 產生器」，然後執行下列步驟 -

    ![Azure AD 產生器](./media/active-directory-enterprise-app-role-management/azure_ad_role_generator.png)
    
    輸入 [角色名稱]、[角色描述] 和 [角色值]。 按一下 [新增] 來新增角色
    
    新增所有必要的角色之後，請按一下 [產生]
    
    按一下 [複製內容] 以複製內容

    > [!NOTE] 
    > 請確定您擁有 **msiam_access** 使用者角色，而且識別碼與所產生角色中的相符。

    g. 返回 [Graph 總管]。 將方法從 **GET** 變更為 **PATCH**。 藉由使用所複製的值來更新 appRoles 屬性，將服務主體物件修補成具有所需的 appRoles。 按一下 [執行查詢]。

    ![Graph 總管對話方塊](./media/active-directory-enterprise-app-role-management/graph-explorer-patch.png)

    > [!NOTE]
    > 以下是 appRoles 物件的範例。 
    ```
    {
       "appRoles": [
    {
        "allowedMemberTypes": [
        "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
        "isEnabled": true,
        "origin": "Application",
        "value": null
    },
    {
        "allowedMemberTypes": [
        "User"
        ],
        "description": "teacher",
        "displayName": "teacher",
        "id": "6478ffd2-5dbd-4584-b2ce-137390b09b60",
        "isEnabled": ,
        "origin": "ServicePrincipal",
        "value": "teacher"
    }
    ] 
    }   
    ```
7. 將服務主體修補成具有更多角色之後，我們便可將使用者指派給個別的角色。 這可藉由移至入口網站，然後瀏覽至個別的應用程式來完成。 接著，按一下最上層的 [使用者和群組] 索引標籤。 此程序將列出所有使用者或群組。

    ![設定單一登入的新增](./media/active-directory-enterprise-app-role-management/userrole.png)

    a. 若要將角色指派給任何使用者，只需選取特定的使用者/群組，然後按一下在頁面底部的 [指派] 按鈕即可。

    ![設定單一登入的新增](./media/active-directory-enterprise-app-role-management/userandgroups.png)

    b. 按一下該按鈕會出現快顯，可供您從針對個別服務主體定義的不同角色中選取角色。

    c. 選擇所需的角色，然後按一下 [提交]。

8. 將角色指派給使用者之後，我們需要更新 [屬性] 資料表，以定義**角色**宣告的自訂對應。

9. 在 [單一登入] 對話方塊的 [使用者屬性] 區段中，如圖所示設定 SAML 權杖屬性，然後執行下列步驟：
    
    | 屬性名稱 | 屬性值 |
    | -------------- | ----------------|    
    | 角色名稱      | user.assignedrole |

    a. 按一下 [新增屬性] 來開啟 [新增屬性] 對話方塊。

    ![設定單一登入的新增](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![設定單一登入屬性](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 在 [值] 清單中，選取該列所顯示的值。

    d. 讓 [命名空間] 保持空白。
    
    e. 按一下 [確定] 。

10. 若要在 IDP 起始的單一登入中測試您的應用程式，請登入「存取面板」(https://myapps.microsoft.com))，然後按一下您的應用程式圖格。 在 SAML 權杖中，您應該會看到具有您所指定宣告名稱之使用者的所有指派角色。

## <a name="update-existing-role"></a>更新現有的角色

1. 若要更新現有的角色，請執行下列步驟 -

    a. 在另一個視窗中開啟 [Azure AD Graph 總管](https://developer.microsoft.com/graph/graph-explorer)。

    b. 使用您租用戶的全域管理員/共同管理員認證來登入 [Graph 總管] 網站。
    
    c. 將版本變更為 [搶鮮版 (Beta)]，然後使用下列查詢，從您的租用戶擷取服務主體清單：
    
    `https://graph.microsoft.com/beta/servicePrincipals`
        
    如果您使用多個目錄，則應該依照此模式 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph 總管對話方塊](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. 從所擷取的服務主體清單中，取得您需要修改的服務主體。 您也可以使用 Ctrl+F，從所有列出的服務主體中搜尋應用程式。 搜尋您從 [屬性] 頁面複製的**物件識別碼**，然後使用下列查詢來移至個別的「服務主體」。
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`。
    
    e. 從服務主體物件擷取 appRoles 屬性。
    
    ![Graph 總管對話方塊](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)
    
    f. 若要更新現有的角色，請依循下列步驟：

    ![Graph 總管對話方塊](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * 將方法從 **GET** 變更為 **PATCH**。

    * 從應用程式複製現有的角色，然後貼到 [要求本文] 中。
    
    * 依照您的組織需求，藉由取代 [角色描述]、[角色值] 及 [角色顯示名稱] 來更新角色值。
    
    * 更新所有必要的角色之後，按一下 [執行查詢]。
        
## <a name="delete-existing-role"></a>刪除現有的角色

若要刪除現有的角色，請執行下列步驟：

a. 在另一個視窗中開啟 [Azure AD Graph 總管](https://developer.microsoft.com/graph/graph-explorer)。

b. 使用您租用戶的全域管理員/共同管理員認證來登入 [Graph 總管] 網站。

c. 將版本變更為 [搶鮮版 (Beta)]，然後使用下列查詢，從您的租用戶擷取服務主體清單：
    
`https://graph.microsoft.com/beta/servicePrincipals`
    
如果您使用多個目錄，則應該依照此模式 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
![Graph 總管對話方塊](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
d. 從所擷取的服務主體清單中，取得您需要修改的服務主體。 您也可以使用 Ctrl+F，從所有列出的服務主體中搜尋應用程式。 搜尋您從 [屬性] 頁面複製的**物件識別碼**，然後使用下列查詢來移至個別的「服務主體」。
     
`https://graph.microsoft.com/beta/servicePrincipals/<objectID>`。
    
e. 從服務主體物件擷取 appRoles 屬性。
    
![Graph 總管對話方塊](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

f. 若要刪除現有的角色，請依循下列步驟：

![Graph 總管對話方塊](./media/active-directory-enterprise-app-role-management/graph-explorer-patchdelete.png)

將方法從 **GET** 變更為 **PATCH**。

從應用程式複製現有的角色，然後貼到 [要求本文] 中。
    
針對您要刪除的角色，將 **IsEnabled** 值設定為 **false**

按一下 [執行查詢]。
    
> [!NOTE] 
> 請確定您擁有 **msiam_access** 使用者角色，而且識別碼與所產生角色中的相符。
    
g. 執行上述程序之後，將方法保持為 **PATCH**，並將其餘角色內容貼到 [要求本文] 中，然後按一下 [執行查詢]。
    
![Graph 總管對話方塊](./media/active-directory-enterprise-app-role-management/graph-explorer-patchfinal.png)

h. 執行查詢後將會刪除該角色。
    
> [!NOTE]
> 必須先停用角色，才可移除該角色。 

## <a name="next-steps"></a>後續步驟

如需了解其他步驟，請參閱[應用程式文件](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-tutorial-list)。

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
