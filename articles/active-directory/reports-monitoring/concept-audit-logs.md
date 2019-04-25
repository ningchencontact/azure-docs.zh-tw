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
ms.openlocfilehash: 3da96c09026baff3965e0a90d1f461fd948a3a50
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440928"
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

* 具有**安全管理员**、**安全读取者**、**报表读取者**或**全局管理员**角色的用户
* 此外，所有使用者 (非系統管理員) 都可看到自己的稽核活動

## <a name="audit-logs"></a>稽核記錄

Azure AD 稽核記錄會提供符合規範的系統活動記錄。 若要存取稽核報告，請選取 [Azure Active Directory] 中 [活動] 區段上的 [稽核記錄]。 請注意，稽核記錄可能會延遲最多一小時，所以稽核活動資料可能會在您完成該工作的一個小時後，才會出現在入口網站中。

![稽核記錄檔](./media/concept-audit-logs/61.png "稽核記錄檔")

稽核記錄的預設清單檢視顯示︰

- 發生時間與日期
- 记录了匹配项的服务
- 活动的类别和名称（内容） 
- 活动的状态（成功或失败）
- 目標
- 活動的啟動者/執行者 (對象)

![稽核記錄檔](./media/concept-audit-logs/listview.png "稽核記錄檔")

您可以按一下工具列中的 [資料行] 來自訂清單檢視。

![稽核記錄檔](./media/concept-audit-logs/columns.png "稽核記錄檔")

這可讓您顯示其他欄位，或移除已顯示的欄位。

![稽核記錄檔](./media/concept-audit-logs/columnselect.png "稽核記錄檔")

選取清單檢視中的項目，即可取得更詳細的資訊。

![稽核記錄檔](./media/concept-audit-logs/details.png "稽核記錄檔")


## <a name="filtering-audit-logs"></a>篩選稽核記錄

您可以在下列欄位上篩選稽核資料：

- 服務
- 類別
- 活動
- 狀態
- 目標
- 啟動者 (執行者)
- 日期範圍

![稽核記錄檔](./media/concept-audit-logs/filter.png "稽核記錄檔")

可以通过“服务”筛选器从包含以下服务的下拉列表中进行选择：

- 全部
- 存取權檢閱
- 帳戶佈建 
- 应用程序 SSO
- 身份验证方法
- B2C
- 條件式存取
- 核心目錄
- 權利管理
- 身分識別保護
- 受邀的使用者
- PIM
- 自助式群組管理
- 自助服务密码管理
- 使用條款

“类别”筛选器用于选择下述筛选器之一：

- 全部
- AdministrativeUnit
- ApplicationManagement
- Authentication
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

“活动”筛选器基于类别以及所做的活动资源类型选择。 您可以選取您想要查看的特定活動或選擇全部。 

您可以使用圖形 API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta 來取得所有稽核活動的清單 (其中，$tenantdomain 是網域名稱)，或請參閱[稽核報告事件](reference-audit-activities.md)一文。

可以使用“状态”筛选器根据审核操作的状态进行筛选。 状态可以是下列其中一项：

- 全部
- 成功
- 失敗

可以通过“目标”筛选器按名称或用户主体名称 (UPN) 搜索特定目标。 目标名称和 UPN 区分大小写。 

“发起者”筛选器用于定义参与者的名称或通用主体名称 (UPN)。 名称和 UPN 区分大小写。

“日期范围”筛选器用于定义已返回数据的时间范围。  
可能的值包括：

- 1 個月
- 7 天
- 24 小時
- 自訂

當您選取自訂時間範圍時，可以設定開始時間和結束時間。

也可选择下载筛选的数据（多达 250,000 条记录），只需选择“下载”按钮即可。 可以选择以 CSV 或 JSON 格式下载日志。 您可以下載的記錄數目會受限於 [Azure Active Directory 報告保留原則](reference-reports-data-retention.md)。

![稽核記錄檔](./media/concept-audit-logs/download.png "稽核記錄檔")

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

如果只想查看与用户相关的审核数据，可以在“用户”选项卡的“活动”部分中的“审核日志”下方查找筛选视图。此入口点已将 **UserManagement** 作为预先选择的类别。

![稽核記錄檔](./media/concept-audit-logs/users.png "稽核記錄檔")

如果只想查看与组相关的审核数据，可以在“组”选项卡的“活动”部分中的“审核日志”下方查找筛选视图。此入口点已将 **GroupManagement** 作为预先选择的类别。

![稽核記錄檔](./media/concept-audit-logs/groups.png "稽核記錄檔")

### <a name="enterprise-applications-audit-logs"></a>企業應用程式稽核記錄

透過以應用程式為基礎的稽核報告，可以取得下列問題的解答︰

* 已新增或更新的應用程式為何？
* 已移除的應用程式為何？
* 應用程式的服務原則已變更嗎？
* 應用程式的名稱已變更嗎？
* 誰已同意應用程式？

如果您想檢閱應用程式相關的稽核資料，可以在 [企業應用程式] 刀鋒視窗的 [活動] 區段中的 [稽核記錄] 之下找到篩選過的檢視。 此入口点已将“企业应用程序”预先选择为“应用程序类型”。

![稽核記錄檔](./media/concept-audit-logs/enterpriseapplications.png "稽核記錄檔")

## <a name="office-365-activity-logs"></a>Office 365 活動記錄

可以从 [Microsoft 365 管理中心](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)查看 Office 365 活动日志。 尽管 Office 365 活动和 Azure AD 活动日志共享大量的目录资源，但只有 Microsoft 365 管理中心提供 Office 365 活动日志的完整视图。 

您也可以透過使用 [Office 365 管理 API](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)，以程式設計的方式存取 Office 365 活動記錄。

## <a name="next-steps"></a>後續步驟

- [Azure AD 稽核活動參考](reference-audit-activities.md)
- [Azure AD 報告保留參考](reference-reports-data-retention.md)
- [Azure AD 記錄延遲參考](reference-reports-latencies.md)
