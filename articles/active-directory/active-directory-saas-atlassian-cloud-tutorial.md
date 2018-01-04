---
title: "教學課程：Azure Active Directory 與 Atlassian Cloud 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Atlassian Cloud 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: jeedes
ms.openlocfilehash: db9e9c7ae8380612bac9d0aeaaaf6df78cba523f
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>教學課程：Azure Active Directory 與 Atlassian Cloud 整合

在本教學課程中，您會了解如何整合 Atlassian Cloud 與 Azure Active Directory (Azure AD)。

Atlassian Cloud 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Atlassian Cloud 的人員。
- 您可以啟用自動登入您的使用者 （單一登入） Atlassian 受雲端其 Azure AD 帳戶。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

使用 Azure AD 服務 (SaaS) 應用程式整合軟體的相關資訊，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Atlassian Cloud 整合，您需要下列項目：

- Azure AD 訂用帳戶。
- 若要啟用安全性聲明標記語言 (SAML) 單一的登入 Atlassian 雲端產品，您需要設定 Identity Manager。 深入了解[Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager)。

> [!NOTE]
> 當您測試步驟，在本教學課程時，我們建議您不要使用實際執行環境。

若要測試本教學課程中的步驟，請遵循下列建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 教學課程中說明的情節由二個主要建置組塊組成：

* 從資源庫新增 Atlassian Cloud
* 設定並測試 Azure AD 單一登入

## <a name="add-atlassian-cloud-from-the-gallery"></a>從資源庫新增 Atlassian Cloud
若要設定的 Atlassian 雲端整合與 Azure AD，將 Atlassian 雲端從資源庫加入清單中的受管理的 SaaS 應用程式執行下列動作：

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory] 按鈕。 

    ![Azure Active Directory 按鈕][1]

2. 選取 [企業應用程式] > [所有應用程式]。

    ![[企業應用程式] 窗格][2]
    
3. 若要新增應用程式，請選取**新的應用程式**。

    ![[新增應用程式] 按鈕][3]

