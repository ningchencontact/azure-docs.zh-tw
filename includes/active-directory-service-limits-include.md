---
title: 包含檔案
description: 包含檔案
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/21/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 38f2dd301ddc2a5f8d28322856b2011bd2034c30
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554621"
---
以下是 Azure Active Directory (Azure AD) 服務的使用條件約束和其他服務限制。

| 類別 | 限制 |
| --- | --- |
| 目錄 | 單一使用者能以成員或來賓的身分，隸屬於最多 500 個 Azure AD 目錄。<br/>單一使用者最多可以建立 20 個目錄。 |
| 網域 | 您可以新增 900 個以內的受控網域名稱。 如果您設定所有與內部部署 Active Directory 的同盟網域，您可以新增不超過 450 個網域名稱，每個目錄中。 |
| 物件 |<ul><li>在免費版的 Azure Active Directory 中，最多可以在單一目錄中建立 500,000 個物件。</li><li>非系統管理員的使用者最多可以建立 250 個物件。 作用中的物件和已刪除的物件，可用來還原至這個配額的計數。 只有已刪除的已刪除的物件少於 30 天就會是可用來還原。 已刪除的物件不再可還原這個配額的值都算是 30 天的四分之一。 或許[指派系統管理員角色](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md)可能重複超過這個配額，在他們的一般工作的過程中的非系統管理員使用者。</li></ul> |
| 結構描述延伸模組 |<ul><li>字串類型延伸模組可以有最多 256 個字元。 </li><li>二進位類型延伸模組受限於 256 個位元組。</li><li>只有 100 個延伸模組值，跨*所有*類型並*所有*應用程式，可以撰寫任何單一物件。</li><li>唯一的使用者、 群組、 TenantDetail、 裝置、 應用程式和 ServicePrincipal 實體可以擴充以字串型別或二進位類型單一值屬性。</li><li>結構描述延伸模組是僅適用於圖形 API 版本 1.21 預覽。 應用程式必須取得寫入權限才能登錄延伸模組。</li></ul> |
| [應用程式] |最多 100 個使用者可以成為單一應用程式的擁有者。 |
| 群組 |<ul><li>最多 100 個使用者可以成為單一群組的擁有者。</li><li>任何數目的物件都可以是單一群組的成員。</li><li>使用者可以是任意數目群組的成員。</li><li>您可以從同步處理您的內部部署 Active Directory 到 Azure Active Directory 使用 Azure AD Connect 群組中的成員數目僅限於 50,000 個成員。</li></ul> |
| 存取面板 |<ul><li>可以看到每位使用者在存取面板中的應用程式數目沒有限制。 這適用於使用者已指派 Azure AD Premium 或 Enterprise Mobility Suite 的授權。</li><li>每個使用者的存取面板中可以看到最多 10 個應用程式磚。 這項限制適用於免費指派授權的使用者或 Azure AD Basic 版的 Azure Active Directory。 應用程式磚的範例包括方塊、 Salesforce 或 Dropbox。 這項限制不適用於系統管理員帳戶。</li></ul> |
| 報告 | 在任何報告中，最多可以檢視或下載 1000 個資料列。 系统会截断其他任何数据。 |
| 管理單位 | 物件可以是有不超過 30 個管理單位的成員。 |
| 系統管理員角色與權限 | <li>無法加入群組的擁有者身分。<li>群組無法指派給角色。<li>預設權限無法變更除了租用戶的參數，也就是在 Azure AD 中的使用者設定的使用者。 |
