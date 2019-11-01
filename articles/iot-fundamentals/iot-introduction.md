---
title: Azure 物聯網 (IoT) 簡介
description: 說明 Azure IoT 與 IoT 服務基本概念的簡介，包括協助說明 IoT 用法的範例。
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/11/2018
ms.author: robinsh
ms.openlocfilehash: 2f46ebcabd98c7a8c3376157c72da9ec5ed424a4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935218"
---
# <a name="what-is-azure-internet-of-things-iot"></a>什麼是 Azure 物聯網 (IoT)？

Azure 物聯網 (IoT) 是由 Microsoft 管理的雲端服務集合，它會連線、監視及控制數十億個 IoT 資產。 簡單地說，IoT 解決方案是由一個或多個在雲端上執行，且互相通訊的 IoT 裝置和後端服務所組成。 

本文將討論 IoT 的基本概念、談論使用案例，以及簡短說明可用的八個不同服務。 了解可用服務後，您就可以找出能協助您設計案例的服務，然後仔細研究。

## <a name="introduction"></a>簡介

以下是 IoT 解決方案的主要部分：裝置、後端服務和兩者之間的通訊。 

### <a name="iot-devices"></a>IoT 裝置

裝置通常是由已連接感應器的電路板所組成，而感應器會連線至網際網路。 許多裝置透過 Wi-Fi 晶片進行通訊。 以下是 IoT 裝置的一些範例：

* 遠端油泵上的壓力感應器
* 空調機組中的溫度和溼度感應器
* 電梯中的加速計
* 房間中的狀態感應器

經常用於原型設計的兩個裝置分別是 Microsoft 的基本 MX Chip IoT Devkit 和 Raspberry PI 裝置。 MX Chip Devkit 具有內建的溫度、壓力和溼度感應器，還有陀螺儀和加速計，以及磁力計和 Wi-Fi 晶片。 Raspberry PI 是 IoT 裝置，您可以對其連接許多不同類型的感應器，藉此選取您案例真正需要的服務。 

