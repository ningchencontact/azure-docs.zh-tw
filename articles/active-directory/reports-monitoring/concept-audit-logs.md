---
title: Azure Active Directory 入口網站中的稽核活動報告 | Microsoft Docs
description: 介紹 Azure Active Directory 入口網站中的稽核活動報告
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59f716ccd7b2e02c9064f13cf1ffd6e8180858c9
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827902"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 入口網站中的稽核活動報告 

透過 Azure Active Directory (Azure AD) 報告，您可以取得判斷環境執行狀況所需的資訊。

報告架構由下列元件組成：

- **活動** 
    - **登入** – [登入報告](concept-sign-ins.md)會提供受控應用程式和使用者登入活動的使用情況相關資訊。
    - **稽核記錄** - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 稽核記錄的範例包括對 Azure AD 中任何資源所做的變更，像是新增或移除使用者、應用程式、群組、角色和原則。
- **安全性** 
    - **有風險的登入** - [有風險的登入](concept-risky-sign-ins.md)表示非使用者帳戶合法擁有者的某人嘗試登入。 
    - **標幟為有風險的使用者** - [有風險的使用者](concept-user-at-risk.md)表示可能被盜用的使用者帳戶。

本文提供稽核報告的概觀。
 
## <a name="who-can-access-the-data"></a>誰可以存取資料？

* 中的使用者**安全性系統管理員**，**安全性讀取者**，**報告讀取器**或是**全域管理員**角色
* 此外，所有使用者 (非系統管理員) 都可看到自己的稽核活動

## <a name="audit-logs"></a>稽核記錄

Azure AD 稽核記錄會提供符合規範的系統活動記錄。 若要存取稽核報告，請選取 [Azure Active Directory]  中 [活動]  區段上的 [稽核記錄]  。 請注意，稽核記錄可能會延遲最多一小時，所以稽核活動資料可能會在您完成該工作的一個小時後，才會出現在入口網站中。

![稽核記錄](./media/concept-audit-logs/61.png "稽核記錄")

稽核記錄的預設清單檢視顯示︰

- 發生時間與日期
- 記錄發生的服務
- 類別和活動的名稱 (*什麼*) 
- 活動 （成功或失敗） 的狀態
- 目標
- 活動的啟動者/執行者 (對象)

![稽核記錄](./media/concept-audit-logs/listview.png "稽核記錄")

您可以按一下工具列中的 [資料行]  來自訂清單檢視。

![稽核記錄](./media/concept-audit-logs/columns.png "稽核記錄")

這可讓您顯示其他欄位，或移除已顯示的欄位。

![稽核記錄](./media/concept-audit-logs/columnselect.png "稽核記錄")

選取清單檢視中的項目，即可取得更詳細的資訊。

![稽核記錄](./media/concept-audit-logs/details.png "稽核記錄")


## <a name="filtering-audit-logs"></a>篩選稽核記錄

您可以在下列欄位上篩選稽核資料：

- 服務
- Category
- activities
- 狀態
- 目標
- 啟動者 (執行者)
- 日期範圍

![稽核記錄](./media/concept-audit-logs/filter.png "稽核記錄")

**服務**篩選器可讓您選取從下拉式清單中的下列服務：

- 全部
- 存取權檢閱
- 帳戶佈建 
- 應用程式 SSO
- 驗證方法
- B2C
- 條件式存取
- 核心目錄
- 權利管理
- 身分識別保護
- 受邀的使用者
- PIM
- 自助式群組管理
- 自助服務 Passord 管理
- 使用條款

**分類**篩選條件可讓您選取其中一個下列的篩選器：

- 全部
- AdministrativeUnit
- ApplicationManagement
- 驗證
- Authorization
- 連絡人
- 裝置
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- 其他
- 原則
- ResourceManagement
- RoleManagement
- UserManagement

**活動**篩選根據類別和活動資源類型的選擇。 您可以選取您想要查看的特定活動或選擇全部。 

