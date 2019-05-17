---
title: 定價 & 計費 - Azure Logic Apps | Microsoft Docs
description: 了解 Azure Logic Apps 的定價和計費方式
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 03/25/2019
ms.openlocfilehash: c3d06d3c0f9c86b4fe6495632b48051fd69dc663
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544513"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure Logic Apps 的定價模式

借助 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)可以创建和运行可在云中缩放的自动化集成工作流。 本文介绍 Azure 逻辑应用的计费和定价方式。 有关具体的定价信息，请参阅 [Azure 逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>取用量定價模式

对于在公共或“全球”Azure 逻辑应用服务中运行的新逻辑应用，只需根据实际使用的资源付费。 這些邏輯應用程式會使用以取用量為基礎的方案和定價模型。 在邏輯應用程式定義中，每一個步驟都是一個動作。 例如，操作包括：

* 触发器（特殊的操作）。 所有逻辑应用需将一个触发器用作第一个步骤。
* “内置”或本机操作，例如 HTTP、对 Azure Functions 和 API 管理的调用，等等
* 对 Outlook 365、Dropbox 等连接器的调用
* 控制流步骤，例如循环、条件语句，等等

Azure 逻辑应用对逻辑应用中运行的所有操作进行计量。 详细了解[触发器](#triggers)和[操作](#actions)的计费方式。

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>固定定價模式

[ *Integration service 環境*(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)提供私用、 隔離和專用的方法，讓您建立和執行可存取 Azure 虛擬網路中的資源的邏輯應用程式。 針對新的邏輯應用程式在 ISE 中執行，您會支付每月費用固定的內建動作和觸發程序以及標準連接器。

您的 ISE 也包括一個免費的企業連接器，其中包含您想要的連線數目上限。 其他企業連接器的使用方式是依據 Enterprise 耗用量價格來收費的。

> [!NOTE]
> 在 ISE、 內建的觸發程序和動作顯示**Core**加上標籤，並在您的 logic apps 為相同的 ISE 中執行。 標準和企業連接器，顯示**ISE**在為您的 logic apps 相同的 ISE 中執行的標籤。 不會顯示在全域的 Logic Apps 服務中執行的 ISE 標籤的連接器。

ISE 基底單元有固定的容量，因此如果您需要更多的輸送量，您可以[新增更多縮放單位](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity)，請在建立期間或之後。 

有关具体的定价信息，请参阅 [Azure 逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="connectors"></a>

## <a name="connectors"></a>連接器

Azure 逻辑应用连接器通过提供[触发器](#triggers)和/或[操作](#actions)，帮助逻辑应用访问云中或本地的应用、服务和系统。 连接器分类为“标准”或“企业”连接器。 有关这些连接器的概述，请参阅[适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)。 以下部分提供有关触发器和操作的计费方式的详细信息。

<a name="triggers"></a>

## <a name="triggers"></a>觸發程序

觸發程序是在特定事件發生時建立邏輯應用程式執行個體的特殊動作。 觸發程序會以不同動作影響邏輯應用程式計量方式。 下面是 Azure 逻辑应用中存在的各种触发器：

* **轮询触发器**：此触发器持续检查终结点是否收到满足创建逻辑应用实例和启动工作流的条件的消息。 即使未建立任何邏輯應用程式執行個體，Logic Apps 也會將每個輪詢要求當作一個執行來計量。 您可以透過邏輯應用程式設計工具來設定觸發程序，以指定輪詢間隔。

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook 触发器**：此触发器等待客户端向特定的终结点发送请求。 每個傳送至 Webhook 端點的要求都會計算為一個動作執行。 例如，要求和 HTTP Webhook 觸發程序都是 Webhook 觸發程序。

* 重复周期触发器：此触发器基于在触发器中设置的重复间隔创建逻辑应用实例。 例如，可以设置每隔三天运行的，或者根据更复杂的计划运行的定期触发器。

<a name="actions"></a>

## <a name="actions"></a>動作

Azure 逻辑应用将 HTTP 等“内置”操作作为本机操作进行计量。 例如，内置操作包括 HTTP 调用、来自 Azure Functions 或 API 管理的调用，以及条件、循环和开关语句等控制流步骤。 每个操作具有自身的操作类型。 例如，调用[连接器](https://docs.microsoft.com/connectors)的操作为“ApiConnection”类型。 这些连接器分类为“标准”或“企业”连接器，根据各自的[定价](https://azure.microsoft.com/pricing/details/logic-apps)进行计量。 预览版的企业连接器按标准连接器计费。

Azure 逻辑应用将所有成功和不成功的操作作为执行进行计量。 但是，逻辑应用不会计量以下操作：

* 因為條件不符而略過的動作
* 因為邏輯應用程式在完成前停止而不會執行的動作

对于在循环内部运行的操作，Azure 逻辑应用会对循环中每个周期的每个操作进行计数。 例如，假設您有可處理清單的 "for each" 迴圈。 Logic Apps 會將清單項目數目乘以迴圈中的動作數目，以計量該迴圈中的動作，並新增可啟動迴圈的動作。 因此，包含 10 个项的列表的计算公式为 (10 * 1) + 1，即 11 个操作执行。

## <a name="disabled-logic-apps"></a>禁用的逻辑应用

禁用的逻辑应用在禁用期间不会产生费用，因为它们无法创建新实例。
停用邏輯應用程式之後，其目前正在執行的執行個體可能需要一些時間才能完全停止。

## <a name="integration-accounts"></a>整合帳戶

取用定價適用於[整合帳戶](logic-apps-enterprise-integration-create-integration-account.md)您可以在其中瀏覽、 開發和測試[B2B 和 EDI](logic-apps-enterprise-integration-b2b.md)並[XML 處理](logic-apps-enterprise-integration-xml.md)不會有任何 Azure Logic Apps 中的功能額外的成本。
您可以在每個 Azure 區域中有一個整合帳戶。 每個整合帳戶可儲存一定[成品數量](../logic-apps/logic-apps-limits-and-config.md)，其中包含交易夥伴、協議、地圖、結構描述、組件、憑證、批次組態等等。

Azure Logic Apps 也提供基本和標準整合帳戶，具有受支援的 Logic Apps SLA。 以下是您可以選擇是否要使用基本或標準整合帳戶的方式：

* 當您只想處理訊息，或做為具有較大商務實體與交易夥伴關係的小型企業夥伴時，請使用基本整合帳戶。

* 當您有更複雜的 B2B 關係，並想要增加您可以管理的實體數目，請使用標準整合帳戶。

如需特定的定價資訊，請參閱 < [Azure Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="data-retention"></a>

## <a name="data-retention"></a>資料保留

所有的輸入和輸出而儲存邏輯應用程式的執行歷程記錄中取得計費依據為邏輯應用程式的[執行保留期間](logic-apps-limits-and-config.md#run-duration-retention-limits)。 如需特定的定價資訊，請參閱 < [Azure Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps)。

為了協助您監視邏輯應用程式的儲存體耗用量，您可以：

* 檢視儲存體單位數以 gb 為單位，您的邏輯應用程式使用每個月。
* 檢視邏輯應用程式的執行歷程記錄中的特定動作的輸入和輸出的大小。

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>檢視邏輯應用程式儲存體耗用量

1. 在 Azure 入口網站中，尋找並開啟邏輯應用程式。

1. 從邏輯應用程式的功能表底下**監視**，選取**計量**。

1. 在右窗格中，在**圖表標題**，從**計量**清單中，選取**計費使用量的儲存體耗用量執行**。

   此計量可讓您的儲存體耗用量單位數以 gb 為單位每月會開始計費。

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>檢視動作的輸入和輸出大小

1. 在 Azure 入口網站中，尋找並開啟邏輯應用程式。

1. 在您的邏輯應用程式 功能表中，選取**概觀**。

1. 在右窗格中，在**執行歷程記錄**，選取執行，輸入，而且您想要檢查的輸出。

1. 底下**邏輯應用程式回合**，選擇**回合詳細資料**。

1. 在 [**邏輯應用程式執行詳細資料**] 窗格中，在動作資料表中，它會列出每個動作的狀態和持續時間，選取您想要檢視的動作。

1. 在**邏輯應用程式動作**窗格中，尋找該動作的輸入和輸出的大小會分別底下出現**輸入連結**並**輸出連結**。

## <a name="next-steps"></a>後續步驟

* [深入了解 Azure Logic Apps](logic-apps-overview.md)
* [建立第一個邏輯應用程式](quickstart-create-first-logic-app-workflow.md)