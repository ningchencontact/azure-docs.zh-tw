---
title: 在 Azure Stack 中提供服務 | Microsoft Docs
description: 作為雲端操作員，您可以向使用者提供服務。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 754a0002aca52462910abe50825cff0254a7068f
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244149"
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Azure Stack 中的服務供應項目概觀

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

[Microsoft Azure Stack](azure-stack-poc.md) 是一個混合式雲端平台，可讓您從資料中心提供服務。 作為服務提供者，您可以提供服務給租用戶。 在企業或政府機構中，您可以提供內部部署服務給您的員工。 

您可以提供[基礎結構即服務](https://azure.microsoft.com/overview/what-is-iaas/) (IaaS) 服務，讓您的使用者能夠建立可從 Azure Stack 使用者入口網站佈建與管理的隨選運算基礎結構。

您也可以為 Azure Stack 部署 Microsoft 和其他第三方提供者的[平台即服務](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) 服務。 您可以傳遞的服務包括、但不限於：

- [將 App Service 資源提供者新增到 Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-overview)

- [將 SQL 伺服器資源提供者新增到 Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy) (\英文\)

- [將 MySQL 伺服器資源提供者新增到 Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy) (\英文\)


您甚至可以結合服務，針對不同的使用者整合和建置複雜的解決方案。

若要將這些服務提供給您的使用者，您必須建立[計劃、供應項目和配額](azure-stack-plan-offer-quota-overview.md)。 您的使用者接著會訂閱您的供應項目來使用服務。

## <a name="plan-your-service-offers"></a>規劃服務供應項目

規劃供應項目時，請記住下列重點：

**試用版供應項目**：您可以使用試用供應項目來吸引新使用者，接著升級到其他服務。 若要建立試用供應項目，請建立小型的[基本方案](azure-stack-plan-offer-quota-overview.md#base-plan)與選用的大附加元件方案。

**容量規劃**：您可能會擔心有使用者擷取大量資源，而妨礙所有使用者使用系統。 若要協助改善效能，您可以[設定方案與配額](azure-stack-plan-offer-quota-overview.md#plans)來規劃使用方式。

**委派的提供者**：您可將在您的環境中建立供應項目的能力授與其他人。 例如，如果您是服務提供者，您可以將此能力[委派](azure-stack-delegated-provider.md)給您的轉售商。 或者，如果您是組織，您可以委派給其他部門/分公司。

## <a name="next-steps"></a>後續步驟

[在 Azure Stack 中建立供應項目](azure-stack-create-offer.md)
