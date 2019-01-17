---
title: 在 Azure Stack 驗證即服務中驗證 Microsoft 的軟體更新 | Microsoft Docs
description: 了解如何使用「驗證即服務」來驗證 Microsoft 的軟體更新。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/14/2019
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: bcb56789b2781cd1d081af8c112222a1f1269a74
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304885"
---
# <a name="validate-software-updates-from-microsoft"></a>驗證來自 Microsoft 的軟體更新

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft 會定期發行 Azure Stack 軟體的更新。 這些更新會提供給 Azure Stack 共同設計製造合作夥伴。 這些更新會在公開發行之前先行提供。 您可以對解決方案檢查這些更新，並提供意見反應給 Microsoft。

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>套用每月更新

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>建立工作流程

更新驗證會使用與**解決方案驗證**相同的工作流程。

## <a name="run-tests"></a>執行測試

1. 更新驗證會使用與**解決方案驗證**相同的工作流程。 

2. 請依照[執行解決方案驗證測試](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests)中的指示操作。 請改為選取下列測試：
    - 每個月的 Azure Stack 更新驗證
    - 雲端模擬引擎

進行更新驗證時不需要求套件簽署。

## <a name="next-steps"></a>後續步驟

- [監視和管理 VaaS 入口網站中的測試](azure-stack-vaas-monitor-test.md)