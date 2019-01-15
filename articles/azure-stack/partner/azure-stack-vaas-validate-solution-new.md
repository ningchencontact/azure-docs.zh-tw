---
title: 驗證新的 Azure Stack 解決方案 | Microsoft Docs
description: 了解如何使用「驗證即服務」來驗證新的 Azure Stack 解決方案。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 12/20/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: e6cfdca207b114871a478262f14ea960be5985df
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104959"
---
# <a name="validate-a-new-azure-stack-solution"></a>驗證新的 Azure Stack 解決方案

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

了解如何使用**解決方案驗證**工作流程來認證新的 Azure Stack 解決方案。

Azure Stack 解決方案是一份硬體用料表 (BoM)，此用料表需在符合 Windows Server 標誌認證需求後，獲得 Microsoft 與合作夥伴的認同。 當硬體 BoM 有變更時，解決方案就必須重新進行認證。 若您想深入了解何時應重新認證解決方案，請與團隊聯絡：[vaashelp@microsoft.com](mailto:vaashelp@microsoft.com)。

若要認證您的解決方案，請執行解決方案驗證工作流程兩次。 首先針對*最低*支援組態執行一次。 其次再對「最高」支援組態執行一次。 如果這兩種組態都通過所有測試，Microsoft 即會認證解決方案。

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>建立解決方案驗證工作流程

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. 選取 [解決方案驗證] 圖格上的 [開始]。

    ![解決方案驗證工作流程圖格](media/tile_validation-solution.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. 選取 [解決方案組態]。
    - **下限**：以支援的節點數目下限設定解決方案。
    - **上限**：以支援的節點數目上限設定解決方案。
6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![解決方案驗證資訊](media/workflow_validation-solution_info.png)

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > 建立工作流程後即無法修改環境參數。

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    系統會將您重新導向至測試摘要頁面。

## <a name="run-solution-validation-tests"></a>執行解決方案驗證測試

在 [解決方案驗證測試摘要] 頁面中，您會看到完成驗證所需的測試清單。

在驗證工作流程中，**排程**測試時會使用您在建立工作流程期間所指定的工作流程層級一般參數 (請參閱 [Azure Stack 驗證即服務的工作流程一般參數](azure-stack-vaas-parameters.md))。 如果有任何測試參數值無效，您就必須依照[修改工作流程參數](azure-stack-vaas-monitor-test.md#change-workflow-parameters)中的指示重新提供參數。

> [!NOTE]
> 對現有的執行個體排程驗證測試時，將建立新的執行個體而取代入口網站中的舊執行個體。 舊執行個體的記錄仍會保留，但無法從入口網站存取。  
測試順利完成後，**排程**動作就會停用。

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

2. 選取下列測試：
    - 雲端模擬引擎
    - 計算 SDK 操作套件
    - 磁碟識別測試
    - KeyVault 擴充功能 SDK 操作套件
    - KeyVault SDK 操作套件
    - 網路 SDK 操作套件
    - 儲存體帳戶 SDK 操作套件

3. 從內容功能表中選取 [排程]，以開啟排程測試執行個體的提示。

4. 檢閱測試參數，然後選取 [提交] 以排程要執行的測試。

![排程解決方案驗證測試](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>後續步驟

- [監視和管理 VaaS 入口網站中的測試](azure-stack-vaas-monitor-test.md)