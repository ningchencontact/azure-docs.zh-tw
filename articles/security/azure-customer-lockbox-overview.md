---
title: 客戶 Lockbox 適用於 Microsoft Azure
description: 技術概觀說明適用於 Microsoft Azure，Microsoft 可能會需要存取客戶資料時，可讓雲端提供者存取控制客戶 Lockbox。
author: cabailey
ms.service: security
ms.topic: article
ms.author: cabailey
manager: barbkess
ms.date: 06/20/2019
ms.openlocfilehash: 0ee2dde5a941d069f5b745eafb35df780f657a47
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312628"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>客戶 Lockbox 適用於 Microsoft Azure

> [!NOTE]
> 若要使用這項功能，您的組織必須擁有[Azure 支援計劃](https://azure.microsoft.com/support/plans/)最低層級**開發人員**。

客戶 Lockbox 適用於 Microsoft Azure 提供的介面，讓客戶以檢閱並核准或拒絕使用者的客戶資料存取要求。 它用於 Microsoft 工程師一定要在支援要求存取客戶資料的情況。

本文涵蓋如何起始、 追蹤，並儲存以供稍後檢閱和稽核客戶 Lockbox 要求。

客戶 Lockbox 現已正式推出，目前已啟用虛擬機器的遠端桌面存取。

## <a name="workflow"></a>工作流程

下列步驟概述客戶 Lockbox 要求的一般工作流程。

1. 組織必須有人使用他們的 Azure 工作負載的問題。

2. 這位人員疑難排解此問題，但無法修正此問題之後，請開啟支援票證[Azure 入口網站](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)。 票證會指派給 Azure 客戶支援工程師。

3. Azure 支援工程師會檢閱服務要求，並判斷後續的步驟來解決此問題。

4. 如果支援工程師不能使用標準的工具和遙測來疑難排解此問題下, 一個步驟是使用 Just-In-Time (JIT) 存取服務，要求提高權限。 此要求可以從原始的支援工程師。 或者，因為問題擴大到 Azure 的 DevOps 小組，它可以是從不同的工程師。

5. Azure 工程師 Just In Time 存取之後提交要求服務會評估要求，將帳戶因素，例如：
    - 資源的範圍
    - 要求者是否隔離的身分識別，或使用多重要素驗證
    - 權限層級
    
    根據 JIT 規則，這項要求也可能包括從內部 Microsoft 核准者核准。 例如，核准者可能客戶支援負責人或 DevOps 管理員。

6. 當要求需要直接存取客戶資料時，會起始客戶 Lockbox 要求。 例如，遠端桌面存取客戶的虛擬機器。
    
    要求現在處於**客戶通知**狀態，等候客戶的核准才能授予存取權。

7. 在客戶組織，使用者的人員[擁有者角色](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles)適用於 Azure 訂用帳戶會收到一封電子郵件從 Microsoft 取得，以通知擱置中的存取要求。 客戶 Lockbox 要求，這位使用者是指定的核准者。
    
    範例電子郵件：
    
    ![Azure 客戶 Lockbox-電子郵件通知](./media/azure-customer-lockbox/customer-lockbox-email-notification.png)

8. 電子郵件通知會提供通往**客戶 Lockbox**刀鋒視窗中，在 Azure 入口網站中的。 使用此連結，指定的核准者登入 Azure 入口網站中檢視任何擱置中的組織有客戶 Lockbox 的要求：
    
    ![Azure 客戶 Lockbox-登陸頁面](./media/azure-customer-lockbox/customer-lockbox-landing-page.png)
    
   要求會四天的保留客戶佇列中。 此時間之後，存取要求自動到期，並沒有存取權會授與 Microsoft 工程師。

9. 若要取得的暫止要求的詳細資訊，請指定核准者可以選取的 lockbox 要求**暫止要求**:
    
    ![Azure 客戶 Lockbox-檢視暫止的要求](./media/azure-customer-lockbox/customer-lockbox-pending-requests.png)

10. 指定的核准者也可以選取**服務要求識別碼**檢視由原始使用者所建立的支援票證要求。 這項資訊提供內容為什麼參與 Microsoft 支援服務，而報告的問題的歷程記錄。 例如:
    
    ![Azure 客戶 Lockbox-檢視支援票證要求](./media/azure-customer-lockbox/customer-lockbox-support-ticket.png)

11. 檢閱之後的要求，請選取指定的核准者**核准**或是**拒絕**:
    
    ![Azure 客戶 Lockbox-選取 核准 或 拒絕](./media/azure-customer-lockbox/customer-lockbox-approval.png)
    
    由於選取項目：
    - **核准**:Microsoft 工程師會授與存取權。 一段的八個小時的預設時間是授與存取權。
    - **拒絕**:提高權限的存取要求，由 Microsoft 工程師會遭到拒絕，而且會採取任何進一步的動作。

作為稽核用途，此工作流程所採取的動作會記錄在[客戶 Lockbox 要求記錄檔](#auditing-logs)。

## <a name="auditing-logs"></a>稽核記錄

客戶 Lockbox 記錄會儲存活動記錄中。 在 Azure 入口網站中，選取**活動記錄**若要檢視與客戶 Lockbox 要求相關的稽核資訊。 您可以如篩選為特定的動作：
- **拒絕 Lockbox 要求**
- **建立 Lockbox 要求**
- **核准 Lockbox 要求**
- **Lockbox 要求到期**

例如：

![Azure 客戶 Lockbox-活動記錄](./media/azure-customer-lockbox/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios"></a>支援的服務和案例

下列服務和案例目前已正式客戶 Lockbox 的可用性。

### <a name="remote-desktop-access-to-virtual-machines"></a>虛擬機器的遠端桌面存取

客戶 Lockbox 目前已啟用虛擬機器的遠端桌面存取要求。 支援下列工作負載：
- 平台即服務 (PaaS)-Azure 雲端服務 （web 角色和背景工作角色）
- 基礎結構即服務 (IaaS)-Windows 和 Linux (僅限 Azure Resource Manager)
- 虛擬機器擴展集-Windows 和 Linux

> [!NOTE]
> 客戶 Lockbox 不支援 IaaS Classic 執行個體。 如果您有傳統 IaaS 執行個體上執行的工作負載，我們建議您移轉從傳統到 Resource Manager 部署模型。 如需相關指示，請參閱 <<c0> [ 平台支援移轉 IaaS 資源從傳統至 Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-overview.md)。

#### <a name="detailed-audit-logs"></a>詳細的稽核記錄檔

牽涉到遠端桌面存取的情況下，您可以使用 Windows 事件記錄檔，以檢閱 Microsoft 工程師所採取的動作。 請考慮使用 Azure 資訊安全中心收集的事件記錄檔，並將資料複製到工作區進行分析。 如需詳細資訊，請參閱 < [Azure 資訊安全中心的資料收集](../security-center/security-center-enable-data-collection.md)。

## <a name="exclusions"></a>排除

客戶 Lockbox 要求不在下列的工程支援情況下觸發：

- Microsoft 工程師必須執行超出標準作業程序的活動。 例如，如果復原或還原在兩個案例中有非預期或無法預測的服務。

- Microsoft 工程師存取 Azure 平台一部分的疑難排解，而不小心有權存取客戶資料。 例如，Azure 網路小組會執行疑難排解導致封包擷取的網路裝置上。 不過，如果在傳輸期間，客戶會加密資料，此工程師無法讀取資料。

## <a name="next-steps"></a>後續步驟

客戶 Lockbox 會自動提供的所有客戶[Azure 支援計劃](https://azure.microsoft.com/support/plans/)最低層級**開發人員**。

當您有合格的支援方案時，不需要採取任何動作是您要啟用客戶 Lockbox。 客戶 Lockbox 要求是由 Microsoft 工程師起始，如果這個動作需要進行提報有人在您的組織中的支援票證。
