---
title: 從 Azure Marketplace 中刪除供應項目或 SKU | Microsoft Docs
description: 刪除供應項目或 SKU 的步驟。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 3370767947399b167f4f1c81b57d8f92edfa0c4d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242682"
---
<a name="delete-an-offer-or-sku-from-azure-marketplace"></a>從 Azure Marketplace 刪除供應項目或 SKU
==========================================

基於各種原因，您可能決定從 Marketplace 中移除您的供應項目。 「移除供應項目」可確保新的客戶無法再購買或部署您的供應項目，但是對現有客戶沒有任何影響。
「終止供應項目」是終止服務及/或您與現有客戶之間授權合約的程序。 供應項目移除及終止的相關指導方針與原則受 [Microsoft Marketplace 發行者合約](https://go.microsoft.com/fwlink/?LinkID=699560) (請參閱節
7) 與[同意參與原則](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)規範 (請參閱 6.2 節)。 本文件討論支援的不同刪除案例，以及您可以對其採取的步驟。

<a name="delete-a-live-sku-from-azure-marketplace"></a>從 Azure Marketplace 中刪除上線的 SKU
----------------------------------------

您可以使用下列步驟從 Azure Marketplace 刪除上線的 SKU︰

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。

2.  從 [所有供應項目] 索引標籤中選取您的供應項目。

3.  在畫面的左側窗格中，選取 [SKU] 索引標籤。

4.  選取您要刪除的 SKU，然後針對該 SKU 按一下 [刪除] 按鈕。

5.  將供應項目[重新發佈至](./cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) Azure Marketplace。

供應項目在 Azure Marketplace 上線之後，就會從 Azure Marketplace 和 Azure 入口網站中刪除 SKU。

<a name="roll-back-to-a-previous-sku-version"></a>復原為先前的 SKU 版本
----------------------------------

您可以遵循下列步驟以從 Azure Marketplace 中刪除上線的 SKU 的目前版本。 此程序完成時，SKU 將會回復為先前的版本。

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。

2.  從 [所有供應項目] 索引標籤中選取您的供應項目。

3.  在畫面的左側窗格中，選取 [SKU] 索引標籤。

4.  從已發佈的磁碟版本清單中刪除最新的 [磁碟版本]。

5.  將供應項目[重新發佈至](./cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) Azure Marketplace。

供應項目在 Azure Marketplace 上線之後，就會從 Azure Marketplace 和 Azure 入口網站中刪除所列 SKU 的目前版本。
SKU 將會回復為先前的版本。

<a name="delete-a-live-offer"></a>刪除上線的供應項目
-------------------

要求移除上線的供應項目時，有幾方面需要注意。 請遵循下列步驟來取得支援小組的指引，以從 Azure Marketplace 中移除上線的供應項目︰

1.  使用此[連結](https://go.microsoft.com/fwlink/?linkid=844975)或按一下Cloud Partner 入口網站右上角的 [支援] 以建立支援票證。

2.  在 [問題類型] 清單中選取您的特定供應項目類型，並選取 [類別] 清單中的 [移除已發佈的供應項目]。

3.  提交要求。

支援小組會引導您完成供應項目刪除程序。

>[!NOTE]
>刪除 SKU/供應項目不會影響目前已購買的 SKU/供應項目。 這些 SKU/供應項目會繼續如往常般運作。 不過，無法提供給新客戶進行購買。
