---
title: 包含檔案
description: 包含檔案
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e5acb8e0f8805da7f14bbce58b4bfd2acdc24f23
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66166314"
---
# <a name="secure-your-internet-of-things-iot-deployment"></a>保護您的物聯網 (IoT) 部署

本文針對以 Azure IoT 為主的物聯網 (IoT) 基礎結構提供更進一層的詳細資料。 它會連結到設定及部署每個元件的實作層級詳細資料。 另外也會提供各種競爭方法之間的比較和選擇。

保護 Azure IoT 部署可分為下列三個安全性區域：

* **裝置安全性**:部署在現實世界時，請保護 IoT 裝置。

* **連線安全性**:確保在 IoT 裝置與 IoT 中樞之間傳輸的所有資料有加密並防止竄改。

* **雲端安全性**:提供一種方法來保護資料，而它通過，並儲存在雲端。

![三個安全性區域](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>保護裝置佈建和驗證

IoT 解決方案加速器可使用下列兩個方法保護 IoT 裝置：

* 提供每部裝置唯一識別金鑰 (安全性權杖)，裝置可使用這個金鑰與 IoT 中樞通訊。

* 使用裝置上的 [X.509 憑證](https://www.itu.int/rec/T-REC-X.509-201210-S)與私密金鑰做為向 IoT 中樞驗證裝置的方法。 這種驗證方法可在任何時候確保無法從裝置外部知道裝置上的私密金鑰，提供較高的安全性等級。

安全性權杖方法藉由將裝置對 IoT 中樞所進行的每個呼叫與對稱金鑰建立關聯，提供每個呼叫的驗證。 X.509 型驗證允許實體層 IoT 裝置的驗證做為 TLS 連線建立作業的一部份。 安全性權杖型方法可不搭配 X.509 驗證使用，該驗證是較不安全的模式。 這兩種方法的選擇主要取決於裝置驗證所需的安全性有多高，以及裝置上安全存放裝置的可用性 (以安全地儲存私密金鑰)。

## <a name="iot-hub-security-tokens"></a>IoT 中樞安全性權杖

IoT 中樞使用安全性權杖來驗證裝置和服務，以避免透過網路傳送金鑰。 此外，安全性權杖有時效性和範圍的限制。 Azure IoT SDK 能在不需要任何特殊組態的情況下自動產生權杖。 不過在某些案例中，使用者必須直接產生及使用安全性權杖。 這些案例包括直接使用 MQTT、AMQP 或 HTTP 介面，或實作權杖服務模式。

如需安全性權杖的結構與其用法的詳細資料，請參閱下列文章：

* [安全性權杖結構](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [將 SAS 權杖當做裝置](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

每個 IoT 中樞都有一個[身分識別登錄](../articles/iot-hub/iot-hub-devguide-identity-registry.md)，可用來在服務中建立各裝置的資源 (例如含有傳送中雲端到裝置訊息的佇列)，以及允許存取裝置面向端點。 IoT 中樞身分識別登錄會針對方案，為裝置身分識別和安全性金鑰提供安全的儲存體。 個別或群組的裝置身分識別可以新增到允許的清單或封鎖清單，以便完整控制裝置存取。 下列文章提供身分識別登錄結構和所支援作業的更詳細資訊。

[IoT 中樞支援 MQTT、AMQP 與 HTTP 之類的通訊協定](../articles//iot-hub/iot-hub-devguide-security.md)。 這些通訊協定會各自以不同的方式使用 IoT 裝置到 IoT 中樞的安全性權杖：

* AMQP：SASL PLAIN 和 AMQP 宣告式安全性 (`{policyName}@sas.root.{iothubName}`與 IoT 中樞層級權杖;`{deviceId}`與裝置範圍權杖)。

* MQTT：使用 CONNECT 封包`{deviceId}`做為`{ClientId}`，`{IoThubhostname}/{deviceId}`中**使用者名稱**欄位和 SAS 權杖，在**密碼**欄位。

* HTTP:有效權杖位於授權要求標頭。

IoT 中樞身分識別登錄可用來設定每一裝置的安全性認證和存取控制。 但是，如果 IoT 解決方案已經大幅投資[自訂裝置身分識別登錄和/或驗證配置](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication)，則可透過建立權杖服務，將現有基礎結構與 IoT 中樞整合。

### <a name="x509-certificate-based-device-authentication"></a>X.509 憑證型裝置驗證

使用[裝置型 X.509 憑證](../articles/iot-hub/iot-hub-devguide-security.md)與其相關聯的私密和公用金鑰組，就可在實體層提供額外驗證。 私密金鑰安全地儲存在裝置中，並且無法從裝置外部探索。 X.509 憑證包含裝置的相關資訊，例如裝置識別碼和其他與組織有關的詳細資料。 憑證的簽章是使用私密金鑰來產生。

高層級裝置佈建流程：

* 將識別碼與實體裝置建立關聯 – 在裝置的製造或是委任期間，將裝置身分識別和/或 X.509 憑證與裝置建立關聯。

* 在 IoT 中樞建立相對應的身分識別項目 - IoT 中樞身分識別登錄中的裝置身分識別與相關聯的裝置資訊。

* 在 IoT 中樞身分識別登錄中安全地儲存 X.509 憑證指紋。

### <a name="root-certificate-on-device"></a>裝置上的根憑證

使用 IoT 中樞建立安全的 TLS 連線時，IoT 裝置會使用裝置 SDK 中的根憑證驗證 IoT 中樞。 針對 C 用戶端 SDK，憑證位於存放庫根目錄底下的 "\\c\\certs" 資料夾。 雖然這些根憑證是長效的，但它們仍會過期或遭到撤銷。 如果沒有方法更新裝置上的憑證，裝置可能就無法再連線到 IoT 中樞 (或任何其他雲端服務)。 在 IoT 裝置部署後提供更新根憑證的方法，可有效地降低此風險。

## <a name="securing-the-connection"></a>保護連線安全

IoT 裝置與 IoT 中樞之間的網際網路連線，是使用傳輸層安全性 (TLS) 標準進行保護。 Azure IoT 支援 [TLS 1.2](https://tools.ietf.org/html/rfc5246)、TLS 1.1 與 TLS 1.0 (依此順序)。 針對 TLS 1.0 的支援僅為提供回溯相容性。 可能的話，請使用 TLS 1.2，因為它可提供最大的安全性。

## <a name="securing-the-cloud"></a>保護雲端

Azure IoT 中樞允許針對每個安全性金鑰定義[存取控制原則](../articles/iot-hub/iot-hub-devguide-security.md)。 它使用下列權限組，授與每個 IoT 中樞端點的存取權。 權限可根據功能限制 IoT 中樞的存取權。

* **RegistryRead**。 為身分識別登錄授與讀取權限。 如需詳細資訊，請參閱[身分識別登錄](../articles/iot-hub/iot-hub-devguide-identity-registry.md)。

* **RegistryReadWrite**。 為身分識別登錄授與讀取和寫入權限。 如需詳細資訊，請參閱[身分識別登錄](../articles/iot-hub/iot-hub-devguide-identity-registry.md)。

* **ServiceConnect**。 授與雲端服務面向通訊和監視端點的存取權。 例如，它授權後端雲端服務接收裝置到雲端的訊息、傳送雲端到裝置的訊息，以及擷取相對應的傳遞通知。

* **DeviceConnect**。 授與裝置面向端點的存取權。 例如，它會授與傳送裝置到雲端的訊息和接收雲端到裝置的訊息的權限。 裝置會使用此權限。

利用[安全性權杖](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)取得 IoT 中樞之 **DeviceConnect** 權限的方法有兩種：使用裝置身分識別金鑰，或使用共用存取金鑰。 此外，請務必注意所有可從裝置存取的功能，在設計上會於前置詞為 `/devices/{deviceId}`的端點公開。

服務元件只能使用授與適當權限的共用存取原則來[產生安全性權杖](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components)。

Azure IoT 中樞和可能屬於解決方案的其他服務，能允許使用 Azure Active Directory 來管理使用者。

由 Azure IoT 中樞內嵌的資料可供各種不同服務使用，例如 Azure 串流分析和 Azure Blob 儲存體。 這些服務允許管理存取。 深入了解這些服務和可用選項：

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)：管理裝置的中繼資料的半結構化資料的可調整且完全編製索引的資料庫服務您佈建，例如屬性、 組態及安全性內容。 Azure Cosmos DB 提供高效能且高輸送量的處理、無從驗證結構描述的資料索引編製，以及豐富的 SQL 查詢介面。

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/):雲端可讓您能夠快速開發並部署低成本的分析解決方案來探索即時深入資訊從裝置、 感應器、 基礎結構和應用程式中處理的即時資料流。 來自這個完全受控服務的資料可調整為任何數量，但仍可達到高輸送量、低遲性和恢復功能。

* [在 azure App Service](https://azure.microsoft.com/services/app-service/):雲端平台來建置強大的 web 和行動裝置連接到任何地方; 資料的應用程式在雲端或內部部署。 建置吸引客戶參與的 iOS、Android 和 Windows 版行動應用程式。 與軟體即服務 (SaaS) 和企業應用程式整合，讓您能夠立即連線到數十種雲端服務和企業應用程式。 使用您愛用的語言 (.NET、Node.JS、PHP、Python 或 Java) 和整合式開發環境 (IDE) 撰寫程式碼，以前所未有的速度建置 Web 應用程式和 API。

* [Logic Apps](https://azure.microsoft.com/services/app-service/logic/):Azure App service 的 Logic Apps 功能可協助您 IoT 解決方案整合到現有的特定業務系統並自動化工作流程。 Logic Apps 可讓開發人員設計從觸發程序開始，然後執行一系列步驟的工作流程 — 使用功能強大的連接器來與您的商務程序整合的規則和動作。 Logic Apps 提供與 SaaS、雲端架構及內部部署應用程式的廣大生態系統的即時連接。

* [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/):您的裝置傳送至雲端之資料的可靠且符合經濟效益的雲端儲存體。

## <a name="conclusion"></a>結論

本文提供使用 Azure IoT 設計和部署 IoT 基礎結構的實作層級詳細資料概觀。 設定每個元件的安全保護，對於保護整體 IoT 基礎結構非常重要。 Azure IoT 中可用的設計選項提供了某種程度的彈性和選擇 ；不過，每個選擇都可能會影響安全性。 建議您透過風險/成本評估謹慎評估每個選擇。
