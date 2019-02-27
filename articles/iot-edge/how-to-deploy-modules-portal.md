---
title: 從 Azure 入口網站部署模組 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure 入口網站將模組部署到 IoT Edge 裝置
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/19/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9d7729dce5419c5813de3c4dfce55c40098f5988
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430109"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>從 Azure 入口網站部署 Azure IoT Edge 模組

以您的商務邏輯建立了 IoT Edge 模組後，您會想要將其部署到裝置以在邊緣上操作。 如果您有多個模組一起運作來收集及處理資料，您可以一次全部部署，並宣告連接它們的路由規則。

本文說明 Azure 入口網站如何引導您建立部署資訊清單，並且將部署推送到 IoT Edge 裝置。 如需根據裝置共用標籤，建立目標為多個裝置的部署資訊，請參閱[大規模部署和監視 IoT Edge 模組](how-to-deploy-monitor.md) (英文)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* 已安裝 IoT Edge 執行階段的 [IoT Edge 裝置](how-to-register-device-portal.md)。

## <a name="select-your-device"></a>選取您的裝置

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
1. 選取功能表中的 [IoT Edge]。
1. 按一下裝置清單中目標裝置的識別碼。
1. 選取 [設定模組]。

## <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 如需部署資訊清單的運作方式以及如何建立的詳細資訊，請參閱[了解如何使用、設定以及重複使用 IoT Edge 模組](module-composition.md)。

Azure 入口網站有一個精靈，可以引導您建立部署資訊清單，而不用手動建置 JSON 文件。 其中包含三個步驟：**新增模組**、**指定路由**和**檢閱部署**。

### <a name="add-modules"></a>新增模組

1. 請在頁面的 [登錄設定] 區段中，提供可以存取任何私人容器登錄 (其中包含您的模組映像) 的認證。

1. 在頁面的 [部署模組] 區段中，選取 [新增]。

1. 從下拉式清單中查看模組的類型：

   * **IoT Edge 模組** - 預設選項。
   * **Azure 串流分析模組** - 從 Azure 串流分析工作負載產生的唯一模組。
   * **Azure Machine Learning 模組** - 從 Azure Machine Learning 工作區產生的唯一模組影像。

1. 選取 [IoT Edge 模組]。

1. 請提供模組的名稱，然後指定容器映像。 例如︰

   * **名稱** - tempSensor
   * **映像 URI** - mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

1. 視需要填寫選擇性欄位。 如需容器建立選項、重新啟動原則和所需狀態的詳細資訊，請參閱 [EdgeAgent 所需屬性](module-edgeagent-edgehub.md#edgeagent-desired-properties)。 如需模組對應項的詳細資訊，請參閱[定義或更新所需屬性](module-composition.md#define-or-update-desired-properties)。

1. 選取 [ **儲存**]。

1. 重複步驟 2-6 以將其他模組新增至您的部署。

1. 選取 [下一步] 繼續前往路由區段。

### <a name="specify-routes"></a>指定路由

根據預設，精靈會提供您名為 **route** 的路由，並且定義為 **FROM /\* INTO $upstream**，這表示任何模組的任何訊息輸出都會傳送到 IoT 中樞。  

請使用[宣告路由](module-composition.md#declare-routes)中的資訊來新增或更新路由，然後選取 [下一步] 繼續前往檢閱區段。

### <a name="review-deployment"></a>檢閱部署

檢閱區段會顯示 JSON 部署資訊清單，該清單會根據您在前兩個區段中的選項而建立。 請注意，還有兩個已宣告的模組尚未新增：**$edgeAgent** 和 **$edgeHub**。 這兩個模組組成 [IoT Edge 執行階段](iot-edge-runtime.md)，在每個部署中都是必要的預設值。

檢閱您的部署資訊，然後選取 [提交]。

## <a name="view-modules-on-your-device"></a>在裝置上檢視模組

一旦您將模組部署到您的裝置，就可以在入口網站的 [裝置詳細資料] 頁面中檢視所有項目。 此頁面會顯示每個已部署模組的名稱，以及像是部署狀態和結束代碼的實用資訊。

## <a name="deploy-modules-from-azure-marketplace"></a>從 Azure Marketplace 部署模組

Azure Marketplace 是集合了應用程式和服務的線上市集，您可以在其中瀏覽各式各樣的企業應用程式和解決方案，這些項目皆已通過認證而可在 Azure 上執行並獲得最佳效能，包括 [IoT Edge 模組](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。 您也可以透過 Azure 入口網站，在 [建立資源] 底下存取 Azure Marketplace。

您可以從 Azure Marketplace 或 Azure 入口網站安裝 IoT Edge 模組：

1. 尋找模組並開始部署程序。

   * Azure 入口網站：尋找模組並選取 [建立]。

   * Azure Marketplace：

     1. 尋找模組並選取 [立即取得]。
     1. 選取 [繼續] 來確認提供者的使用規定和隱私權原則。

1. 選擇要作為目標裝置連結對象的訂用帳戶和 IoT 中樞。

1. 選擇 [部署至裝置]。

1. 輸入裝置名稱或選取 [尋找裝置] 以瀏覽已向中樞註冊的裝置。

1. 選取 [建立] 以繼續執行設定部署資訊清單的標準程序，包括視需要新增其他資訊清單。 會預先定義新模組的詳細資料 (例如影像 URI、建立選項與必要屬性)，而且您可以視需要編輯它們。

## <a name="next-steps"></a>後續步驟

了解如何[大規模部署和監視 IoT Edge 模組](how-to-deploy-monitor.md) (英文)
