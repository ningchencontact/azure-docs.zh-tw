---
title: 使用 Azure IoT Central 應用程式範本 |Microsoft Docs
description: 如何以操作員的身分在 Azure IoT Central 應用程式中使用裝置集合。
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a26f70c5a61f3855a3de991072a7e84103e87b69
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499215"
---
# <a name="use-application-templates"></a>使用應用程式範本

這篇文章說明如何，身為方案經理，來建立和使用應用程式範本。

當您建立 Azure IoT Central 應用程式時，您會有選擇的內建的範例範本。 您也可以從現有的 IoT Central 應用程式建立您自己的應用程式範本。 然後，當您建立新的應用程式時，您可以使用您自己的應用程式範本。

當您建立應用程式範本時，它會包含下列項目，從您現有的應用程式：

- 預設應用程式儀表板，包括儀表板版面配置和您已定義的所有圖格。
- 裝置範本，包括度量、 設定、 屬性、 命令和儀表板。
- 此規則。 包含的所有規則定義。 不過除了電子郵件動作的動作，不會包含在內。
- 裝置設定時，它會包含其條件和儀表板。

> [!WARNING]
> 如果儀表板包含顯示特定裝置的相關資訊的圖格，則這些圖格會顯示**找不到要求的資源**中新的應用程式。 您必須重新設定這些磚，以在新的應用程式中顯示裝置的相關資訊。

當您建立應用程式範本時，它不包含下列項目：

- 裝置
- 使用者
- 工作定義
- 連續資料匯出定義

應用程式範本所建立的任何應用程式，以手動方式將這些項目。

## <a name="create-an-application-template"></a>建立應用程式範本

若要從現有的 IoT Central 應用程式建立應用程式範本：

1. 移至**管理**應用程式中的區段。
1. 選取 **應用程式範本匯出**。
1. 在 **應用程式範本匯出**頁面上，輸入的名稱和描述您的範本。
1. 選取 [**匯出**] 按鈕來建立應用程式範本。 您現在可以將複製**共用連結**，可讓其他人從範本建立新的應用程式：

![建立應用程式範本](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>使用應用程式範本

若要使用的應用程式範本建立新的 IoT Central 應用程式，您需要先前建立**共用連結**。 貼上**共用連結**瀏覽器的網址列。 **建立應用程式**頁面會顯示您選取的自訂應用程式範本：

![從範本建立應用程式](media/howto-use-app-templates/create-app.png)

選取您的付款方案，並填妥表單上的其他欄位。 然後選取**建立**從應用程式範本建立新的 IoT Central 應用程式。

## <a name="manage-application-templates"></a>管理應用程式範本

在 [**應用程式範本匯出**] 頁面上，您可以刪除或更新的應用程式範本。

如果您刪除應用程式範本時，您無法再使用先前產生的可共用連結，來建立新的應用程式。

若要更新您的應用程式範本，在變更範本名稱或描述**應用程式範本匯出**頁面。 然後選取**匯出**按鈕一次。 這個動作會產生新**共用的連結**則任何先前**可共用連結**URL。

## <a name="next-steps"></a>後續步驟

既然您已了解如何使用應用程式範本，建議的下一個步驟是了解如何[管理 IoT Central 從 Azure 入口網站](howto-manage-iot-central-from-portal.md)
