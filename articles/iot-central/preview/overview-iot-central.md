---
title: 什麼是 Azure IoT 中心 | Microsoft Docs
description: Azure IoT Central 是一個 IoT 應用程式平台，能簡化 IoT 解決方案的建立，有助於降低 IoT 管理、作業與開發的負擔及費用。 本文提供 Azure IoT 中心的功能概觀。
author: dominicbetts
ms.author: dobett
ms.date: 11/12/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 8cfcbddfc8d50855860af655847f997fb2a01711
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479710"
---
# <a name="what-is-azure-iot-central-preview-features"></a>什麼是 IoT Central (預覽功能)？

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Azure IoT Central 中的 [IoT 隨插即用](../../iot-pnp/overview-iot-plug-and-play.md)功能目前為公開預覽狀態。 請勿將已啟用 IoT 隨插即用的 IoT Central [應用程式範本](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)用於生產工作負載。 針對生產環境，請使用從目前正式推出的[應用程式範本](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)建立的 IoT Central 應用程式。

IoT Central 是一個 IoT 應用程式平台，可降低開發、管理及維護企業級 IoT 解決方案的負擔和成本。 選擇使用 IoT Central 進行建置，可讓您有機會將時間、金錢和心力專注在 IoT 資料的企業轉型上，而不只是維護和更新複雜且不斷演進的 IoT 基礎結構。

Web UI 可讓您監視裝置狀況、建立規則，以及在數百萬個裝置的整個生命週期中管理裝置及其資料。 此外，它可讓您將 IoT 智慧擴充至企業營運應用程式，以根據裝置的深入解析採取行動。

本文針對 IoT Central 提供下列概述：

- 與專案相關聯的典型角色。
- 如何建立您的應用程式。
- 如何將裝置連線到您的應用程式。
- 如何管理您的應用程式。
- IoT Central 中的 Azure IoT Edge 功能。
- 如何將採用 Azure IoT Edge 執行階段的裝置連線至您的應用程式。

## <a name="known-issues"></a>已知問題

> [!Note]
> 這些已知問題只會出現在 IoT Central 預覽版應用程式中。

- 規則不支援所有動作 (僅限電子郵件)。
- 針對複雜類型 - 規則、分析和裝置群組不受支援。
- 連續資料匯出不支援 Avro 格式 (不相容)。
- 模擬裝置不支援所有複雜類型。
- 目前不支援 GeoJSON。
- 目前不支援地圖底圖。
- 作業不支援複雜類型。
- 不支援陣列結構描述類型。
- 不支援應用程式範本匯出和應用程式複製。
- 僅支援 C 裝置 SDK 和 Node.js 裝置與服務 SDK。
- 僅適用於美國與歐洲。
- 裝置功能模型必須具有在相同檔案中以內嵌方式定義的所有介面。

## <a name="personas"></a>角色

IoT Central 文件提及四個會與 IoT Central 應用程式互動的角色：

- 「解決方案建置者」  負責定義連線至應用程式的裝置類型，以及為操作員自訂應用程式。
- 「操作員」  管理已連線到應用程式的裝置。
- 「管理員」  負責進行管理工作，例如管理應用程式內的[使用者角色和權限](howto-administer.md)。
- 「裝置開發人員」  會為連線到您應用程式的裝置或 IoT Edge 模組，建立可在其中執行的程式碼。

## <a name="create-your-iot-central-application"></a>建立您的 IoT Central 應用程式

解決方案建置者可使用 IoT Central，為組織建立自訂、雲端裝載的 IoT 解決方案。 自訂 IoT 解決方案通常包括：

- 雲端式應用程式，可接收來您的裝置的遙測，並可讓您管理這些裝置。
- 多個裝置，這些裝置會執行已連線到雲端式應用程式的自訂程式碼。

您可以快速部署新的 IoT Central 應用程式，然後在瀏覽器中自訂它來滿足您的特定需求。 身為解決方案建置者，您會使用 Web 型工具，為連線到您應用程式的裝置建立「裝置範本」  。 裝置範本是定義某種裝置之特性和行為的藍圖，例如：

- 它所傳送的遙測。
- 操作員可以修改的業務屬性。
- 由裝置設定而且在應用程式中唯讀的裝置屬性。
- 屬性由操作員所設定，會決定裝置的行為。

此裝置範本包含：

- 一個_裝置功能模型_，說明裝置所應實作的功能，例如它所傳送的遙測資料和它所報告的屬性。
- 未儲存在裝置上的雲端屬性。
- 屬於 IoT Central 應用程式一部分的自訂、儀表板和表單。

### <a name="create-device-templates"></a>建立裝置範本

[IoT 隨插即用](../../iot-pnp/overview-iot-plug-and-play.md)可讓 IoT Central 整合裝置，您不需要撰寫任何內嵌的裝置程式碼。 IoT 隨插即用的核心是說明裝置功能的裝置功能模型結構描述。 在 IoT Central 預覽版應用程式中，裝置範本會使用這些 IoT 隨插即用裝置功能模型。

