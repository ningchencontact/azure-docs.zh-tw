---
title: Azure Stack 驗證即服務重要概念 | Microsoft Docs
description: 說明「Azure Stack 驗證即服務」的重要概念。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 6a14a806b48b30d84b021a84a1ef0a2f4318ea94
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106457"
---
# <a name="validation-as-a-service-key-concepts"></a>驗證即服務重要概念

本文說「驗證即服務」(VaaS) 的重要概念。

## <a name="solutions"></a>解決方案

VaaS 解決方案代表具有特定硬體用料表 (BoM) 的 Azure Stack 解決方案。 VaaS 解決方案會作為針對 Azure Stack 解決方案執行之工作流程的容器。

### <a name="create-a-solution-in-the-vaas-portal"></a>在 VaaS 入口網站中建立解決方案

1. 登入 [VaaS 入口網站](https://azurestackvalidation.com)。
2. 在解決方案儀表板上，選取 [新增解決方案]。
3. 輸入解決方案的名稱。 如需命名建議，請參閱 [VaaS 解決方案的命名慣例](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions)。
4. 選取 [儲存] 以建立解決方案。

## <a name="workflows"></a>工作流程

VaaS 工作流程會在 VaaS 解決方案內容內運作。 它代表一組執行 Azure Stack 部署功能的測試套件。 您應該針對 Azure Stack 解決方案的每個部署或軟體更新都建立工作流程。

工作流程會依測試案例類型分類。 在非正式的測試中，**測試進行**工作流程可讓您從所有可用的 VaaS 附隨品選取測試。 在正式的測試中，**驗證**工作流程的目標會是 Microsoft 所選取的特定測試案例。

![VaaS 工作流程圖格](media/tile_all-workflows.png)

> [!NOTE]
> **解決方案驗證**工作流程目前支援兩種案例：[驗證 OEM 套件](azure-stack-vaas-validate-oem-package.md)和[驗證來自 Microsoft 的軟體更新](azure-stack-vaas-validate-microsoft-updates.md)。

如需有關工作流程類型的詳細資訊，請參閱[什麼是 Azure Stack 的驗證即服務？](azure-stack-vaas-overview.md)

### <a name="getting-started-with-vaas-workflows"></a>開始使用 VaaS 工作流程

1. 在解決方案儀表板上，建立新的解決方案或選取現有的解決方案。 這會重新整理工作流程並顯示成圖格。
2. 若要建立新的工作流程，請在任何圖格上選取 [開始]。 如需有關每個工作流程的特定資訊，請參閱下列文章：
    - 測試進行：[快速入門：使用驗證即服務入口網站來排程第一個測試](azure-stack-vaas-schedule-test-pass.md)
    - 解決方案驗證：[驗證新的 Azure Stack 解決方案](azure-stack-vaas-validate-solution-new.md)
    - 解決方案驗證：[驗證來自 Microsoft 的軟體更新](azure-stack-vaas-validate-microsoft-updates.md)
    - 解決方案驗證：[驗證 OEM 套件](azure-stack-vaas-validate-oem-package.md)

3. 若要管理或監視現有的工作流程，請在工作流程圖格上選取 [管理]。 選取工作流程的名稱，然後使用 [編輯] 按鈕來檢視屬性或變更通用的測試參數。

如需有關工作流程屬性和參數的詳細資訊，請參閱 [Azure Stack 驗證即服務的工作流程通用參數](azure-stack-vaas-parameters.md)。

## <a name="tests"></a>測試

VaaS 中的測試是由針對 Azure Stack 解決方案執行的一組動作所組成的。 測試會有依分類 (例如功能性或可靠性) 識別的各種不同預定目的，並且會以一或多項 Azure Stack 服務為目標。 每個測試會定義一組自己的參數，其中有些會由上層工作流程的通用參數所指定。

如需有關管理和監視測試的詳細資訊，請參閱[監視和管理 VaaS 入口網站中的測試](azure-stack-vaas-monitor-test.md)。

如需有關測試參數的詳細資訊，請參閱 [Azure Stack 驗證即服務的工作流程通用參數](azure-stack-vaas-parameters.md)。

## <a name="agents"></a>代理程式

VaaS 代理程式會驅動測試執行。 有兩種類型的代理程式會執行 VaaS 測試：

- **雲端代理程式**。 這是 VaaS 中可用的預設代理程式。 無須任何設定，但需要能夠對您的環境進行輸入連線，而且必須可從網際網路解析 Azure Stack 端點。 有些測試與雲端代理程式不相容。
- **本機代理程式**。 這可讓您在無法對您環境進行輸入連線的情況下執行驗證。 有些測試需要透過本機代理程式執行。

本機代理程式不會繫結至任何特定的 Azure Stack 或 VaaS 解決方案。 最佳做法是，應該讓它們在 Azure Stack 環境外執行。

如需有關新增本機代理程式的指示，請參閱[部署本機代理程式](azure-stack-vaas-local-agent.md)。

## <a name="next-steps"></a>後續步驟

- [驗證即服務的最佳做法](azure-stack-vaas-best-practice.md)