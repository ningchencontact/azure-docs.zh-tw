---
title: 更新 Azure Marketplace 的現有供應項目
description: 更新 Azure Marketplace 的現有供應項目
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
ms.openlocfilehash: 2bfa10441cf5531c9383527a21b033da26322b34
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073233"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>更新 Azure Marketplace 的現有供應項目 
==============================================

在您的供應項目上線之後，您可能會想要對它進行各種更新，例如：

1.  將新「套件」新增至現有的 SKU
2.  將新 SKU 新增至現有的「供應項目」
3.  更新供應項目/SKU Marketplace 中繼資料

您將使用 Cloud Partner 入口網站來實作這些變更，然後重新發佈供應項目。 本文將逐步引導您完成更新「Azure 應用程式」供應項目的各種不同層面。

<a name="unpermitted-changes-to-azure-application-offer"></a>不允許對 Azure 應用程式供應項目進行的變更 
-----------------------------------------------

「Azure 應用程式」供應項目/SKU 有些屬性在供應項目於 Azure Marketplace 中上線後，便無法修改。

* 供應項目的供應項目識別碼和發行者識別碼。
* 現有 SKU 的 SKU 識別碼。
* 更新已發佈的套件。

<a name="adding-a-new-package-to-an-existing-sku"></a>將新套件新增至現有的 SKU 
---------------------------------------

發行者可能會想要新增新版套件來更新現有的套件。 藉由上傳具有不同版本號碼的新套件，即可達成此新增。

1.  登入 [Cloud Partner 入口網站](http://cloudpartner.azure.com)
2.  在 [所有供應項目] 中，尋找您要更新的供應項目
3.  在 [SKU] 表單上，按一下您要更新其套件的 SKU\'
4.  按一下 [新增套件]\"\"，然後提供一個新版本
5.  上傳包含已更新之範本檔案的新 .zip 檔案
6.  按一下 [發佈] 來開始進行發佈工作流程，以便讓新 SKU 上線。

<a name="adding-a-new-sku-to-an-existing-offer"></a>將新 SKU 新增至現有的供應項目
-------------------------------------

您可以透過下列步驟選擇為現有供應項目提供新的 SKU：

1.  登入 [Cloud Partner 入口網站](http://cloudpartner.azure.com)
2.  在 [所有供應項目] 中，尋找您要更新的供應項目
3.  在 [SKU] 表單上，按一下 [新增 SKU]，然後在快顯視窗中提供 [SKU 識別碼]。
4.  請遵循[這裡](./cloud-partner-portal-managed-app-publish.md)指定的其餘步驟。
5.  按一下 [發佈] 來開始進行發佈工作流程，以便讓新 SKU 上線。

<a name="updating-offer-marketplace-metadata"></a>更新供應項目 Marketplace 中繼資料 
-----------------------------------

在某些情況下，您可能需要更新與您的供應項目相關聯的 Marketplace 中繼資料，例如更新您的公司標誌等。請依照下列步驟進行操作。

1.  登入 Cloud Partner 入口網站。
2.  在 [所有供應項目] 中，尋找您要更新的供應項目
3.  移至 [Marketplace] 表單，然後依照[這裡](../cloud-partner-portal/azure-applications/cpp-marketplace-tab.md)的指示來進行任何變更。
4.  按一下 [發佈] 來開始進行發佈工作流程，以便讓變更生效。
