---
title: 關於 Azure App Service 中的 Mobile Apps
description: 了解 App Service 為您的企業行動裝置應用程式帶來的優勢。
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: yochayk
editor: ''
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 55f33821a748789fb11b44dc08c367961b4258f4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440282"
---
# <a name="getting-started"> </a>關於 Azure App Service 中的 Mobile Apps

> [!NOTE]
> Visual Studio App Center 投入新的和整合式服務行動應用程式開發的核心。 開發人員可以使用**建置**，**測試**並**散發**services 設定持續整合和傳遞管線。 應用程式部署之後，開發人員可以監視的狀態和其應用程式使用的使用方式**Analytics**並**診斷**服務，並使用使用者參與**推播**服務。 開發人員也可以利用**Auth**來驗證使用者並**資料**保存和同步處理雲端中的應用程式資料的服務。 請參閱[App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-value-prop)今天。
>

Azure App Service 是完全受控的[平台即服務](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) 供應項目，適用於專業開發人員。 此服務為 Web、行動及整合案例提供一組豐富的功能。 

Azure App Service 的 Mobile Apps 功能為企業開發人員和系統整合人員提供一個高擴充性且全球可用的行動裝置應用程式開發平台。

![Mobile Apps 功能的視覺概觀](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>為何選擇 Mobile Apps？
使用 Mobile Apps 功能，您可以：

* **建置原生和跨平台的應用程式**：不論您要建置原生 iOS、Android 和 Windows 應用程式或跨平台 Xamarin 或 Cordova (PhoneGap) 應用程式，都可以使用原生 SDK 來利用 App Service。
* **連線至您的企業系統**：使用 Mobile Apps 功能，您可以在數分鐘內新增公司登入，並連線至您的企業內部部署或雲端資源。
* **建置具有資料同步功能的可離線應用程式**：藉由建置可離線運作的應用程式，並在出現與任何企業資料來源或軟體即服務 (SaaS) API 的連線時，使用 Mobile Apps 在背景同步處理資料，讓您的行動工作力更具生產力。
* **在數秒內推播通知給數百萬人**：在任何裝置上利用即時推播通知與客戶聯繫，依照其需求將推播通知個人化並適時傳送。

## <a name="mobile-apps-features"></a>Mobile Apps 功能
下列功能對於具備雲端功能的行動開發十分重要︰

* **驗證和授權**：支援識別提供者，包括適用於企業驗證的 Azure Active Directory，以及 Facebook、Google、Twitter 和 Microsoft 帳戶等社交提供者。 Mobile Apps 為每個提供者提供 OAuth 2.0 服務。 您也可以為識別提供者整合 SDK，以取得提供者特定功能。

    深入探索[驗證功能]。

* **資料存取**：Mobile Apps 提供連結至 Azure SQL Database 或內部部署 SQL Server 且適合行動用途的 OData v3 資料來源。 因為這項服務以 Entity Framework 為基礎，您才可以輕鬆地與其他 NoSQL 和 SQL 資料提供者整合，包括 [Azure 資料表儲存體]、MongoDB、[Azure Cosmos DB] 和 SaaS API 提供者 (如 Office 365 和 Salesforce.com)。

* **離線同步處理**：用戶端 SDK 可讓您輕鬆地建置強大且回應靈敏的行動裝置應用程式 (利用離線資料集運作)。 您可以自動同步處理此資料集與後端資料，包括衝突解決支援。

  深入探索[資料功能]。

* **推播通知**：用戶端 SDK 會與 Azure 通知中樞的註冊功能緊密整合，以便您將推播通知同時傳送給數百萬個使用者。

  深入探索[推播通知功能]。

* **用戶端 SDK**：提供一組完整的用戶端 SDK，涵蓋原生開發 ([iOS]、[Android] 和 [Windows])、跨平台開發 ([Xamarin.iOS 和 Xamarin.Android]、[Xamarin.Forms]) 和混合式應用程式開發 ([Apache Cordova])。 每個用戶端 SDK 都是透過 MIT 授權提供並為開放原始碼。

## <a name="azure-app-service-features"></a>Azure App Service 功能
下列平台功能適合用於行動生產網站：

* **自動調整**：使用 App Service，您可以快速地相應增加或相應放大，以處理任何傳入的客戶負載。 手動選取 VM 的數目和大小，或設定自動調整以根據負載或排程調整行動裝置應用程式後端。

  深入探索[自動調整]。

* **預備環境**：App Service 可以執行多個版本的網站，以便您執行 A/B 測試、在較大型 DevOps 方案中進行生產環境測試，以及就地預備新的後端。

  深入探索 [預備環境]。

* **持續部署**：App Service 可與常見的「原始檔控制管理」  (SCM) 系統整合，讓您輕鬆部署新版的後端。

  深入探索 [部署選項](../app-service/deploy-local-git.md)。

* **虛擬網路**：App Service 可以使用虛擬網路、Azure ExpressRoute 或混合式連線，連線到內部部署資源。

  深入探索[混合式連線]、[虛擬網路]和 [ExpressRoute]。

* **隔離且專用的環境**：為了安全地執行 Azure App Service 應用程式，您可以在一個完全隔離且專用的環境中執行 App Service。 此環境很適合於需要高規格、隔離或安全網路存取的應用程式工作負載。

  深入探索 [App Service 環境]。

## <a name="next-steps"></a>後續步驟

若要在 Azure App Service 中開始使用 Mobile Apps，請完成[快速入門]教學課程。 本教學課程涵蓋產生您所選擇之行動後端和用戶端的基本概念。 其中也涵蓋整合驗證、離線同步處理及推播通知。 您可以多次完成教學課程，每個用戶端應用程式進行一次。

如需 Mobile Apps 的詳細資訊，請檢閱我們的[學習地圖]。
如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service]。

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[快速入門]: app-service-mobile-ios-get-started.md
[Azure 資料表儲存體]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/sql-api-get-started.md
[驗證功能]: ./app-service-mobile-auth.md
[資料功能]: ./app-service-mobile-offline-data-sync.md
[推播通知功能]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS 和 Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[自動調整]: ../app-service/web-sites-scale.md
[預備環境]: ../app-service/deploy-staging-slots.md
[混合式連線]: ../biztalk-services/integration-hybrid-connection-overview.md
[虛擬網路]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[App Service 環境]: ../app-service/environment/intro.md
[學習地圖]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/
