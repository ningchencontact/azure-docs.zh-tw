---
title: 向 SaaS 應用程式報告自動使用者帳戶布建 |Microsoft Docs
description: 了解如何檢查使用者帳戶自動佈建作業的狀態，以及如何針對個別使用者的佈建進行疑難排解。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: fda7654ca2d825ae4112dd06021c7e83ed6867cd
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381253"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>教學課程：關於使用者帳戶自動佈建的報告

Azure Active Directory (Azure AD) 包含[使用者帳戶](user-provisioning.md)布建服務, 可協助您在 SaaS 應用程式和其他系統中自動布建使用者帳戶, 以進行端對端身分識別生命週期管理。 對於 [Azure AD 應用程式庫](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)之 [精選] 區段中的所有應用程式和系統，Azure AD 支援已預先整合的使用者佈建連接器。

本文會說明如何在佈建作業設定好之後檢查其狀態，以及如何針對個別使用者和群組的佈建進行疑難排解。

## <a name="overview"></a>總覽

我們會使用 [Azure 入口網站](https://portal.azure.com)，依照針對支援之應用程式[所提供的文件](../saas-apps/tutorial-list.md)來設定佈建連接器。 在設定好並開始執行之後，您就可以使用下列兩種方法的其中一種來獲得佈建作業的報告：

* **Azure 入口網站**-本文主要說明如何從[Azure 入口網站](https://portal.azure.com)中抓取報告資訊, 以提供布建摘要報告, 以及特定應用程式的詳細布建審核記錄。
* **稽核 API** - Azure Active Directory 也會提供稽核 API，以便透過程式擷取詳細的佈建稽核記錄。 如需此 API 專屬的使用說明文件，請參閱 [Azure Active Directory 稽核 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)。 雖然本文未具體說明如何使用 API，但會詳細說明稽核記錄中所記下的佈建事件類型。

### <a name="definitions"></a>定義

本文會使用下列詞彙，其定義如下︰

* **來源系統** - Azure AD 佈建服務用來進行同步處理的來源端使用者存放庫。 已預先整合的佈建連接器大多使用 Azure Active Directory 作為來源系統，但有一些例外 (範例︰Workday 的輸入同步處理)。
* **目標系統** - Azure AD 佈建服務用來進行同步處理的目標端使用者存放庫。 這通常是 SaaS 應用程式 (範例：Salesforce、ServiceNow、G Suite、Dropbox for Business), 但在某些情況下, 可以是內部部署系統, 例如 Active Directory (例如:Workday 對 Active Directory 的輸入同步處理)。

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>從 Azure 入口網站取得布建報告

若要取得給定應用程式的布建報告資訊, 請先啟動[Azure 入口網站](https://portal.azure.com), 然後流覽至已設定布建的企業應用程式。 例如，如果您要在 LinkedIn Elevate 中佈建使用者，則應用程式詳細資料的導覽路徑為︰

**Azure Active Directory > 企業應用程式 > 所有應用程式 > LinkedIn Elevate**

您可以從這裡取得佈建摘要報告和佈建稽核記錄，兩者的說明如下。

## <a name="provisioning-summary-report"></a>佈建摘要報告

佈建摘要報告顯示在給定應用程式的 [佈建] 索引標籤上。 它位於 [設定] 底下的 [同步處理詳細資料] 區段中，並會提供下列資訊：

* 已同步處理且目前正在來源系統與目標系統之間的佈建範圍內的使用者和群組總數。
* 上次執行同步處理的時間。 完成[首次同步處理](user-provisioning.md#what-happens-during-provisioning)之後，系統通常每隔 20 到 40 分鐘就會進行一次同步處理。
* 是否已完成[初始同步處理](user-provisioning.md#what-happens-during-provisioning)。
* 佈建程序是否已進入隔離狀態，以及造成隔離狀態的原因為何 (例如，因管理員認證無效而無法與目標系統進行通訊)。

系統管理員首先應該看的就是佈建摘要報告，以便了解佈建作業的作業健康情況。

 ![摘要報告](./media/check-status-user-account-provisioning/summary_report.PNG)

## <a name="provisioning-audit-logs"></a>佈建稽核記錄

佈建服務所執行的活動全都會記錄在 Azure AD 稽核記錄中，您可以在 [帳戶佈建] 類別底下的 [稽核記錄] 索引標籤中檢視這些記錄。 所記錄的活動事件類型包括︰

* **匯入事件** - Azure AD 佈建服務每次從來源系統或目標系統擷取個別使用者或群組的相關資訊時，就會記錄下「匯入」事件。 在同步處理期間，服務會先從來源系統擷取使用者，而其結果便會記錄為「匯入」事件。 接著，服務便會對目標系統查詢所擷取使用者的比對識別碼，以檢查這些使用者是否存在，而其結果也會記錄為「匯入」事件。 這些事件會記錄下 Azure AD 佈建服務在事件發生當下所看到的所有對應使用者屬性與屬性值。
* **同步處理規則事件** - 這些事件會報告在服務已從來源和目標系統匯入使用者資料並加以評估後，屬性對應規則和任何已設定之範圍篩選的結果。 例如，如果服務認為來源系統中的使用者位於佈建範圍內，而且不存在於目標系統中，則此事件會記錄下「使用者將佈建到目標系統」。
* **匯出事件** - Azure AD 佈建服務每次在目標系統中寫入使用者帳戶或群組物件時，就會記錄下「匯出」事件。 這些事件會記錄下 Azure AD 佈建服務在事件發生當下所寫入的所有使用者屬性與屬性值。 如果在目標系統中寫入使用者帳戶或群組物件時發生錯誤，則會在此顯示出來。
* **處理序委付事件** - 當佈建服務在嘗試進行作業時遇到失敗，系統就會發生處理序委付事件，並開始在輪詢間隔時間重試此作業。 每次重試佈建作業時，都會記錄「委付」事件。

在查看個別使用者的佈建事件時，其事件的發生順序通常如下︰

1. 匯入事件︰從來源系統擷取使用者。
1. 匯入事件︰查詢目標系統以檢查所擷取的使用者是否存在。
1. 同步處理規則事件︰對所設定的屬性對應規則與範圍篩選評估得自來源和目標系統的使用者資料，以判斷應該執行什麼動作 (如果有的話)。
1. 匯出事件︰如果同步處理規則事件指出應執行某動作 (新增、更新、刪除)，則匯出事件中會記錄該動作的執行結果。

   ![範例:顯示活動和狀態的 [審核記錄] 頁面](./media/check-status-user-account-provisioning/audit_logs.PNG)

### <a name="looking-up-provisioning-events-for-a-specific-user"></a>查閱特定使用者的佈建事件

稽核記錄佈建最常見的使用案例是檢查個別使用者帳戶的佈建狀態。 若要查閱特定使用者最新的佈建事件：

1. 移至 [稽核記錄] 區段。
1. 在 [類別] 功能表上選取 [帳戶佈建]。
1. 在 [日期範圍] 功能表中，選取您想要搜尋的日期範圍。
1. 在 [搜尋] 列上輸入您要搜尋之使用者的使用者識別碼。 識別碼值的格式應該符合您選取來作為屬性對應設定中主要比對識別碼的任何項目 (例如 userPrincipalName 或員工識別碼)。 所需的識別碼值會顯示在 [目標] 資料行上。
1. 按 Enter 鍵以開始搜尋。 最新的佈建事件會最先傳回。
1. 在事件傳回後，請注意活動類型以及該活動是成功還失敗。 如果未傳回任何結果，則表示使用者不存在，或因為尚未完成完整同步處理，所以佈建程序未能偵測到。
1. 按一下個別事件可檢視延伸的詳細資料，包括作為事件一部分而擷取、評估或寫入的所有使用者屬性。

如需如何使用稽核記錄的示範，請參閱以下影片。 稽核記錄會在 5:30 左右出現：

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>佈建稽核記錄的檢視秘訣

為了清楚了解記錄，請在 Azure 入口網站中選取 [資料行] 按鈕，然後選擇下列資料行︰

* **日期** - 顯示事件的發生日期。
* **目標** - 顯示身為事件主體的應用程式名稱和使用者識別碼。
* **活動** - 活動類型，其說明如前。
* **狀態** - 事件是否成功。
* **狀態原因** - 佈建事件發生經過的摘要。

## <a name="troubleshooting"></a>疑難排解

佈建摘要報告和稽核記錄扮演了重要角色，可協助系統管理員針對各種使用者帳戶佈建問題進行疑難排解。

如需透過案例來獲得如何針對自動使用者佈建進行疑難排解的指引，請參閱[為應用程式設定和佈建使用者時發生問題](application-provisioning-config-problem.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](what-is-single-sign-on.md)
