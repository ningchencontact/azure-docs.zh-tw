---
title: 管理存放庫中的 IoT 隨插即用預覽模型 |Microsoft Docs '
description: 如何使用 Azure IoT 認證入口網站、Azure CLI 和 Visual Studio 程式碼，管理存放庫中的裝置功能模型。
author: YasinMSFT
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: yahajiza
ms.openlocfilehash: 1b71d8bd0f0417c7dc408c580a1c73ac654743ce
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932865"
---
# <a name="manage-models-in-the-repository"></a>管理存放庫中的模型

IoT 隨插即用預覽模型存放庫會儲存裝置功能模型和介面。 儲存機制讓解決方案開發人員可以探索及取用模型和介面。

有三種工具可用來管理存放庫：

- Azure IoT 認證入口網站
- Azure CLI
- Visual Studio Code

## <a name="model-repositories"></a>模型存放庫

有兩種類型的模型存放庫可用於儲存裝置功能模型和介面：

- 有一個公用存放_庫_，可儲存[Azure IoT 認證裝置目錄](https://aka.ms/iotdevcat)中裝置的裝置功能模型和介面。 此存放庫也會儲存[Microsoft 合作夥伴所發行的](./howto-onboard-portal.md)[通用介面](./concepts-common-interfaces.md)和 DCMs 和介面。 若要瞭解如何認證裝置，並將其裝置功能模型新增至公用存放庫，請參閱 <<c0>認證您的 IoT 隨插即用裝置教學課程。
- 有多個_公司存放庫_。 當您上[架到 Azure IoT 認證入口網站](./howto-onboard-portal.md)時，系統會自動為您的組織建立公司存放庫。 您可以使用公司存放庫，在開發和測試期間儲存裝置功能模型和介面。

## <a name="azure-certified-for-iot-portal"></a>Azure IoT 認證入口網站

在[Azure IoT 認證入口網站](https://preview.catalog.azureiotsolutions.com)中，您可以完成下列工作：

- [完成 IoT 裝置的認證程式](./tutorial-certification-test.md)。
- 尋找 IoT 隨插即用裝置功能模型。 您可以使用這些模型來[快速建立 IoT 就緒裝置，並將其與解決方案整合](./quickstart-connect-pnp-device-solution.md)。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 提供命令來管理 IoT 隨插即用公用和公司模型存放庫中的裝置功能模型和介面。 如需詳細資訊，請參閱[安裝和使用適用于 Azure CLI 操作指南的 Azure IoT 擴充](./howto-install-pnp-cli.md)功能。

## <a name="visual-studio-code"></a>Visual Studio Code

若要在 Visual Studio Code 中開啟**模型存放庫**視圖。

1. 開啟 Visual Studio Code，使用**Ctrl + Shift + P**，輸入並選取**IoT 隨插即用：開啟 模型**存放庫。

1. 您可以選擇**開啟公用模型存放庫**，或**開啟組織模型存放庫**。 針對 [公司模型存放庫]，您必須輸入模型存放庫連接字串。 您可以使用 [Azure IoT 認證入口網站](https://preview.catalog.azureiotsolutions.com)，在**公司存放庫**的 [連接字串] 索引標籤上中找到此連接字串。

1. 新的索引標籤會開啟 [**模型存放庫**] 視圖。

    使用此視圖來新增、下載和刪除裝置功能模型和介面。 您可以使用篩選準則來尋找清單中的特定專案。

1. 若要在您的公司模型存放庫和公用模型存放庫之間切換，請使用**Ctrl + Shift + P**，然後輸入並選取**IoT 隨插即用：登出模型存放庫**。 然後使用**IoT 隨插即用：再次開啟 [** 模型存放庫] 命令。

> [!NOTE]
> 在 VS Code 中，公用模型存放庫是唯讀的。 Microsoft 合作夥伴可以在[Azure IoT 認證入口網站](https://preview.catalog.azureiotsolutions.com)中更新公用存放庫。

## <a name="next-steps"></a>後續步驟

建議的下一個步驟是瞭解如何[提交 IoT 隨插即用裝置以進行認證](tutorial-certification-test.md)。
