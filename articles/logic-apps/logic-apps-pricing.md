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
ms.openlocfilehash: 4e5b63642b5090b50d29d6d3adc49ea683b90faa
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437708"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure Logic Apps 的定價模式

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)幫助您建立及執行自動化的整合工作流程，可以在雲端中調整。 本文說明如何計費和定價適用於 Azure Logic Apps。 如需特定的定價資訊，請參閱 < [Azure Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>取用量定價模式

新的邏輯執行的應用程式在公用或 「 全域 」 Azure Logic Apps 服務中，您只需支付您所使用的項目。 這些邏輯應用程式會使用以取用量為基礎的方案和定價模型。 在邏輯應用程式定義中，每一個步驟都是一個動作。 例如，動作包括： 

* 觸發程序，也就是特殊的動作。 所有的邏輯應用程式需要觸發程序的第一個步驟。
* 「 內建 」 或原生動作，例如 HTTP、 呼叫 Azure Functions 和 API 管理等等
* 呼叫連接器，例如 Outlook 365、 Dropbox 等等
* 控制流程的步驟，例如迴圈、 條件陳述式，並依此類推

Azure Logic Apps 會測量執行邏輯應用程式中的所有動作。 深入了解帳單如何運作[觸發程序](#triggers)並[動作](#actions)。

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>固定定價模式

新的邏輯應用程式內執行的[ *integration service 環境*(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)，內建動作和標準連接器中支付每月費用固定。 ISE 可讓您建立和執行隔離的邏輯應用程式，以便存取 Azure 虛擬網路中的資源。

> [!NOTE]
> ISE 處於[*公開預覽*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 如需特定的定價資訊，請參閱 < [Azure Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps)。

ISE 基底單元有固定的容量，因此如果您需要更多的輸送量，您可以[新增更多縮放單位](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity)，請在建立期間或之後。 您的 ISE 包含了一個免費的企業連接器，其中包含您想要的連線數目。 其他企業連接器的使用方式是依據 Enterprise 耗用量價格來收費的。

<a name="connectors"></a>

## <a name="connectors"></a>連接器

Azure Logic Apps 連接器提供協助您的邏輯應用程式存取應用程式、 服務和系統在雲端或內部部署[觸發程序](#triggers)，[動作](#actions)，或兩者。 連接器會歸類為 Standard 或 Enterprise。 如需有關這些連接器的概觀，請參閱 <<c0> [ 適用於 Azure Logic Apps 的連接器](../connectors/apis-list.md)。 下列各節提供如何計費觸發程序的詳細資訊，並執行工作。

<a name="triggers"></a>

## <a name="triggers"></a>觸發程序

觸發程序是在特定事件發生時建立邏輯應用程式執行個體的特殊動作。 觸發程序會以不同動作影響邏輯應用程式計量方式。 以下是不同種類的存在於 Azure Logic Apps 的觸發程序：

* **輪詢觸發程序**:此觸發程序會持續檢查端點來取得滿足的準則建立邏輯應用程式執行個體，而啟動的工作流程的訊息。 即使未建立任何邏輯應用程式執行個體，Logic Apps 也會將每個輪詢要求當作一個執行來計量。 您可以透過邏輯應用程式設計工具來設定觸發程序，以指定輪詢間隔。

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook 觸發程序**:此觸發程序會等候用戶端將要求傳送至特定端點。 每個傳送至 Webhook 端點的要求都會計算為一個動作執行。 例如，要求和 HTTP Webhook 觸發程序都是 Webhook 觸發程序。

* 重复周期触发器：此觸發程序會建立邏輯應用程式的執行個體，根據您在觸發程序中設定循環間隔。 比方說，您可以設定每隔三天執行一次循環觸發程序，或在更複雜的排程上。

<a name="actions"></a>

## <a name="actions"></a>動作

Azure Logic Apps 會測量 「 內建 」 的動作，例如 HTTP、 以原生動作。 比方說，內建動作包含 HTTP 呼叫，呼叫 Azure Functions 或 API 管理中，從控制流程步驟，例如條件、 迴圈和 switch 陳述式。 每個動作都有自己的動作類型。 例如，呼叫的動作[連接器](https://docs.microsoft.com/connectors)具有"ApiConnection"類型。 這些連接器會歸類為標準或企業連接器，計量，以其各自[定價](https://azure.microsoft.com/pricing/details/logic-apps)。 在企業連接器*預覽*收取標準連接器。

Azure Logic Apps 會測量為執行成功和失敗的所有動作。 不過，Logic Apps 不測量這些動作：

* 因為條件不符而略過的動作
* 因為邏輯應用程式在完成前停止而不會執行的動作

在迴圈內執行的動作，Azure Logic Apps 會計算每個循環迴圈中的每個動作。 例如，假設您有可處理清單的 "for each" 迴圈。 Logic Apps 會將清單項目數目乘以迴圈中的動作數目，以計量該迴圈中的動作，並新增可啟動迴圈的動作。 因此，10 個項目清單的計算是 (10 * 1) + 1，11 個動作執行結果。

## <a name="disabled-logic-apps"></a>停用的邏輯應用程式

停用，因為他們無法建立新的執行個體，它們停用時，不需支付費用應用程式的邏輯。
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