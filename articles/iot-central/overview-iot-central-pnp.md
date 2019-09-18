---
title: 什麼是 Azure IoT 中心 | Microsoft Docs
description: Azure IoT 中心是一個端對端 SaaS 解決方案，您可用來建置及管理自訂 IoT 解決方案。 此文章提供 Azure IoT 中心的功能概觀。
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: bfdad0d81599035e7d8c270ec4e8ee8d6a45125e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858932"
---
# <a name="what-is-azure-iot-central-preview-features"></a>什麼是 IoT Central (預覽功能)？

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Azure IoT Central 中的 [IoT 隨插即用](../iot-pnp/overview-iot-plug-and-play.md)功能目前為公開預覽狀態。 請勿將已啟用 IoT 隨插即用的 IoT Central 應用程式用於生產工作負載。 針對生產環境，請使用從目前正式推出的應用程式範本建立的 IoT Central 應用程式。

Azure IoT Central 是一個完全受控的 IoT 軟體即服務解決方案，可讓您輕鬆地建立連接實體與數位世界的產品。 您可以將連線的產品願景帶到生活中，方法如下：

- 從連線的裝置衍生新的見解，讓您的客戶擁有更好的產品和體驗。
- 為您的組織創造新商機。

與典型的 IoT 專案相比，Azure IoT Central 可以：

