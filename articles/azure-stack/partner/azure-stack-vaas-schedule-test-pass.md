---
title: 使用 Azure Stack 的驗證即服務入口網站來排定第一個測試 | Microsoft Docs
description: 使用「Azure Stack 的驗證即服務」入口網站來排定第一個測試。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 3fb5c3deeddb6f3ee381ca45df76feebf3405b21
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766559"
---
# <a name="scheduling-a-test"></a>排程測試

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

在 Azure Stack 解決方案的「驗證即服務」(VaaS ) 入口網站中排定測試。 VaaS 解決方案代表具有特定硬體用料表 (BoM) 的 Azure Stack 解決方案。 您可以排定測試來檢查您的硬體是否能夠執行 Azure Stack。

若要檢查您的解決方案，請為測試建立工作流程。 VaaS 工作流程會在 VaaS 解決方案內容內運作。 它代表一組在您硬體上執行 Azure Stack 部署功能的測試套件。 請新增您的解決方案環境變數，然後選取一或多個要在解決方案上執行的測試。

雖然「測試進行」工作流程可用來執行 VaaS 所提供的任何測試 (包括來自驗證工作流程的測試)，但來自「測試進行」工作流程的結果並不會被視為「正式」結果。 如需有關正式驗證工作流程的資訊，請參閱[工作流程](azure-stack-vaas-key-concepts.md#workflows)。

## <a name="prerequisites"></a>必要條件

在您依照本快速入門進行操作之前，應該先完成下列項目：

- [設定您的驗證即服務資源](azure-stack-vaas-set-up-resources.md)
- [部署本機代理程式](azure-stack-vaas-local-agent.md) (必要)
- [驗證即服務重要概念](azure-stack-vaas-key-concepts.md) (必要)

## <a name="start-a-workflow"></a>開始工作流程

![登入 VaaS 入口網站](media/vaas_portalsignin.png)

登入入口網站，選取或建立解決方案，然後選取該解決方案。

1. 登入 [VaaS 入口網站](https://azurestackvalidation.com)。
2. 輸入現有解決方案的名稱，或選取 [新增解決方案] 以建立新的解決方案。 如需相關指示，請參閱[在 VaaS 入口網站中建立解決方案](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-vaas-portal)。
3. 選取 [測試進行] 圖格中的 [開始]。

## <a name="specify-parameters"></a>指定參數

![Alt 鍵文字](media/vaas_test_pass_parameters.png)

提供會套用至工作流程內所有測試的參數。

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. 選取 [下一步] 以選取要排定的測試。

## <a name="select-tests-to-run"></a>選取要執行的測試

工作流程建立好之後，就會對您選取的測試進行排程。

1. 選取要在您工作流程中執行的測試。

    如果您想要覆寫任何測試的通用參數 (也就是在上一節中提供的參數)，請接著選取 [編輯] 連結來指定新值。

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

1. 選取 [下一步] 以檢閱工作流程。

## <a name="review-and-submit"></a>檢閱並提交

完成工作流程的建立。

1. 檢閱所顯示的資訊。

    服務會以所提供的資訊建立您的工作流程，並且會排定所選取的測試。

    如果有任何項目顯得不正確，請使用 [上一步] 按鈕來返回先前的區段。

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>後續步驟

- [監視和管理 VaaS 入口網站中的測試](azure-stack-vaas-monitor-test.md)
