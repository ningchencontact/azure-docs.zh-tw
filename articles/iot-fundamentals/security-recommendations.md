---
title: Azure IoT 的安全性建議 |Microsoft Docs
description: 本文摘要說明額外的步驟，以確保您的 Azure IoT 中樞解決方案中的安全性。
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 4416f3149c33a0c9a437b2fbd6a48729a5a7f044
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722865"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Azure 物聯網 (IoT) 部署的安全性建議

本文包含 Azure IoT 服務的安全性建議。 實作這些建議會說明您身為客戶的 Azure IoT 中，並將符合您安全性責任有所改善您的 IoT 解決方案的整體安全性。 如需有關 Azure IoT 所提供的內建安全性功能的詳細資訊，請參閱[從頭建立 IoT 安全性](iot-security-ground-up.md)。

## <a name="general"></a>一般

| 建議 | 註解 |
|-|-|
| 保持最新狀態 | 使用支援的平台，程式設計語言、 通訊協定和架構的最新版本。 |
| 維護驗證金鑰安全 | 保護裝置識別碼，且其驗證金鑰實際安全部署之後。 這可避免惡意裝置偽裝為已註冊的裝置。 |
| 使用 如果可能的話，裝置 Sdk | 裝置 Sdk 會實作各種不同的安全性功能，例如加密、 驗證和等等，以協助您開發強固且安全的裝置應用程式。 請參閱[了解和使用 Azure IoT 中樞 Sdk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks)如需詳細資訊。 |


## <a name="identity-and-access-management"></a>身分識別和存取管理

| 建議 | 註解 |
|-|-|
| 定義中樞的存取控制 | [了解，並定義的存取類型](iot-security-deployment.md#securing-the-cloud)每個元件都會在您的 IoT 中樞解決方案的功能。 允許的權限*登錄讀取*， *RegistryReadWrite*， *ServiceConnect*，以及*DeviceConnect*。 預設值[共用存取原則，在 IoT 中樞](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions)也可協助定義於其角色為基礎的每個元件的權限。 |
| 定義後端服務的存取的控制 | 內嵌您的 IoT 中樞解決方案的資料可供其他 Azure 服務這類[Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)， [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)， [App Service](https://docs.microsoft.com/azure/app-service/)， [Logic Apps](https://docs.microsoft.com/azure/logic-apps/)，並[Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)。 請務必了解，並允許適當的存取權限，這些服務如所述。 |


## <a name="data-protection"></a>資料保護

| 建議 | 註解 |
|-|-|
| 安全的裝置驗證 | 使用其中一種確保您的裝置與您的 IoT 中樞之間的通訊安全[唯一的識別索引鍵或安全性語彙基元](iot-security-deployment.md#iot-hub-security-tokens)，或[的裝置上的 X.509 憑證](iot-security-deployment.md#x509-certificate-based-device-authentication)每個裝置。 使用適當的方法，來[搭配使用所選擇的通訊協定 （MQTT、 AMQP 或 HTTPS） 為基礎的安全性權杖](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security)。 |
| 保護裝置通訊 | IoT 中樞會保護使用傳輸層安全性 (TLS) 標準的裝置連線支援 1.2、 1.2 和 1.0 的版本。 使用[TLS 1.2](https://tools.ietf.org/html/rfc5246)以確保最高的安全性。 |
| 保護服務通訊 | IoT 中樞提供例如連接到後端服務的端點[Azure 儲存體](/azure/storage/)或是[事件中樞](/azure/event-hubs)使用只能使用 TLS 通訊協定，與任何端點未加密通道上公開。 一旦這項資料會達到這些儲存體或分析的後端服務，請務必將該服務的適當安全性和加密方法，並保護在後端的機密資訊。 |


## <a name="networking"></a>網路功能

| 建議 | 註解 |
|-|-|
| 保護您的裝置存取權 | 將硬體連接埠在您的裝置以最低限度，以避免未經授權的存取。 此外，建置機制，以防止或偵測實體竄改的裝置。 讀取[IoT 安全性最佳做法](iot-security-best-practices.md)如需詳細資訊。 |
| 建置安全的硬體 | 將安全性功能，例如加密的儲存體或受信任的平台模組 (TPM)，若要讓裝置和基礎結構更安全。 保留的裝置作業系統和驅動程式已升級至最新版本，和如果空間允許的話，安裝防毒軟體和反惡意程式碼的功能。 讀取[IoT 安全性架構](iot-security-architecture.md)以了解這可以協助降低數種安全性威脅。 |


## <a name="monitoring"></a>監視

| 建議 | 註解 |
|-|-|
| 監視未經授權的存取您的裝置 |  您可以使用裝置作業系統的記錄功能來監視任何安全性缺口或實體裝置或其連接埠遭到竄改。 |
| 監視您的 IoT 解決方案，從雲端 | 監視您的 IoT 中樞的解決方案使用的整體健全狀況[Azure 監視器計量](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics)。 |
| 設定診斷 | 方案中記錄事件，然後將診斷記錄檔傳送給 Azure 監視器，以了解效能，密切地監看您的作業。 讀取[監視和診斷問題，您的 IoT 中樞](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health)如需詳細資訊。 |

## <a name="next-steps"></a>後續步驟

對於牽涉到 Azure IoT 的進階案例，您可能需要考量的其他安全性需求。 請參閱[IoT 安全性架構](iot-security-architecture.md)如需詳細指引。

