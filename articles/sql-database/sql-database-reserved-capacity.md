---
title: 預付 Azure SQL Database 虛擬核心的費用以節省成本 | Microsoft Docs
description: 了解如何購買 Azure SQL Database 保留容量，以節省計算費用。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 07/19/2019
ms.openlocfilehash: 1cc8828f5a936b130480c2c7516d00b8bee6760f
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357271"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>預付具有 Azure SQL Database 保留容量的 SQL Database 計算資源

使用 Azure SQL Database 時，對於計算資源的預付費用，會比隨用隨付的價格還要划算。 若使用 Azure SQL Database 保留容量，即表示您預先承諾會使用 SQL Database 一或三年的時間，來換取大幅的計算費用折扣。 若要購買 SQL Database 保留容量，您必須指定 Azure 區域、部署類型、效能層級與期間。


您不需將保留指派給特定的 SQL Database 執行個體 (單一資料庫、彈性集區或受控執行個體)。 只要符合已執行或新部署的 SQL Database 執行個體，就會自動獲得好處。 透過購買保留，您會預付為期一或三年的計算費用。 購買保留容量後，符合保留屬性的 SQL Database 計算費用就不會再按照預付型方案的費率來收費。 保留容量並未涵蓋與 SQL Database 執行個體相關聯的軟體、網路或儲存體費用。 在保留期限結束時，計費好處會到期，隨後 SQL Database 便會按照預付型方案的費率來計費。 保留不會自動更新。 如需定價資訊，請參閱 [SQL Database 保留容量供應項目](https://azure.microsoft.com/pricing/details/sql-database/managed/)。

您可以在 [Azure 入口網站](https://portal.azure.com)購買 Azure SQL Database 保留容量。 若要購買 SQL Database 保留容量：

- 您至少必須是一個企業或個別訂用帳戶的擁有者角色, 且具有隨用隨付費率。
- 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com)中啟用**新增保留執行個體**。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。
- 若為雲端解決方案提供者 (CSP) 方案，則只有系統管理員代理人或銷售人員可以購買 SQL Database 保留容量。

如需如何向企業客戶和隨用隨付客戶收取購買保留費用的詳細資料，請參閱[了解 Enterprise 註冊的 Azure 保留使用量](../billing/billing-understand-reserved-instance-usage-ea.md)與[了解隨用隨付訂用帳戶的 Azure 保留使用量](../billing/billing-understand-reserved-instance-usage.md)。

## <a name="determine-the-right-sql-size-before-purchase"></a>先決定正確的 SQL 大小再購買

保留的大小應根據特定區域內現有或即將部署的單一資料庫、彈性集區或受控執行個體所使用的總計算量，並使用相同效能層級和硬體世代來決定。

例如，假設您要執行一個一般用途 Gen5 – 16 個虛擬核心的彈性集區，以及兩個商務關鍵性 Gen5 – 4 個虛擬核心的單一資料庫。 此外，假設您打算在下個月再部署一個一般用途 Gen5 – 16 個虛擬核心的彈性集區，以及一個商務關鍵性 Gen5 – 32 個虛擬核心的彈性集區。 還有，假設您知道您需要使用這些資源至少 1 年的時間。 在此情況下，您應該針對單一/彈性集區一般用途 - Gen5 購買 32 (2x16) 虛擬核心、為期 1 年的保留，以及針對單一/彈性集區商務關鍵性 - Gen5 購買 40 (2x4 + 32) 虛擬核心、為期 1 年的保留。

## <a name="buy-sql-database-reserved-capacity"></a>購買 SQL Database 保留容量

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務] > [保留]。
3. 選取 [**新增**], 然後在 [購買保留] 窗格中, 選取 [ **SQL Database** ] 以購買 SQL Database 的新保留。
4. 填寫必要欄位。 符合所選屬性的現有或全新單一資料庫、彈性集區或受控執行個體，就有資格獲得保留容量折扣。 取得折扣的 SQL Database 執行個體實際數目取決於選取的範圍和數量。
    ![提交 SQL Database 保留容量購買之前的螢幕擷取畫面](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

下表描述必要的欄位。

| 欄位      | 描述|
|------------|--------------|
|訂閱|用來支付 SQL Database 保留容量費用的訂用帳戶。 SQL Database 保留容量的預付費用會透過訂用帳戶的付款方式收取。 訂用帳戶類型必須是 Enterprise 合約 (供應項目號碼：MS-AZR-0017P-Ms-azr-0017p 或 MS-AZR-0017P-Ms-azr-0148p) 或隨用隨付定價的個別合約 (供應專案號碼:MS-AZR-0003P 或 MS-AZR-0023P)。 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。 針對使用隨用隨付定價的個別訂用帳戶, 費用會依訂用帳戶的信用卡或發票付款方法計費。|
|`Scope`       |虛擬核心保留容量範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。 如果您選取： <br/><br/>**共用**的 vCore 保留折扣會套用至計費內容內任何訂用帳戶中執行的 SQL Database 實例。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 針對隨用隨付客戶，共用範圍是帳戶系統管理員所建立的所有隨用隨付訂用帳戶。<br/><br/>**單一訂**用帳戶, vCore 保留折扣會套用到此訂用帳戶中的 SQL Database 實例。 <br/><br/>**單一資源群組**, 保留折扣會套用至所選訂用帳戶中的 SQL Database 實例, 以及該訂用帳戶內選取的資源群組。|
|區域      |SQL Database 保留容量所涵蓋的 Azure 區域。|
|RootFile|您要為其購買保留容量的 SQL 資源類型。|
|效能層級|SQL Database 執行個體的服務層級。
|詞彙        |一年或三年。|
|數量    |在 SQL Database 保留容量保留內購買的計算資源量。 數量是所選 Azure 區域中的數個虛擬核心, 以及要保留的效能層級, 將會獲得計費折扣。 例如, 如果您正在執行或計畫在美國東部區域的第5代16虛擬核心總計算容量中執行 SQL Database 實例, 則您會將數量指定為 16, 以將所有實例的權益最大化。 |

1. 檢閱 [成本] 區段中 SQL Database 保留容量的成本。
1. 選取 [購買]。
1. 選取 [檢視此保留] 以查看您的購買狀態。

## <a name="cancellations-and-exchanges"></a>取消和交換

如果您需要取消保留 SQL Database 保留容量，可能會有 12% 的提前解約金。 退款以您的購買價格或目前保留的價格為準，視孰者為低。 每年的退款金額上限為 50,000 美元。 您收到的退款是按比例計算的餘額扣除 12% 提前解約金的金額。 若要取消, 請移至 Azure 入口網站中的保留區, 然後選取 [**退款**]。

如果您需要將 [SQL Database 保留容量] 保留區變更為另一個區域、部署類型、效能層級或期間，您可以將它與等值或更高價值的另一個保留區交換。 新保留區的期間開始日期不會延續自交換的保留區。 1 或 3 年的期限會從您建立新的保留時起算。 若要交換, 請移至 Azure 入口網站中的保留區, 然後選取 [ **exchange**]。

如需有關如何交換或退款保留的詳細資訊, 請參閱[保留交換和退款](../billing/billing-azure-reservations-self-service-exchange-and-refund.md)。

## <a name="vcore-size-flexibility"></a>vCore 大小彈性

vCore 大小彈性可協助您在效能層級和區域內相應增加或相應減少，而不會失去保留容量優勢。 SQL Database 保留容量為您提供彈性地在集區與單一資料庫之間暫時移動最忙碌的資料庫，作為一般作業 (在相同的區域和效能層級) 的一部分，而不會失去保留容量優勢。 透過在保留區中保留未套用的緩衝區，您可以在不超出您預算的情況下有效地管理效能高峰。

## <a name="need-help-contact-us"></a>需要協助嗎? 請與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

虛擬核心保留容量折扣會自動套用至符合 SQL Database 保留容量範圍和屬性的 SQL Database 執行個體數目。 您可以透過 [Azure 入口網站](https://portal.azure.com)、PowerShell、CLI 或 API 來更新 SQL Database 保留容量的範圍。

若要了解如何管理 SQL Database 保留的容量保留，請參閱[管理 SQL Database 保留容量](../billing/billing-manage-reserved-vm-instance.md)。

若要深入了解 Azure 保留項目，請參閱下列文章：

- [什麼是 Azure 保留項目？](../billing/billing-save-compute-costs-reservations.md)
- [管理 Azure 保留項目](../billing/billing-manage-reserved-vm-instance.md)
- [了解 Azure 保留折扣](../billing/billing-understand-reservation-charges.md)
- [了解隨用隨付訂用帳戶的保留使用量](../billing/billing-understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](../billing/billing-understand-reserved-instance-usage-ea.md)
- [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](https://docs.microsoft.com/partner-center/azure-reservations)