4. 在 [搜尋] 方塊中，輸入**Atlassian 雲端**，在 [結果] 清單中，選取**Atlassian 雲端**，然後選取**新增**。

    ![結果清單中的 [Atlassian Cloud]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，設定並測試 Azure AD 單一登入與名為測試使用者為基礎的 Atlassian 雲端*許 Simon*。

單一登入工作，Azure AD 需要在 Azure AD 中識別 Atlassian 雲端使用者和其對應項目。 換句話說，您必須建立 Azure AD 使用者和相關的使用者之間的連結關聯性 Atlassian 雲端中。

若要建立的連結關聯性，將指派為 Atlassian 雲端*Username*相同的值指派給 Azure AD*使用者名*。

若要設定和測試 Azure AD 單一登入與 Atlassian 雲端，您需要完成下列各節中的建置組塊。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Atlassian Cloud 應用程式中設定單一登入。

若要以 Atlassian 雲端設定 Azure AD 單一登入，執行下列作業：

1. 在 Azure 入口網站中**Atlassian 雲端**應用程式整合窗格中，選取**單一登入**。

    ![設定單一登入連結][4]

2. 在**單一登入**視窗，請在**單一登入模式**方塊中，選取**SAML 型登入**。
 
    ![單一登入視窗](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. 在設定應用程式在 IDP 初始化模式中， **Atlassian 雲端網域和 Url**，執行下列動作：

    ![Atlassian 雲端網域和 Url 的單一登入資訊](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. 在**識別碼**方塊中，輸入 **`https://auth.atlassian.com/saml/<unique ID>`** 。
    
    b. 在**回覆 URL**方塊中，輸入 **`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`** 。

    c. 在**轉送狀態**方塊中，輸入的 URL 使用下列語法：  **`https://<instancename>.atlassian.net`** 。

4. 若要設定應用程式在 SP 初始模式中，選取**顯示進階的 URL 設定**，然後在**登入 URL**方塊中，輸入的 URL 使用下列語法：  **`https://<instancename>.atlassian.net`** .

    ![Atlassian 雲端網域和 Url 的單一登入資訊](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE] 
    > 上述值並非真正的值。 實際的識別項，用來更新這些回覆 URL 和登入 URL 值。 您可以從 [Atlassian 雲端 SAML 組態] 畫面，以取得實際的值。 我們稍後在本教學課程中說明的值。

5. 在下**SAML 簽章憑證**，選取**Certificate(Base64)**，然後儲存您的電腦上的憑證檔案。

    ![憑證下載連結](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. Atlassian 雲端應用程式預期找到 SAML 判斷提示，以特定格式，會要求您新增自訂屬性對應到 SAML 權杖屬性組態。 

    根據預設，**使用者識別碼**該值會對應至 user.userprincipalname。 變更此值對應至 user.mail。 您也可以選擇任何其他適當的值根據組織的安裝程式，但在許多情況下，應該使用電子郵件。

    ![憑證下載連結](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png) 

7. 選取 [ **儲存**]。

    ![設定單一登入儲存按鈕](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

8. 若要開啟**設定登入**視窗，請在**Atlassian 雲端組態**區段中，選取**設定 Atlassian 雲端**。 

9. 在**快速參考**區段中，複製**SAML 實體識別碼**和**SAML 單一登入服務 URL**。 

    ![Atlassian 雲端組態](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

10. 若要取得設定應用程式的 SSO，Atlassian 入口網站，以系統管理員認證登入。

11. 移至**Atlassian 網站管理** > **組織與安全性**。 如果您尚未這樣做，請建立並命名您的組織，然後在左窗格中，選取**網域**。

    ![設定單一登入](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

12. 選取您想要驗證您的網域的方式： **DNS**或**HTTPS**。

    ![設定單一登入](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

13. 為 DNS 驗證，請在**網域**視窗中，選取**DNS**索引標籤，然後再執行下列動作：

    ![設定單一登入](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    a. 若要複製的值為您的文字記錄 （TXT 記錄），請選取**複製**。

    b. 若要新增一筆記錄，請在 DNS 中的 [設定] 頁面。

    c. 選取選項可新增新的記錄，然後再貼上您在複製的值**網域**視窗**值**欄位。 您的 DNS 記錄也可能會將它稱為參考**回應**或**描述**。

    d. 您的 DNS 記錄也可能包括下列欄位：
    
    * 在**記錄類型**方塊中，輸入**TXT**。
    * 在**名稱/主機別名**方塊中，保留預設值 （@ 或空白）。
    * 在**存留時間 (TTL)**方塊中，輸入**86400**。
    
    e.  儲存記錄。

14. 返回**網域** 視窗中的組織管理，然後選取**驗證網域**。 在**網域**方塊，輸入您的網域名稱，然後選取**驗證網域**。

    ![設定單一登入](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)  

    > [!NOTE]
    > 因為可能需要 72 小時才會生效，TXT 記錄變更，您將不會立即知道您的網域驗證是否成功。 若要檢視您的驗證狀態，請檢查**網域**在您完成此程序後的視窗。 更新的狀態將會顯示為*Verified*，如下列影像所示：
    > 
    > ![設定單一登入](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)
    > 
    > 

15. 為 HTTPS 驗證，請在**網域**視窗中，選取**HTTPS**索引標籤，然後再執行下列動作：

    ![設定單一登入](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    a. 若要下載的 HTML 檔案，請選取**下載檔案**。

    b. 將 HTML 檔案上傳至網域的根目錄。

16. 返回**網域**在組織內的頁面，然後選取**驗證網域**。 在**驗證網域**視窗，請在**網域**方塊中，輸入您**網域名稱**，然後選取**驗證網域**。

    ![設定單一登入](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

17. 如果驗證程序可以找到您上傳在根目錄的檔案，網域的狀態已更新為*Verified*，如下所示：

    ![設定單一登入](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > 如需詳細資訊，請參閱[Atlassian 網域驗證](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)。

18. 在左窗格中，選取**SAML 單一登入**。 如果您尚未這樣做，請訂閱到 Atlassian 身分識別管理員。

    ![設定單一登入](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

19. 在**新增 SAML 組態**視窗中，執行下列動作：

    ![設定單一登入](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. 在**身分識別提供者實體識別碼**方塊中，貼上您從 Azure 入口網站複製的 SAML 實體識別碼。

    b. 在**身分識別提供者 SSO URL**方塊中，貼上您從 Azure 入口網站複製的 SAML 單一登入服務 URL。

    c. 開啟下載的憑證，從 Azure 入口網站，在.txt 檔案中，將值複製 (不含*開始憑證*和*終端憑證*行)，然後貼在**公用 X509憑證**方塊。
    
    d. 選取**儲存組態**。
     
20. 若要確保您已設定正確的 Url，更新的 Azure AD 設定執行下列動作：
  
    ![設定單一登入](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. 在 [SAML] 視窗中，複製**預存程序識別 ID** ，然後在 Azure 入口網站，Atlassian 雲端下**網域和 Url**中, 貼上**識別碼**方塊。
    
    b. 在 [SAML] 視窗中，複製**預存程序判斷提示取用者服務 URL** ，然後在 Azure 入口網站，Atlassian 雲端下**網域和 Url**中, 貼上**回覆 URL**方塊。  
        登入 URL 是 Atlassian 雲端的租用戶 URL。 

    > [!NOTE]
    > 如果您是現有客戶，在更新後**預存程序識別 ID**和**預存程序判斷提示取用者服務 URL**值在 Azure 入口網站中，選取**[是]，更新組態**. 如果您是新客戶，您可以略過此步驟。 
    
21. 在 Azure 入口網站中，選取**儲存**。

    ![設定單一登入](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> 當您設定的應用程式，您可以閱讀的前述指示中的精簡版本[Azure 入口網站](https://portal.azure.com)。 您將從這個應用程式之後**Active Directory** > **企業應用程式**區段中，選取**單一登入**索引標籤，然後再存取 內嵌中的文件**組態**視窗的底部區段。 如需詳細資訊，請參閱 [Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會執行下列步驟，在 Azure 入口網站中建立測試使用者 Britta Simon：

   ![建立 Azure AD 測試使用者][100]

1. 在 **Azure 入口網站**的左側窗格中，選取 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. 若要顯示使用者清單，請選取 [使用者和群組] > [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. 在**所有使用者**視窗中，選取**新增**。

    ![[新增] 按鈕](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. 在**使用者**視窗中，執行下列動作：

    ![[使用者] 視窗](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [建立] 。
  
### <a name="create-an-atlassian-cloud-test-user"></a>建立 Atlassian Cloud 測試使用者

若要讓 Azure AD 使用者能夠登入 Atlassian 雲端，使用者帳戶佈建 Atlassian 雲端中手動執行下列動作：

1. 在**管理**窗格中，選取**使用者**。

    ![Atlassian 雲端使用者連結](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. 若要建立使用者 Atlassian 雲端中，選取**邀請使用者**。

    ![建立 Atlassian 雲端使用者](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. 在**電子郵件地址**方塊中輸入使用者的電子郵件地址，然後再指派應用程式的存取。 

    ![建立 Atlassian 雲端使用者](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. 若要傳送給使用者的電子郵件邀請，請選取**邀請使用者**。  
    電子郵件邀請傳送給使用者，並接受邀請之後, 使用者是系統中作用中。 

>[!NOTE] 
>您也可以大量-選取來建立使用者**大量建立**按鈕**使用者**> 一節。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，以啟用使用者使用 Azure 單一登入授與存取權 Atlassian 雲端許 Simon。 若要這樣做，請執行下列動作：

![指派使用者角色][200] 

1. 在 Azure 入口網站中，開啟**應用程式**檢視，請移至 [目錄] 檢視，然後選取**企業應用程式** > **所有應用程式**。

    ![指派使用者][201] 

2. 在**應用程式**清單中，選取**Atlassian 雲端**。

    ![應用程式清單中的 [Atlassian Cloud] 連結](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. 在左側窗格中，選取 [使用者和群組]。

    ![[使用者和群組] 連結][202]

4. 選取 [新增]，然後在 [新增指派] 窗格中，選取 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在**使用者和群組**視窗，請在**使用者**清單中，選取**許 Simon**。

6. 在**使用者和群組**視窗中，選取**選取**。

7. 在**將作業加入**視窗中，選取**指派**。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

當您選取**Atlassian 雲端**磚在存取面板中，您應該登入，會自動 Atlassian 雲端應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png

