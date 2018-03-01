---
title: "建立活動記錄警示並使用 Azure 監視器中的新警示 (預覽) 體驗來管理 | Microsoft Docs"
description: "本文提供的資訊說明如何從 Azure 監視器下的 [警示 (預覽)] 索引標籤建立活動記錄警示。 本文詳細說明此功能的新使用者經驗。"
author: JYOTHIRMAISURI
manager: vvithal
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: aabc0e57-78cd-44dd-a8d1-af5e1e567360
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: v-jysur
ms.custom: 
ms.openlocfilehash: afdd82617c47f0dee22c229feba87bdf79b90a69
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="create-activity-log-alerts-using-the-new-alerts-preview-experience"></a>使用新的警示 (預覽) 體驗建立活動記錄警示

活動記錄警示是發生符合警示中指定條件的新活動記錄事件時所啟動的警示。

這些警示都適用於 Azure 資源，可以使用 Azure Resource Manager 範本來建立。 也可以在 Azure 入口網站中將它們建立、更新或刪除。 本文介紹活動記錄警示背後的概念。 接著說明如何使用 Azure 入口網站，利用 [Azure 警示 (預覽)](monitoring-overview-unified-alerts.md) 中的新體驗來設定活動記錄事件的警示。

一般而言，您可以建立活動記錄警示，以在 Azure 訂用帳戶中的資源發生特定變更時接收通知 (通常會將範圍限定在特定資源群組或資源)。 例如，在 (範例資源群組) **myProductionResourceGroup** 中的任何虛擬機器被刪除時，您可能會想要收到通知，或者，您可能想要在有任何新角色被指派給訂用帳戶中的使用者時收到通知。

您可以針對活動記錄事件，以 JSON 物件中任何最上層屬性作為基礎設定活動記錄警示。 不過，入口網站會顯示最常用的選項，詳細資料如下列各節中所示：

>[!NOTE]

> 當類別為「系統管理」時，您必須在警示中指定至少一個上述準則。 您不能建立會在每次活動記錄中建立事件時即啟動的警示。
>

當活動記錄警示啟動時，會使用動作群組來產生動作或通知。 動作群組是一組可重複使用的通知接收者，例如電子郵件地址、Webhook URL 或 SMS 電話號碼。 可以從多個警示參考接收者，將通知通道集中管理並群組。 當您定義活動記錄警示時，會有兩個選項。 您可以：

* 在您的活動記錄警示中使用現有的動作群組。
* 建立新的動作群組。

若要深入了解動作群組，請參閱[在 Azure 入口網站中建立和管理動作群組](monitoring-action-groups.md)。

若要深入了解服務健康情況通知，請參閱[在服務健康情況通知上接收活動記錄警示](monitoring-activity-log-alerts-on-service-notifications.md)。


## <a name="whats-new-in-alerts-preview-for-activity-logs"></a>活動記錄的警示 (預覽) 中有哪些新增功能？

[Azure 警示 (預覽)](monitoring-overview-unified-alerts.md) 現在加強活動記錄警示的使用者體驗。 有了[加強的警示使用者經驗](monitoring-overview-unified-alerts.md)，您現在可以：

