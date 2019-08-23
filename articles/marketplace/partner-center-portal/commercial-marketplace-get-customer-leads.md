---
title: 設定潛在客戶 |Azure Marketplace
description: 在商業 marketplace 中設定客戶潛在客戶。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 31dcc8c1e35b627b231dbe2a62998c8514d05a20
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902646"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>來自您 marketplace 供應專案的客戶領導

潛在客戶有興趣, 或從[Azure Marketplace](https://azuremarketplace.microsoft.com)或從[AppSource](https://appsource.microsoft.com)部署您的供應專案。 一旦您的供應專案發佈至 marketplace, 您就會收到客戶的潛在客戶。 本文將說明:

* 您的 marketplace 供應專案如何產生潛在客戶, 以確保您不會錯過商機。 
* 將您的 CRM 連線到您的供應專案, 讓您可以在單一集中位置管理您的潛在客戶。
* 瞭解我們傳送給您的潛在客戶資料, 讓您可以追蹤與您聯繫的客戶。

## <a name="generate-customer-leads"></a>產生潛在客戶

以下是產生潛在客戶的地方:

1. 當客戶在從 marketplace 選取 [聯絡我] 之後, 同意分享他們的資訊。 這位潛在客戶是**初始興趣**的潛在客戶, 我們會在其中分享有關取得您的產品之客戶的資訊。 潛在客戶位居分享購買漏斗的最頂端。

      ![Dynamics 365 聯絡我](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. 當客戶選取 [立即取得] 或 [建立] (在[Azure 入口網站](https://portal.azure.com/)中) 以取得您的供應專案時, 此潛在客戶為使用中的**潛在客戶**, 我們會分享已開始部署您的產品之客戶的相關資訊。

    ![立即取得 SQL](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server 建立](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. 客戶接受「試用產品」或啟動您供應專案的「免費試用」。 試用產品或免費試用版是加速的機會, 可讓您立即與潛在客戶分享您的業務, 而不會有任何進入障礙。

    ![Dynamics 365 試用產品](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 試用產品](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>連接到您的 CRM 系統

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>瞭解潛在客戶資料

您在客戶擷取程序期間收到的每位潛在客戶，在特定欄位中都有資料。 要查看的第一個欄位是`LeadSource`欄位, 其遵循此格式:**來源-動作** | **供應**專案。

**來源**：此欄位的值會根據產生潛在客戶的 marketplace 來填入。 可能的值`"AzureMarketplace"`為`"AzurePortal"`、和`"AppSource (SPZA)"`。

**動作**：此欄位的值會根據客戶在 marketplace 中所採取的動作 (產生潛在客戶) 來填入。 

可能的值包括：

- "INS" -- 安裝。 當客戶購買您的產品時，會在 Azure Marketplace 或 AppSource 上進行此動作。
- "PLT" -- 代表合作夥伴主導的試用版。 當客戶使用 [與我連絡] 選項時，會在 AppSource 上進行此動作。
- "DNC" -- 請勿連絡。 當交叉列於您的應用程式頁面的合作夥伴收到連絡要求時，會在 AppSource 上進行此動作。 我們分享此客戶交叉列於您的應用程式的通知，但無須連絡他們。
- 「建立」--此動作僅在 Azure 入口網站內, 而且會在客戶向其帳戶購買您的供應專案時產生。
- "StartTestDrive" -- 此動作僅限試用產品，並當客戶開始進行產品試用時產生。

**供應項目**：您在 marketplace 中可能有多個供應專案。 此欄位的值會根據產生潛在客戶的供應專案而填入。 發行者識別碼和供應專案識別碼都是在此欄位中傳送, 而且是您將供應專案發佈至 marketplace 時提供的值。

下列範例會以預期的格式`publisherid.offerid`顯示範例值: 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>客戶資訊

客戶的資訊會透過多個欄位傳送。 下列範例會顯示潛在客戶中包含的客戶資訊。

- 姓John
- LastNameSmith
- 電子郵件： jsmith\@microsoft.com
- 來電1234567890
- 國家/地區:US
- 家Microsoft
- 標題:CTO

>[!Note]
>上述範例中，並非所有資料一律可供每位潛在客戶使用。 因為您會從「客戶領導區」一節中所述的多個步驟中取得潛在客戶, 所以處理潛在客戶的最佳方式是將記錄刪除, 並將後續的資料個人化。 透過這個方式，每位客戶都能獲得適當的訊息，而您能打造獨特的客戶關係。

## <a name="best-practices-for-lead-management"></a>潛在客戶管理的最佳做法

1. *處理*-定義清楚的銷售程式, 包含里程碑、kpi 和明確小組擁有權。
2. *限定*性-定義必要條件, 以指出潛在客戶是否已完整限定。 請確定銷售或行銷代表會謹慎地限定潛在客戶, 再透過完整的銷售程式取得。
3. *後續操作*-別忘了追蹤, 預期一般交易需要5到12個後續呼叫
4. *培養*-培養您的潛在客戶, 以取得更高收益的方式。

## <a name="leads-frequently-asked-questions"></a>潛在客戶常見問題

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>我可以在哪裡取得設定潛在客戶目的地的協助？

您可以在[這裡](#connect-to-your-crm-system)找到檔, 或透過 aka.ms/marketplacepublishersupport 提交支援票證, 然後選取 [**供應專案建立**] →**您的供應**專案→ [**潛在客戶管理**設定]。

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>我是否必須設定潛在客戶目的地, 才能在 marketplace 上發佈供應專案？

答案取決於您要發佈的供應專案類型。 SaaS 和 Dynamics 365 for Customer Engagement 供應專案清單為「連絡人 Me」、所有 Dynamics 365 for Operations 供應專案、所有 Dynamics 365 Business Central 供應專案, 以及所有諮詢服務供應專案都需要連接至潛在客戶目的地。 如果未列出您的供應專案類型, 則不需要。 不過, 建議您設定潛在客戶目的地, 讓您不會錯過商機。

### <a name="how-can-i-find-the-test-lead"></a>如何找出測試潛在客戶？

`“MSFT_TEST”`在您的潛在客戶目的地中搜尋, 以下是 Microsoft 的範例測試潛在客戶:

```
company = MSFT_TEST_636573304831318844
country = US
description = MSFT_TEST_636573304831318844
email = MSFT_TEST_636573304831318844@test.com
encoding = UTF-8
encoding = UTF-8
first_name = MSFT_TEST_636573304831318844
last_name = MSFT_TEST_636573304831318844
lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name>
oid = 00Do0000000ZHog
phone = 1234567890
title = MSFT_TEST_636573304831318844
```

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>我有上線的供應專案, 但我沒看到任何潛在客戶嗎？

請確定您的潛在客戶目的地連接有效。 當您在合作夥伴中心的供應專案上按 [發佈] 之後, 我們會將測試潛在客戶傳送給您。 如果您看到測試潛在客戶, 則連接是有效的。 您也可以在預覽步驟中嘗試取得供應專案預覽, 方法是在 marketplace 的清單中按一下 [立即取得]、[連絡人我] 或 [免費試用], 藉此測試您的潛在客戶連線。

此外, 請確定您要尋找正確的資料。 本檔的[瞭解潛在客戶資料](#understand-lead-data)一節中的內容說明我們傳送到潛在客戶目的地的潛在客戶資料。

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>我已將 Azure BLOB 設定為我的潛在客戶目的地, 為什麼看不到潛在客戶？

已不再支援 Azure Blob 潛在客戶目的地, 因此您缺少供應專案所產生的任何客戶潛在客戶。 切換至其他任何[潛在客戶目的地選項](./commercial-marketplace-get-customer-leads.md)。 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>我收到來自 Marketplace 的電子郵件, 為什麼在我的 CRM 中找不到潛在客戶？

終端使用者的電子郵件網域可能來自 .edu。 基於隱私權考慮, 我們不會從 edu 網域傳遞 PII 資料。 透過 aka.ms/marketplacepublishersupport 提交支援票證。

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>我已將 Azure 資料表設定為我的潛在客戶目的地, 如何才能查看潛在客戶？

您可以從 Azure 入口網站存取儲存在 Azure 資料表中的潛在客戶資料, 或者您可以免費下載並安裝[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/), 以查看您的 Azure 儲存體帳戶的資料表資料。

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>我已將 Azure 資料表設定為我的潛在客戶目的地, 每當 Marketplace 傳送新的潛在客戶時, 是否可以收到通知？

可以, 請遵循指示來設定 Microsoft flow, 以在[這裡](./commercial-marketplace-lead-management-instructions-azure-table.md)的檔中將潛在客戶新增至 Azure 資料表時傳送電子郵件。

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>我已將 Salesforce 設定為我的潛在客戶目的地, 為什麼找不到潛在客戶？

檢查「web 到潛在客戶」表單是否為根據挑選清單的必要欄位。 如果是，請將欄位切換為非必要文字欄位。

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>我的潛在客戶目的地發生問題, 而我錯過了一些潛在客戶。 我可以在電子郵件中傳送給我嗎？

由於 PII (個人識別資訊) 原則，我們無法透過不安全的電子郵件分享潛在客戶資訊。

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>我已將 Azure 資料表設定為我的潛在客戶目的地, 它需要多少費用？

潛在客戶的一般資料很小 (幾乎所有的發行者都 < 1 GB)。 售價取決於所收到的潛在客戶數量，如果在一個月內收到 1,000 個潛在客戶，則成本大約 50 分。 如需儲存體定價的詳細資訊，請參閱[儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

如果您的問題仍未獲得解答, 請透過 aka.ms/marketplacepublishersupport 聯絡支援, 然後選取 [**供應專案建立**] →**您的供應**專案→ [**潛在客戶管理**設定]。 

## <a name="next-steps"></a>後續步驟

技術設定就緒之後, 您應該將這些潛在客戶納入目前的銷售 & 行銷策略和操作程式。 我們有興趣進一步了解您的整體銷售流程，並想與您密切合作，為您提供高品質潛在客戶及足夠的資料，讓您獲致成功。 我們歡迎您針對我們如何能最佳化及改善我們傳送給您的潛在客戶，及提供額外資料協助這些客戶達成您的成就，提出您的意見反應。 請讓我們知道您是否有興趣[提供意見](mailto:AzureMarketOnboard@microsoft.com)反應和建議, 讓您的銷售小組能夠更成功地使用 Marketplace 潛在客戶。
