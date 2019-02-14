---
title: 更新現有 Power BI 應用程式供應項目 - Azure Marketplace | Microsoft Docs
description: 在 Power BI 應用程式發佈到 Microsoft AppSource Marketplace 之後更新它。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 635e2e71bb952aaee761df6a1d5d87c46db531f6
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665699"
---
# <a name="update-an-existing-power-bi-app-offer"></a>更新現有 Power BI 應用程式供應項目

本文將逐步說明在 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)更新 Power BI 應用程式供應項目並重新發佈的各個層面。  需要更新供應項目的常見原因如下：

- 更新 Power BI 中應用程式的內容，並從新封裝的應用程式取得新的安裝 URL
- 更新供應項目的市集中繼資料：銷售、行銷或支援資訊和資產
 
為了協助您修改這些項目，入口網站提供了 **「比較」** 與 **「記錄」** 的功能。


## <a name="unpermitted-changes-to-offer"></a>不允許對供應項目進行的變更

供應項目在 AppSource 上架之後，Power BI 應用程式的某些屬性就無法變更，主要為 [供應項目識別碼] 和 [發行者識別碼]。


## <a name="common-update-operations"></a>一般更新作業

雖然在 Power BI 應用程式應用程式上有各式各樣的特性可供變更，以下幾種作業較為常見。


### <a name="update-app-content-in-power-bi"></a>更新 Power BI 中的應用程式內容

Power BI 中的應用程式通常會定以新期更新新內容、安全性更新程式等等。 在此情況下，您可以使用下列步驟來更新新應用程式內容安裝的 URL：

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。
2.  在 [所有供應項目] 下方，尋找您要更新的供應項目。
3.  在 [技術資訊] 索引標籤中，輸入新的安裝程式 URL。
4.  按一下 [發佈] 來開始工作流程，將您應用程式的新版本發佈到 AppSource。


### <a name="update-offer-marketplace-metadata"></a>更新供應項目市集中繼資料

使用下列步驟來更新與供應項目關聯的市集中繼資料，例如公司名稱、標誌等等：

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。
2.  在 [所有供應項目] 下，尋找您要更新的供應項目。
3.  移至 [電子店面詳細資料] 索引標籤，然按照 [Power BI 應用程式 [電子店面詳細資料] 索引標籤](./cpp-storefront-details-tab.md)中的指示操作，以進行中繼資料變更。
4.  按一下 [發佈] 啟動工作流程，以發佈您的變更。


## <a name="compare-feature"></a>比較功能

當您變更已發行的供應項目時，您可以使用「比較」功能來稽核已進行的變更。 若要使用此功能：

1.  在編輯流程中的任何時間點，按一下您供應項目的 [比較] 按鈕。

    ![比較功能按鈕](./media/compare-feature-button.png)

2.  並排檢視行銷資產與中繼資料。


## <a name="history-of-publishing-actions"></a>發佈動作的歷程記錄

若要檢視發佈活動記錄，請在 Cloud Partner 入口網站左邊導覽功能表列中，按一下 [記錄] 索引標籤。 可在該處檢視您 AppSource 供應項目生命週期中發生的動作 (含時間戳記)。


## <a name="next-steps"></a>後續步驟

您應該定期使用 [Cloud Partner 入口網站](https://cloudpartner.azure.com/#insights)的 [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) 功能，來提供您對 Marketplace 客戶及使用方式的見解。  
