---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: 5cd64b806392162fd3bee14ddaf607385ac05264
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55807127"
---
在驗證工作流程中，**排程**測試時會使用您在建立工作流程期間所指定的工作流程層級一般參數 (請參閱 [Azure Stack 驗證即服務的工作流程一般參數](../azure-stack-vaas-parameters.md))。 如果有任何測試參數值無效，您就必須依照[修改工作流程參數](../azure-stack-vaas-monitor-test.md#change-workflow-parameters)中的指示重新提供參數。

> [!NOTE]
> 對現有的執行個體排程驗證測試時，將建立新的執行個體而取代入口網站中的舊執行個體。 舊執行個體的記錄仍會保留，但無法從入口網站存取。  
測試順利完成後，**排程**動作就會停用。

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. 從內容功能表中選取 [排程]，以開啟排程測試執行個體的提示。

1. 檢閱測試參數，然後選取 [提交] 以排程要執行的測試。