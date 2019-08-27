---
title: 開始導覽 Azure IoT 中心 UI | Microsoft Docs
description: 建置者需熟悉用來建立 IoT 解決方案之 Azure IoT 中心 UI 的主要區域。
author: dominicbetts
ms.author: dobett
ms.date: 07/05/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 1b6b5aff52673443fe19da684e85663a5ca8fe19
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69881867"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui-preview-features"></a>開始導覽 Azure IoT Central UI (預覽功能)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

本文為您介紹 Microsoft Azure IoT 中心 UI。 您可以使用此 UI 來建立、管理和使用 Azure IoT 中心解決方案及其連線的裝置。

「建置者」  可使用 Azure IoT 中心 UI 來定義 Azure IoT 中心解決方案。 您可以使用 UI：

* 定義可連線到您的解決方案的裝置類型。
* 為您的裝置設定規則和動作。
* 為使用您的解決方案的「操作員」  自訂 UI。

「操作員」  可使用 Azure IoT 中心 UI 來管理 Azure IoT 中心解決方案。 您可以使用 UI：

* 監視您的裝置。
* 設定您的裝置。
* 針對裝置問題進行移難排解並修復。
* 佈建新裝置。

## <a name="use-the-left-navigation-menu"></a>使用左側導覽功能表

您可以使用左側導覽功能表來存取應用程式的不同區域。 您可以選取 **<** 或 **>** 將導覽列展開或摺疊：

| 功能表 | 說明 |
| ---- | ----------- |
| ![左側導覽功能表](media/overview-iot-central-tour-pnp/navigationbar.png) | <ul><li>[儀表板]  頁面會顯示您的應用程式儀表板。 身為建置者，您可以為操作員自訂儀表板。 使用者也可以建立自己的儀表板。</li><li>[裝置]  頁面會列出與應用程式中的每個裝置範本相關聯的模擬和實際裝置。 身為操作員，您可以使用 [裝置]  來管理已連線的裝置。</li><li>[裝置群組]  頁面可讓您檢視及建立裝置群組。 身為操作員，您可以建立裝置群組，作為查詢所指定的裝置邏輯集合。</li><li>[規則]  頁面可讓您編輯會根據裝置遙測引發，並觸發可自訂動作的規則。</li><li>[分析]  頁面可針對裝置和裝置群組，顯示衍生自裝置遙測的分析。 操作員可以根據裝置資料建立自訂檢視，以從您的應用程式衍生見解。</li><li>[作業]  頁面可讓您建立及執行大規模更新裝置的作業，以啟用大量服務管理。</li><li>[裝置範本]  頁面會顯示建置者用來建立和管理裝置範本的工具。</li><li>[資料匯出]  頁面可供系統管理員用於連續匯出至其他 Azure 服務的設定，例如儲存體和佇列。</li><li>[系統管理]  頁面可顯示應用程式系統管理頁面，讓管理員可以管理應用程式設定、使用者和角色。</li></ul> |

## <a name="search-help-and-support"></a>搜尋、說明及支援

頂端功能表會出現在每個頁面上：

![工具列](media/overview-iot-central-tour-pnp/toolbar.png)

* 若要搜尋裝置範本與裝置，請輸入 [搜尋]  值。
* 若要變更 UI 語言或佈景主題，請選擇 [語言]  圖示。
* 若要登出應用程式，請選擇 [帳戶]  圖示。
* 若要取得說明及支援，請選擇 [說明]  下拉式功能表以取得資源清單。 在試用版的應用程式中，支援資源會包括[即時聊天](howto-show-hide-chat.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)的存取權。

您可以為 UI 選擇淺色佈景主題或深色調佈景主題：

![選擇 UI 的佈景主題](media/overview-iot-central-tour-pnp/themes.png)

> [!NOTE]
> 如果您的系統管理員已為應用程式設定自訂佈景主題，則無法選擇淺色與深色佈景主題選項。

## <a name="dashboard"></a>儀表板

![儀表板](media/overview-iot-central-tour-pnp/homepage.png)

