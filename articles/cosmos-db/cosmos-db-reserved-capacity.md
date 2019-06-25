---
title: 運用保留容量使 Azure Cosmos DB 資源費用達到最佳化
description: 了解如何購買 Azure Cosmos DB 保留容量，以節省計算費用。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 7944980ec1806d2c8c4ab908c71efd971ee0d7aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968962"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中獲得最低的保留容量費用

Azure Cosmos DB 保留容量有助於您透過預付一年或三年的 Azure Cosmos DB 資源來節省成本。 您可以利用 Azure Cosmos DB 保留容量來取得針對 Cosmos DB 資源佈建的輸送量折扣。 資源範例是資料庫和容器 (資料表、集合及圖表)。

Azure Cosmos DB 保留容量可以大幅降低 Cosmos DB 成本&mdash;預付一或三年期承諾用量費用，即可節省高達一般價格的 65%。 保留容量提供帳單折扣，而且不會影響 Azure Cosmos DB 資源的執行階段狀態。

Azure Cosmos DB 保留容量可涵蓋針對資源所佈建的輸送量。 它未涵蓋儲存體和網路費用。 購買保留容量後，符合保留屬性的輸送量費用就不會再按照預付型方案的費率來收費。 如需有關保留的詳細資訊，請參閱 [Azure 保留](../billing/billing-save-compute-costs-reservations.md)一文。

您可以從 [Azure 入口網站](https://portal.azure.com)購買 Azure Cosmos DB 保留容量。 若要購買保留容量：

* 您必須至少為一個企業或隨用隨付訂用帳戶的擁有者角色。  
* 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com)中啟用**新增保留執行個體**。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。
* 若為雲端解決方案提供者 (CSP) 方案，則只有系統管理員代表或銷售代表可以購買 Azure Cosmos DB 保留容量。

## <a name="determine-the-required-throughput-before-purchase"></a>購買之前，請決定所需的輸送量

保留大小應該以現有或即將部署的 Azure Cosmos DB 資源將要使用的輸送量總量為基礎。 您可以透過下列方式來判斷所需的輸送量：

* 取得跨 Azure Cosmos DB 帳戶、資料庫以及跨所有區域集合的總佈建輸送量歷程記錄資料。 例如，您可以從 `https://account.azure.com` 下載每日使用量聲明，以評估每日平均佈建的輸送量。

* 如果您是 Enterprise 合約 (EA) 客戶，則可以下載您的使用量檔案，以取得 Azure Cosmos DB 輸送量詳細資料。 請參閱使用量檔案中 [其他資訊]  區段的 [服務類型]  值。

* 您可以針對預期在接下來一年或三年期間執行的 Azure Cosmos DB 帳戶，加總所有工作負載的平均輸送量。 接著，您可以針對保留使用該數量。

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>購買 Azure Cosmos DB 保留容量

