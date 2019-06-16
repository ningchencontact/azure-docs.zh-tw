---
title: Azure 受控應用程式的啟用和要求-just-in-time 存取
description: 描述 Azure 受控應用程式的發行者如何要求在 just-in-time 存取受管理的應用程式。
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: ea933f5382cb42c01de523326b094c1813401132
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481770"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Azure 受控應用程式的啟用和要求-just-in-time 存取

受控應用程式的取用者可能不願意將您永久存取權授與受控的資源群組。 為管理員應用程式的發行者，您可能會偏好取用者知道需要用來存取受管理的資源。 消費者提供對受管理的資源，Azure 受控應用程式的存取權授與更多控制會提供稱為在 just-in-time (JIT) 存取，目前為預覽狀態的功能。

JIT 存取可讓您要求提高權限的資源的存取權的受管理應用程式進行疑難排解或維護。 您一律可以唯讀存取資源，但對於特定時段中，您可以有更大的存取。

存取權授與的工作流程是：

1. 您將新增到 marketplace 的受管理應用程式，並指定使用 JIT 存取的。

1. 在部署期間，取用者可讓受管理的應用程式的該執行個體的 JIT 存取權。

1. 在部署後，取用者可以變更 JIT 存取的設定。

1. 當您需要疑難排解或更新受控的資源時，您就會傳送要求以進行存取。

1. 取用者核准您的要求。

本文著重於 「 發行者 」 以啟用 JIT 存取，以及送出要求所採取的動作。 若要深入了解核准 JIT 存取要求，請參閱[核准 Azure 受控應用程式中的-just-in-time 存取](approve-just-in-time-access.md)。

## <a name="add-jit-access-step-to-ui"></a>將 JIT 存取步驟新增至 UI

CreateUiDefinition.json 檔案是完全一樣的 UI 檔案建立永久的存取，但您必須包含可讓取用者啟用 JIT 存取的步驟。 若要深入了解發行第一個受管理應用程式在 Azure Marketplace 供應項目，請參閱[在 Marketplace 中的 Azure 受控應用程式](publish-marketplace-app.md)。

若要支援您的供應項目 JIT 功能，請先 CreateUiDefinition.json 檔案中加入下列內容：

在 「 步驟 」:

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```
 
在 「 輸出 」:

```json
"jitAccessPolicy": "[parse(concat('{\"jitAccessEnabled\":', string(steps('jitConfiguration').jitConfigurationControl.jitEnabled), ',\"jitApprovalMode\":\"', steps('jitConfiguration').jitConfigurationControl.jitApprovalMode, '\",\"maximumJitAccessDuration\":\"', steps('jitConfiguration').jitConfigurationControl.maxAccessDuration, '\",\"jitApprovers\":', string(steps('jitConfiguration').jitConfigurationControl.approvers), '}'))]"
```

> [!NOTE]
> JIT 存取為預覽狀態。 JIT 組態結構描述無法變更在未來的反覆項目。

## <a name="enable-jit-access"></a>啟用 JIT 存取

當定義您的供應在 marketplace 中，請確定您啟用 JIT 存取權。

1. 登入[Cloud Partner 發佈入口網站](https://cloudpartner.azure.com)。

1. 提供在 marketplace 中發行受控的應用程式的值。 選取  **是**如**啟用 JIT 存取嗎？**

   ![啟用 Just-In-Time 存取](./media/request-just-in-time-access/marketplace-enable.png)

您已將 JIT 組態步驟加入您的 UI，並啟用 JIT 存取 marketplace 供應項目。 當取用者部署受控的應用程式時，他們可以[開啟 JIT 存取個別執行個體](approve-just-in-time-access.md#enable-during-deployment)。

## <a name="request-access"></a>要求存取

當您需要存取取用者的受管理的資源時，您就會傳送特定的角色、 時間和持續時間的要求。 然後，取用者必須核准要求。

若要傳送的 JIT 存取要求：

1. 選取  **JIT 存取**您需要存取受管理的應用程式。

1. 選取**合格的角色**，然後選取**Activate**您想要的角色的 [動作] 欄中。

   ![啟用存取要求](./media/request-just-in-time-access/send-request.png)

1. 在 **啟用角色**表單上，選取 開始時間和持續時間，您要使用的角色。 選取  **Activate**將要求傳送。

   ![啟用存取權](./media/request-just-in-time-access/activate-access.png) 

1. 檢視以查看新的 JIT 要求已成功傳送給取用者的通知。

   ![通知](./media/request-just-in-time-access/in-progress.png)

   現在，您必須等到取用者[核准您的要求](approve-just-in-time-access.md#approve-requests)。

1. 若要檢視受管理的應用程式的所有 JIT 要求的狀態，請選取**JIT 存取**並**要求歷程記錄**。

   ![檢視狀態](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>已知問題

在受管理的應用程式定義中都必須包含明確的要求 JIT 存取權的帳戶的主體識別碼。 帳戶不得只包含透過在封裝中指定的群組。 在未來版本中，將會修正這項限制。

## <a name="next-steps"></a>後續步驟

若要深入了解核准 JIT 存取要求，請參閱[核准 Azure 受控應用程式中的-just-in-time 存取](approve-just-in-time-access.md)。
