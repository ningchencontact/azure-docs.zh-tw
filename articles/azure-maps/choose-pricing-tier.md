---
title: 選擇 Azure 地圖服務的正確定價層 | Microsoft Docs
description: 深入了解 Azure 地圖服務提供的定價層
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: ac88bc95ac9ffe4c2f32307b0bee427829242add
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234674"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>在 Azure 地圖服務中選擇正確的定價層

Azure 地圖服務提供兩個定價層。 本文旨在協助您根據需求來選擇正確的定價層。 若要協助選擇正確的定價層，請詢問自己以下兩個問題。

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>我打算使用哪些地理空間功能？
如果核心地理空間 API 符合您的服務需求，那麼 S0 定價層適合您。 如果您想要應用程式有更進階的功能，請考慮選擇 S1 定價層。 舉例來說，會有空拍加上混合影像、取得路線範圍，以及批次地理編碼的功能。 隨後的**定價層功能**表可讓您深入了解您應用程式的需求。 此外，也會協助您選擇最適合您應用程式的定價層。

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>我打算支援多少並行使用者人數？ 
S0 和 S1 定價層會處理不同的資料輸送量。 選擇 Azure 地圖服務定價層之前，請先自問一些問題。 例如「我想要支援多少並行使用者？」 S0 定價層最多可處理**每秒 50 筆查詢**。 S1 定價層可處理**每秒超過 50 筆查詢**。

### <a name="pricing-tier-capabilities"></a>定價層功能

| 功能                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Search                                  |        ✓           |     ✓    |
| 批次地理編碼 (預覽)              |                   |     ✓    |
| 透過搜尋繪製多邊形          |                   |     ✓    |
| 路由                                 |        ✓           |     ✓    |
| 路線範圍                    |                   |     ✓    |
| 批次路線規劃 (預覽)                |                   |     ✓    |
| 矩陣路線規劃 (預覽)               |                   |     ✓    |
| 轉譯                                  |        ✓           |     ✓    |
| 意象加上混合意象    |            |     ✓    |
| 交通流量                                 |        ✓           |     ✓    |
| 時區                              |        ✓           |     ✓    |
| 地理位置 (預覽)                |        ✓           |     ✓    |
| 資料 (預覽)               |                   |     ✓    |
| 空間 (預覽)               |                   |     ✓    |
| 地理柵欄 (預覽)               |                   |     ✓    |



以下這些資料點也值得考量：
* 您的企業是哪一種？
* 正在建置的應用程式有多麼關鍵？

請參閱**定價層的目標客戶**表，以深入了解 S0 和 S1 定價層。 如需詳細資訊，請參閱 [Azure 地圖服務定價](https://azure.microsoft.com/pricing/details/azure-maps/)。 

### <a name="pricing-tier-targeted-customers"></a>定價層的目標客戶

| 定價層  |     目標客戶                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>S0 定價層適用於中小型企業的客戶。 如果您預期不會有大量的並行使用者，則此為適合您的定價層。 如果上表中所顯示的核心地理空間 API 符合您的地理需求，則這也適合您。 此層級已正式推出， 而且適用於所有生產階段中的應用程式，從概念證明開發和測試早期階段到應用程式的生產和部署皆適用。<p>|
| S1            |    <p>S1 定價層適用於需要大型企業、任務關鍵性應用程式支援，或並行使用者人數眾多， 也適用於需要進階地理空間服務的客戶。</p>|

## <a name="next-steps"></a>後續步驟

深入了解如何檢視及變更定價層：

> [!div class="nextstepaction"] 
> [管理定價層](how-to-manage-pricing-tier.md)