* 儀表板是您登入 Azure IoT Central 應用程式時所看到的第一個頁面。 身為建置者，您可以藉由新增圖格，為其他使用者自訂應用程式儀表板。 若要深入了解，請參閱[設定裝置範本](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)教學課程。

* 身為操作員，您可以建立個人化儀表板，並在其與預設儀表板之間做切換。 若要進一步了解，請參閱[建立及管理個人儀表板](howto-personalize-dashboard.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)操作說明文章。

## <a name="devices"></a>裝置

![[裝置] 頁面](media/overview-iot-central-tour-pnp/explorer.png)

總管頁面可顯示您 Azure IoT Central 應用程式中依_裝置範本_分組的_裝置_。

* 裝置範本會定義可以連線到您應用程式的裝置類型。 若要深入了解，請參閱[在 Azure IoT 中心應用程式中定義新的裝置類型](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。
* 裝置代表應用程式中的實際或模擬裝置。 若要深入了解，請參閱[將新的裝置新增至 Azure IoT 中心應用程式](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。

## <a name="device-groups"></a>裝置群組

![裝置群組頁面](media/overview-iot-central-tour-pnp/devicesets.png)

[裝置群組]  頁面可顯示建置者所建立的裝置群組。 裝置群組為相關裝置的集合。 建置者可定義查詢，以識別裝置群組中包含的裝置。 當您在應用程式中自訂分析時，您可以使用裝置群組。 若要深入了解，請參閱[在 Azure IoT Central 應用程式中使用裝置群組](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)一文。

## <a name="rules"></a>規則

![規則頁面](media/overview-iot-central-tour-pnp/rules.png)

[規則] 頁面可讓您根據遙測、裝置狀態或裝置事件來定義規則。 規則在引發時可以觸發動作，例如傳送電子郵件給操作員。 建置者可使用此頁面來建立和管理規則。 如需詳細資訊，請參閱[為 Azure IoT Central 中的裝置設定規則和動作](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)教學課程。

## <a name="analytics"></a>分析

![分析頁面](media/overview-iot-central-tour-pnp/analytics.png)

分析頁面會顯示圖表，協助您了解連線到您應用程式的裝置行為。 操作員可使用此頁面來監視及調查已連線裝置的問題。 建置者可以定義在此頁面上顯示的圖表。 若要深入了解，請參閱[為 Azure IoT 中心應用程式建立自訂分析](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。

## <a name="jobs"></a>工作

![[工作] 頁面](media/overview-iot-central-tour-pnp/jobs.png)

[作業] 頁面可讓您在您的裝置上執行大量裝置管理作業。 建置者使用此頁面來更新裝置內容、設定與命令。 若要深入了解，請參閱[執行作業](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)一文。

## <a name="device-templates"></a>裝置範本

![裝置範本頁面](media/overview-iot-central-tour-pnp/templates.png)

[裝置範本] 頁面是建置者在應用程式中建立和管理裝置範本之處。 裝置範本會指定裝置特性，例如：

* 遙測、狀態和事件量值。
* 屬性。
* 命令。

建置者也可以建立表單和儀表板，供操作員用來管理裝置。

若要深入了解，請參閱[在 Azure IoT 中心應用程式中定義新的裝置類型](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。

## <a name="data-export"></a>資料匯出

![資料匯出頁面](media/overview-iot-central-tour-pnp/export.png)

[資料匯出] 頁面可讓系統管理員定義如何從應用程式中串流資料，例如遙測資料。 其他服務可儲存匯出的資料，或將其用於分析。 若要深入了解，請參閱[匯出 Azure IoT Central 中的資料](howto-export-data.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)一文。

## <a name="administration"></a>系統管理

![系統管理頁面](media/overview-iot-central-tour-pnp/administration.png)

[系統管理] 頁面包含管理員使用的工具連結，例如在應用程式中定義使用者和角色及自訂 UI。 若要深入了解，請參閱[管理 Azure IoT 中心應用程式](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)一文。

## <a name="next-steps"></a>後續步驟

您現在已大致了解 Azure IoT 中心並熟悉 UI 版面配置，下一個建議步驟是完成[建立 Azure IoT 中心應用程式](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)快速入門。
