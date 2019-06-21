---
title: 利用自願性移轉工具移轉您在 Azure 監視器中的傳統警示
description: 了解如何使用自願性移轉工具來移轉傳統警示規則。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 0c8aa00d069ae54584d8e828dab35c22048f1876
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295551"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>使用自願性移轉工具來移轉傳統警示規則

作為[先前所宣布](monitoring-classic-retirement.md)，即將在 2019 年 9 月淘汰 Azure 監視器中的傳統警示 (已原先於 2019 年 7 月)。 客戶使用傳統的警示規則的人員，而且想要自行觸發移轉至 Azure 入口網站中使用移轉工具。 這篇文章說明如何使用移轉工具將自動移轉開始在 2019 年 9 月之前，主動移轉傳統警示規則。

> [!NOTE]
> 因為導入的移轉工具中的延遲，所以傳統警示移轉的停用日期已[延伸到 2019 年 8 月 31 日](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)從最初宣布於 2019 年 6 月 30 日的日期。

## <a name="benefits-of-new-alerts"></a>新警示的優點

新的整合 Azure 監視器中的警示會取代傳統的警示。 新的警示平台具有下列優點：

- 您可以透過各種不同的多維度計量警示[許多更多 Azure 服務](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)。
- 新的計量警示支援[多重資源的警示規則](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)，大幅降低管理多個規則的額外負荷。
- 統一的通知機制，可支援：
  - [動作群組](action-groups.md)，適用於所有新的警示類型 （計量、 記錄和活動記錄檔） 的模組化的通知機制。
  - 新的通知機制，例如 SMS、 語音和 ITSM 連接器。
- [統一的警示體驗](alerts-overview.md)上不同的訊號 （計量、 記錄和活動記錄檔） 的所有警示都帶入同一個地方。

## <a name="before-you-migrate"></a>在移轉之前

移轉程序將傳統的警示規則轉換至新的對等的警示規則，並建立動作群組。 在準備時，請注意下列幾點：

- 通知承載格式和使用 Api 來建立和管理新的警示規則是不同於傳統的警示規則，因為它們支援更多的功能。 [了解如何準備移轉](alerts-prepare-migration.md)。

- 某些傳統的警示規則不能移轉使用的工具。 [了解哪些規則無法移轉，以及要如何處理它們](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)。

    > [!NOTE]
    > 移轉程序不會影響您的傳統警示規則的評估。 它們會繼續執行並傳送警示，直到他們在移轉和新的警示規則才會生效。

## <a name="how-to-use-the-migration-tool"></a>如何使用移轉工具

若要觸發您傳統的警示規則，在 Azure 入口網站移轉，請遵循下列步驟：

1. 在  [Azure 入口網站](https://portal.azure.com)，選取**監視器**。

1. 選取 **警示**，然後選取**管理警示規則**或是**檢視傳統警示**。

1. 選取 **移轉至新的規則**移至 移轉 登陸頁面。 此頁面會顯示一份所有訂用帳戶和其移轉狀態：

    ![移轉登陸](media/alerts-migration/migration-landing.png "移轉規則")

    可以使用工具來移轉的所有訂用帳戶會標示**準備好移轉**。

    > [!NOTE]
    > 移轉工具推出階段中使用傳統的警示規則的所有訂閱。 在推出初期階段，您可能會看到一些標示為未準備好進行移轉的訂用帳戶。

1. 選取一或多個訂用帳戶，然後選取**預覽移轉**。

    產生的頁面會顯示將會移轉訂用帳戶一次的傳統警示規則的詳細資料。 您也可以選取**下載移轉詳細資料，此訂用帳戶**取得採用 CSV 格式的詳細資料。

    ![移轉-preview](media/alerts-migration/migration-preview.png "預覽移轉")

1. 指定的移轉狀態通知的一或多個電子郵件地址。 當移轉完成，或如果您需要進行任何動作，您會收到電子郵件。

1. 選取 **開始移轉**。 閱讀 [確認] 對話方塊中顯示的資訊，並確認您已準備好開始移轉程序。

    > [!IMPORTANT]
    > 您起始移轉訂用帳戶之後，您將無法編輯或建立該訂用帳戶的傳統警示規則。 這項限制可確保您傳統的警示規則的任何變更都會遺失，期間移轉至新的規則。 雖然您無法變更您的傳統警示規則，它們仍會繼續執行，並提供警示，直到它們已經移轉。 完成您的訂用帳戶移轉之後，您無法再使用傳統的警示規則。

    ![移轉確認](media/alerts-migration/migration-confirm.png "確認開始移轉")

1. 移轉完成時，或不需要您採取動作，您會收到電子郵件到您稍早提供的位址。 您可以也會定期檢查移轉登陸頁面，在入口網站中的狀態。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>為什麼我的訂用帳戶列為未準備好進行移轉？

移轉工具即將推出的客戶在階段中。 在早期階段中，大部分或所有訂用帳戶可能會標示為**還沒準備好移轉**。 

準備好進行移轉訂用帳戶時，訂用帳戶擁有者會收到電子郵件訊息，指出此工具位。 提早寄出這個訊息。

### <a name="who-can-trigger-the-migration"></a>誰可以觸發移轉？

在訂用帳戶層級指派給他們 「 監視參與者 」 角色的使用者就能夠移轉觸發程序。 [深入了解在移轉程序的角色型存取控制](alerts-understand-migration.md#who-can-trigger-the-migration)。

### <a name="how-long-will-the-migration-take"></a>移轉需要多久？

完成大部分訂用帳戶中在一小時的移轉。 您可以追蹤的移轉登陸頁面上的移轉進度。 在移轉期間，確保您的警示仍在傳統警示系統或新的。

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>如果我在移轉期間遇到問題，我可以做什麼？

請參閱[疑難排解指南](alerts-understand-migration.md#common-problems-and-remedies)在移轉期間，可能會面臨的問題的相關說明。 如果您需要任何動作來完成移轉，將會在您設定此工具時所提供的電子郵件地址通知您。

## <a name="next-steps"></a>後續步驟

- [為移轉做準備](alerts-prepare-migration.md)
- [了解移轉工具的運作方式](alerts-understand-migration.md)
