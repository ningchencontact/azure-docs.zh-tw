---
title: Seller Insights 版本資訊 | Microsoft Docs
description: 提供 Seller Insights 功能變更的相關資訊。
services: Azure, Marketplace, Cloud Partner Portal,
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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ec3784a91f8aeb7f0fedd13c9ab86a844832a578
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805482"
---
<a name="seller-insights-release-notes"></a>Seller Insights 版本資訊 
===============================
(發行日期：2018 年 7 月 28 日)

此文章提供 [Cloud Partner 入口網站](https://cloudpartner.azure.com/#insights)中 Seller Insights 功能變更的相關資訊。


<a name="release-highlights"></a>版本要點
------------------

-   *預估價格*提供含有貨幣轉換影響的客戶收費檢視。
-   *預測支出*提供潛在支出的預先檢視。
-  *使用量參考識別碼*提供客戶使用量與支出訂單之間的資料精確度
-   *每日使用量*提供針對客戶使用量更細微且更佳的見解。


<a name="changes-to-data-structure-and-taxonomy"></a>變更資料結構和分類法
--------------------------------------

下表列出此版本已新增或大幅變更的計量資訊。 

| **新字詞**                   |    **定義**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| 價格 (CC)                     | 指定 SKU 單位使用量的價格 (以客戶的貨幣為單位)。       |
| 預估擴充費用 (CC) | 指定 SKU 單位使用量數量的預估擴充費用 (以客戶的貨幣為單位)。 由於四捨五入或截斷錯誤，這個值可能並不精確。   |
| 發行者貨幣 (PC)        | 發行者用於支出的常用貨幣。                               |
| 預估價格 (PC)           | 指定 SKU 的單位使用量預估價格，以計算使用量當日的外幣兌換匯率為基礎 (以發行者的貨幣為單位)。 由於四捨五入或截斷錯誤，這個值可能並不精確。   |
| 預估擴充費用 (PC) | 指定 SKU 的單位使用量數量預估擴充費用，以計算使用量當日的外幣兌換匯率為基礎 (以發行者的貨幣為單位)。 由於四捨五入或截斷錯誤，這個值可能並不精確。 |
| 預估支出 (PC)          | 指定 SKU 的單位使用量數量預估支出，以計算使用量當日的外幣兌換匯率為基礎 (以發行者的貨幣為單位)。 由於四捨五入或截斷錯誤，這個值可能並不精確。   |
| 使用量參考                | 針對指定 SKU 的一或多天客戶使用量識別碼，與支出報告中的某個項目相關聯。 |
|  |  |
