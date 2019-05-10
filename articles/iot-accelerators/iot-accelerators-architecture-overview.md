---
title: IoT 解決方案加速器參考架構 - Azure | Microsoft Docs
description: 深入了解 Azure IoT 解決方案加速器參考架構。 現有的解決方案加速器會運用此參考架構。 您建置您專屬的自訂 IoT 解決方案時，也可以使用該參考架構。
author: dominicbetts
ms.author: dobett
ms.date: 12/04/2018
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: philmea
ms.openlocfilehash: 1e3ec677f2c3ce2a7f942c47269fe1f70885ac02
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466477"
---
# <a name="introduction-to-the-azure-iot-reference-architecture"></a>Azure IoT 參考架構簡介

本文介紹 [Azure IoT 參考架構](https://aka.ms/iotrefarchitecture)，並提供 [Azure IoT 解決方案加速器](about-iot-accelerators.md)如何遵循其建議的範例。

開放原始碼[遠端監視](iot-accelerators-remote-monitoring-sample-walkthrough.md)和[連線的處理站](iot-accelerators-connected-factory-sample-walkthrough.md)解決方案加速器會遵循許多參考架構建議。 您可以使用該解決方案加速器作為您專屬 IoT 解決方案的起點，或是作為學習工具。

## <a name="overview"></a>概觀

參考架構說明 IoT 解決方案的項目，例如技術原則、Azure IoT 服務的組合和裝置。 該架構也提供實作技術的建議。

在最高的層級，您可以檢視 IoT 解決方案的組成：

* 產生並傳送遙測的物項，例如度量和事件。 在遠端監視解決方案加速器中，卡車或電梯之類的裝置是傳送遙測的物項。
* 系統會自物項傳送的遙測資料來產生深入解析。 在遠端監視解決方案加速器中，規則會產生深入解析。 例如，規則會識別引擎的溫度何時達到臨界值。
* 以深入解析為基礎且有助於改善商務或處理程序的動作。 在遠端監視解決方案加速器中，電子郵件動作會向操作員通知引擎的潛在問題。

[Azure IoT 參考架構](https://aka.ms/iotrefarchitecture)是隨著技術發展而進行的即時文件更新。

## <a name="core-subsystems"></a>核心子系統

參考架構會識別核心子系統，如下圖所示：

![核心子系統](media/iot-accelerators-architecture-overview/coresubsystems1.png)

下列各節說明遠端監視解決方案加速器的元件如何對應於核心子系統。

### <a name="iot-devices"></a>IoT 裝置

IoT 解決方案應可讓幾乎任何類型的裝置與雲端閘道之間，進行安全、有效率且穩定的通訊。 裝置是企業資產，從簡單的溫度感應器到有數百個元件和感應器的複雜工廠生產線皆涵蓋在內。

現場閘道或邊緣裝置是特製化的裝置設備或一般用途的軟體，其作用為：

* 處理通訊協定轉換的通訊啟用器。
* 本機裝置控制系統和裝置遙測處理中樞。 邊緣裝置可以在本機處理遙測以控制裝置，完全不需要與雲端進行通訊。 邊緣裝置也可以篩選或彙總裝置遙測，以減少傳送至雲端的遙測量。

在遠端監視解決方案中，裝置會連線到 IoT 中樞，並傳送遙測進行處理。 遠端監視解決方案也可讓操作員使用工作或自動裝置管理來管理裝置。 您可以使用 Azure IoT 裝置 SDK 來實作您的裝置。

遠端監視解決方案會部署和管理 IoT Edge 裝置。 在邊緣處理有助於減少傳送到雲端的遙測量，並加速回應裝置事件。

### <a name="cloud-gateway"></a>雲端閘道

雲端閘道可供裝置和邊緣裝置進行通訊。 這些裝置可能出現在許多遠端站台。

閘道會管理裝置通訊，包括連線管理、通訊路徑的保護、驗證，以及授權。 閘道也會強制執行連線及輸送量配額，並且會收集計費、診斷和其他監視工作的遙測。

遠端監視解決方案會部署 IoT 中樞，以便提供讓裝置傳送遙測的安全端點。 IoT 中樞也會：

* 包含裝置身分識別存放區，可管理能夠連線至解決方案的裝置。
* 可讓解決方案將命令傳送至裝置。 例如，開啟洩壓閥釋放壓力。
* 啟用大量裝置管理。 例如，升級一組裝置上的軔體。

### <a name="stream-processing"></a>串流處理

由於解決方案內嵌遙測，因此必須了解遙測處理的流程會如何變化。 根據案例，資料記錄會經歷不同的流程：

* 儲存體，例如記憶體內部快取、暫存佇列，以及永久封存。

* 分析，依據一組條件執行輸入遙測，而且可以產生不同的輸出資料記錄。 例如，輸入以 Avro 編碼的遙測可能會傳回以 JSON 編碼的輸出遙測。

* 原始輸入遙測和分析輸出的記錄通常會儲存，而且可供顯示。 輸入遙測和輸出記錄可能也會觸發動作，例如電子郵件、事件票證，以及裝置命令。

路由可以將遙測分派至一個或多個儲存體端點、分析處理程序和動作。 解決方案可能會以不同的順序結合階段，並且連同並行的平行工作處理這些階段。

遠端監視解決方案會使用 [Azure 串流分析](/azure/stream-analytics/)進行串流處理。 解決方案中的規則引擎會使用串流分析查詢來產生警示和動作。 例如，解決方案可以使用查詢來識別卡車儲存體區間平均溫度超過 5 分鐘低於 36 度的情況。

### <a name="storage"></a>儲存體

IoT 解決方案會產生大量的資料，這通常是時間序列資料。 這些資料必須儲存於可呈現視覺效果和報告的位置。 解決方案可能也需要稍後存取資料進行分析或其他處理。 通常會將資料分成暖資料及冷資料存放區。 暖資料存放區會保留最近的資料，以達到低延遲存取的效果。 冷資料存放區通常會儲存歷史資料。

遠端監視解決方案會使用 [Azure Time Series Insights](/azure/time-series-insights/) 作為其暖資料存放區，並使用 Cosmos DB 作為其冷資料存放區。

### <a name="ui-and-reporting-tools"></a>使用者介面和報告工具

解決方案使用者介面可以提供：

* 裝置資料和分析結果的存取和視覺效果。
* 透過登錄進行的裝置探索。
* 裝置的命令與控制。
* 裝置佈建工作流程。
* 通知和警示。
* 與即時的互動式儀表板整合，顯示大量裝置的資料。  
* 地理位置和地理感知的服務。

遠端監視解決方案包含的 Web 使用者介面可提供這項功能。 Web 使用者介面包含：

* 顯示裝置位置的互動式地圖。
* 可查詢和分析遙測的時間序列深入解析總管。

### <a name="business-integration"></a>商務整合

商務整合層會處理 IoT 解決方案與商務系統 (例如 CRM、ERP 及 LOB 應用程式) 的整合。 範例包括服務帳單、客戶支援服務，以及替換組件提供。

遠端監視解決方案會使用規則，在條件符合時傳送電子郵件。 例如，解決方案可以在貨車中的溫度低於 36 度時通知操作員。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解 Azure IoT 參考架構，並了解遠端監視解決方案加速器如何遵循建議的一些範例。 下一個步驟是閱讀 [Microsoft Azure IoT 參考架構](https://aka.ms/iotrefarchitecture)。