---
title: 教學課程︰以 Azure Active Directory 設定 Workday 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 將使用者帳戶自動佈建及取消佈建至 Workday。
services: active-directory
author: cmmdesai
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/18/2018
ms.author: chmutali
ms.openlocfilehash: 30354ddb010c22dabe5cd69373ae59daaf4a8b46
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2018
ms.locfileid: "51346740"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning-preview"></a>教學課程︰設定 Workday 來自動佈建使用者 (預覽)

本教學課程的目標是說明將人員設定檔從 Workday 匯入 Active Directory 和 Azure Active Directory，以及將電子郵件地址選擇性回寫至 Workday 需要執行的步驟。

## <a name="overview"></a>概觀

[Azure Active Directory 使用者佈建服務](../manage-apps/user-provisioning.md)與 [Workday Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 整合以佈建使用者帳戶。 Azure AD 使用此連接啟用下列使用者佈建工作流程：

* **將使用者佈建至 Active Directory** - 將選取的使用者集合從 Workday 同步至一或多個 Active Directory 網域。

* **將僅限雲端使用者佈建至 Azure Active Directory** - 在未使用內部部署 Active Directory 的情況下，使用 Azure AD 使用者佈建服務可以直接將使用者從 Workday 佈建至 Azure Active Directory。 

* **將電子郵件地址回寫至 Workday** - Azure AD 使用者佈建服務可以將 Azure AD 使用者電子郵件地址回寫至 Workday。 

### <a name="what-human-resources-scenarios-does-it-cover"></a>它涵蓋了哪些人力資源案例？

Azure AD 使用者佈建服務支援的 Workday 使用者佈建工作流程，可讓下列人力資源和身分識別生命週期管理案例的自動化：

* **雇用新員工** - 將新員工新增至 Workday 時，系統會在 Active Directory、Azure Active Directory、Office 365 (選擇性) 和 [Azure AD 支援的其他 SaaS 應用程式](../manage-apps/user-provisioning.md)中自動建立使用者帳戶，並將電子郵件地址寫回 Workday。

* **員工屬性和設定檔更新** - 在 Workday 中更新員工記錄時 (例如姓名、職稱或經理)，系統會在 Active Directory、Azure Active Directory、Office 365 (選擇性) 和 [Azure AD 支援的其他 SaaS 應用程式](../manage-apps/user-provisioning.md)中自動更新其使用者帳戶。

* **員工離職** - 在 Workday 中將員工設定為離職時，系統會在 Active Directory、Azure Active Directory、Office 365 (選擇性) 和 [Azure AD 支援的其他 SaaS 應用程式](../manage-apps/user-provisioning.md)中自動停用其使用者帳戶。

* **重新雇用員工** - 在 Workday 中重新雇用員工時，系統會自動重新啟用其舊帳戶或將其重新佈建 (取決於您的喜好設定) 至 Active Directory、Azure Active Directory、Office 365 (選擇性) 和 [Azure AD 支援的其他 SaaS 應用程式](../manage-apps/user-provisioning.md)。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>誰最適合使用此使用者佈建解決方案？

這個新的 Workday 的使用者佈建解決方案目前處於公開預覽狀態，最適合用於：

* 需要以預先建置的雲端式解決方案進行 Workday 使用者佈建的組織

* 需要直接將使用者從 Workday 佈建到 Active Directory 或 Azure Active Directory 的組織

* 需要使用從 Workday HCM 模組取得的資料來佈建使用者的組織 (請參閱 [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* 需要僅根據在 Workday HCM 模組中偵測到的變更資訊聯結、移動及保留使用者，使其同步至一或多個 Active Directory 樹系、網域和 OU 的組織 (請參閱 [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* 使用 Office 365 處理電子郵件的組織

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="solution-architecture"></a>方案架構

本節針對常見的混合式環境，說明端對端使用者佈建方案架構。 有兩個相關的流程：

* **授權 HR 資料流程 – 從 Workday 到內部部署 Active Directory：** 在此流程中，人員事件 (例如新雇用、調動、解雇) 會先發生在雲端 Workday HR 租用戶，然後事件資料會透過 Azure AD 和佈建代理程式流入內部部署 Active Directory。 視事件而定，它可能會在 AD 中產生建立/更新/啟用/停用作業。
* **電子郵件回寫流程 – 從內部部署 Active Directory 到 Workday：** 一旦在 Active Directory 中完成建立帳號之後，它就會透過 Azure AD Connect 與 Azure AD 同步處理，而來自 Active Directory 的電子郵件屬性可回寫到 Workday。

![概觀](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>端對端使用者資料流程

1. HR 小組在 Workday HCM 中執行人員異動 (新進人員/異動人員/離職人員或新雇用/異動/解雇)
2. Azure AD 佈建服務會執行排程好的 Workday HR 身分識別同步處理作業，並找出需要處理以便與內部部署 Active Directory 同步的變更。
3. Azure AD 佈建服務會使用包含 AD 帳戶建立/更新/啟用/停用作業的要求承載，叫用內部部署 AAD Connect 佈建代理程式。
4. Azure AD Connect 佈建代理程式使用服務帳戶來新增/更新 AD 帳戶資料。
5. Azure AD Connect / AD 同步引擎會執行差異同步來提取 AD 中的更新。
6. Active Directory 會與 Azure Active Directory 同步更新。
7. 如果已設定 Workday 回寫連接器，它會將電子郵件屬性回寫到 Workday (根據所使用的比對屬性)。


## <a name="planning-your-deployment"></a>規劃您的部署

開始進行 Workday 整合之前，請檢查下列必要條件，並閱讀下列有關如何讓目前的 Active Directory 結構和使用者佈建需求與 Azure Active Directory 提供解決方案相符的指導。

### <a name="prerequisites"></a>必要條件

本教學課程中說明的案例假設您已經具有下列項目：

* 具有全域系統管理員存取權的有效 Azure AD Premium P1 訂用帳戶
* 可供測試和整合之用的 Workday 實作租用戶
* 可供測試之用的 Workday 系統管理員權限，以建立系統整合使用者和進行變更以測試員工資料
* 針對佈建至 Active Directory 的使用者，需要執行 Windows Server 2012 或更新版本且含有 .NET 4.7+ 執行階段的伺服器，才能裝載[內部部署佈建代理程式](https://go.microsoft.com/fwlink/?linkid=847801)
* 可在 Active Directory 與 Azure AD 之間同步處理的 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md)

### <a name="planning-considerations"></a>規劃考量

Azure AD 提供一組豐富的佈建連接器，協助您解決從 Workday 到 Active Directory、Azure AD、SaaS 應用程式等產品的佈建和身分識別生命週期管理。 您將使用的功能和設定解決方案的方法將會視組織環境和需求而有所不同。 您的第一步便是評估組織中存在且已部署下列項目的數量：

* 您正在使用多少 Active Directory 樹系？
* 您正在使用多少 Active Directory 網域？
* 有多少 Active Directory 組織單位 (OU) 正在使用？
* 有多少 Azure Active Directory 租用戶正在使用？
* 是否需要將任何使用者佈建至 Active Directory 和 Azure Active Directory (例如「混合式」使用者)？
* 是否需要將任何使用者佈建至 Azure Active Directory，但不需要將其佈建至 Active Directory (例如「僅限雲端」使用者)？
* 是否需要將使用者電子郵件地址寫回至 Workday？

找到這些問題的答案之後，您便可以依照下列指導來計劃 Workday 佈建部署。

#### <a name="planning-deployment-of-aad-connect-provisioning-agent"></a>AAD Connect 佈建代理程式的部署規劃

「Workday 至 AD 使用者佈建」方案必須在執行 Windows 2012 R2 或更新版本的伺服器 (其具有最低 4GB RAM 和 .NET 4.7+ 執行階段) 部署一或多個佈建代理程式。 在安裝佈建代理程式之前，您必須將下列事項列入考量：

* 確定執行佈建代理程式的主機伺服器具有目標 AD 網域的網路存取權限
* 佈建代理程式設定精靈會向 Azure AD 租用戶註冊代理程式，且註冊程序需要使用連接埠 8082 存取 *.msappproxy.net。 確定輸出防火牆規則已備妥可使用此通訊。
* 佈建代理程式使用服務帳戶與內部部署 AD 網域通訊。 在安裝代理程式之前，建議您先建立具有「使用者」屬性、讀取/寫入權限且密碼尚未過期的服務帳戶。  
* 在佈建代理程式設定期間，您可以選取應處理佈建要求的網域控制站。 如果您有數個分布在不同地理位置的網域控制站，在與您慣用之網域控制站的相同網站中安裝佈建代理程式，可改善端對端方案的可靠性和效能
* 如需高可用性，您可以部署多個佈建代理程式並加以註冊，來處理相同的內部部署 AD 網域集合。

> [!IMPORTANT]
> 在生產環境中，Microsoft 建議 Azure AD 租用戶設定至少 3 個佈建代理程式以實現高可用性。

#### <a name="selecting-provisioning-connector-apps-to-deploy"></a>選取要部署的佈建連接器應用程式

將 Workday 與 Active Directory 整合時，需要考慮多個來源和目標系統：

| 來源系統 | 目標系統 | 注意 |
| ---------- | ---------- | ---------- |
| Workday | Active Directory 網域 | 系統會將每個網域視為相異目標系統 |
| Workday | Azure AD 租用戶 | 根據僅限雲端使用者的要求 |
| Active Directory 樹系 | Azure AD 租用戶 | 目前由 AAD Connect 處理此流程 |
| Azure AD 租用戶 | Workday | 適用於電子郵件地址的回寫 |

為了加速 Workday 和 Active Directory 之間的佈建工作流程，Azure AD 提供可以從 Azure AD 應用程式庫新增的多個佈建連接器應用程式：

![AAD 應用程式庫](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday 至 Active Directory 佈建** - 此應用程式可協助將使用者帳戶從 Workday 佈建至單一 Active Directory 網域。 如果您擁有多個網域，則可以針對要進行佈建的每個 Active Directory 網域，從 Azure AD 應用程式庫新增此應用程式的執行個體。

* **Workday to Azure AD Provisioning** -雖然應使用 AAD Connect 這項工具將 Active Directory 使用者同步處理至 Azure Active Directory，但此應用程式可用於協助將僅限雲端使用者從 Workday 佈建至單一 Azure Active Directory 租用戶。

* **Workday 回寫** - 此應用程式可協助將使用者的電子郵件地址從 Azure Active Directory 回寫至 Workday。

> [!TIP]
> 您可以使用一般「Workday」應用程式設定 Workday 與 Azure Active Directory 之間的單一登入。 

#### <a name="determine-workday-to-ad-user-attribute-mapping-and-transformations"></a>決定 Workday 至 AD 使用者的屬性對應和轉換

在設定對 Active Directory 網域的使用者佈建之前，請考量下列問題。 這些問題的答案將會決定您的篩選範圍和屬性對應必須如何設定。

* **Workday 中的哪些使用者需要佈建到此 Active Directory 樹系？**

   * *範例：Workday "Company" 屬性包含 "Contoso" 值，且 "Worker_Type" 屬性包含 "Regular" 的使用者*

* **使用者如何路由到不同的組織單位 (OU) 中？**

   * *範例：使用者路由到對應於辦公室所在地點的 OU，如 Workday "Municipality" 和 "Country_Region_Reference" 屬性所定義*

* **下列屬性應如何填入 Active Directory 中？**

   * 一般名稱 (cn)
      * *範例：使用 Workday User_ID 值，如人力資源所設定*
      
   * 員工識別碼 (employeeId)
      * *範例：使用 Workday Worker_ID 值*
      
   * SAM 帳戶名稱 (sAMAccountName)
      * *範例：使用 Workday User_ID 值，透過 Azure AD 佈建運算式進行篩選以移除不合法的字元*
      
   * 使用者主體名稱 (userPrincipalName)
      * *範例：使用 Workday User_ID 值，並使用 Azure AD 佈建運算式附加網域名稱*

* **如何在 Workday 與 Active Directory 之間比對使用者？**

  * *範例：具有特定 Workday "Worker_ID" 值的使用者與 "employeeID" 具有相同值的 Active Directory 使用者相比對。如果在 Active Directory 中找不到 Worker_ID 值，則建立新的使用者。*
  
* **Active Directory 樹系是否已包含比對邏輯運作所需的使用者識別碼？**

  * *範例：如果這是新的 Workday 部署，強烈建議您為 Active Directory 預先填入正確的 Workday Worker_ID 值 (或選擇的唯一識別碼值)，讓比對邏輯越簡單越好。*



如何安裝及設定這些特殊佈建連接器應用程式是本教學課程其餘章節的主題。 您選擇要設定的應用程式將取決於需要進行佈建的系統，以及環境中的 Active Directory 網域和 Azure AD 租用戶數量。



## <a name="configure-integration-system-user-in-workday"></a>在 Workday 中設定整合系統使用者

所有 Workday 佈建連接器的常見需求，是其需要 Workday 系統整合帳戶的認證才能連線至 Workday Human Resources API。 本節說明如何在 Workday 中建立整合系統使用者。

> [!NOTE]
> 您可以略過此程序，改用 Workday 全域系統管理員帳戶做為系統整合帳戶。 這可能會在示範中正常運作，但不建議用於生產環境部署。

### <a name="create-an-integration-system-user"></a>建立整合系統使用者

**建立整合系統使用者：**

1. 使用系統管理員帳戶登入 Workday 租用戶。 在 [Workday 應用程式] 中，在搜尋方塊內輸入 create user，然後按一下 [建立整合系統使用者]。

    ![建立使用者](./media/workday-inbound-tutorial/wd_isu_01.png "建立使用者")
2. 為新的「整合系統使用者」 提供使用者名稱和密碼來完成「建立整合系統使用者」  工作。  
 * 保持 [下次登入時要求新密碼] 選項未核取，因為該使用者會以程式設計的方式登入。
 * 保持 [工作階段逾時分鐘數] 為預設值 [0]，這會防止使用者的工作階段提早逾時。
 * 選取選項 [不允許 UI 工作階段]，因為它會提供一層額外的安全性，防止使用整合系統密碼的使用者登入 Workday。 

    ![建立整合系統使用者](./media/workday-inbound-tutorial/wd_isu_02.png "建立整合系統使用者")

### <a name="create-a-security-group"></a>建立安全性群組
在此步驟中，您將在 Workday 中建立不受限制的整合系統安全性群組，並將上個步驟中建立的整合系統使用者指派到此群組。

**建立安全性群組：**

1. 在搜尋方塊中輸入 create security group，然後按一下 [建立安全性群組] 連結。

    ![建立安全性群組](./media/workday-inbound-tutorial/wd_isu_03.png "建立安全性群組")
2. 完成**建立安全性群組**工作。  
   * 從 [租用安全性群組類型] 下拉式清單中，選取 [整合系統安全性群組 (未受限制)]。

    ![建立安全性群組](./media/workday-inbound-tutorial/wd_isu_04.png "建立安全性群組")

3. 安全性群組成功建立之後，您會看到可用來將成員指派到安全性群組的頁面。 將新的整合系統使用者新增到此安全性群組，並選取適當的組織範圍。
![編輯安全性群組](./media/workday-inbound-tutorial/wd_isu_05.png "編輯安全性群組")
 
### <a name="configure-domain-security-policy-permissions"></a>設定網域安全性原則權限
在此步驟中，您會為安全性群組的人員資料授與「網域安全性」原則權限。

**設定網域安全性原則權限：**

1. 在搜尋方塊中輸入**網域安全性設定**，然後按一下 [網域安全性設定報告] 上的連結。  

    ![網域安全性原則](./media/workday-inbound-tutorial/wd_isu_06.png "網域安全性原則")  
2. 在 [網域] 文字方塊中，搜尋下列網域，並將它們一一新增到篩選。  
   * *外部帳戶佈建*
   * *人員資料：公用人員報告*
   * *人員資料：工作連絡人資訊*
   * *人員資料：所有職位*
   * *人員資料：目前人員配置資訊*
   * *人員資料：人員個人檔案的職稱*
 
    ![網域安全性原則](./media/workday-inbound-tutorial/wd_isu_07.png "網域安全性原則")  

    ![網域安全性原則](./media/workday-inbound-tutorial/wd_isu_08.png "網域安全性原則") 

    按一下 [確定]。

3. 在顯示的報表中，選取顯示在 [外部帳戶佈建] 旁邊的省略號 (...)，然後按一下功能表選項 [網域] -> [編輯安全性原則權限]

    ![網域安全性原則](./media/workday-inbound-tutorial/wd_isu_09.png "網域安全性原則")  

4. 在 [編輯網域安全性原則權限] 頁面上，向下捲動至 [整合權限] 區段。 按一下 [+] 符號，將整合系統群組新增到具有**取得**和**放入**整合權限的安全性群組清單中。

    ![編輯權限](./media/workday-inbound-tutorial/wd_isu_10.png "編輯權限")  

5. 按一下 [+] 符號，將整合系統群組新增到具有**取得**和**放入**整合權限的安全性群組清單中。

    ![編輯權限](./media/workday-inbound-tutorial/wd_isu_11.png "編輯權限")  

6. 針對其餘的各個安全性原則重複前述的步驟 3-5：

   | 作業 | 網域安全性原則 |
   | ---------- | ---------- | 
   | Get 和 Put | 人員資料：公用人員報告 |
   | Get 和 Put | 人員資料：工作連絡人資訊 |
   | 取得 | 人員資料：所有職位 |
   | 取得 | 人員資料：目前人員配置資訊 |
   | 取得 | 人員資料：人員個人檔案的職稱 |

### <a name="configure-business-process-security-policy-permissions"></a>設定商務程序安全性原則權限
在此步驟中，您會為安全性群組的人員資料授與「商務程序安全性」原則權限。 這是設定 Workday 回寫應用程式連接器的必要項目。 

**設定商務程序安全性原則權限：**

1. 在搜尋方塊中輸入**商務程序原則**，然後按一下連結 [編輯商務程序安全性原則] 工作。  

    ![商務程序安全性原則](./media/workday-inbound-tutorial/wd_isu_12.png "商務程序安全性原則")  

2. 在 [商務程序類型] 文字方塊中，搜尋*連絡人*，然後選取 [連絡人變更] 商務程序，並按一下 [確定]。

    ![商務程序安全性原則](./media/workday-inbound-tutorial/wd_isu_13.png "商務程序安全性原則")  

3. 在 [編輯商務程序安全性原則] 頁面上，捲動到 [維護連絡人資訊 (Web 服務)] 區段。

    ![商務程序安全性原則](./media/workday-inbound-tutorial/wd_isu_14.png "商務程序安全性原則")  

4. 選取新的整合系統安全性群組，並新增到可起始 Web 服務要求的安全性群組清單。 按一下 [完成]。 

    ![商務程序安全性原則](./media/workday-inbound-tutorial/wd_isu_15.png "商務程序安全性原則")  

 
### <a name="activate-security-policy-changes"></a>啟用安全性原則變更

**啟用安全性原則變更：**

1. 在搜尋方塊中輸入 activate，然後按一下 [啟用擱置的安全性原則變更] 連結。

    ![啟用](./media/workday-inbound-tutorial/wd_isu_16.png "啟用") 
2. 輸入供稽核用的註解並按一下 [確定] 按鈕，以開始「啟用擱置的安全性原則變更」工作。 

    ![啟用擱置的安全性](./media/workday-inbound-tutorial/wd_isu_17.png "啟用擱置的安全性")  
1. 在下一個畫面核取 [確認] 核取方塊，然後按一下 [確定] 以完成工作。

    ![啟用擱置的安全性](./media/workday-inbound-tutorial/wd_isu_18.png "啟用擱置的安全性")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>設定將使用者從 Workday 佈建至 Active Directory

請遵循下列指示來設定將使用者帳戶從 Workday 佈建至整合範圍內的每個 Active Directory 網域。

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>第 1 部分：安裝並設定內部部署佈建代理程式

若要佈建至 Active Directory 內部部署，您必須在伺服器上安裝代理程式，該伺服器必須具有 .NET 4.7+ Framework 並且可存取所需的 Active Directory 網域。

> [!TIP]
> 您可以使用[這裡](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)提供的指示，檢查您伺服器上的 .NET Framework 版本。
> 如果伺服器未安裝 .NET 4.7 或更高版本，您可以從[這裡](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)下載。  

一旦您部署 .NET 4.7+ 之後，您可以在**[這裡下載內部部署佈建代理程式](https://go.microsoft.com/fwlink/?linkid=847801)**，並遵循下方提供的步驟來完成代理程式設定。

1. 登入您要安裝新代理程式的 Windows Server。
2. 啟動佈建代理程式安裝程式，同意條款並按一下 [安裝] 按鈕。
![安裝畫面](./media/workday-inbound-tutorial/pa_install_screen_1.png "安裝畫面")

3. 安裝完成之後，精靈將會啟動，而您將會看到 [連線 Azure AD] 畫面。 按一下 [驗證] 按鈕以連線到您的 Azure AD 執行個體。
![連線 Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "連線 Azure AD")

4. 使用全域管理員認證驗證您的 Azure AD 執行個體。 
![管理員驗證](./media/workday-inbound-tutorial/pa_install_screen_3.png "管理員驗證")

5. 成功使用 Azure AD 驗證之後，您會看到 [連線 Active Directory] 畫面。 在此步驟中，請輸入您的 AD 網域名稱，並按一下 [新增目錄] 按鈕。
![新增目錄](./media/workday-inbound-tutorial/pa_install_screen_4.png "新增目錄")

6. 現在系統會提示您輸入認證，才能連線至 AD 網域。 在相同畫面中，您可以使用 [選取網域控制站優先權] 來指定代理程式應用來傳送佈建要求的網域控制站。
![網域認證](./media/workday-inbound-tutorial/pa_install_screen_5.png "網域認證")

7. 設定網域之後，安裝程式會顯示已設定的網域清單。 在此畫面上，您可以重複步驟 #5 和 #6 來新增更多網域，或是按一下 [下一步] 來繼續進行代理程式註冊。 
![已設定的網域](./media/workday-inbound-tutorial/pa_install_screen_6.png "已設定的網域")

   > [!NOTE]
   > 如果您有多個 AD 網域 (例如 a.contoso.com、emea.contoso.com)，則請將每個網域個別新增至清單。 只新增父網域 (例如 contoso.com) 並不足夠，且建議您使用代理程式註冊每個子網域。 

8. 檢閱設定詳細資料，並按一下 [確認] 以註冊代理程式。 
![確認畫面](./media/workday-inbound-tutorial/pa_install_screen_7.png "確認畫面")

9. 設定精靈會顯示代理程式註冊的進度。
![代理程式註冊](./media/workday-inbound-tutorial/pa_install_screen_8.png "代理程式註冊")

10. 一旦代理程式註冊成功之後，您可以按一下 [結束] 來結束精靈。 
![結束畫面](./media/workday-inbound-tutorial/pa_install_screen_9.png "結束畫面")

11. 您可以透過開啟 [服務] 嵌入式管理單元，並尋找名稱為「Microsoft Azure AD Connect 佈建代理程式」來驗證代理程式的安裝，並確認它正在執行 ![服務](./media/workday-inbound-tutorial/services.png)  


**代理程式疑難排解**

裝載代理程式的 Windows Server 機器上的 [Windows 事件記錄檔](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx)中，包含了所有代理程式所執行的作業事件。 若要檢視這些事件：
    
1. 請開啟 **Eventvwr.msc**。
2. 選取 [Windows 記錄檔 > 應用程式]。
3. 檢視來源 **AAD.Connect.ProvisioningAgent** 底下記錄的所有事件。 
4. 請檢查錯誤和警告。

    
### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>第 2 部分：新增佈建連接器應用程式和建立 Workday 連線

**若要設定 Workday 至 Active Directory 佈建：**

1.  移至 <https://portal.azure.com>。

2.  在左側導覽列中，選取 [Azure Active Directory]

3.  依序選取 [企業應用程式] 和 [所有應用程式]。

4.  選取 [新增應用程式]，然後選取 [全部] 類別。

5.  搜尋 **Workday Provisioning to Active Directory**，並從資源庫新增該應用程式。

6.  新增應用程式並顯示應用程式詳細資料畫面之後，請選取 [佈建]

7.  將 [佈建模式] 變更為 [自動]

8.  完成 [系統管理員認證] 區段，如下所示：

   * **系統管理員使用者名稱** – 輸入 Workday 整合系統帳戶的使用者名稱，並附加租用戶網域名稱。 **其應該類似於：username@contoso4**

   * **系統管理員密碼 –** 輸入 Workday 整合系統帳戶的密碼

   * **租用戶 URL –** 輸入租用戶 Workday Web 服務端點的 URL。 這應該看起來像這樣： https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources，其中會以您的正確租用戶名稱取代 contoso4，以正確的環境字串取代 wd3-impl。

   * **Active Directory 樹系 -** 使用代理程式註冊時，您的 Active Directory 網域的「名稱」。 此字串通常類似於：*contoso.com*

   * **Active Directory 容器 -** 輸入容器 DN，其中是代理程式預設應建立使用者帳戶的位置。 
        範例：*OU=Standard Users,OU=Users,DC=contoso,DC=test*
> [!NOTE]
> 如果並未在屬性對應中設定 *parentDistinguishedName* 屬性，此設定僅適用於使用者帳戶建立。 此設定不適用於使用者搜尋或更新作業。 整個網域的子樹狀會落在搜尋作業的範圍中。
   * **電子郵件通知 –** 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。
> [!NOTE]
> 如果佈建作業進入[隔離](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine)狀態，Azure AD 佈建服務會傳送電子郵件通知。

   * 按一下 [測試連線] 按鈕。 如果連線測試成功，請按一下頂端的 [儲存] 按鈕。 如果失敗，請仔細檢查代理程式上設定的 Workday 認證和 AD 認證是否有效。

![Azure 入口網站](./media/workday-inbound-tutorial/wd_1.png)

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：設定屬性對應 

在本節中，您會設定使用者資料從 Workday 流動至 Active Directory 的方式。

1.  在 [佈建] 索引標籤的 [對應] 下，按一下 [將 Workday 人員同步至內部部署]。

2.  在 [來源物件範圍] 欄位中，您可以透過定義一組屬性型篩選，選取應該佈建至 AD 的 Workday 使用者集合範圍。 預設範圍是「Workday 中的所有使用者」。 範例篩選：

   * 範例：人員識別碼介於 1000000 到 2000000 之間的使用者範圍

      * 屬性：WorkerID

      * 運算子：REGEX Match

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 範例：僅員工和非約聘人員 

      * 屬性：EmployeeID

      * 運算子：IS NOT NULL

3.  在 [目標物件動作] 欄位中，您可以全域篩選允許在 Active Directory 上執行哪些動作。 最常見的動作是 [建立] 和 [更新]。

4.  在 [屬性對應] 區段中，您可以定義個別 Workday 屬性如何對應至 Active Directory 屬性。

5. 按一下現有的屬性對應以進行更新，或按一下畫面底端的 [新增新對應] 以新增新對應。 個別屬性對應支援下列屬性：

      * **對應類型**

         * **直接** – 將 Workday 屬性的值寫入 AD 屬性，且不進行變更

         * **常數** - 將靜態的常數字串值寫入 AD 屬性

         * **運算式** – 可讓您根據一或多個 Workday 屬性，將自訂值寫入 AD 屬性。 [如需詳細資訊，請參閱這篇有關運算式的文章](../manage-apps/functions-for-customizing-application-data.md)。

      * **來源屬性** - 來自 Workday 的使用者屬性。 如果您要尋找的屬性不存在，請參閱[自訂 Workday 使用者屬性的清單](#customizing-the-list-of-workday-user-attributes)。

      * **預設值** – 選用。 如果來源屬性具有空值，則對應將會改為寫入此值。
            最常見的設定是將其保留空白。

      * **目標屬性** – Active Directory 中的使用者屬性。

      * **使用此屬性比對物件** – 是否應該將此對應用於唯一識別 Workday 與 Active Directory 之間的使用者。 此設定通常會設定於 Workday 的 [人員識別碼] 欄位，且通常會在 Active Directory 中對應至其中一個員工識別碼屬性。

      * **比對優先順序** – 您可以設定多個比對屬性。 具有多個屬性時，系統會以此欄位定義的順序進行評估。 只要找到相符項目，便不會評估進一步比對屬性。

      * **套用此對應**
       
         * **一律** – 將此對應套用於使用者建立和更新動作

         * **僅限建立期間** - 僅將此對應套用於使用者建立動作

6. 若要儲存您的對應，請按一下 [屬性對應] 區段頂端的 [儲存]。

![Azure 入口網站](./media/workday-inbound-tutorial/WD_2.PNG)

**下列為 Workday 與 Active Directory 之間的一些範例屬性對應，以及一些常用運算式**

-   對應至 parentDistinguishedName 屬性的運算式可用來根據一或多個 Workday 來源屬性，將使用者佈建至不同的 OU。 此處的範例會根據使用者的所在城市將使用者放在不同 OU 中。

-   Active Directory 中的 userPrincipalName 屬性會藉由串連 Workday 使用者識別碼和網域尾碼來產生

-   [此為有關撰寫運算式的文件](../manage-apps/functions-for-customizing-application-data.md)。 其中包含說明如何移除特殊字元的範例。

  
| WORKDAY 屬性 | ACTIVE DIRECTORY 屬性 |  比對識別碼？ | 建立/更新 |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **是** | 僅於建立時寫入 |
| **UserID**    |  cn    |   |   僅於建立時寫入 |
| **Join("@", [UserID], "contoso.com")**   | userPrincipalName     |     | 僅於建立時寫入 
| **Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         僅於建立時寫入 |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | 建立 + 更新 |
| **名字**   | givenName       |     |    建立 + 更新 |
| **姓氏**   |   sn   |     |  建立 + 更新 |
| **PreferredNameData**  |  displayName |     |   建立 + 更新 |
| **Company**         | company   |     |  建立 + 更新 |
| **SupervisoryOrganization**  | department  |     |  建立 + 更新 |
| **ManagerReference**   | manager  |     |  建立 + 更新 |
| **BusinessTitle**   |  title     |     |  建立 + 更新 | 
| **AddressLineData**    |  streetAddress  |     |   建立 + 更新 |
| **Municipality**   |   l   |     | 建立 + 更新 |
| **CountryReferenceTwoLetter**      |   co |     |   建立 + 更新 |
| **CountryReferenceTwoLetter**    |  c  |     |         建立 + 更新 |
| **CountryRegionReference** |  st     |     | 建立 + 更新 |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  建立 + 更新 |
| **PostalCode**  |   postalCode  |     | 建立 + 更新 |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | 建立 + 更新 |
| **Fax**      | facsimileTelephoneNumber     |     |    建立 + 更新 |
| **Mobile**  |    mobile       |     |       建立 + 更新 |
| **LocalReference** |  preferredLanguage  |     |  建立 + 更新 |                                               
| **Switch(\[Municipality\], "OU=Standard Users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", “London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  建立 + 更新 |
  


### <a name="part-4-start-the-service"></a>第 4 部分：啟動服務
完成第 1-3 部分之後，您就可以在 Azure 入口網站中重新啟動佈建服務。

1.  在 [佈建] 索引標籤中，將 [佈建狀態] 設定為 [開啟]。

2. 按一下 [檔案] 。

3. 這會啟動初始同步，取決於 Workday 中的使用者人數，其可能要花費數小時。

4. 您可隨時檢查 Azure 入口網站中的 [稽核記錄] 索引標籤，查看佈建服務執行了哪些動作。 稽核記錄會列出佈建服務執行的所有個別同步處理事件，例如從 Workday 外部讀取了哪些使用者，接著又新增到或更新到 Active Directory 中。 **[請參閱佈建報告指南，瞭解有關如何讀取稽核記錄的詳細指示](../manage-apps/check-status-user-account-provisioning.md)**

1.  請檢查裝載代理程式的 Windows Server 機器上的 [Windows 事件記錄檔](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx)中，有哪些新錯誤或警告。 可透過在伺服器上啟動 **Eventvwr.msc** 並選取 [Windows 記錄檔 > 應用程式]，來檢視這些事件。 所有佈建相關的訊息都會記錄在來源 **AADSyncAgent** 底下。

6. 完成之後，其會寫入 [佈建] 索引標籤中的稽核摘要報告內，如下所示。

![Azure 入口網站](./media/workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>設定將使用者佈建至 Azure Active Directory
設定 Azure Active Directory 佈建的方法將取決於佈建需求，如下表所述。

| 案例 | 解決方法 |
| -------- | -------- |
| **需要佈建至 Active Directory 和 Azure AD 的使用者** | 使用 **[AAD Connect](../hybrid/whatis-hybrid-identity.md)** |
| **只需要佈建至 Active Directory 的使用者** | 使用 **[AAD Connect](../hybrid/whatis-hybrid-identity.md)** |
| **只需要佈建至 Azure AD 的使用者 (僅限雲端)** | 使用應用程式庫中的 **Workday to Azure Active Directory Provisioning** 應用程式 |

如需有關設定 Azure AD Connect 的指示，請參閱 [Azure AD Connect 文件](../hybrid/whatis-hybrid-identity.md)。

下列各節說明如何設定 Workday 與 Azure AD 之間的連接以佈建僅限雲端使用者。

> [!IMPORTANT]
> 如果您擁有需要佈建至 Azure AD 的僅限雲端使用者且沒有內部部署 Active Directory，請僅遵循下列程序。

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>第 1 部分：新增 Azure AD 佈建連接器應用程式和建立 Workday 連接

**若要針對僅限雲端使用者設定 Workday 至 Azure Active Directory 佈建：**

1.  移至 <https://portal.azure.com>。

2.  在左側導覽列中，選取 [Azure Active Directory]

3.  依序選取 [企業應用程式] 和 [所有應用程式]。

4.  選取 [新增應用程式]，然後選取 [全部] 類別。

5.  搜尋 **Workday to Azure AD Provisioning**，並從資源庫新增該應用程式。

6.  新增應用程式並顯示應用程式詳細資料畫面之後，請選取 [佈建]

7.  將 [佈建模式] 變更為 [自動]

8.  完成 [系統管理員認證] 區段，如下所示：

   * **系統管理員使用者名稱** – 輸入 Workday 整合系統帳戶的使用者名稱，並附加租用戶網域名稱。 其應該類似於：username@contoso4

   * **系統管理員密碼 –** 輸入 Workday 整合系統帳戶的密碼

   * **租用戶 URL –** 輸入租用戶 Workday Web 服務端點的 URL。 這應該看起來像這樣： https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources，其中會以您的正確租用戶名稱取代 contoso4，以正確的環境字串取代 wd3-impl。 如果不知道此 URL，請與您的 Workday 整合夥伴或支援代表合作，來判斷要使用的正確 URL。

   * **電子郵件通知 –** 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。

   * 按一下 [測試連線] 按鈕。

   * 如果連線測試成功，請按一下頂端的 [儲存] 按鈕。 如果失敗，請仔細檢查 Workday URL 和認證在 Workday 中是否有效。

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：設定屬性對應 

在本節中，您會針對僅限雲端使用者設定使用者資料從 Workday 流動至 Azure Active Directory 的方式。

1. 在 [佈建] 索引標籤的 [對應] 下，按一下 [將人員同步至 Azure AD]。

2. 在 [來源物件範圍] 欄位中，您可以透過定義一組屬性型篩選，選取應該佈建至 Azure AD 的 Workday 使用者集合範圍。 預設範圍是「Workday 中的所有使用者」。 範例篩選：

   * 範例：人員識別碼介於 1000000 到 2000000 之間的使用者範圍

      * 屬性：WorkerID

      * 運算子：REGEX Match

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 範例：僅約聘人員和非正式員工

      * 屬性：ContingentID

      * 運算子：IS NOT NULL

3. 在 [目標物件動作] 欄位中，您可以全域篩選允許在 Azure AD 上執行哪些動作。 最常見的動作是 [建立] 和 [更新]。

4. 在 [屬性對應] 區段中，您可以定義個別 Workday 屬性如何對應至 Active Directory 屬性。

5. 按一下現有的屬性對應以進行更新，或按一下畫面底端的 [新增新對應] 以新增新對應。 個別屬性對應支援下列屬性：

   * **對應類型**

      * **直接** – 將 Workday 屬性的值寫入 AD 屬性，且不進行變更

      * **常數** - 將靜態的常數字串值寫入 AD 屬性

      * **運算式** – 可讓您根據一或多個 Workday 屬性，將自訂值寫入 AD 屬性。 [如需詳細資訊，請參閱這篇有關運算式的文章](../manage-apps/functions-for-customizing-application-data.md)。

   * **來源屬性** - 來自 Workday 的使用者屬性。 如果您要尋找的屬性不存在，請參閱[自訂 Workday 使用者屬性的清單](#customizing-the-list-of-workday-user-attributes)。

   * **預設值** – 選用。 如果來源屬性具有空值，則對應將會改為寫入此值。
            最常見的設定是將其保留空白。

   * **目標屬性** – Azure AD 中的使用者屬性。

   * **使用此屬性比對物件** – 是否應該將此對應用於唯一識別 Workday 與 Azure AD 之間的使用者。 此設定通常會設定於 Workday 的 [人員識別碼] 欄位，且通常會在 Azure AD 中對應至員工識別碼屬性 (新) 或擴充屬性。

   * **比對優先順序** – 您可以設定多個比對屬性。 具有多個屬性時，系統會以此欄位定義的順序進行評估。 只要找到相符項目，便不會評估進一步比對屬性。

   * **套用此對應**

     * **一律** – 將此對應套用於使用者建立和更新動作

     * **僅限建立期間** - 僅將此對應套用於使用者建立動作

6. 若要儲存您的對應，請按一下 [屬性對應] 區段頂端的 [儲存]。

### <a name="part-3-start-the-service"></a>第 3 部分：啟動服務
完成第 1-2 部分之後，您可以啟動佈建服務。

1. 在 [佈建] 索引標籤中，將 [佈建狀態] 設定為 [開啟]。

2. 按一下 [檔案] 。

3. 這會啟動初始同步，取決於 Workday 中的使用者人數，其可能要花費數小時。

4. 您可以在 [稽核記錄] 索引標籤中檢視個別同步事件。**[請參閱佈建報告指南，瞭解有關如何讀取稽核記錄的詳細指示](../manage-apps/check-status-user-account-provisioning.md)**

5. 完成之後，其會寫入 [佈建] 索引標籤中的稽核摘要報告內，如下所示。

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>設定將電子郵件地址回寫至 Workday
請遵循下列指示來設定將使用者電子郵件地址從 Azure Active Directory 回寫至 Workday。

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>第 1 部分：新增佈建連接器應用程式和建立 Workday 連接

**設定 Workday 回寫連接器：**

1. 移至 <https://portal.azure.com>。

2. 在左側導覽列中，選取 [Azure Active Directory]

3. 依序選取 [企業應用程式] 和 [所有應用程式]。

4. 選取 [新增應用程式]，然後選取 [全部] 類別。

5. 搜尋 **Workday Writeback**，並從資源庫新增該應用程式。

6. 新增應用程式並顯示應用程式詳細資料畫面之後，請選取 [佈建]

7. 將 [佈建模式] 變更為 [自動]

8. 完成 [系統管理員認證] 區段，如下所示：

   * **系統管理員使用者名稱** – 輸入 Workday 整合系統帳戶的使用者名稱，並附加租用戶網域名稱。 其應該類似於：username@contoso4

   * **系統管理員密碼 –** 輸入 Workday 整合系統帳戶的密碼

   * **租用戶 URL –** 輸入租用戶 Workday Web 服務端點的 URL。 這應該看起來像這樣： https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources，其中會以您的正確租用戶名稱取代 contoso4，以正確的環境字串取代 wd3-impl (如有必要)。

   * **電子郵件通知 –** 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。

   * 按一下 [測試連線] 按鈕。 如果連線測試成功，請按一下頂端的 [儲存] 按鈕。 如果失敗，請仔細檢查 Workday URL 和認證在 Workday 中是否有效。

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：設定屬性對應 

在本節中，您會設定使用者資料從 Workday 流動至 Active Directory 的方式。

1. 在 [佈建] 索引標籤的 [對應] 下，按一下 [將 Azure AD 使用者同步至 Workday]。

2. 在 [來源物件範圍] 欄位中，您可以選擇性地篩選應該將電子郵件地址寫回至 Workday 的 Azure Active Directory 使用者集合。 預設範圍是「Azure AD 中的所有使用者」。 

3. 在 [屬性對應] 區段中，更新相符的識別碼，代表 Azure Active Directory 中儲存了 Workday 人員識別碼或員工識別碼的屬性。 常用的比對方法是將 Workday 人員識別碼或員工識別碼同步至 Azure AD 中的 extensionAttribute1-15，然後在 Azure AD 中使用此屬性再次比對 Workday 的使用者。 

4. 若要儲存您的對應，請按一下 [屬性對應] 區段頂端的 [儲存]。

### <a name="part-3-start-the-service"></a>第 3 部分：啟動服務
完成第 1-2 部分之後，您可以啟動佈建服務。

1. 在 [佈建] 索引標籤中，將 [佈建狀態] 設定為 [開啟]。

2. 按一下 [檔案] 。

3. 這會啟動初始同步，取決於 Workday 中的使用者人數，其可能要花費數小時。

4. 您可以在 [稽核記錄] 索引標籤中檢視個別同步事件。**[請參閱佈建報告指南，瞭解有關如何讀取稽核記錄的詳細指示](../manage-apps/check-status-user-account-provisioning.md)**

5. 完成之後，其會寫入 [佈建] 索引標籤中的稽核摘要報告內，如下所示。

## <a name="customizing-the-list-of-workday-user-attributes"></a>自訂 Workday 使用者屬性的清單
Active Directory 和 Azure AD 的 Workday 佈建應用程式都包含您可以從中選取的預設 Workday 使用者屬性清單。 不過，這些清單並不完整。 Workday 支援數百個可能的使用者屬性，這些屬性可以是 Workday 租用戶的標準或唯一屬性。 

Azure AD 佈建服務支援自訂清單或 Workday 屬性的功能，以包含任何在人力資源 API 的 [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 作業中公開的屬性。

若要這麼做，您必須使用 [Workday Studio](https://community.workday.com/studio-download) 擷取代表您要使用之屬性的 XPath 運算式，然後在 Azure 入口網站中使用進階屬性編輯器將這些運算式新增至您的佈建組態。

**若要擷取 Workday 使用者屬性的 XPath 運算式：**

1. 下載並安裝 [Workday Studio](https://community.workday.com/studio-download)。 您需要 Workday 社群帳戶才能存取安裝程式。

2. 從下列 URL 下載 Workday Human_Resources WDSL 檔案： https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. 啟動 Workday Studio。

4. 從命令列中，選取 [Workday] > [在測試器中測試 Web 服務] 選項。

5. 選取 [外部]，然後選取您在步驟 2 下載的 Human_Resources WSDL 檔案。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. 將 [位置] 欄位設定為 `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`，但是以您實際的執行個體類型取代 "IMPL-CC"，並以您實際的租用戶名稱取代 "TENANT"。

7. 將 [作業] 設定為 [Get_Workers]

8.  按一下 [要求]/[回應] 窗格下方的小型**設定**連結，以設定您的 Workday 認證。 勾選 [驗證]，然後輸入 Workday 整合系統帳戶的使用者名稱和密碼。 請務必將使用者名稱的格式設為 name@tenant，並讓 [WS-Security 使用者名稱權杖] 選項繼續保持已選取狀態。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. 選取 [確定] 。

10. 在 [要求] 窗格中，貼上以下的 XML 並將 [Employee_ID] 設定為您的 Workday 租用戶中真實使用者的員工識別碼。 選取已填入您想擷取之屬性的使用者。

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
            <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```
 
11. 按一下 [傳送要求] (綠色箭頭) 來執行命令。 如果成功，回應應會出現在 [回應] 窗格中。 請檢查回應以確定它具有您所輸入的使用者識別碼資料，而非錯誤。

12. 如果成功，請從 [回應] 窗格複製 XML 並將它儲存為 XML 檔案。

13. 在 Workday Studio 的命令列中，選取 [File] \(檔案\) > [Open File...] \(開啟檔案...\) 並開啟您剛儲存的 XML 檔案。 這會在 Workday Studio XML 編輯器中開啟檔案。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. 在檔案樹狀目錄中，瀏覽 **/env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker** 來尋找您的使用者資料。 

15. 在 **wd:Worker** 之下，尋找您要新增的屬性並加以選取。

16. 從 [文件路徑] 欄位複製您所選屬性的 XPath 運算式。

1. 從複製的運算式中移除 **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** 前置詞。

18. 如果所複製運算式中的最後一個項目是節點 (例如："/wd:Birth_Date")，則在運算式的結尾附加 **/text()**。 如果最後一個項目是屬性 (例如："/@wd: type")，則不一定要這麼做。

19. 結果應該類似 `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`。 這是您將複製到 Azure 入口網站中的內容。


**若要將自訂 Workday 使用者屬性新增至佈建組態：**

1. 如本教學課程稍早所述，啟動 [Azure 入口網站](https://portal.azure.com)，並瀏覽至 Workday 佈建應用程式的 [佈建] 區段。

2. 將 [佈建狀態] 設定為 [關閉]，然後選取 [儲存]。 這有助於確保您的變更只會在您準備好時生效。

3. 在 [對應] 之下，選取 [將背景工作角色同步至內部部署環境] (或 [將背景工作角色同步至 Azure AD])。

4. 捲動到下一個畫面的底部，然後選取 [顯示進階選項]。

5. 選取 [編輯 Workday 的屬性清單]。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. 捲動到輸入欄位所在屬性清單的底部。

7. 針對 [名稱]，輸入屬性的顯示名稱。

8. 針對 [類型]，選取屬性的適當對應類型 ([字串] 最常見)。

9. 針對 [API 運算式]，輸入您從 Workday Studio 複製的 XPath 運算式。 範例： `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. 選取 [新增屬性]。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. 選取上方的 [儲存]，然後在對話方塊中選取 [是]。 如果 [屬性對應] 畫面仍然開啟，請將它關閉。

12. 回到主要 [佈建] 索引標籤，再次選取 [將背景工作角色同步至內部部署環境] (或 [將背景工作角色同步至 Azure AD])。

13. 選取 [新增對應]。

14. 您的新屬性現在應出現在 [來源屬性] 清單中。

15. 視需要為您的新屬性新增對應。

16. 完成後，請記得將 [佈建狀態] 設回 [開啟] 並儲存。

## <a name="known-issues"></a>已知問題

* 目前不支援在內部部署 Active Directory 中將資料寫入 thumbnailPhoto 使用者屬性。

* 目前在已啟用 AAD Connect 的 Azure AD 租用戶上，不支援「Workday 至 Azure AD」連接器。  

## <a name="managing-personal-data"></a>管理個人資料

適用於 Active Directory 的 Workday 佈建解決方案會要求在已加入網域的伺服器上安裝同步處理代理程式，而此代理程式會在 Windows 事件記錄中建立可包含個人識別資訊的記錄。

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
* [了解如何設定 Workday 與 Azure Active Directory 之間的單一登入](workday-tutorial.md)
* [了解如何將其他 SaaS 應用程式與 Azure Active Directory 整合](tutorial-list.md)