您可以使用圖形 API https://graph.windows.net/ $tenantdomain/activities/auditActivityTypes?api-version=beta 來取得所有稽核活動的清單 (其中，$tenantdomain 是網域名稱)，或請參閱[稽核報告事件](reference-audit-activities.md)一文。

**狀態**篩選器可讓您篩選稽核作業的狀態為基礎。 狀態可以是下列其中一項：

- 全部
- 成功
- 失敗

**目標**篩選器可讓您依名稱或使用者主體名稱 (UPN) 搜尋特定的目標。 UPN 與目標名稱會區分大小寫。 

**由**篩選條件可讓您定義執行者的名稱或通用主要名稱 (UPN)。 UPN 與名稱會區分大小寫。

**日期範圍**篩選條件可讓您定義傳回資料的時間範圍。  
可能的值包括：

- 1 個月
- 7 天
- 24 小時
- 自訂

當您選取自訂時間範圍時，可以設定開始時間和結束時間。

您也可以選擇下載已篩選的資料，最多 250,000 記錄，方法是選取**下載** 按鈕。 您可以選擇下載 CSV 或 JSON 格式記錄。 您可以下載的記錄數目會受限於 [Azure Active Directory 報告保留原則](reference-reports-data-retention.md)。

![稽核記錄](./media/concept-audit-logs/download.png "稽核記錄")

## <a name="audit-logs-shortcuts"></a>稽核記錄快速鍵

除了 **Azure Active Directory** 之外，Azure 入口網站可提供您稽核資料的兩個額外進入點︰

- 使用者和群組
- 企業應用程式

### <a name="users-and-groups-audit-logs"></a>使用者和群組稽核記錄

透過以使用者和群組為基礎的稽核報告，可以取得下列問題的解答︰

- 使用者已套用哪些類型的更新？

- 有多少使用者已變更？

- 有多少密碼已變更？

- 系統管理員已在目錄中執行哪些作業？

- 已新增的群組為何？

- 群組有成員資格變更嗎？

- 群組的擁有者已變更嗎？

- 指派給群組或使用者的授權為何？

如果您只想檢閱與使用者相關的稽核資料，您可以找到篩選過的檢視之下**稽核記錄檔**中**活動**一節**使用者** 索引標籤。此進入點**UserManagement**做為預先選取的類別目錄。

![稽核記錄](./media/concept-audit-logs/users.png "稽核記錄")

如果您只想檢閱與群組相關的稽核資料，您可以找到篩選過的檢視之下**稽核記錄檔**中**活動**一節**群組** 索引標籤。此進入點**GroupManagement**做為預先選取的類別目錄。

![稽核記錄](./media/concept-audit-logs/groups.png "稽核記錄")

### <a name="enterprise-applications-audit-logs"></a>企業應用程式稽核記錄

透過以應用程式為基礎的稽核報告，可以取得下列問題的解答︰

* 已新增或更新的應用程式為何？
* 已移除的應用程式為何？
* 應用程式的服務原則已變更嗎？
* 應用程式的名稱已變更嗎？
* 誰已同意應用程式？

如果您想檢閱應用程式相關的稽核資料，可以在 [企業應用程式]  刀鋒視窗的 [活動]  區段中的 [稽核記錄]  之下找到篩選過的檢視。 此進入點**企業應用程式**預先選取作為**應用程式類型**。

![稽核記錄](./media/concept-audit-logs/enterpriseapplications.png "稽核記錄")

## <a name="office-365-activity-logs"></a>Office 365 活動記錄

您可以檢視從 Office 365 活動記錄[Microsoft 365 系統管理中心](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)。 即使 Office 365 活動和 Azure AD 活動記錄共用許多目錄資源，只在 Microsoft 365 系統管理中心會提供 Office 365 活動記錄檔的完整檢視。 

您也可以透過使用 [Office 365 管理 API](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)，以程式設計的方式存取 Office 365 活動記錄。

## <a name="next-steps"></a>後續步驟

- [Azure AD 稽核活動參考](reference-audit-activities.md)
- [Azure AD 報告保留參考](reference-reports-data-retention.md)
- [Azure AD 記錄延遲參考](reference-reports-latencies.md)
