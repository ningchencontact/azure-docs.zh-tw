---
title: 從 Google Maps 遷移 |Microsoft Docs
description: 如何從 Google Maps 遷移至 Microsoft Azure Maps 的教學課程。 指引會引導您瞭解如何切換至 Azure 地圖服務 Api 和 Sdk。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6709c42b19b18c8cae783a6b4ecc2c0721e1217d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770300"
---
# <a name="migrate-from-google-maps"></a>從 Google Maps 進行遷移

本教學課程提供有關如何將 web、行動裝置和伺服器應用程式從 Google Maps 遷移至 Microsoft Azure Maps 平臺的深入解析。 本教學課程包含比較程式碼範例、遷移建議，以及遷移至 Azure 地圖服務的最佳做法。

## <a name="azure-maps-platform-overview"></a>Azure 地圖服務平臺總覽

Azure 地圖服務為開發人員提供了功能強大的地理空間功能，並以定期更新的地圖資料進行封裝，以提供 web 和行動應用程式的地理內容。 Azure 地圖服務具有一組適用于地圖、搜尋、路由、流量、時區、地理位置、地理柵欄、地圖資料、天氣、行動性及空間作業的 REST Api，並同時隨附 Web 和 Android Sdk，讓開發變得簡單、彈性，以及可在多個平臺上進行移植。

## <a name="high-level-platform-comparison"></a>高階平臺比較

下表提供 Google Maps 功能的高階清單，以及這些功能在 Azure 地圖服務中的相對支援。 此清單不包含額外的 Azure 地圖服務功能，例如協助工具、地理柵欄 Api、等時線、空間作業、直接地圖底圖存取、batch 服務，以及資料涵蓋範圍比較（例如影像涵蓋範圍）。

| Google Maps 功能         | Azure 地圖服務支援                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | 已規劃                                |
| REST 服務 Api           | ✓                                      |
| 方向（路由）        | ✓                                      |
| 距離矩陣             | ✓                                      |
| Elevation                   | 已規劃                                |
| 地理編碼（順向/反向） | ✓                                      |
| 地理位置                 | N/A                                    |
| 放置搜尋               | ✓                                      |
| 位置詳細資料              | N/A –可用的網站 & 電話號碼 |
| 放置相片               | N/A                                    |
| 放置自動完成          | ✓                                      |
| 靜態對應                 | ✓                                      |
| 靜態街道視圖          | N/A                                    |
| 時區                   | ✓                                      |
| 地圖內嵌 API           | N/A                                    |
| 對應 Url                    | N/A                                    |

Google Maps 提供基本的金鑰型驗證。 Azure 地圖服務同時提供基本的金鑰型驗證，以及高度安全的 Azure Active Directory 驗證。

## <a name="licensing-considerations"></a>授權考慮

從 Google Maps 遷移到 Azure 地圖服務時，應該考慮下列關於授權的要點。

- 根據已載入的地圖底圖數目，使用互動式地圖的 Azure 地圖服務費用，而將地圖控制項載入的 Google Maps 費用則收取。 在互動式 Azure 地圖服務 Sdk 中，地圖底圖會自動快取，以降低開發人員的成本。 每15個已載入的地圖底圖都會產生一個 Azure 地圖服務交易。 互動式 Azure 地圖服務 Sdk 使用512圖元磚，而平均每個網頁檢視會產生一或少筆交易。
- 將 Google Maps web 服務的靜態地圖影像取代為 Azure 地圖服務 Web SDK 通常會更符合成本效益，因為這會使用地圖底圖，而且除非使用者移動並縮放地圖，否則通常只會針對每個地圖負載產生一小部分的交易。 Azure 地圖服務 web SDK 有選項可停用移動流覽和縮放。 此外，Azure 地圖服務 web SDK 提供比靜態地圖 web 服務更多的視覺效果選項。
- Azure 地圖服務允許將其平臺的資料儲存在 Azure 中。 您也可以根據[使用](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)規定，在別處快取最多六個月的時間。

以下是 Azure 地圖服務的一些相關資源：

- [Azure 地圖服務定價頁面](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure 地圖服務使用](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)規定（包含在 Microsoft Online Services 條款中）
- [在 Azure 地圖服務中選擇正確的定價層](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>建議的遷移計畫

以下是高階的遷移計畫。

1. 清查您的應用程式所使用的 Google Maps Sdk 和服務，並確認 Azure 地圖服務提供替代的 Sdk 和服務供您遷移至。
2. 在[https://azure.com](https://azure.com)建立 Azure 訂用帳戶（如果您還沒有的話）。
3. 建立 Azure 地圖服務帳戶（[檔](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)）和驗證金鑰或 Azure Active Directory （[檔](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)）。
4. 遷移您的應用程式程式碼。
5. 測試您已遷移的應用程式。
6. 將已遷移的應用程式部署至生產環境。

## <a name="azure-maps-technical-resources"></a>Azure 地圖服務技術資源

以下是 Azure 地圖服務的實用技術資源清單。

- 總覽： [https://azure.com/maps](https://azure.com/maps)
- 檔： [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Web SDK 程式碼範例： [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- 開發人員論壇： [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- 影片： [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog： [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Azure 地圖服務意見反應（UserVoice）： [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>移轉支援

開發人員可以透過[論壇](https://aka.ms/AzureMapsForums)或許多 Azure 支援選項之一來尋找遷移支援： [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>後續步驟

瞭解如何使用下列文章來遷移您的 Google Maps 應用程式：

> [!div class="nextstepaction"]
> [遷移 web 應用程式](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [遷移 Android 應用程式](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [遷移 web 服務](migrate-from-google-maps-web-services.md)
