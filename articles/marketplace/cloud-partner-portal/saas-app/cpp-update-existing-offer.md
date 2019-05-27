---
title: 更新現有的 Azure SaaS 應用程式供應項目 |Azure Marketplace
description: 如何在 Azure Marketplace 上更新現有的 SaaS 應用程式供應項目。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: pbutlerm
ms.openlocfilehash: 2195c9a5e1f0d3683ea8cf6564d97cbabd072f81
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834206"
---
# <a name="update-an-existing-saas-application-offer"></a>更新現有的 SaaS 應用程式供應項目

在您的供應項目發佈並上線之後，您可能想要對它進行多種更新。 您對供應項目新版本所做的任何變更都應該儲存並重新發行，以在 Marketplace 中反映您所做的變更。 本文將逐步說明在 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)中更新 SaaS 供應項目的各個層面。

> [!IMPORTANT] 
> SaaS 提供的功能正移轉至[Microsoft 合作夥伴中心](https://partner.microsoft.com/dashboard/directory)。  所有新的發行者必須使用合作夥伴中心建立新的 SaaS 供應項目，以及管理現有的供應項目。  SaaS 供應項目目前的發行者 batchwise 移轉從 Cloud Partner 入口網站來合作夥伴中心。  Cloud Partner 入口網站會顯示狀態訊息，指出何時已移轉特定的現有供應項目。

您可能會因為某些原因而想要更新供應項目，例如：

- 在現有的應用程式中新增版本。
- 更新應用程式。
- 在應用程式中新增功能。
- 更新供應項目的市集中繼資料。

為了協助您修改這些項目，入口網站提供了 [比較] 與 [歷程記錄] 功能。

## <a name="unpermitted-changes-to-a-saas-offer"></a>不允許對 SaaS 供應項目進行的變更

當 SaaS 供應項目於 Azure Marketplace 上線後，便無法變更該供應項目的某些屬性。 您無法變更下列設定：

- 供應項目的供應項目識別碼和發行者識別碼
- 版本標記，例如：`1.0.1`
- 現有供應項目的計費/授權模式變更。

## <a name="common-update-operations"></a>一般更新作業
 
下列更新作業很常見。

### <a name="update-offer-contacts"></a>更新供應項目連絡人

使用下列步驟來更新供應項目的支援連絡人。

1. 登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。
2. 在 [所有供應項目] 下，尋找您要更新的供應項目。
3. 移至 [連絡人] 索引標籤。更新您的連絡人。
4. 選取 [發佈] 啟動工作流程，以發佈變更。


### <a name="update-offer-marketplace-metadata"></a>更新供應項目市集中繼資料

使用下列步驟來更新與供應項目關聯的市集中繼資料。 (例如：公司名稱、標誌等等)

1. 登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。
2. 在 [所有供應項目] 下，尋找您要更新的供應項目。
3. 移至 [電子店面詳細資料] 索引標籤。使用[發佈 SaaS 供應項目](./cpp-publish-offer.md)一文的指示來進行中繼資料變更。
4. 選取 [發佈] 啟動工作流程，以發佈變更。

## <a name="compare-feature"></a>比較功能

當您變更已發佈的供應項目之後，即可使用 [比較] 功能來稽核您所做的變更。 下一個螢幕擷取畫面顯示已發佈供應項目的 [比較] 選項。

![使用 [比較] 來查看 Cloud Partner 入口網站的供應項目變更](./media/saas-offer-compare.png)

### <a name="to-use-the-compare-feature"></a>若要使用 [比較] 功能：

1. 在編輯流程中的任何時間點，選取您供應項目的 [比較]。
2. 並排檢視行銷資產與中繼資料。

## <a name="publishing-history"></a>發佈記錄

若要查看發佈活動記錄，請在 Cloud Partner 入口網站左邊導覽功能表列中，選擇 [記錄] 索引標籤。 您可以檢視在您的 Microsoft Azure Marketplace 供應項目生命週期期間發生的動作 (含時間戳記)。

![查看 Cloud Partner 入口網站的供應項目記錄](./media/saas-offer-history.png)

您可以使用 [稽核記錄] 頁面來搜尋特定的供應項目，並套用篩選條件，例如發行者、 優惠和事件類型 (例如 OfferGoLiveRequested。)您也可以將稽核記錄下載為 csv 檔案。


## <a name="next-steps"></a>後續步驟

[SaaS 應用程式供應項目](./cpp-saas-offer.md)