如需可用 IoT 裝置的詳細資訊，請參閱業界最大的 [IoT 認證裝置目錄](https://catalog.azureiotsolutions.com/alldevices)。

[IoT 裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md) 可讓您建置在裝置上執行的應用程式，讓裝置能執行您所需的工作。 透過 SDK，您可以將遙測傳送至 IoT 中樞、接收來自 IoT 中樞的訊息和更新等等。

### <a name="communication"></a>通訊

您的裝置可以與後端服務進行雙方通訊。 以下是裝置與後端解決方案通訊的一些方法範例。

#### <a name="examples"></a>範例 

* 您的裝置可能會每隔 5 分鐘將行動冷藏車上的溫度傳送至 IoT 中樞。 

* 後端服務可要求裝置更頻繁地傳送遙測資料，以協助診斷問題。 

* 您的裝置可以根據其感應器中的讀取值來傳送警示。 例如，如果您正在監視化學工廠中的批次反應器，您可以在溫度超過特定值時傳送警示。

* 您的裝置可以將資訊傳送到儀表板，以便操作員進行檢視。 例如，煉油廠中的控制室可能會顯示每一條管線的溫度和壓力，以及通過管線的流量，並讓操作員觀看此管線。 

這些工作和其他更多工作都可以使用 [IoT 裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md) 來實作。

#### <a name="connection-considerations"></a>連線考量

安全、可靠的裝置連線通常是 IoT 方案中的最大挑戰。 這是因為相較於其他用戶端 (例如瀏覽器和行動應用程式)，IoT 裝置有不同的特性。 具體來說，IoT 裝置有以下特性：

* 通常是無人操作的嵌入式系統 (不同於手機)。

* 可以部署於實體存取成本昂貴的遠端位置。

* 可能只能透過解決方案後端來存取。 沒有其他方式可與裝置互動。

* 能力和/或處理資源可能都有限。

* 網路連線能力可能不穩定、緩慢或昂貴。

* 可能需要使用專屬、自訂或業界特定的應用程式通訊協定。

### <a name="back-end-services"></a>後端服務 

以下是後端服務可提供的一些功能。

* 大規模接收來自裝置的遙測資料，並判斷如何處理與儲存該資料。

* 分析遙測資料並提供見解 (即時或事後)。

* 將命令從雲端傳送到特定裝置。 

* 佈建裝置並控制哪些裝置可連接到您的基礎結構。

* 控制裝置狀態並監視其活動。

例如，在預測性維護案例中，雲端後端會儲存歷史遙測資料。 解決方案會使用此資料來識別特定幫浦上的潛在異常行為，以免真的發生問題。 使用資料分析，雲端後端就可以識別預防性解決方案要將命令傳回裝置，並採取矯正措施。 此程序會在裝置與雲端間產生自動回應迴圈，大幅提升解決方案的效率。

## <a name="an-iot-example"></a>IoT 範例

以下是一間公司如何使用 IoT 來節省數百萬元的案例。 

有一間大型牧場，其中有數十萬隻牛。 追蹤這麼大量的牛隻並知道這些牛的動向是一大工程，而且需要長時間開車巡邏。 他們在每一隻牛身上連接了感應器，並將資訊 (例如 GPS 座標和溫度) 傳送至後端服務，以寫入資料庫。

然後，他們會有一項分析服務，可掃描每一隻牛的傳入資料並分析資料，以檢查類似下列的問題：

* 有牛隻發燒了嗎？ 這隻牛發燒多久了？ 如果已經超過一天，他們會取得 GPS 座標並找到那一隻牛，然後在適當的狀況下，以抗生素進行治療。 

* 有隻牛在相同位置待超過一天了嗎？ 如果是的話，可取得 GPS 座標並找到那隻牛。 這隻牛掉下懸崖了嗎？ 這隻牛受傷了嗎？ 這隻牛是否需要協助？ 

實作此 IoT 解決方案讓公司能夠快速檢查牛隻狀況，並做出適當處理，同時減少必須開車四處查看牛隻的大量時間，也省下了大量成本。 如需更多公司如何使用 IoT 的實際範例，請參閱 [IoT 的 Microsoft 技術案例研究](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)。 

## <a name="iot-services"></a>IoT 服務

Azure 中有數個與 IoT 相關的服務，因此，您可能會不曉得要使用哪一個。 有些服務 (例如 IoT Central 和 IoT 解決方案加速器) 會提供範本，以協助您建立自己的解決方案並快速上手。 您也可以使用其他可用服務來完整開發自己的解決方案，這完全取決於您需要多少協助和多大的控制權。 以下是可用服務的清單和用途。

1. [**IoT Central**](../iot-central/core/overview-iot-central.md)：這是 SaaS 解決方案，可協助您連接、監視及管理 IoT 裝置。 若要開始，您可以選取適用您裝置類型的範本，然後建立及測試裝置操作員將使用的基本 IoT Central 應用程式。 IoT Central 應用程式也可讓您監視裝置，並佈建新的裝置。 此服務適用於簡單而不需要深入自訂服務的解決方案。 

2. [**IoT 解決方案加速器**](/azure/iot-suite)：這是 PaaS 解決方案的集合，可用來加速開發您的 IoT 解決方案。 您會從所提供的 IoT 解決方案著手，然後再根據需求來完整自訂此解決方案。 您需要 Java 或 .NET 技能來自訂後端，並需要 JavaScript 技能來自訂視覺效果。 

3. [**IoT 中樞**](/azure/iot-hub/)：此服務可讓您從裝置連線到 IoT 中樞，並監視及控制數十億個 IoT 裝置。 如果您需要讓 IoT 裝置與您的後端進行雙向通訊，這會特別實用。 這是 IoT Central 和 IoT 解決方案加速器的基礎服務。 

4. [**IoT 中樞裝置佈建服務**](/azure/iot-dps/)：這是您可用來安全地將裝置佈建到 IoT 中樞的 IoT 中樞協助程式服務。 若使用此服務，您可以輕鬆且快速地佈建數百萬個裝置，而不是逐一佈建這些裝置。 

5. [**IoT Edge**](/azure/iot-edge/)：此服務建立在 IoT 中樞之上。 可用來分析 IoT 裝置上的資料 (並非雲端中的資料)。 藉由將部分工作負載移到邊緣裝置，您就只需將較少的訊息傳送至雲端。 

6. [**Azure Digital Twins**](../digital-twins/index.yml)：此服務可讓您建立完整的實體環境模型。 您可以為人員、空間和裝置之間的關聯性和互動方式建立模型。 例如，您可以預測工廠的維修需求、分析輸電網路的即時能源需求，或最佳化辦公室的可用空間利用。

7. [**時間序列深入解析**](/azure/time-series-insights)：此服務可讓您儲存、視覺化及查詢 IoT 裝置所產生的大量時間序列資料。 您可以搭配 IoT 中樞來使用此服務。 

8. [**Azure 地圖服務**](/azure/azure-maps)：此服務提供地理資訊給 Web 和行動應用程式。 此服務有一組完整的 REST API 和 Web 型 JavaScript 控制項，可用來建立有彈性的應用程式，並且適用於 Apple 和 Windows 裝置的桌面或行動應用程式。

## <a name="next-steps"></a>後續步驟

如需一些實際的商業案例及所使用的架構資訊，請參閱 [Microsoft Azure IoT 技術案例研究](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)。

如需可透過 IoT DevKit 嘗試完成的一些範例專案，請參閱 [IoT DevKit 專案目錄](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)。 

如需不同服務和其使用方式的更完整說明，請參閱 [Azure IoT 服務與技術](iot-services-and-technologies.md)。

如需 IoT 架構的深入討論，請參閱 [Microsoft Azure IoT 參考架構](https://aka.ms/iotrefarchitecture)。
