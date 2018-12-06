---
title: 定價 & 計費 - Azure Logic Apps | Microsoft Docs
description: 了解 Azure Logic Apps 的定價和計費方式
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
manager: carmonm
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 10/16/2018
ms.openlocfilehash: 04fb86f9b8f8be2c013f9bd7449dd5a4b2bcf90c
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854113"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure Logic Apps 的定價模式

當您使用 Azure Logic Apps 時，可以建立和執行可在雲端調整規模的自動化整合工作流程。 以下是關於 Logic Apps 計費和定價運作方式的詳細資料。 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>取用量定價模式

對於在公用或「全域」Logic Apps 服務中執行的新邏輯應用程式，您只需對您使用的部分付費。 這些邏輯應用程式會使用以取用量為基礎的方案和定價模型。 在邏輯應用程式定義中，每一個步驟都是一個動作。 動作包括觸發程序、任何控制流程步驟、內建動作和連接器呼叫。 Logic Apps 會計量在邏輯應用程式中執行的所有動作。  
如需詳細資訊，請參閱 [Logic Apps 價格](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>固定定價模式

對於在[*整合服務環境* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中執行的新邏輯應用程式，您每個月會支付固定的費用來使用其內建的動作與標準 ISE 標示的連接器。 ISE 可讓您建立和執行隔離的邏輯應用程式，以便存取 Azure 虛擬網路中的資源。  

您的 ISE 包含了一個免費的企業連接器，其中包含您想要的連線數目。 額外企業連接器的使用量會根據企業使用量價格計費。 

> [!NOTE]
> 整合服務環境目前為*個人預覽版*。 若要要求存取，請在[這裡建立您的加入要求](https://aka.ms/iseprivatepreview)。 如需詳細資訊，請參閱 [Logic Apps 價格](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="triggers"></a>

## <a name="triggers"></a>觸發程序

觸發程序是在特定事件發生時建立邏輯應用程式執行個體的特殊動作。 觸發程序會以不同動作影響邏輯應用程式計量方式。

* **輪詢觸發程序**：此觸發程序會持續檢查端點來取得訊息，其會符合符合用來建立邏輯應用程式執行個體以啟動工作流程的準則。 即使未建立任何邏輯應用程式執行個體，Logic Apps 也會將每個輪詢要求當作一個執行來計量。 您可以透過邏輯應用程式設計工具來設定觸發程序，以指定輪詢間隔。

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook 觸發程序**：此觸發程序會等候用戶端將要求傳送到特定端點。 每個傳送至 Webhook 端點的要求都會計算為一個動作執行。 例如，要求和 HTTP Webhook 觸發程序都是 Webhook 觸發程序。

* **週期性觸發程序**：此觸發程序會根據您在觸發程序中設定的週期間隔來建立邏輯應用程式執行個體。 例如，您可以設定一個週期性觸發程序，每隔三天執行一次或基於更複雜的排程來執行。

## <a name="actions"></a>動作

Logic Apps 會將內建動作當作原生動作計量。 比方說，內建動作包含透過 HTTP 的呼叫、來自 Azure Functions 或 API 管理的呼叫，以及控制流程步驟 (例如迴圈和條件) 
- 各自擁有自己的動作類型。 呼叫[連接器](https://docs.microsoft.com/connectors) \(英文\) 的動作具有 "ApiConnection" 類型。 這些連接器會歸類為標準或企業連接器，並以其各自的[定價][pricing]來計量。 預覽版的企業連接器會依標準連接器的標準收費。

Logic Apps 會將所有執行成功和失敗的動作當作動作執行來計量。 Logic Apps 不會計量下列動作： 

* 因為條件不符而略過的動作
* 因為邏輯應用程式在完成前停止而不會執行的動作

停用的邏輯應用程式在停用時不會計費，因為它們無法建立新的執行個體。

> [!NOTE]
> 停用邏輯應用程式之後，其目前正在執行的執行個體可能需要一些時間才能完全停止。

對於在迴圈內部執行的動作，Logic Apps 會計算迴圈中每個週期的每個動作。 例如，假設您有可處理清單的 "for each" 迴圈。 Logic Apps 會將清單項目數目乘以迴圈中的動作數目，以計量該迴圈中的動作，並新增可啟動迴圈的動作。 10 個項目的清單計算是 (10 * 1) + 1，結果是 11 個動作執行。

## <a name="integration-account-usage"></a>整合帳戶使用量

以取用量為基礎的使用量適用於[整合帳戶](logic-apps-enterprise-integration-create-integration-account.md)，您可以在其中探勘、開發和測試 Logic Apps 的 [B2B/EDI](logic-apps-enterprise-integration-b2b.md) 和 [XML 處理](logic-apps-enterprise-integration-xml.md)功能，而不需額外成本。 您在每個區域可以有一個整合帳戶。 每個整合帳戶可儲存一定[成品數量](../logic-apps/logic-apps-limits-and-config.md)，其中包含交易夥伴、協議、地圖、結構描述、組件、憑證、批次組態等等。

Logic Apps 也會搭配支援的 Logic Apps SLA 來提供基本和標準整合帳戶。 當您只想使用訊息處理，或者作為與較大商務實體有交易夥伴關係的小型企業夥伴時，就可以使用基本整合帳戶。 標準整合帳戶支援更複雜的 B2B 關係，並增加您可以管理的實體數目。 如需詳細資訊，請參閱 [Azure 價格](https://azure.microsoft.com/pricing/details/logic-apps)。

## <a name="next-steps"></a>後續步驟

* [深入了解 Logic Apps][whatis]
* [建立第一個邏輯應用程式][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

