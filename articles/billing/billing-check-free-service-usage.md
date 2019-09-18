---
title: 監視和追蹤 Azure 免費服務使用量
description: 了解如何在 Azure 入口網站和使用量 CSV 檔案中檢查免費服務使用量。
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3543bed7f699fd149ca7f2a6f61e9eb5aad5f1a3
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491429"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>檢查 Azure 免費帳戶隨附的免費服務使用量

對於 Azure 免費帳戶隨附的服務，除非您超過這些服務的限額，否則不會向您收費。 為了保持在限額內，您可以使用 Azure 入口網站或使用量檔案來監視和追蹤免費服務使用量。

## <a name="check-usage-in-the-azure-portal"></a>在 Azure 入口網站中檢查使用量

1.  登入 [Azure 入口網站](https://portal.azure.com)。

2.  在左側導覽區域中，選取 [所有服務]  。

3.  選取 **訂用帳戶** 。

4.  選取您註冊免費帳戶時建立的訂用帳戶。

    ![顯示全部訂用帳戶的螢幕擷取畫面](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  概觀區段會顯示訂用帳戶的基本資訊。 例如，訂用帳戶識別碼、供應項目類型和訂用帳戶名稱。 您也可以找到免費帳戶額度何時到期的資訊。

    ![顯示訂用帳戶基本資訊的螢幕擷取畫面](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  向下捲動找出目前和預估費用的相關資訊。 成本包括免費帳戶未隨附的服務使用量，以及超過免費服務限額的使用量。

    ![顯示訂用帳戶費用資訊的螢幕擷取畫面](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  概觀區段的最後一個部分有一個表格會顯示免費服務使用量。

    ![顯示免費服務使用量的螢幕擷取畫面](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    該表有下列資料行：

* **計量名稱：** 識別耗用計量的量值單位。 若要了解服務與計量的對應，請參閱[了解免費服務與計量的對應](billing-understand-free-service-meter-mapping.md)。
* **使用量/限制：** 目前月份的使用量和計量的限額。 您也可在狀態列中找到此資訊。
* **狀態：** 計量的使用量狀態。 根據您的使用量模式，您可有下列其中一個狀態：
  * **不在使用中：** 您未使用計量，或計費的使用量未達到計費系統的標準。
  * **已在 \<Date> 超過：** 您在 \<Date> 超過計量的限額。
  * **不太可能超過：** 您不太可能超過計量的限額。
  * **在 \<Date> 超過：** 您可能在 \<Date> 超過計量的限額。

## <a name="check-usage-with-the-usage-file"></a>透過使用量檔案查看使用量

使用量檔案會提供更詳細的 Azure 訂用帳戶資訊。 您可以從 Azure 帳戶中心下載每月和每日使用量檔案。 若要了解如何下載使用量檔案，並了解必要的存取，請參閱[取得發票和使用量](billing-download-azure-invoice-daily-usage-date.md)。 若要了解使用量檔案中的資料行，請參閱[了解使用量的字詞](billing-understand-your-usage.md)。

使用量檔案有免費和付費服務的使用量資訊。 免費服務計量的計量名稱結果會附加**免費**。 若要尋找免費計量，請在 Excel 中開啟該檔案，並篩選**計量類別資料行**找出有文字的儲存格 **- 免費** (使用文字篩選條件 &rarr; 包含篩選條件)。


![顯示免費服務使用量的螢幕擷取畫面](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- [升級您的訂用帳戶](billing-upgrade-azure-subscription.md)
