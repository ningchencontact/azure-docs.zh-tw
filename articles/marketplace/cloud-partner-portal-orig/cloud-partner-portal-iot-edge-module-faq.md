---
title: IoT Edge 模組 Marketplace 常見問題集 | Microsoft Docs
description: IoT Edge 模組 Marketplace 常見問題集。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2c2e7729eb70a5dd37dc4df10605eec9429e1043
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805549"
---
# <a name="iot-edge-module-marketplace-frequently-asked-questions"></a>IoT Edge 模組 Marketplace 常見問題集


## <a name="what-is-the-edge-module-marketplace"></a>什麼是 Edge 模組 Marketplace？


IoT Edge 模組 Marketplace 是「經過認證的」預先建置 Edge 模組清單，這些模組可透過 Azure Marketplace 「探索」。

![IoT Edge 模組](./media/cloud-partner-portal-iot-edge-module-faq/iot-edge-modules.png)

## <a name="where-will-edge-modules-be-visible"></a>Edge 模組於何處顯示？ 


在物聯網類別下 [Azure 入口網站 Marketplace](https://ms.portal.azure.com/) (已驗證的體驗) 中，標記為「IoT Edge 模組」。

在物聯網類別下的 [Azure Marketplace 網站](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1) (匿名體驗) 中，標記為「IoT Edge 模組」。

## <a name="is-it-open-to-partners"></a>開放給合作夥伴嗎？


尚未提供。 目前，只有 Microsoft 所設計的模組才會在 Marketplace 的 [IoT Edge] 區段中發行。 

## <a name="why-should-partners-publish-their-iot-edge-modules"></a>為何合作夥伴應該發行其 IoT Edge 模組？


-   透過將這個通道新增至市集，並展示其解決方案增加其產品能見度。

-   取得更多潛在客戶。 作為 Azure Marketplace 的一部分，它們可以取得有關誰對其解決方案感興趣的詳細資訊。

-   及早運用更多的營收功能。

## <a name="what-is-the-onboarding-process"></a>上架程序為何？


雖然尚未開啟，但上架程序將透過 Azure Marketplace 完成。 [Azure Marketplace 和 AppSource 發行指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)中有詳細的指導方針。 請參閱容器的交易清單類型。 

合作夥伴首先必須成為 Azure Marketplace 的發行者。 然後，他們將能夠透過 [Cloud Partner 入口網站](./cloud-partner-portal-getting-started-with-the-cloud-partner-portal.md)上架其 Edge 模組。

## <a name="are-there-any-monetization-capabilities"></a>是否有任何的營收功能？


目前沒有現成功能。 我們的第一個目標是開放一個包含*免費*或*自備授權* Edge 模組的市場。 我們將持續加入更多的營收功能，例如耗用量計費模型。

## <a name="what-is-bring-your-own-license-byol"></a>什麼是自備授權 (BYOL)？


[Microsoft Azure Marketplace 參與政策](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)中的正式定義為：

- 客戶在 Azure Marketplace 以外取得存取或使用供應項目的權利， 在 Azure Marketplace 中使用供應項目不會被收取 Azure Marketplace 費用。

它是由合作夥伴授權軟體，並為其收集營收。

## <a name="can-partners-publish-a-freemium-module"></a>合作夥伴可以發行「免費增值」模組嗎？


可以。免費增值模組是免費提供但有一些限制的模組，會視為與任何其他發行集相同。

## <a name="is-intellectual-property-protected"></a>智慧財產權受到保護嗎？


Edge 模組是 Docker 相容容器。 它是由合作夥伴來保護他們在分佈式容器中封裝的智慧財產 (IP)。

## <a name="where-will-the-modules-be-hosted"></a>模組將在何處裝載？


IoT Edge 模組將裝載在 Microsoft 擁有的容器登錄中，該登錄將可匿名查詢，例如 Docker Hub。

## <a name="what-are-the-integration-plans-between-the-azure-marketplace-and-the-azure-iot-tools"></a>Azure Marketplace 和 Azure IoT 工具之間的整合方案是什麼？

我們將在 Azure Marketplace 和 Azure IoT 工具之間建立更緊密的整合。 我們想到的一些範例是直接從 IoT 中樞入口網站或直接從 Visual Studio Code 中瀏覽 IoT Edge 模組 Marketplace。

## <a name="which-operating-system-or-architecture-should-my-container-support"></a>我的容器應該支援哪種作業系統或架構？

IoT Edge 模組必須支援與生產環境中的 IoT Edge 相同的作業系統 / 架構矩陣。 這份清單在 [Azure IoT Edge 支援](https://docs.microsoft.com/azure/iot-edge/support)中維護。 例如，模組目前必須支援 Linux x64 和 Linux ARM32。

## <a name="are-there-any-other-certification-constraints-to-be-aware-of"></a>是否還有其他認證限制需要注意？

我們仍在研究確切的認證限制。 我們的指導原則包括︰

-   提升品質而不是數量。

-   IoT Edge 專屬容器 (不是隨機的容器)。

-   生產環境等級。

-   1-click 部署 (至少提供一組預設的設定值)。

## <a name="any-other-questions"></a>有任何其他問題嗎？


請連絡 [Azure IoT Edge 上架](mailto:azureiotedgeonboarding@service.microsoft.com)。
