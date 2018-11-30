---
title: Azure Stack 驗證最佳做法。 | Microsoft Docs
description: 本文包含「驗證即服務」的最佳做法。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: John.Haskin
ms.openlocfilehash: d83eb501bbe685890decb3acbb8116164f4199db
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422655"
---
# <a name="best-practices-for-validation-as-a-service"></a>驗證即服務的最佳做法

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

本文涵蓋管理「驗證即服務」(VaaS) 中資源的最佳做法。 如需 VaaS 資源的概觀，請參閱[驗證即服務重要概念](azure-stack-vaas-key-concepts.md)。

## <a name="solution-management"></a>解決方案管理

### <a name="naming-convention-for-vaas-solutions"></a>VaaS 解決方案的命名慣例

針對在 VaaS 中註冊的所有解決方案，請使用一致的命名慣例。 例如，可以從以下的硬體屬性建構解決方案名稱，如下所示：

|產品名稱 | 唯一硬體元素 1 | 唯一硬體元素 2 | 方案名稱
|---|---|---|---|
My Solution XYZ |  All Flash | My Switch X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>建立新 VaaS 解決方案的時機

針對相同的硬體 SKU 執行工作流程時，請使用相同的 VaaS 解決方案。 只有當硬體 SKU 變更時，才應該建立新的 VaaS 解決方案。

## <a name="workflow-management"></a>工作流程管理

### <a name="naming-convention-for-vaas-workflows"></a>VaaS 工作流程的命名慣例

請針對所有 VaaS 工作流程回合使用一致的命名慣例。 例如，可以從以下的組建屬性建構工作流程名稱，如下所示：

|組建編號 (主要) | 日期 | 解決方案大小 | 工作流程名稱
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>後續步驟

- 了解[驗證即服務重要概念](azure-stack-vaas-key-concepts.md)
