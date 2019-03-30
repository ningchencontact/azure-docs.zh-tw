---
title: 將移轉中使用自願性移轉工具的 Azure 監視器傳統警示
description: 了解如何使用自願性移轉工具移轉傳統警示規則。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 58c664beee942fe7115c7fff38a039c23bed6ac3
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632032"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>使用自願性移轉工具來移轉傳統警示規則

作為[先前所宣布](monitoring-classic-retirement.md)，在 Azure 監視器傳統警示會在 2019 年 7 月即將淘汰。 主動觸發移轉的移轉工具可在 Azure 入口網站中，而且即將推出，客戶在使用傳統的警示規則。 這篇文章會逐步引導您如何使用移轉工具在 2019 年 7 月中的自動移轉開始之前，主動移轉傳統警示規則。

## <a name="benefits-of-new-alerts"></a>新警示的優點

由 Azure 監視器中新整合警示取代傳統的警示。 新的警示平台具有下列優點：

- 透過各種不同的多維度計量警示[許多更多 Azure 服務](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)
- 新的計量警示支援[多重資源的警示規則](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)其中大幅降低管理多個規則的額外負荷。
- 統一的通知機制
  - [動作群組](action-groups.md)是一種模組化通知機制，適用於所有新的警示類型 （計量、 記錄和活動記錄檔）
  - 您也可以利用新的通知機制，例如 SMS、 語音及 ITSM 連接器
- [統一的警示體驗](alerts-overview.md)會在不同的訊號的所有警示 (計量、 活動記錄檔和記錄檔) 在一個地方

## <a name="before-you-migrate"></a>在移轉之前

移轉的一部分，傳統的警示規則會轉換為相等的新警示規則，並不會建立動作群組。

- 支援更多的功能，所以，通知裝載格式，以及使用 Api 來建立和管理新的警示規則是不同於傳統的警示規則。 了解[如何為移轉做準備](alerts-prepare-migration.md)。

- 無法使用此工具可移轉一些傳統的警示規則。 [了解哪些規則不是可移轉，並了解如何將它們移轉](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated)。

    > [!NOTE]
    > 移轉程序不會影響您的傳統警示規則的評估。 它們會繼續執行並傳送警示，直到移轉和新的警示規則可讓您開始評估。


## <a name="how-to-use-the-migration-tool"></a>如何使用移轉工具

下列程序說明如何移轉您在 Azure 入口網站中的傳統警示規則的觸發程序：

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [監視]。

2. 按一下 **警示**然後按一下**管理警示規則**或是**檢視傳統警示**。

3. 按一下 **移轉至新的規則**移至 移轉 登陸頁面。 此頁面會顯示一份所有訂用帳戶，並為其移轉狀態。

    ![移轉登陸](media/alerts-migration/migration-landing.png "移轉規則")

4. 使用此工具可移轉的所有訂閱會都標示**準備好移轉**。

    > [!NOTE]
    > 移轉工具推出階段中使用傳統的警示規則的所有訂閱。 導入的早期階段，您可能會看到一些為未準備好進行移轉的訂用帳戶。

5. 選取一或多個訂用帳戶，然後按一下**預覽移轉**

6. 在此頁面上，您可以看到傳統的警示規則，將訂用帳戶一次移轉的詳細資料。 您也可以**下載移轉詳細資料，此訂用帳戶**存成.csv 格式。

    ![移轉-preview](media/alerts-migration/migration-preview.png "預覽移轉")

7. 提供一或多個**電子郵件地址**移轉狀態的通知。 在移轉完成，或從您需要採取動作時，我們會傳送一封電子郵件。

8. 按一下 **開始移轉**。 閱讀 [確認] 對話方塊中顯示的資訊，並確認您是否已準備好開始移轉程序。

    >[!IMPORTANT]
    > 一旦您起始訂用帳戶的移轉程序，您不能編輯/建立訂用帳戶的傳統警示規則。 不過，傳統的警示規則會繼續執行，並提供您警示到都移轉為止。 這是為了確保之間傳統的警示規則和在移轉期間建立的新規則的精確度。 一旦完成您的訂用帳戶移轉，您可以不使用傳統的警示規則不再。

    ![移轉確認](media/alerts-migration/migration-confirm.png "確認開始移轉")

9. 為我們完成移轉，或需要來自您的動作，您會收到一封電子郵件在步驟 8 中提供的電子郵件地址。 您可以也會定期檢查從入口網站中的移轉登陸頁面的狀態。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="why-is-my-subscriptions-listed-as-not-ready-for-migration"></a>**為什麼我的訂用帳戶列為未準備好進行移轉？**

移轉工具推出階段中所有的客戶。 在早期階段中，大部分或所有訂用帳戶可能會標示為**還沒準備好移轉**。 不過，由 mid 年 4 月，所有訂用帳戶應該已準備好移轉。

準備好進行移轉訂用帳戶時，訂用帳戶擁有者會收到電子郵件通知此工具的可用性。 留意這項通知。

### <a name="who-can-trigger-the-migration"></a>**誰可以觸發移轉？**

在訂用帳戶層級指派給他們 「 監視參與者 」 角色的使用者將能夠移轉觸發程序。 深入了解[移轉程序的角色型存取控制](alerts-understand-migration.md#who-can-trigger-the-migration)。

### <a name="how-long-is-the-migration-going-to-take"></a>**多久移轉要花？**

對於大多數的訂閱，移轉通常完成下一個小時的時間。 您可以追蹤的移轉進度，從 [移轉] 登陸頁面。  在此期間，請確保您的警示仍在傳統警示系統或新的。

### <a name="what-can-i-do-if-i-run-into-an-issue-during-migration"></a>**如果我在移轉期間遇到問題，我可以做什麼？**

請遵循[疑難排解指南，以查看任何可能在移轉期間遇到的問題的補救步驟](alerts-understand-migration.md#common-issues-and-remediations)。 如果您需要任何動作來完成移轉，將會在移轉期間所提供的電子郵件地址通知您。

## <a name="next-steps"></a>後續步驟

- [為移轉做準備](alerts-prepare-migration.md)
- [了解移轉工具的運作方式](alerts-understand-migration.md)
