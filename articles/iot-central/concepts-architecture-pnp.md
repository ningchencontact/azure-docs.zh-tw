---
title: Azure IoT 中心的架構概念 | Microsoft Docs
description: 本文介紹 Azure IoT 中心架構的重要相關概念
author: dominicbetts
ms.author: dobett
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: abc09ac1a13537c31fe96fae14edefd0d06b6aef
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880250"
---
# <a name="azure-iot-central-architecture-preview-features"></a>Azure IoT Central 架構 (預覽功能)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

本文提供 Microsoft Azure IoT 中心架構的概觀。

![最上層架構](media/concepts-architecture-pnp/architecture.png)

## <a name="devices"></a>裝置

與 Azure IoT 中心應用程式交換資料的裝置。 裝置可以︰

- 傳送遙測等量值。
- 同步處理您應用程式的設定。

在 Azure IoT 中心，裝置範本會指定裝置可與您的應用程式交換的資料。 如需裝置範本的詳細資訊，請參閱[中繼資料管理](#metadata-management)。

若要深入了解如何將裝置連線到 Azure IoT 中心應用程式，請參閱[裝置連線能力](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。

## <a name="cloud-gateway"></a>雲端閘道

Azure IoT 中心使用 Azure IoT 中樞作為裝置連線的雲端閘道。 IoT 中樞可讓您︰

- 在雲端大規模擷取資料。
- 裝置管理。
- 安全的裝置連線能力。

若要深入了解 IoT 中樞，請參閱 [Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub/)。

若要深入了解 Azure IoT 中心的裝置連線能力，請參閱[裝置連線能力](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。

## <a name="data-stores"></a>資料存放區

Azure IoT 中心會在雲端儲存應用程式資料。 儲存的應用程式資料包括：

- 裝置範本。
- 裝置身分識別。
- 裝置中繼資料。
- 使用者和角色資料。

Azure IoT 中心會使用時間序列來儲存您的裝置所傳送的量值資料。 分析服務會使用裝置的時間序列資料。

## <a name="analytics"></a>分析

分析服務負責產生應用程式顯示的自訂報告資料。 操作者可以[自訂分析](howto-create-analytics.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (適用於應用程式中顯示的分析)。 分析服務是以 [Azure 時間序列深入解析](https://azure.microsoft.com/services/time-series-insights/)為基礎，可處理您的裝置所傳送的量值資料。

## <a name="rules-and-actions"></a>執行和動作

[規則和動作](howto-create-telemetry-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)會密切搭配運作，讓應用程式內的工作自動化。 建置者可以根據裝置遙測定義規則，例如超過所定義閾值的溫度。 Azure IoT 中心會使用資料流處理器來判斷何時符合規則條件。 符合規則條件時，就會觸發建置者所定義的動作。 例如，動作可以傳送一封電子郵件來通知工程師，裝置的溫度過高。

## <a name="metadata-management"></a>中繼資料管理

在 Azure IoT 中心應用程式中，裝置範本可定義各類型裝置的行為與功能。 例如，冰箱裝置範本可指定冰箱傳送給應用程式的遙測。

![範本架構](media/concepts-architecture-pnp/template-architecture.png)

在 [IoT Central 預覽] 應用程式裝置範本中:

- **裝置功能模型**會指定裝置的功能, 例如它所傳送的遙測、定義裝置狀態的屬性, 以及裝置所回應的命令。 裝置功能會組織成一或多個介面。 如需裝置功能模型的詳細資訊, 請參閱[IoT 隨插即用](https://aka.ms/iot-pnp-docs)檔。
- **雲端屬性**指定裝置 IoT Central 存放區的屬性。 這些屬性只會儲存在 IoT Central 中, 而且永遠不會傳送至裝置。
- **Views**會指定產生器所建立的儀表板和表單, 讓操作員監視和管理裝置。
- **自訂**可讓建立器覆寫裝置功能模型中的某些定義, 使其更與 IoT Central 應用程式相關。

根據每個裝置範本，應用程式可以有一或多個模擬與真實裝置。

## <a name="data-export"></a>資料匯出

在 Azure IoT Central 應用程式中, 您可以[持續將資料匯出](howto-export-data-event-hubs-service-bus-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)至自己的 Azure 事件中樞, 並 Azure 服務匯流排實例。 您也可以定期將資料匯出至 Azure Blob 儲存體帳戶。 IoT Central 可以匯出度量、裝置和裝置範本。

## <a name="batch-device-updates"></a>Batch 裝置更新

在 Azure IoT Central 應用程式中, 您可以[建立和執行作業](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)來管理已連線的裝置。 這些作業可讓您對裝置屬性或設定進行大量更新, 或執行命令。 例如, 您可以建立作業來增加多個冷飲自動販賣機器的風扇速度。

## <a name="role-based-access-control-rbac"></a>角色型存取控制 (RBAC)

針對使用預先定義角色的 Azure IoT 中心應用程式，[系統管理員可以為其定義存取規則](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。 系統管理員可以將使用者指派給角色，而這些角色可判斷使用者可以存取應用程式的哪些區域。

## <a name="security"></a>安全性

Azure IoT 中心內的安全性功能包括：

- 資料在傳送過程及待用期間都會予以加密。
- 驗證會經由 Azure Active Directory 或 Microsoft 帳戶提供。 支援雙因素驗證。
- 完整租用戶隔離。
- 裝置層級安全性。

## <a name="ui-shell"></a>UI Shell

UI Shell 是現代化、回應式、HTML5 瀏覽器型應用程式。
系統管理員可以自訂應用程式的 UI, 方法是套用自訂主題, 並修改說明連結, 以指向您自己的自訂説明資源。 若要深入瞭解 UI 自訂, 請參閱[自訂 Azure IOT CENTRAL UI](howto-customize-ui.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)文章。

操作員可以建立個人化的應用程式儀表板。 您可以有數個顯示不同資料的儀表板, 並在兩者之間切換。

## <a name="next-steps"></a>後續步驟

既然您已瞭解 Azure IoT Central 的架構, 建議的下一個步驟是瞭解 Azure IoT Central 中的[裝置連線能力](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。