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
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 7fcc7d5a1d87fe93d32772dbbb84f1d3c91d5631
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648777"
---
# <a name="validate-software-updates-from-microsoft"></a>驗證來自 Microsoft 的軟體更新

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft 會定期發行 Azure Stack 軟體的更新。 這些更新在公開發行之前會先提供給 Azure Stack 共同設計製造合作夥伴，讓他們對其解決方案驗證這些更新，並提供意見反應給 Microsoft。

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>套用每月更新

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>建立工作流程

更新驗證會使用與**套件驗證**相同的工作流程。 請依照[建立套件驗證工作流程](azure-stack-vaas-validate-oem-package.md#create-a-package-validation-workflow)中的指示操作。

## <a name="run-tests"></a>執行測試

更新驗證會使用與**套件驗證**相同的工作流程。 請依照[執行套件驗證測試](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests)中的指示操作。

進行更新驗證時不需要求套件簽署。

## <a name="next-steps"></a>後續步驟

- [監視和管理 VaaS 入口網站中的測試](azure-stack-vaas-monitor-test.md)
