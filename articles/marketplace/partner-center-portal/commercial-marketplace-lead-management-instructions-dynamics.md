---
title: Dynamics 365 for Customer Engagement 的潛在客戶管理 |Azure Marketplace
description: 針對 Customer Engagement 設定 Dynamics 365 的潛在客戶管理。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 17ae6dc8d1d583e5223a64ee079731fa5d9dc63c
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902308"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>設定 Dynamics 365 for Customer Engagement 的潛在客戶管理

本文說明如何設定 Dynamics 365 for Customer Engagement (先前為 Dynamics CRM Online), 請參閱[這裡](https://docs.microsoft.com/dynamics365/customer-engagement/admin/switch-dynamics-crm-online-dynamics-365)的變更, 以處理來自 Marketplace 供應專案的銷售潛在客戶。 

>[!Note]
>這些指示適用于 Microsoft hosted cloud Dynamics 365 for Customer Engagement 環境。 目前不支援直接連接到 Dynamics 內部內部部署環境, 還有其他選項可供您接收潛在客戶, 例如設定[HTTPs 端點](./commercial-marketplace-lead-management-instructions-https.md)或[Azure 資料表](./commercial-marketplace-lead-management-instructions-azure-table.md)來接收潛在客戶。

## <a name="prerequisites"></a>必要條件

若要完成本文中的步驟, 需要下列使用者權限:

* 您必須是 Dynamics 365 for Customer Engagement 實例的系統管理員, 才能夠安裝解決方案並遵循這些指示。
* 您必須是租使用者系統管理員, 才能為潛在客戶服務建立新的服務帳戶, 以用來傳送 marketplace 供應專案的潛在客戶。
* 您必須具有 Office 365 管理員入口網站的存取權。
* 您必須具有 Azure 入口網站的存取權。

## <a name="install-the-solution"></a>安裝解決方案

1.  下載[Microsoft Marketplace 潛在客戶撰寫者解決方案](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip), 並將它儲存在本機電腦上。

2.  流覽至 Dynamics 實例的 URL (例如`https://tenant.crm.dynamics.com`), 以開啟 Dynamics 365 for Customer Engagement。

3.  選取頂端導覽列上的齒輪圖示和 [**高級設定**] 來存取設定。
 
    ![Dynamics-Advanced 設定](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  在 [設定] 頁面上, 存取頂端導覽列的 [設定] 功能表, 然後選取 [**方案**]。

    >[!Note]
    >如果您在下一個螢幕擷取畫面中看不到這些選項, 表示您沒有需要繼續進行的許可權。 向外與 Dynamics 365 for Customer Engagement 實例的系統管理員聯繫。

    ![Dynamics 365-解決方案](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. 在 [方案] 頁面上, 選取 [匯**入**], 然後流覽至您在步驟1中所下載*Microsoft Marketplace 潛在客戶寫入器*解決方案的儲存位置。

    ![Dynamics 365 for Customer Engagement-匯入](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. 遵循 [匯入解決方案嚮導], 完成匯入解決方案的工作。

## <a name="configure-user-permissions"></a>設定使用者權限

若要將潛在客戶寫入 Dynamics 365 for Customer Engagement 實例, 您必須與我們共用服務帳戶, 並設定帳戶的許可權。

使用下列步驟建立服務帳戶，並指派權限。 您可以使用 **Azure Active Directory** 或 **Office 365**。

>[!Note]
>根據您選取的驗證選項, 您可以根據您的選擇跳至對應的指示。 請參閱[Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory)或[Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365)。

### <a name="azure-active-directory"></a>Azure Active Directory

我們建議這個選項, 因為您可以獲得額外的好處, 也就是您永遠不需要更新使用者名稱/密碼, 就能繼續獲得潛在客戶。 若要使用 [Azure Active Directory] 選項, 您可以從 Active Directory 應用程式提供應用程式識別碼、應用程式金鑰和目錄識別碼。

使用下列步驟來設定 Dynamics 365 for Customer Engagement 的 Azure Active Directory。

1. 登入[Azure 入口網站](https://portal.azure.com/), 然後從左側導覽中選取 [Azure Active Directory 服務]。

2. 從 Azure Active Directory 左側導覽中選取 [**屬性**], 然後複製該頁面上的 [**目錄識別碼**] 值。 儲存此值, 因為它是您需要在發佈入口網站中提供的*目錄識別碼*值, 以接收 marketplace 供應專案的潛在客戶。

    ![Azure Active Directory-屬性](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. 從 Azure Active Directory 左側導覽中選取 [**應用程式註冊**], 然後在該頁面上選取 [**新增註冊**]。
4. 輸入應用程式名稱的名稱。 提供有意義的應用程式名稱。
5. 在 [支援的帳戶類型] 底下, 選取 [**任何組織目錄中的帳戶**]。
6. 在 [重新導向 URI] 底下, 選取 [ **Web** ] 並`https://contosoapp1/auth`提供 URI (例如)。 
7. 選取 [註冊]。

    ![註冊應用程式](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. 現在, 您的應用程式已註冊, 請存取應用程式的 [總覽] 頁面, 並複製該頁面上的 [**應用程式 (用戶端) 識別碼**] 值。 儲存此值, 因為它是您需要在發佈入口網站中提供的*應用程式 (用戶端) 識別碼*值, 而在 Dynamics 中會收到您 marketplace 供應專案的潛在客戶。

    ![應用程式 (用戶端) 識別碼](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. 從應用程式的左側導覽中選取 [**憑證和秘密**], 然後在該頁面上選取 [**新增用戶端密碼**]。 為用戶端密碼輸入有意義的描述, 然後選取 [到期] 底下的 [**永不**] 選項。 選取 [**新增**] 以建立用戶端密碼。

    ![應用程式-認證和秘密](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. 一旦成功建立用戶端密碼, 請**複製用戶端密碼值**。 離開頁面之後, 您將無法取出此值。 儲存此值, 因為它是您需要在發佈入口網站中提供的*用戶端密碼*值, 以接收 marketplace 供應專案的潛在客戶。 
11. 從 [應用程式] 左側導覽選取 [ **API 許可權**], 然後選取 [**新增許可權**]。
12. 選取 [Microsoft Api], 然後選取 [ **DYNAMICS CRM** ] 作為 API。
13. 在*您的應用程式所需的許可權類型*之下, 請確定已選取 [**委派的許可權**]。 *以組織使用者*的身分, 檢查**user_impersonation** Access Common Data Service 的許可權。 選取 [**新增許可權**]。

    ![加入權限](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. 完成 Azure 入口網站上的步驟1-13 之後, 流覽至您的 Dynamics 365 for Customer Engagement 實例, 方法是流覽至 URL ( https://tenant.crm.dynamics.com) 例如)。
15. 在上方導覽列上選取齒輪圖示和 [**高級設定**] 來存取設定。
16. 在 [設定] 頁面上, 存取頂端導覽列的 [設定] 功能表, 然後選取 [**安全性**]。
17. 在 [安全性] 頁面上, 選取 [**使用者**]。  在 [使用者] 頁面上, 選取 [已啟用的使用者] 下拉式清單, 以切換至 [**應用程式使用者**]。
18. 選取 [新增] 以建立新的使用者。 

    ![建立新的使用者](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. 在 [**新使用者**] 中, 確認使用者:已選取應用程式使用者。 針對您要與此連線搭配使用的使用者, 提供使用者名稱、全名和電子郵件地址。 此外, 請貼上您在步驟8的 Azure 入口網站中所建立應用程式的**應用程式識別碼**。 選取 [**儲存後關閉**] 以完成新增使用者。

    ![新增使用者](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. 移至此文章中的「安全性設定」以完成設定此使用者的連線。

### <a name="office-365"></a>Office 365

如果您不想要使用 Azure Active Directory, 您可以在*Microsoft 365 系統管理中心*註冊新的使用者。 您需要每 90 天更新一次使用者名稱/密碼，以繼續獲得潛在客戶。

使用下列步驟, 將 Office 365 for Dynamics 365 設定為可供客戶參與。

1. 登入 Microsoft 365 系統[管理中心](https://admin.microsoft.com)。

2. 選取 [新增使用者]。

    ![Microsoft 365 系統管理中心-新增使用者](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. 為潛在客戶寫入器服務建立新使用者。 設定下列設定：

    * 提供使用者名稱
    * 提供密碼, 然後取消選取 [讓此使用者在第一次登入時變更密碼] 選項。
    * 選取 [使用者 (沒有系統管理員存取權)] 作為使用者的角色。
    * 選取 [Dynamics 365 Customer Engagement 方案] 做為下一個螢幕擷取畫面中所示的 [產品授權]。 您將需要支付您所選擇的授權。 

儲存這些值, 因為它們是您需要在發佈入口網站中提供的使用者*名稱和密碼*值, 以接收 marketplace 供應專案的潛在客戶。

![Microsoft 365 系統管理中心-新使用者](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>安全性設定

最後一個步驟是讓您所建立的 [使用者] 寫入潛在客戶。

1. 流覽至 Dynamics 實例的 URL (例如`https://tenant.crm.dynamics.com`), 以開啟 Dynamics 365 for Customer Engagement。
2. 選取頂端導覽列上的齒輪圖示和 [**高級設定**] 來存取設定。
3. 在 [設定] 頁面上, 存取頂端導覽列的 [設定] 功能表, 然後選取 [**安全性**]。
4. 在 [安全性] 頁面上, 選取 [**使用者**], 然後選取您在本檔的 [設定使用者權限] 區段中建立的使用者, 然後選取 [**管理角色**]。 

    ![管理角色](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. 搜尋角色名稱「Microsoft Marketplace 潛在客戶寫入器」並加以選取, 以將角色指派給使用者。

    ![管理使用者角色](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >此角色由您匯入的解決方案所建立，僅擁有寫入潛在客戶，以及追蹤解決方案版本以確保相容性的權限。

6. 流覽回 [安全性] 頁面, 然後選取 [**安全性角色**]。 搜尋角色「Microsoft Marketplace 潛在客戶寫入器」並加以選取。

    ![安全性角色](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. 一旦進入安全性角色, 請選取 [**核心記錄**] 索引標籤。搜尋 [使用者實體 UI 設定] 實體, 並在每個對應的圓形中按一下一次, 即可為該實體啟用 [建立]、[讀取] 和 [寫入] 許可權給使用者 (1/4 黃色圓形)。

    ![Microsoft Marketplace 潛在客戶寫入器核心記錄](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. 現在, 流覽至 [**自訂**] 索引標籤。搜尋 tor 「系統作業」實體, 並在每個對應的圓形中按一下四次, 即可為該實體的組織 (綠色) 提供讀取、寫入和 AppendTo 許可權。

    ![Microsoft Marketplace 潛在客戶撰寫者-自訂](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **儲存並關閉**。

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>設定您的供應專案, 以將潛在客戶傳送至 Dynamics 365

當您準備好在發佈入口網站中為您的供應專案設定潛在客戶管理資訊時, 請遵循下列步驟:

1. 流覽至供應專案的 [**供應專案設定**] 頁面。
2. 選取 [潛在客戶管理] 區段下的 **[連接]** 。

    ![連接到潛在客戶管理](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. 在 [連線詳細資料] 快顯視窗中, 針對 [潛在客戶目的地] 選取 [ **Dynamics 365 For Customer Engagement** ]

    ![連接詳細資料-潛在客戶目的地](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. 提供**Dynamics 365 實例 URL** , 例如`https://contoso.crm4.dynamics.com`。
5. 選取**驗證**、Azure Active Directory 或 Office 365 的方法。 
6. 如果您選取 [Azure Active Directory], 請提供**應用程式 (用戶端) 識別碼**(範例: `23456052-aaaa-bbbb-8662-1234df56788f`)、**目錄識別碼**(範例`12345678-8af1-4asf-1234-12234d01db47`:) 和**用戶端密碼**(範例: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`)。

    ![連接詳細資料-Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. 如果您選取了 Office 365, 請提供**使用者名稱**(例如`contoso@contoso.onmicrosoft.com`:) 和密碼 (例如: `P@ssw0rd`)。

    ![連接詳細資料-使用者名稱](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>您必須先完成供應專案的其餘部分設定並加以發佈, 才能接收供應專案的潛在客戶。