- 從[監視器] > [警示 (預覽)] 刀鋒視窗[建立](#create-an-alert-rule-for-an-activity-log)及[管理](#view-and-manage-activity-log-alert-rules)活動記錄警示規則。 深入了解[活動記錄](monitoring-overview-activity-logs.md)。

- **新的警示目標選項**：在建立新的活動記錄警示規則時，您現在可以選取目標資源、資源群組或訂用帳戶。


## <a name="create-an-alert-rule-for-an-activity-log"></a>建立活動記錄的警示規則

> [!NOTE]

>  在建立警示規則時，請確定下列各項：

> - 範圍中的訂用帳戶和警示建立所在訂用帳戶並無不同。
- 設定警示必須以層級/狀態/呼叫端/資源群組/資源識別碼/資源類型/事件類別目錄為準則。
- 警示設定 JSON 中不得有 “anyOf” 條件或巢狀條件 (基本上，只允許 allOf，不得進一步使用 allOf/anyOf)。


請使用下列程序：

1. 從 Azure 入口網站選取 [監視器] > [警示 (預覽)]。
2. 按一下 [警示 (預覽)] 視窗頂端的 [新增警示規則]。

     ![新增警示規則](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule.png)

     [建立規則] 視窗隨即出現。

      ![新增警示規則選項](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. 在 [定義警示條件] 下提供下列資訊，然後按一下 [完成]。

    - **警示目標：**若要檢視和選取新警示的目標，請使用 [依訂用帳戶篩選] / [依資源類型篩選]，然後從顯示的清單中選取資源或資源群組。

    > [!NOTE]

    > 您可以選取資源、資源群組或整個訂用帳戶。

    **警示目標範例檢視**

     ![選取目標](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - 在 [目標準則] 下，按一下 [新增準則]，然後將訊號類型選取為 [活動記錄]。

    - 從顯示的清單中選取訊號。

    您可以選取此目標訊號的記錄歷程記錄時間軸和對應的警示邏輯：

    **新增準則畫面**

    ![新增準則](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **記錄時間**：過去 6/12/24 小時內、過去一週內。

    **警示邏輯**：

        - **Event Level**- The severity level of the event.**Verbose,Informational, Warning, Error**, or **Critical**.
        - **Status**: The status of the event.**Started, Failed**, or **Succeeded**.
        - **Event initiated by**: Also known as the caller; The email address or Azure Active Directory identifier of the user who performed the operation.

        **Sample signal graph with alert logic applied** :

        ![ criteria selected](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. 在 [定義警示規則] 下，提供下列詳細資料：

    - **警示規則名稱**：新警示規則的名稱
    - **描述**︰新警示規則描述
    - **將警示儲存到資源群組**：選取您儲存此新規則的資源群組。

5. 在 [動作群組] 下，從下拉式功能表中指定您想要指派給此新警示規則的動作群組。 或者，[建立新的動作群組](monitoring-action-groups.md)並指派給新規則。 若要建立新群組，請按一下 [+ 新增群組]。

6. 若要在建立規則之後啟用，請按一下 [在建立時啟用規則] 選項的 [是]。
7. 按一下 [建立警示規則]。

    活動記錄的新警示規則隨即建立，而且視窗右上方會顯示確認訊息。

    ![ 已建立警示規則](./media/monitoring-activity-log-alerts-new-experience/alert-created.png)

    您可以啟用、停用、編輯或刪除規則。 [深入了解](#view-and-manage-activity-log-alert-rules)如何管理活動記錄規則。

## <a name="view-and-manage-activity-log-alert-rules"></a>檢視和管理活動記錄警示規則

1. 從 Azure 入口網站按一下 [監視器] > [警示 (預覽)]，然後按一下視窗左上方的 [管理規則]。

    ![ 管理警示規則](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    可用規則清單隨即出現。

2. 搜尋要修改的活動記錄規則。

    ![ 搜尋活動記錄警示規則](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    您可以使用可用的篩選條件 ([訂用帳戶]、[資源群組] 或 [資源]) 來尋找您想要編輯的活動規則。

    > [!NOTE]

    > 您只能編輯 [描述]、[目標準則] 與 [動作群組]。

3.  選取規則，然後按兩下以編輯規則選項。 進行所需的變更，然後按一下 [儲存]。

    ![ 管理警示規則](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  您可以停用、啟用或刪除規則。 選取規則之後，在視窗頂端選取適當選項，詳細資料如步驟 2 所示。


## <a name="next-steps"></a>後續步驟

- [封存活動記錄警示](monitoring-archive-activity-log.md)
- [將活動記錄串流至事件中樞](monitoring-stream-activity-logs-event-hubs.md)
- [移轉至活動記錄](monitoring-migrate-management-alerts.md)
