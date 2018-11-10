---
title: 從 Azure Marketplace 中刪除供應項目/SKU
description: 從 Azure Marketplace 中刪除供應項目/SKU
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5ad106d45c8bae2d41e0bde74b27f80f4d8ab79b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241271"
---
<a name="delete-an-offersku-from-azure-marketplace"></a>從 Azure Marketplace 中刪除供應項目/SKU 
==========================================

基於各種原因，您可能決定從 Marketplace 中移除您的供應項目。 新客戶無法再購買或部署您的供應項目，但現有客戶將不會受影響。
「終止供應項目」是終止服務及/或您與現有客戶之間授權合約的程序。 供應項目移除及終止的相關指導方針與原則受 [Microsoft Marketplace 發行者合約](https://go.microsoft.com/fwlink/?LinkID=699560) (請參閱節
7) 與[同意參與原則](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)規範 (請參閱 6.2 節)。 我們將討論不同的刪除案例，以及您可以採取的步驟。

<a name="delete-a-live-sku-from-azure-marketplace"></a>從 Azure Marketplace 中刪除上線的 SKU 
----------------------------------------

您可以遵循下列步驟以從 Azure Marketplace 中刪除上線的 SKU︰

1.  登入 [Cloud Partner 入口網站](http://cloudpartner.azure.com)。
2.  從 [所有供應項目] 索引標籤選取您的供應項目。
3.  在畫面的左側窗格中，選取 [SKU] 索引標籤。
4.  選取您要刪除的 SKU，然後針對該 SKU 按一下 [刪除] 按鈕。
5.  將供應項目重新發佈到 Azure Marketplace。

供應項目在 Azure Marketplace 上線之後，就會從 Azure Marketplace 和 Azure 入口網站中刪除 SKU。

<a name="roll-back-to-a-previous-sku-version"></a>復原為先前的 SKU 版本 
----------------------------------

您可以遵循下列步驟以從 Azure Marketplace 中刪除上線的 SKU 的目前版本。 此程序完成時，SKU 將會回復為先前的版本。

1.  登入 [Cloud Partner 入口網站](http://cloudpartner.azure.com)。
2.  從 [所有供應項目] 索引標籤選取您的供應項目。
3.  在畫面的左側窗格中，選取 [SKU] 索引標籤。
4.  從已發佈的套件清單刪除最新的 [套件版本]。
5.  將供應項目重新發佈到 Azure Marketplace。

供應項目在 Azure Marketplace 上線之後，就會從 Azure Marketplace 和 Azure 入口網站中刪除所列 SKU 的目前版本。
SKU 將會回復為先前的版本。

<a name="delete-a-live-offer"></a>刪除上線的供應項目 
-------------------

請遵循下列步驟來取得支援小組的指引，以從 Azure Marketplace 中移除上線的供應項目︰

1.  使用這個[連結](https://go.microsoft.com/fwlink/?linkid=844975)提出支援票證
2.  選取 [問題類型] 清單中的 [管理供應項目]，以及[類別] 清單中的 [修改已在生產中的供應項目和/或 SKU]。
3.  提交要求。

支援小組會引導您完成供應項目刪除程序。

刪除 SKU/供應項目將不會影響目前已購買的 SKU/供應項目。 它們將如以往般運作，但已無法提供給新客戶進行購買。