1. 登入 [Azure 入口網站](https://portal.azure.com)。  

2. 選取 [所有服務]   > [保留]   > [新增]  。  

3. 從 [選取產品類型]  窗格中，選擇 [Azure Cosmos DB]   > [選擇]  來購買新的保留容量。  

4. 請填寫必填欄位，如下表中所述：

   ![填寫保留容量表單](./media/cosmos-db-reserved-capacity/fill_reserved_capacity_form.png)

   |欄位  |描述  |
   |---------|---------|
   |名稱   |    保留容量的名稱。 此欄位會自動填入 `CosmosDB_Reservation_<timeStamp>`。 建立保留容量時，您可以提供不同的名稱。 或者，您可以在保留容量建立之後將它重新命名。      |
   |訂用帳戶  |   用來支付 Azure Cosmos DB 保留容量費用的訂用帳戶。 收取預付費用時，會使用所選訂用帳戶的付款方式。 訂用帳戶類型必須是下列其中一項： <br/><br/>  Enterprise 合約 (供應項目號碼：MS-AZR-0017P 或 MS-AZR-0148 P)：針對 Enterprise 訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。 <br/><br/> 隨用隨付 (供應項目號碼：MS-AZR-0003P 或 MS-AZR-0023P)：針對隨用隨付訂用帳戶，費用是透過訂用帳戶的信用卡或發票付款方式收取。    |
   |`Scope`   |   控制多少訂用帳戶可以使用與保留容量相關聯的計費權益選項。 它也會控制保留容量套用至特定訂用帳戶的方式。   <br/><br/>  如果您選取 [單一訂用帳戶]  ，保留容量折扣會套用至所選訂用帳戶中的 Azure Cosmos DB 執行個體。 <br/><br/>  如果您選取 [共用]  ，保留容量折扣會套用至計費內容內任何訂用帳戶中執行的 Azure Cosmos DB 執行個體。 計費內容取是以您註冊 Azure 的方式為基礎。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 針對隨用隨付客戶，共用範圍是帳戶系統管理員所建立的所有隨用隨付訂用帳戶。  <br/><br/> 您可以在購買保留容量之後變更保留範圍。  |
   |保留容量類型   |  佈建為要求單位的輸送量。 您可以購買的保留項目佈建的輸送量，這兩種設定-單一區域寫入以及多個區域寫入。|
   |保留容量單位  |      您想要保留的輸送量數量。 您可以藉由判斷每個區域所有 Cosmos DB 資源 (例如資料庫或容器) 所需的輸送量來計算此值。 然後將此值乘上與您 Cosmos DB 資料庫相關聯的區域數相。  <br/><br/> 例如：如果您有五個區域，其中每個區域每秒為 1 百萬個要求單位，則購買保留容量時，請選擇每秒 5 百萬個要求單位。    |
   |詞彙  |   一年或三年。   |

5. 在 [成本]  區段中，檢閱保留容量的折扣和價格。 此保留容量價格適用於其輸送量針對所有區域而佈建的 Azure Cosmos DB 資源。  

6. 選取 [購買]  。 購買成功時，您會看到下列頁面：

   ![填寫保留容量表單](./media/cosmos-db-reserved-capacity/reserved_capacity_successful.png)

購買保留容量之後，會立即套用至符合保留容量條件的任何現有 Azure Cosmos DB 資源。 如果您沒有任何現有的 Azure Cosmos DB 資源，保留容量將會在您部署符合保留容量條件的新 Cosmos DB 執行個體時套用。 在這兩種情況下，保留容量的期間都會在成功購買後立即生效。

當您的保留容量到期時，Azure Cosmos DB 執行個體將會繼續執行，而且會以一般隨用隨付費率計費。

## <a name="cancellation-and-exchanges"></a>取消和交換

在識別的權限的保留容量的協助，請參閱[了解如何將保留折扣套用至 Azure Cosmos DB](../billing/billing-understand-cosmosdb-reservation-charges.md)。 如果您要取消或交換 Azure Cosmos DB 的保留項目，請參閱[保留交換記錄和退費記錄](../billing/billing-azure-reservations-self-service-exchange-and-refund.md)。

## <a name="next-steps"></a>後續步驟

保留容量折扣會自動套用到符合保留容量範圍和屬性的 Azure Cosmos DB 資源中。 您可以透過 Azure 入口網站、PowerShell、Azure CLI 或 API 來更新保留容量的範圍。

*  若要了解如何將保留容量折扣套用至 Azure Cosmos DB，請參閱[了解 Azure 保留折扣](../billing/billing-understand-cosmosdb-reservation-charges.md)。

* 若要深入了解 Azure 保留項目，請參閱下列文章：

   * [什麼是 Azure 保留項目？](../billing/billing-save-compute-costs-reservations.md)  
   * [管理 Azure 保留](../billing/billing-manage-reserved-vm-instance.md)  
   * [了解 Enterprise 註冊的保留項目使用量](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [了解隨用隨付方案訂用帳戶的保留項目使用量](../billing/billing-understand-reserved-instance-usage.md)
   * [合作夥伴中心 CSP 計劃中的 Azure 保留容量](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
