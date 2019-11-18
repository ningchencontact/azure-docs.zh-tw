---
title: Azure 資訊安全中心中的工作流程自動化（預覽） |Microsoft Docs
description: 瞭解如何在 Azure 資訊安全中心中建立和自動化工作流程
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 1abfd8af7e0ef18d4e7bcf05b4726c5d0bcb0a84
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151702"
---
# <a name="workflow-automation-preview"></a>工作流程自動化（預覽）

每個安全性程式都包含事件回應的多個工作流程。 這些流程可能包括通知相關的專案關係人、啟動變更管理程式，以及套用特定的補救步驟。 安全性專家建議您盡可能地自動化這些程式的許多步驟。 自動化可減少額外負荷。 它也可以藉由確保按照您預先定義的需求快速、一致地完成程式步驟，來改善您的安全性。

本文說明 Azure 資訊安全中心的工作流程自動化功能（預覽）。 此預覽功能可以觸發安全性警示和建議的 Logic Apps。 例如，您可能會想要資訊安全中心在警示發生時，以電子郵件傳送特定的使用者。 您也將瞭解如何使用[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)建立 Logic Apps。

> [!NOTE]
> 如果您先前已在提要欄位上使用操作手冊（預覽），您會在 [新增工作流程自動化（預覽）] 頁面中找到相同的功能和擴充的功能。


## <a name="requirements"></a>需求

* 若要使用 Azure Logic Apps 工作流程，您必須具有下列 Logic Apps 角色/許可權：

    * [邏輯應用程式操作員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator)許可權是必要的或邏輯應用程式讀取/觸發程式存取（此角色無法建立或編輯邏輯應用程式; 只*執行*現有的）

    * 邏輯應用程式的建立和修改必須要有[邏輯應用程式參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor)許可權

* 如果您想要使用邏輯應用程式連接器，您可能需要額外的認證來登入其各自的服務（例如，您的 Outlook/小組/時差實例）


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>建立邏輯應用程式，並定義應該自動執行的時機 

1. 從資訊安全中心的提要欄位中，選取 **[工作流程自動化（預覽）** ]。

    [![工作流程自動化清單](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    在此頁面中，您可以建立新的自動化規則，以及啟用、停用或刪除現有的。  
1. 若要定義新的工作流程，請按一下 [**新增工作流程自動化**]。 

    隨即會出現一個窗格，其中包含新自動化的選項。 您可以在這裡輸入：
    1. 自動化的名稱和描述。
    1. 將起始這個自動工作流程的觸發程式。 例如，您可能會想要在產生包含 "SQL" 的安全性警示時執行邏輯應用程式。
    1. 符合觸發條件時將執行的邏輯應用程式。 

        [![工作流程自動化清單](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. 從 [動作] 區段中，按一下 [**建立新**的]，以開始建立邏輯應用程式。

    您將會進入 Azure Logic Apps。

    [![建立新的邏輯應用程式](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. 輸入 [名稱]、[資源群組] 和 [位置]，然後按一下 [**建立**]。

1. 在您的新邏輯應用程式中，您可以從 [安全性] 類別中的內建預先定義範本進行選擇。 或者，您可以定義觸發此進程時所要發生的自訂事件流程。

    在邏輯應用程式設計工具中，支援來自資訊安全中心連接器的下列觸發程式：

    * **建立或觸發 Azure 資訊安全中心建議時（預覽）**
    * **建立或觸發 Azure 資訊安全中心警示時（預覽）**
    
    > [!NOTE]
    > 如果您使用舊版觸發程式「觸發對 Azure 資訊安全中心警示的回應」，則您的 Logic Apps 將不會由工作流程自動化功能啟動。 相反地，請使用上述其中一個觸發程式。 

    [![範例邏輯應用程式](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. 定義邏輯應用程式之後，返回 [工作流程自動化定義] 窗格（[新增工作流程自動化]）。 按一下 [重新整理] 以確保您的新邏輯應用**程式可供**選擇。

    ![重新整理](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. 選取您的邏輯應用程式並儲存自動化。 請注意，[邏輯應用程式] 下拉式清單只會顯示上述支援資訊安全中心連接器 Logic Apps。


## <a name="manually-trigger-a-logic-app"></a>手動觸發邏輯應用程式

您也可以在查看安全性建議時，手動執行 Logic Apps。

若要手動執行邏輯應用程式，請開啟建議，然後按一下 [觸發邏輯應用程式（預覽）]：

[![手動觸發邏輯應用程式](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何建立 Logic Apps、以手動方式在資訊安全中心中執行這些工作，並將其執行自動化。 

如需其他相關內容，請參閱下列文章： 

- [Azure 資訊安全中心中的安全性建議](security-center-recommendations.md)
- [Azure 資訊安全中心的安全性警示](security-center-alerts-overview.md)
- [關於 Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Logic Apps 連接器](https://docs.microsoft.com/connectors/)