- 降低管理負擔。
- 降低營運成本和額外負荷。
- 讓您在使用下列項目時，輕鬆自訂應用程式：
  - 領先業界的技術，例如 [Azure IoT 中樞](https://azure.microsoft.com/services/iot-hub/) 和 [Azure 時間序列深入解析](https://azure.microsoft.com/services/time-series-insights/)。
  - 企業級安全性功能，例如端對端加密。

下列影片將介紹 Azure IoT 中心的概觀：

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

此文章針對 Azure IoT Central 提供下列概述：

- 與專案相關聯的典型角色。
- 如何建立您的應用程式。
- 如何將裝置連線到您的應用程式。
- 如何管理您的應用程式。

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
- 這僅適用於歐洲北部和美國中部區域。
- 裝置功能模型必須具有在相同檔案中以內嵌方式定義的所有介面。

## <a name="personas"></a>角色

Azure IoT Central 文件提及四個會與 Azure IoT Central 應用程式互動的角色：

- 「建置者」  負責定義連線到應用程式的裝置類型，以及為操作員自訂應用程式。
- 「操作員」  管理已連線到應用程式的裝置。
- 「管理員」  負責管理各項工作，例如管理應用程式的使用者和角色。
- 「裝置開發人員」  建立可在連線到您應用程式的裝置上執行的程式碼。

## <a name="create-your-azure-iot-central-application"></a>建立 Azure IoT 中心應用程式

建置者可使用 Azure IoT 中心，為組織建立自訂、雲端裝載的 IoT 解決方案。 自訂 IoT 解決方案通常包括：

- 雲端式應用程式，可接收來您的裝置的遙測，並可讓您管理這些裝置。
- 多個裝置，這些裝置會執行已連線到雲端式應用程式的自訂程式碼。

您可以快速部署新的 Azure IoT Central 應用程式，然後在瀏覽器中自訂它來滿足您的特定需求。 身為「建置者」，您會使用 Web 型工具，為連線到您應用程式的裝置建立「裝置範本」  。 裝置範本是定義某種裝置之特性和行為的藍圖，例如：

- 它所傳送的遙測。
- 操作員可以修改的業務屬性。
- 由裝置設定而且在應用程式中唯讀的裝置屬性。
- 屬性由決定裝置行為的操作員所設定。

此裝置範本包含：

- 一個_裝置功能模型_，說明裝置所應實作的功能，例如它所傳送的遙測資料和它所報告的屬性。
- 未儲存在裝置上的雲端屬性。
- 屬於 IoT Central 應用程式一部分的自訂、儀表板和表單。

### <a name="create-device-templates"></a>建立裝置範本

[IoT 隨插即用](../iot-pnp/overview-iot-plug-and-play.md)可讓 IoT Central 整合裝置，您不需要撰寫任何內嵌的裝置程式碼。 IoT 隨插即用的核心是說明裝置功能的裝置功能模型結構描述。 在 IoT Central 預覽版應用程式中，裝置範本會使用這些 IoT 隨插即用裝置功能模型。

身為建置者，您可以利用數個選項來建立裝置範本：

- 在 IoT Central 中設計裝置範本，然後在您的裝置程式碼中實作其裝置功能模型。
- 從 [Azure IoT 認證裝置目錄](https://aka.ms/iotdevcat)匯入裝置功能模型，然後新增您的 IoT Central 應用程式所需的任何雲端屬性、自訂和儀表板。
- 使用 Visual Studio Code 建立裝置功能模型。 從模型實作您的裝置程式碼，並將您的裝置連線至 IoT Central 應用程式。 IoT Central 會從存放庫中尋找裝置功能模型，並為您建立簡單的裝置範本。
- 使用 Visual Studio Code 建立裝置功能模型。 從模型實作您的裝置程式碼。 手動將裝置功能模型匯入您的 IoT Central 應用程式中，然後新增您的 IoT Central 應用程式所需的任何雲端屬性、自訂和儀表板。

身為建置者，您可以使用 IoT Central 來產生測試裝置的程式碼，以驗證您的裝置範本。

### <a name="customize-the-ui"></a>自訂 UI

建置者也可以為負責應用程式日常使用的操作員，自訂 Azure IoT 中心應用程式 UI。 建置者可以進行的自訂包括：

- 在裝置範本上定義屬性和設定的版面配置。
- 設定自訂儀表板，協助操作員更快發現見解及解決問題。
- 設定自訂分析，以探索來自已連線裝置的時間序列資料。

## <a name="connect-your-devices"></a>連線您的裝置

在建置者定義可連線到應用程式的裝置類型之後，裝置開發人員會建立要在裝置上執行的程式碼。 裝置開發人員可以使用 Microsoft 的開放原始碼 [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 來建立裝置程式碼。 這些 SDK 有廣泛的語言、平台和通訊協定支援，符合您將裝置連線到 Azure IoT 中心應用程式的需求。 SDK 可協助您實作下列裝置功能：

- 建立安全的連線。
- 傳送遙測。
- 報告狀態。
- 接收組態更新。

如需詳細資訊，請參閱部落格文章[使用 Azure IoT SDK 的優點，以及未使用時所要避免的隱藏危險](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)。

## <a name="manage-your-application"></a>管理您的應用程式

Azure IoT 中心應用程式完全由 Microsoft 裝載，可降低管理您的應用程式的系統管理負荷。

操作員可以使用 Azure IoT 中心應用程式來管理您的 Azure IoT 中心解決方案中的裝置。 操作員會執行下列工作：

- 監視已連線到應用程式的裝置。
- 針對裝置問題進行移難排解並修復。
- 佈建新裝置。

身為建置者，您可以定義對來自所連線裝置之資料串流操作的自訂規則和動作。 操作員可以在裝置層級啟用或停用這些規則，以控制和自動執行應用程式內的工作。

管理員可以利用[使用者角色和權限](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)，管理對您應用程式的存取。

## <a name="next-steps"></a>後續步驟

既然您已大致了解 Azure IoT 中心，以下是建議的後續步驟：

- 了解 [Azure IoT 中心和 Azure IoT 解決方案加速器](overview-iot-options.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)之間的差異。
- 熟悉 [Azure IoT 中心 UI](overview-iot-central-tour-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。
- 從[建立 Azure IoT 中心應用程式](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)著手。
- 請依照下列一系列的教學課程操作，這些教學課程會告訴您如何：
  - [以建置者身分，建立裝置範本](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [以建置者身分，新增規則以讓您的解決方案自動化](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [以操作員身分，監視您的裝置](tutorial-monitor-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [以操作員身分，將裝置新增至解決方案](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- 深入了解 [IoT 隨插即用](../iot-pnp/overview-iot-plug-and-play.md)
