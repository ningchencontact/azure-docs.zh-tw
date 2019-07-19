---
title: 預付保留容量的 Azure App Service 隔離戳記費用
description: 瞭解如何預付保留容量的 Azure App Service 隔離戳記費用, 以節省成本。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: banders
ms.openlocfilehash: 40ccee7a993ce39a9b4c7a86309b0554daa56026
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298265"
---
# <a name="prepay-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>預付保留容量的 Azure App Service 隔離戳記費用

您可以在三年期內還要划算您的戳記使用量, 以節省 Azure App Service 隔離戳記費用的金錢。 若要購買隔離的戳記費用保留容量, 您必須選擇將會部署戳記的 Azure 區域, 以及要購買的戳記數目。

當您購買保留時, 符合保留屬性的隔離戳記費用使用量不再以隨用隨付費率計費。 保留會自動套用至符合保留容量範圍和區域的隔離戳記數目。 您不需要將保留指派給隔離的戳記。 保留專案不會套用至背景工作, 因此與戳記相關聯的任何其他資源也會分開計費。

當保留容量到期時, 隔離戳記會繼續執行, 但會以隨用隨付費率計費。 保留不會自動更新。

## <a name="determine-the-right-reservation-to-purchase"></a>決定要購買的正確保留專案

藉由購買保留區, 您就可以在接下來的三年期間, 預先支付使用保留數量的費用。 檢查您的使用量資料, 以判斷您目前使用的 App Service 隔離式方案戳記數目, 並可能會在未來使用。

此外, 請確定您瞭解隔離戳記如何發出 Linux 或 Windows 計量。

- 根據預設, 空的隔離戳記會發出 Windows 戳記計量。 例如, 未部署任何背景工作角色。 如果 Windows 背景工作角色部署在戳記上, 它會繼續發出此計量。
- 如果您部署 Linux 背景工作角色, 計量會變更為 Linux 戳記計量。
- 在部署 Linux 和 Windows 背景工作角色的情況下, 戳記會發出 Windows 計量。

因此, 戳記計量可能會在戳記的生命週期內, 于 Windows 和 Linux 之間變更。

如果您在戳記上有一或多個 Windows worker, 請購買 Windows 戳記保留專案。 只有當您打算在戳記上_只有_linux 背景工作角色時, 才應該購買 linux 戳記保留專案。

## <a name="buy-isolated-stamp-reserved-capacity"></a>購買隔離戳記保留容量

您可以在[Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)中購買隔離的戳記保留容量。 若要購買保留容量, 您必須至少擁有一個 enterprise 訂用帳戶的「擁有者」角色, 或具有「隨用隨付」費率的個別訂用帳戶。

- 針對企業訂用帳戶, 必須在[EA 入口網站](https://ea.azure.com/)中啟用 [**新增保留實例**] 選項。 或者, 如果設定已停用, 您必須是 EA 系統管理員。
- 針對雲端解決方案提供者 (CSP) 計畫, 只有系統管理員代理程式或銷售代理人可以 SQL 資料倉儲保留容量購買。

**購買:**

1. 移至 [ [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)]。
1. 選取一個訂用帳戶。 使用 [**訂**用帳戶] 清單選擇用來支付保留容量的訂用帳戶。 訂用帳戶的付款方法會收取保留容量的預付成本。 訂用帳戶類型必須是 Enterprise 合約 (供應項目號碼：MS-AZR-0017P 或 MS-AZR-0148P) 或隨用隨付 (供應項目號碼：MS-AZR-0017P-Ms-azr-0003p 或 MS-MS-AZR-0017P-Ms-azr-0023p) 或 CSP 訂用帳戶。
    - 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。
    - 針對隨用隨付訂用帳戶，費用是透過訂用帳戶的信用卡或發票付款方式收取。
1. 選取**範圍**以選擇訂用帳戶範圍。
    - **單一資源群組範圍**—只會將保留折扣套用至所選資源群組中的相符資源。
    - **單一訂**用帳戶範圍-將保留折扣套用至所選訂用帳戶中的相符資源。
    - **共用範圍**-將保留折扣套用至帳單內容中合格訂用帳戶的相符資源。 針對 Enterprise 合約客戶, 計費內容為註冊。 針對使用隨用隨付費率的個別訂用帳戶, 計費範圍是由帳戶管理員所建立的所有合格訂用帳戶。
1. 選取**區域**以選擇保留容量涵蓋的 Azure 區域, 並將保留專案新增至購物車。
1. 選取隔離的計畫類型, 然後按一下 [**選取**]。  
    ![實例](./media/billing-prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. 輸入要保留的 App Service 隔離式方案戳記數量。 例如, 有三個的數量會提供一個區域的「保留戳記」給您。 按一下 **下一步審查 + 購買**。
1. 請參閱, 並按一下 [**立即購買**]。

購買之後, 請移至 [[保留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)] 以查看購買狀態, 並隨時加以監視。

## <a name="cancellations-and-exchanges"></a>取消和交換

如果您需要取消隔離戳記保留容量, 可能會有 12% 的提前終止費用。 退款以您購買價格或目前保留價格的最低價格為準。 每年的退款限制為 $50000.00。 您收到的退款是剩餘的按比例餘額減去 12% 的提前終止費用。 若要取消, 請移至 Azure 入口網站中的保留區, 然後選取 [**退款**]。

如果您需要將隔離的戳記保留容量移到另一個區域, 您可以將它交換為等於或大於價值的另一個保留。 新保留的期限開始日期不會延續自交換的保留。 當您建立新的保留時, 會啟動三年期。 若要交換, 請移至 Azure 入口網站, 選取您想要交換的保留區, 然後選取 [ **exchange**]。

如需有關如何交換或退款保留的詳細資訊, 請參閱[保留交換和退款](billing-azure-reservations-self-service-exchange-and-refund.md)。

## <a name="discount-application-shown-in-usage-data"></a>使用量資料中顯示的折扣應用程式

使用方式資料的有效價格為零, 可取得保留折扣。 使用量資料會顯示每個保留中每個戳記實例的保留折扣。

如需保留折扣如何顯示在使用量資料中的詳細資訊, 請參閱如果您是 Enterprise 合約 (EA) 客戶, 則[取得 Enterprise 合約保留成本和使用量](billing-understand-reserved-instance-usage-ea.md)。 否則, 請參閱[使用隨用隨付費率, 瞭解個別訂用帳戶的 Azure 保留使用量](billing-understand-reserved-instance-usage.md)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure 保留項目，請參閱下列文章：
  - [什麼是 Azure 保留項目？](billing-save-compute-costs-reservations.md)
  - [瞭解如何套用 Azure App Service 隔離戳記保留折扣](billing-reservation-discount-app-service-isolated-stamp.md)
  - [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