身為解決方案建置者，您可以利用數個選項來建立裝置範本：

- 在 IoT Central 中設計裝置範本，然後在您的裝置程式碼中實作其裝置功能模型。
- 從 [Azure IoT 認證裝置目錄](https://aka.ms/iotdevcat)匯入裝置功能模型，然後新增您的 IoT Central 應用程式所需的任何雲端屬性、自訂和儀表板。
- 使用 Visual Studio Code 建立裝置功能模型。 從模型實作您的裝置程式碼，並將您的裝置連線至 IoT Central 應用程式。 IoT Central 會從存放庫中尋找裝置功能模型，並為您建立簡單的裝置範本。
- 使用 Visual Studio Code 建立裝置功能模型。 從模型實作您的裝置程式碼。 手動將裝置功能模型匯入您的 IoT Central 應用程式中，然後新增您的 IoT Central 應用程式所需的任何雲端屬性、自訂和儀表板。

身為解決方案建置者，您可以使用 IoT Central 來產生測試裝置的程式碼，以驗證您的裝置範本。

### <a name="customize-the-ui"></a>自訂 UI

解決方案建置者也可以為負責應用程式日常使用的操作員，自訂 IoT Central 應用程式 UI。 解決方案建置者可以進行的自訂包括：

- 在裝置範本上定義屬性和設定的版面配置。
- 設定自訂儀表板，協助操作員更快發現見解及解決問題。
- 設定自訂分析，以探索來自已連線裝置的時間序列資料。

## <a name="connect-your-devices"></a>連線您的裝置

在建置者定義可連線到應用程式的裝置類型之後，裝置開發人員會建立要在裝置上執行的程式碼。 裝置開發人員可以使用 Microsoft 的開放原始碼 [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 來建立裝置程式碼。 這些 SDK 有廣泛的語言、平台和通訊協定支援，符合您將裝置連線到 IoT 中心應用程式的需求。 SDK 可協助您實作下列裝置功能：

- 建立安全的連線。
- 傳送遙測。
- 報告狀態。
- 接收組態更新。

如需詳細資訊，請參閱部落格文章[使用 Azure IoT SDK 的優點，以及未使用時所要避免的隱藏危險](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)。

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge 裝置

如同使用 [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 建立的裝置，您也可以將 [Azure IoT Edge 裝置](../../iot-edge/about-iot-edge.md)連線到 IoT Central 應用程式。 Azure IoT Edge 可讓您直接在 IoT Central 所管理的 IoT 裝置上執行雲端智慧和自訂邏輯。 IoT Edge 執行階段可讓您：

- 在裝置上安裝和更新工作負載。
- 在裝置上維護 Azure IoT Edge 安全性標準。
- 確定 IoT Edge 模組始終在執行。
- 將模組健康情況報告至雲端，以便進行遠端監控。
- 管理下游分葉裝置與 IoT Edge 裝置之間、IoT Edge 裝置上的模組之間，以及 IoT Edge 裝置與雲端之間的通訊。

如需詳細資訊，請參閱 [Azure IoT Edge 裝置和 IoT Central](./concepts-architecture.md#azure-iot-edge-devices)。

## <a name="manage-your-application"></a>管理您的應用程式

IoT Central 應用程式完全由 Microsoft 裝載，可降低管理您的應用程式的系統管理負荷。

操作員可以使用 IoT Central 應用程式來管理您 IoT Central 解決方案中的裝置。 操作員會執行下列工作：

- 監視已連線到應用程式的裝置。
- 針對裝置問題進行移難排解並修復。
- 佈建新裝置。

身為解決方案建置者，您可以定義對來自所連線裝置之資料串流操作的自訂規則和動作。 操作員可以在裝置層級啟用或停用這些規則，以控制和自動執行應用程式內的工作。

管理員可以利用[使用者角色和權限](howto-administer.md)，管理對您應用程式的存取。

## <a name="quotas"></a>配額

每個 Azure 訂用帳戶都有預設配額，而此配額會對 IoT 解決方案的範圍造成影響。 目前，IoT Central 會將您可在訂用帳戶中部署的應用程式數目限制為 10 個。 如果您需要提高此限制，請連絡 [Microsoft 支援服務](https://azure.microsoft.com/support/options/)。

## <a name="next-steps"></a>後續步驟

既然您已大致了解 IoT Central，以下是建議的後續步驟：

- 了解 [IoT Central 和 Azure IoT 解決方案加速器](../core/overview-iot-options.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)之間的差異。
- 熟悉 [Azure IoT 中心 UI](overview-iot-central-tour.md)。
- 從[建立 Azure IoT 中心應用程式](quick-deploy-iot-central.md)著手。
- 深入了解 [IoT 隨插即用](../../iot-pnp/overview-iot-plug-and-play.md)
- 了解如何[建立 Azure IoT Edge 裝置範本](./tutorial-define-edge-device-type.md)
