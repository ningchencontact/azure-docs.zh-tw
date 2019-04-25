---
title: 針對適用於 Azure 資源的 RBAC 進行疑難排解 | Microsoft Docs
description: 針對適用於 Azure 資源的角色型存取控制 (RBAC) 問題進行疑難排解。
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: c6f947ad6f2f8dba2df17132243eb6d918539c14
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344394"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>針對適用於 Azure 資源的 RBAC 進行疑難排解

此文章將回答適用於 Azure 資源之角色型存取控制 (RBAC) 的常見問題，讓您知道在 Azure 入口網站中使用角色時會有什麼樣的結果，以及如何解決存取權相關問題。

## <a name="problems-with-rbac-role-assignments"></a>RBAC 角色指派的問題

- 如果您就無法在 Azure 入口網站中的角色指派，將**存取控制 （iam） 分頁**因為**新增** > **新增角色指派**選項會停用或因為您收到 「 物件識別碼的用戶端沒有執行動作的授權 」 權限錯誤，請檢查您目前登入的使用者指派的角色`Microsoft.Authorization/roleAssignments/write`權限，例如[擁有者](built-in-roles.md#owner)或是[使用者存取系統管理員](built-in-roles.md#user-access-administrator)在您嘗試將角色指派的範圍內。
- 如果您收到錯誤訊息 「 您可以建立沒有更多的角色指派 (程式碼：RoleAssignmentLimitExceeded)」，請嘗試改為將角色指派給群組，以減少角色指派的數目。 Azure 支援每個訂用帳戶最多 **2000** 個角色指派。

## <a name="problems-with-custom-roles"></a>自訂角色的問題

- 如果您需要如何建立自訂角色的步驟，請參閱使用自訂角色教學課程[Azure PowerShell](tutorial-custom-role-powershell.md)或是[Azure CLI](tutorial-custom-role-cli.md)。
- 如果您無法更新現有的自訂角色時，請檢查您目前登入的使用者指派的角色`Microsoft.Authorization/roleDefinition/write`權限，例如[擁有者](built-in-roles.md#owner)或[使用者存取系統管理員](built-in-roles.md#user-access-administrator).
- 如果您無法刪除自訂角色並收到錯誤訊息「有參考角色的現有角色指派 (代碼：RoleDefinitionHasAssignments)」，則有仍在使用自訂角色的角色指派。 請移除這些角色指派，並試著再次刪除自訂角色。
- 如果您收到「已超過角色定義限制」錯誤訊息。 您可以建立沒有更多的角色定義 (程式碼：RoleDefinitionLimitExceeded) 「 當您嘗試建立新的自訂角色時，刪除不會使用的任何自訂角色。 Azure 支援每個租用戶中最多 **2000** 個自訂角色。
- 如果您收到的錯誤類似於 「 用戶端有對範圍 ' / 訂用帳戶 / {subscriptionid}'，執行動作 'Microsoft.Authorization/roleDefinitions/write'，但找不到連結的訂用帳戶的權限 」 當您嘗試更新自訂角色，請檢查是否一或多個[可指派範圍](role-definitions.md#assignablescopes)已刪除租用戶中。 如果範圍已刪除，則可以建立支援票證，因為此時已沒有可用的自助解決方案。

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>當訂用帳戶在租用戶之間移動時復原 RBAC

- 如果您需要的步驟說明如何以訂用帳戶轉移至不同的 Azure AD 租用戶，請參閱 <<c0> [ 的 Azure 訂用帳戶的擁有權轉移給另一個帳戶](../billing/billing-subscription-transfer.md)。
- 如果您將訂用帳戶轉移至不同的 Azure AD 租用戶，所有角色指派都會從來源 Azure AD 租用戶中永久刪除，而且不會遷移至目標 Azure AD 租用戶。 您必須在目標租用戶中重新建立角色指派。 您也必須以手動方式重新建立適用於 Azure 資源管理的身分識別。 如需詳細資訊，請參閱 <<c0> [ 常見問題集和已知的問題的受管理身分識別](../active-directory/managed-identities-azure-resources/known-issues.md)。
- 如果您是 Azure AD 全域管理員，而且您沒有存取權的訂用帳戶租用戶之間移動之後，請使用**存取 Azure 資源管理**暫時切換[提高您的存取權](elevate-access-global-admin.md)以取得存取權的訂用帳戶。

## <a name="issues-with-service-admins-or-co-admins"></a>服務管理員或共同管理員的問題

- 如果您以服務系統管理員或共同管理員時發生問題，請參閱[新增或變更 Azure 訂用帳戶系統管理員](../billing/billing-add-change-azure-subscription-administrator.md)和[傳統訂用帳戶系統管理員角色，Azure RBAC 角色，與 Azure AD系統管理員角色](rbac-and-directory-admin-roles.md)。

## <a name="access-denied-or-permission-errors"></a>拒絕存取或權限錯誤

- 如果在嘗試建立資源時收到權限錯誤「具有物件識別碼的用戶端沒有在範圍中執行動作的權限 (錯誤碼：AuthorizationFailed)」，請檢查您目前用於登入的使用者是否獲派對所選範圍內的資源具有寫入權限的角色。 例如，若要管理資源群組中的虛擬機器，您應該具有資源群組 (或父範圍) 的[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)角色。 如需每個內建角色的權限清單，請參閱 [Azure 資源的內建角色](built-in-roles.md)。
- 如果您收到 「 您沒有建立支援要求的權限 」 權限錯誤時您嘗試建立或更新支援票證，請檢查您目前登入的使用者指派的角色具有`Microsoft.Support/supportTickets/write`權限，如[支援要求參與者](built-in-roles.md#support-request-contributor)。

## <a name="rbac-changes-are-not-being-detected"></a>偵測不到 RBAC 的變更

Azure Resource Manager 有時候會快取組態和資料來改善效能。 建立或刪除角色指派時，這些變更可能需要 30 分鐘的時間才會生效。 如果您使用 Azure 入口網站、Azure PowerShell 或 Azure CLI，您可以藉由登出再登入，來強制重新整理角色指派變更。 如果您使用 REST API 呼叫來變更角色指派，您可以重新整理存取權杖來強制重新整理。

## <a name="web-app-features-that-require-write-access"></a>需要寫入存取權的 Web 應用程式功能

如果您授與對單一 Web 應用程式授與使用者唯讀存取權限，部分功能可能會在未預期的情況下停用。 以下管理功能需要 Web 應用程式的**寫入**存取權限 (參與者或擁有者)，而且無法在任何唯讀情況中使用。

* 命令 (像是開始、停止等)
* 更改设置（如常规配置、缩放设置、备份设置和监视设置）
* 存取發佈認證與其他機密，像是應用程式設定與連接字串
* 串流記錄
* 診斷記錄組態
* 主控台 (命令提示字元)
* 有效的近期部署項目 (以便本機 git 持續部署)
* 預估的花費
* Web 測試
* 虛擬網路 (只有當虛擬網路先前是由具備寫入存取權限的使用者所設定，才會顯示出來)。

如果您無法存取上述任何一個磚，請洽詢您的系統管理員，取得 Web 應用程式的參與者存取權限。

## <a name="web-app-resources-that-require-write-access"></a>需要寫入存取權的 Web 應用程式資源

Web 應用程式因為幾個互有關聯的資源而顯得複雜。 以下是具有多個網站的典型資源群組：

![Web 應用程式資源群組](./media/troubleshooting/website-resource-model.png)

如此一來，如果您只授予某人 Web 應用程式存取權限，Azure 入口網站的網站刀鋒視窗上的諸多功能即會停用。

這些項目需要對應至您網站的「應用程式服務方案」的**寫入**權：  

* 檢視 Web 應用程式的定價層 (免費或標準)  
* 調整組態 (執行個體數量、虛擬機器大小、自動調整設定)  
* 配额（存储空间、带宽、CPU）  

這些項目都需要包含您網站的整個「資源群組」的**寫入**權：  

* SSL 憑證與繫結 (相同資源群組與地理位置中的各個網站之間，可共用 SSL 憑證)  
* 警示規則  
* 自動調整設定  
* 應用程式見解元件  
* Web 測試  

## <a name="virtual-machine-features-that-require-write-access"></a>需要寫入存取權的虛擬機器功能

類似於 Web 應用程式，虛擬機器刀鋒視窗上的某些功能需要具備虛擬機器 (或是資源群組中的其他資源) 的寫入存取權限。

虛擬機器與網域名稱、虛擬網路、儲存體帳戶及警示規則相關。

這些項目都需要具備「虛擬機器」的**寫入**權：

* 端點  
* IP 位址  
* 磁碟  
* 擴充功能  

這些項目都需要同時具備「虛擬機器」與所屬之「資源群組」(連同網域名稱) 的**寫入**權：  

* 可用性設定組  
* 負載平衡集合  
* 警示規則  

如果您無法存取上述任何一個磚，請洽詢您的系統管理員，以取得資源群組的參與者存取權限。

## <a name="azure-functions-and-write-access"></a>Azure Functions 和寫入權限

[Azure Functions](../azure-functions/functions-overview.md) 的某些功能需要寫入存取權。 例如，如果指派使用者讀者角色，他們將無法檢視函數應用程式內的函數。 入口網站將顯示 **(無存取權)**。

![函數應用程式無存取權](./media/troubleshooting/functionapps-noaccess.png)

讀者可以按一下 [平台功能] 索引標籤，然後按一下 [所有設定] 以檢視與函數應用程式相關的一些設定 (類似於 Web 應用程式)，但他們不能修改這些設定。

## <a name="next-steps"></a>後續步驟
* [使用 RBAC 和 Azure 入口網站管理 Azure 資源的存取權](role-assignments-portal.md)
* [檢視活動記錄中 Azure 資源的各種 RBAC 變更](change-history-report.md)

