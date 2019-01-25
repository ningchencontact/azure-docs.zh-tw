---
title: IoT Edge 模組認證 | Microsoft Docs
description: 認證 Marketplace 的 IoT Edge 模組。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c8056bd4912605a4cd3ee333b1be87d4f3a6d5ba
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198043"
---
# <a name="the-iot-edge-module-certification-process"></a>IoT Edge 模組認證程序

本文說明對於要在 Azure Marketplace 上發佈的 IoT Edge 模組進行認證的步驟和需求。 

>[!Note]
>本文件是暫時性的。 IoT Edge 模組 Marketplace 正同時建置中，本文日後將由公開文件取代。

## <a name="understanding-an-iot-edge-module"></a>了解 IoT Edge 模組

模組術語：

-   **模組映像**是套件，其中包含定義模組的軟體。

-   **模組執行個體**是在 IoT Edge 裝置上執行模組映像的計算單位。 模組執行個體是由 IoT Edge 執行階段啟動。

模組也可包含使用下列術語的 IoT 模組 SDK：

-   **模組身分識別**是一段資訊 (包括安全性認證)，儲存在與每個模組執行個體相關聯的 IoT 中樞。

-   **模組對應項**是儲存在 IoT 中樞的 JSON 文件，其中包含模組執行個體的狀態資訊，包括中繼資料、設定和條件。

-   **SDK** 用來以多種語言開發自訂模組，例如：C\#、C、Python、JAVA 和 Node.JS。

## <a name="the-onboarding-process-for-an-iot-edge-module"></a>IoT Edge 模組的上架程序

下列螢幕擷取畫面顯示 IoT Edge 模組在 Azure Marketplace 中公開發行的程序。

![認證程序](./media/cloud-partner-portal-iot-edge-module-certification-process/onboarding-process.png)

### <a name="onboarding-step-details"></a>上架步驟詳細資料

-   **步驟 1** - 合作夥伴在 Cloud Partner 入口網站工具中將供應項目類型為 **IoT Edge 模組**的供應項目提交給 Azure Marketplace 小組。 您必須是正式的 MS 合作夥伴，才能存取 Cloud Partner 入口網站工具。 如需詳細資訊，請參閱[發行者指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。

-   **步驟 2** - Marketplace 自動執行防毒和反惡意程式碼檢查。 IoT 小組可在此階段中執行其自訂的自動化擷取測試。

-   **步驟 3** - 公開發行模組。 模組會列於 Marketplace 中，且容器可供匿名者從 URL 提取。 例如 *marketplace.azurecr.io/module-identifier*。

## <a name="iot-edge-module-certification-criteria"></a>IoT Edge 模組認證準則

以下是 IoT Edge 模組要通過認證並且在 Azure Marketplace 中發佈所需符合的主要需求。

>[!Note]
>這些需求可能會變更。 您會事先收到通知，並且有給定的時間可更新您的容器，使其符合更新的需求。

### <a name="technical-requirements"></a>技術需求

**屬於 IoT Edge 模組**

-   目前只有與 Docker 相容的容器可作為 IoT Edge 模組。 模組必須在 [Moby](https://mobyproject.org/) 上執行。 

    >[!Note]
    >Docker 容器應該可與 Moby 搭配運作，因為 Moby 是 Docker 的基礎開放原始碼專案。

-   合作夥伴必須提供下列預設設定： 

    -   預設**標記** (不可空白)。

    -   預設 **createOptions** (可以空白)。

    -   如果使用 IoT 模組 SDK，則需要預設**對應項** (可以空白)。

    -   如果使用 IoT 模組 SDK，則需要預設**路由** (可以空白)。

**平台支援**

-   僅需支援已在 IoT Edge 第 1 層**正式推出**的平台 (如 [Azure IoT Edge 支援](https://docs.microsoft.com/azure/iot-edge/support)中所記錄)。 例如，其目前的意義為必須支援下列作業系統和架構組合：

    -   Ubuntu Server 18.04 for x64

    -   Ubuntu Server 16.04 for x64

    -   Raspbian-stretch for arm32 (armhf)

**裝置維度設定**

-   任何具有相等維度 (CPU/RAM/儲存體/GPU 等等) 的 Raspberry Pi 或更新版本的裝置，都可以作為 IoT Edge 裝置。 如果模組只能在特定的維度條件約束下運作，則必須將這些條件約束指定在模組描述中。

**預設設定**

-   模組應以每個支援平台 (作業系統 + 架構) 的預設參數啟動。

-   組態設定應明確列載 (標記、環境變數、對應項、路由)。在清單中，合作夥伴可以為其模組定義支援基本 HTML 標記或指向外部網頁的描述。

**版本控制**

-   客戶必須能夠選擇他們是要自動更新來自 Marketplace 的模組，還是要使用他們已測試的版本。 Marketplace 模組必須遵循與 IoT Edge 執行階段相同的版本控制模式。 例如︰

    -   累積標記 (例如 "1.0") 可進行更新。

    -   次要版本標記 (例如 "1.3.24") 不可更新。

**遙測**

-   使用 IoT 模組 SDK 的模組必須設定由 Marketplace 指派的唯一模組識別碼，供遙測之用。 這可讓 Azure Marketplace 識別執行中的模組執行個體數目。 這個唯一識別碼是 Marketplace 在擷取時指派的容器名稱。 請使用下列 SDK 的方法來設定此識別碼：
    - [C\#](https://hub.docker.com/_/mysql/)
    - [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

-   對於未使用 IoT 模組 SDK 的模組，在 Cloud Partner 入口網站中提供的深入解析會較不精確。 例如下載次數。

**安全性**

-   容器對主機的存取權應盡可能具有最低的特殊權限。 「具特殊權限」的容器應僅佔極少數。

-   合作夥伴必須在其提供的支援中確保其模組的安全性。

**更新**

-   合作夥伴必須將其模組保持在最新狀態且正常運作。 如果為 IoT Edge 執行階段規劃了任何重大變更，他們將事先獲得通知。

**模組 IoT SDK**

-   包含 IoT 模組 SDK 並不是認證的必要條件。
    不過，包含 IoT 模組 SDK 或許可提升使用者體驗。 例如，要支援路由、將訊息傳送至雲端等作業時。 必須使用 IoT 模組 SDK，才能取得與執行中的模組執行個體數目有關的遙測資料。

**主觀需求**

-   必須符合至少一個實際的 IoT Edge 使用案例。 例如，除非客戶願意從 IoT Edge 使用 WordPress 容器，否則就不應將該容器上架。

**法規需求 (就 AMP 原則而言)**

-   模組必須遵循 Microsoft Azure Marketplace 合約和原則。 如需詳細資訊，請參閱附加的發行者合約和[參與原則](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)。

-   除了 Azure Marketplace 合約和原則以外，可能還會有 IoT Edge 模組供應項目類型特有的其他法規需求。 此需求目前正在檢閱中。

-   此模組必須具有*使用條款*和*隱私權原則*。

-   如果模組使用任何第三方產品，則模組也應有第三方聲明。

**支援需求 (就 AMP 原則而言)**

-   合作夥伴須負責支援自己的 IoT Edge 模組。 他們應確保在 IoT Edge 模組描述中提供的支援選項可供使用，且至少有一個支援選項一律可用。 (請參閱發行者合約的第 4 款，以取得詳細資訊。)

**分類**

-   所有 IoT Edge 模組都會出現在**物聯網 \> IoT Edge 模組**類別下。 合作夥伴須自行為其產品選擇最適合的子類別。
