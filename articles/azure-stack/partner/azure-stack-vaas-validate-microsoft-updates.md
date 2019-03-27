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
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ad9d5057c18d316dcf3254dc57a3184c1b75fc50
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780827"
---
# <a name="validate-software-updates-from-microsoft"></a>驗證來自 Microsoft 的軟體更新

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft 會定期發行 Azure Stack 軟體的更新。 這些更新會提供給 Azure Stack 共同設計製造合作夥伴。 這些更新會在公開發行之前先行提供。 您可以對解決方案檢查這些更新，並提供意見反應給 Microsoft。

Microsoft 對 Azure Stack 的軟體更新是使用命名慣例指定的，例如，1803 表示 2018 年 3 月的更新。 如需 Azure Stack 更新原則、頻率和可用版本資訊的相關資訊，請參閱 [Azure Stack 服務原則](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy)。

## <a name="prerequisites"></a>必要條件

在 VaaS 中練習每月更新程序之前，您應該先熟悉下列項目：

- [驗證即服務的重要概念](azure-stack-vaas-key-concepts.md)
- [互動式功能驗證測試](azure-stack-vaas-interactive-feature-verification.md)

## <a name="required-tests"></a>必要的測試

依照下列順序執行下列測試，以便進行每月軟體驗證：

1. 每個月的 Azure Stack 更新驗證
2. 雲端模擬引擎

## <a name="validating-software-updates"></a>驗證軟體更新

1. 建立新的 [套件驗證] 工作流程。
1. 對於上述必要測試，請依照[執行套件驗證測試](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests)中的指示操作。 如需**每個月的 Azure Stack 更新驗證**測試的其他指示，請參閱下一節。

### <a name="apply-the-monthly-update"></a>套用每月更新

1. 選取要執行測試的代理程式。
1. 排程**每個月的 Azure Stack 更新驗證**。
1. 提供目前部署於戳記的 OEM 擴充功能套件位置，以及將在更新期間套用的 OEM 擴充功能套件位置。 若要設定這些套件的 URL，請參閱[管理套件以供驗證](azure-stack-vaas-validate-oem-package.md#managing-packages-for-validation)。
1. 請遵循所選代理程式 UI 中的步驟。

如有任何問題或疑慮，請連絡 [VaaS 說明](mailto:vaashelp@microsoft.com)。

## <a name="next-steps"></a>後續步驟

- [監視和管理 VaaS 入口網站中的測試](azure-stack-vaas-monitor-test.md)