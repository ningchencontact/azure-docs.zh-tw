---
title: 選擇 Azure 地圖服務的正確定價層 | Microsoft Docs
description: 深入了解 Azure 地圖服務提供的定價層
author: walsehgal
ms.author: v-musehg
ms.date: 12/05/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: ee277867f449afddeb89c3fd73b5b577a68a4497
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998372"
---
# <a name="choosing-the-right-pricing-tier-in-azure-maps"></a>在 Azure 地圖服務中選擇正確的定價層

Azure 地圖服務提供兩個定價層。 本文旨在協助您根據需求來選擇正確的定價層。 若要選擇正確的定價層，請詢問自己兩個問題：

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>我打算使用哪些地理空間功能？
如果您認為核心地理空間 API 符合您的服務需求，那麼 S0 定價層是最適合您。 如果您想要更進階的應用程式功能，例如空拍+混合影像、取得路線範圍和批次進行地理編碼等，請考慮選擇 S1 定價層。 下列**定價層功能**表可為您的應用程式需求提供更好的建議，並協助您選擇最適合應用程式的定價層。

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>我打算支援多少並行使用者人數？ 
S0 和 S1 定價層可處理不同的資料輸送量。 選擇 Azure 地圖服務定價層之前，請先試著問自己一些問題，例如您想要支援多少並行使用者人數？ S0 定價層最多可以處理**每秒 50 個查詢**，而 S1 定價層可以處理**每秒 50 個以上的查詢**。

### <a name="pricing-tier-capabilities"></a>定價層功能

| 功能                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Search                                  |        ✓           |     ✓    |
| 路由                                 |        ✓           |     ✓    |
| 轉譯                                  |        ✓           |     ✓    |
| 交通流量                                 |        ✓           |     ✓    |
| 時區                              |        ✓           |     ✓    |
| *影像 + 混合影像 (預覽)     |        ✓           |     ✓    |
| *路線範圍 (預覽)                  |        ✓           |     ✓    |
| * IP 2 位置 (預覽)                |        ✓           |     ✓    |
| *搜尋中的多邊形功能 (預覽)         |        ✓           |     ✓    |
| *批次地理編碼 (預覽)              |        ✓           |     ✓    |
| *批次路由 (預覽)                |        ✓           |     ✓    |
| *矩陣路由 (預覽)               |        ✓           |     ✓    |

> [!Note]
> 2019 年 2 月 4 日之後，將無法再從 S0 定價層存取這些功能。

其他值得考慮的一些額外資料點是，您有何種企業，以及所要建置的應用程式有多重要？

請參閱**定價層的目標客戶**資料表，以深入了解 S0 和 S1 定價層。 如需 Azure 地圖服務定價層的詳細資訊，請參閱 [Azure 地圖服務的定價](https://azure.microsoft.com/pricing/details/azure-maps/)。 

### <a name="pricing-tier-targeted-customers"></a>定價層的目標客戶

| 定價層  |        目標客戶                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>S0 定價層適用於中小型企業的客戶。 如果您預期不會有大量的並行使用者，而且下表所示的核心地理空間 API 可符合您服務的需求，則這是適合您的定價層。 此層級已正式推出，而且適用於所有生產階段中的應用程式，從概念證明開發和測試早期階段到應用程式的生產和部署皆適用。<p>|
| S1            |    <p>S1 定價層適用於需要大型企業任務關鍵性應用程式支援、並行使用者人數眾多，或需要進階地理空間服務的客戶。</p>|


## <a name="next-steps"></a>後續步驟

深入了解如何檢視及變更定價層：

> [!div class="nextstepaction"]
> [管理定價層](how-to-manage-pricing-tier.md)
