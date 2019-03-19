---
title: Azure Stack 的驗證即服務概觀 | Microsoft Docs
description: Azure Stack 驗證即服務的概觀。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ca22f29597cb452d6d33338b8ba0367c9377f6fb
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772512"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>什麼是 Azure Stack 的驗證即服務？

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

驗證即服務 (VaaS) 是專為與 Microsoft 共同規劃 Azure Stack 供應項目的解決方案合作夥伴設計的原生 Azure 服務。 解決方案合作夥伴可以使用此服務，檢查其解決方案是否符合 Microsoft 的需求並可搭配 Azure Stack 正常運作。

VaaS 的主要用途為：

- 驗證新的 Azure Stack 解決方案
- 驗證對 Azure Stack 軟體進行的變更
- 以數位方式簽署在部署期間使用的解決方案合作夥伴套件
- 預覽 VaaS 測試附隨品

## <a name="validate-a-new-azure-stack-solution"></a>驗證新的 Azure Stack 解決方案

合作夥伴可使用**解決方案驗證**工作流程來驗證新的 Azure Stack 解決方案。 解決方案必須通過必要的 Hardware Lab Kit (HKL) Azure Stack 元件測試。 若要認證一系列硬體設定，必須針對每個新解決方案執行工作流程兩次：最小值和最大值設定各一次。

如需詳細資訊，請參閱[驗證新的 Azure Stack 解決方案](azure-stack-vaas-validate-solution-new.md)。

## <a name="validate-changes-to-the-azure-stack-software"></a>驗證對 Azure Stack 軟體進行的變更

合作夥伴可使用**套件驗證**工作流程，來檢查其解決方案是否可與最近的 Azure Stack 軟體更新搭配運作。 「套件驗證」工作流程必須在已使用修補程式和更新 (P&U) 來套用更新的 Microsoft 建議硬體環境上執行。 建議您也在基準組建上執行此工作流程。

如需詳細資訊，請參閱[驗證來自 Microsoft 的軟體更新](azure-stack-vaas-validate-microsoft-updates.md)。

## <a name="get-digitally-signed-solution-partner-packages"></a>取得以數位方式簽署的解決方案合作夥伴套件

除了驗證 Azure Stack 更新之外，合作夥伴還會使用**套件驗證**工作流程來驗證對 OEM 自訂套件進行的更新，這包括 Azure Stack 合作夥伴特定的驅動程式、韌體及其他在 Azure Stack 軟體部署期間所使用的軟體。 請至少使用將會支援的最小大小解決方案，將您要驗證的套件部署在目前版本的 Azure Stack 軟體上。 系統會先將該套件提交給 VaaS，然後才執行測試。 如果測試成功，請通知 [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) 該套件已完成測試，應該使用 Azure Stack 數位簽章以數位方式簽署該套件。 Microsoft 會簽署該套件，並通知 Azure Stack 合作夥伴該套件已在 VaaS 入口網站中可供下載。

如需詳細資訊，請參閱[驗證 OEM 套件](azure-stack-vaas-validate-oem-package.md)。

## <a name="preview-vaas-test-collateral"></a>預覽 VaaS 測試附隨品

Microsoft 會定期在 Azure Stack 中提供新的功能。 在為市場提供這些功能的開發過程中，會在**測試進行**工作流程中提供新的測試附隨品。 「測試進行」工作流程包含來自其他工作流程的測試附隨品，用來允許執行非正式測試。 請勿使用「測試進行」工作流程來提交結果以供核准。 請使用「解決方案驗證」和「套件驗證」工作流程，來為您的解決方案取得正式核准。

如需詳細資訊，請參閱[快速入門：使用「驗證即服務」入口網站來排程第一個測試](azure-stack-vaas-schedule-test-pass.md)。

## <a name="validation-workflow-tests-summary"></a>驗證工作流程測試摘要

| 驗證工作流程 | 必要的測試 |
|----|------------|
| [新解決方案驗證](azure-stack-vaas-validate-solution-new.md) | 雲端模擬引擎<br>計算 SDK 操作套件<br>磁碟識別測試<br>KeyVault 擴充功能 SDK 操作套件<br>KeyVault SDK 操作套件<br>網路 SDK 操作套件<br>儲存體帳戶 SDK 操作套件<br> |
| [OEM 套件驗證](azure-stack-vaas-validate-oem-package.md) | OEM 擴充功能套件驗證<br>雲端模擬引擎 |
| [每月更新驗證](azure-stack-vaas-validate-microsoft-updates.md) | 每個月的 Azure Stack 更新驗證<br>雲端模擬引擎<br> |

## <a name="next-steps"></a>後續步驟

- [設定您的驗證即服務資源](azure-stack-vaas-set-up-resources.md)
- 了解[驗證即服務重要概念](azure-stack-vaas-key-concepts.md)
