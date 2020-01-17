---
title: 選擇正確的定價層 |Microsoft Azure 對應
description: 在本文中，您將瞭解 Microsoft Azure Maps 所提供的定價層。
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 8764e9161f952118ca7ae28343dcd16477cf1eee
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155752"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>在 Azure 地圖服務中選擇正確的定價層

Azure 地圖服務提供兩個定價層： S0 和 S1。 本文旨在協助您根據需求來選擇正確的定價層。 若要協助選擇正確的定價層，請詢問自己以下兩個問題。

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>我打算使用哪些地理空間功能？
如果核心地理空間 API 符合您的服務需求，那麼 S0 定價層適合您。 如果您想要應用程式有更進階的功能，請考慮選擇 S1 定價層。 先進的功能範例：空中加上混合影像、取得路線範圍和批次地理編碼。 **定價層功能**表可協助您選擇最適合您應用程式的定價層。

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>我打算支援多少並行使用者人數？ 
S0 和 S1 定價層會處理不同的資料輸送量。 S0 定價層**每秒**最多可處理50個查詢，而 S1 層則處理**超過每秒50個查詢**。

### <a name="pricing-tier-capabilities"></a>定價層功能

| 功能                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| 地圖呈現                              | ✓                   | ✓       |
| 衛星圖像                       |                     | ✓        |
| 搜尋                                  | ✓                    | ✓        |
| 批次搜尋                            |                     | ✓        |
| 路由                                   | ✓                    |✓        |
| 批次路線規劃                            |                    | ✓        |
| 矩陣路線規劃                          |                     | ✓        |
| 路線範圍 (等時)                |                     | ✓        |
| 交通流量                                |✓                    |✓        |
| 時區                               |✓                    |✓        |
| 地理位置（預覽）                    |✓                   |✓        |
| 空間作業                        |                    |✓        |
| 地理柵欄                                |                    |✓        |
| Azure 地圖服務資料（預覽）                |                     | ✓        |
| 行動性（預覽）                       |                     | ✓        |
| 氣象（預覽）                        |✓                    |✓        |

以下這些資料點也值得考量：
* 您有何種類型的企業？
* 您的應用程式有多重要？

### <a name="pricing-tier-targeted-customers"></a>定價層的目標客戶

請參閱**定價層的目標客戶**表，以深入了解 S0 和 S1 定價層。 如需詳細資訊，請參閱 [Azure 地圖服務定價](https://azure.microsoft.com/pricing/details/azure-maps/)。 

| 價格層  |     目標客戶                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>S0 定價層適用于所有生產階段中的應用程式：從概念證明開發和早期階段測試到應用程式生產和部署。 不過，這一層是專為小規模開發或具有低並行使用者或兩者的客戶所設計。 <p>|
| S1            |    <p>S1 定價層適用於需要大型企業、任務關鍵性應用程式支援，或並行使用者人數眾多， 也適用於需要進階地理空間服務的客戶。</p>|

## <a name="next-steps"></a>後續步驟

深入了解如何檢視及變更定價層：

> [!div class="nextstepaction"] 
> [管理定價層](how-to-manage-pricing-tier.md)
