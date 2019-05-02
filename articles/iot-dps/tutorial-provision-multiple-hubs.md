---
title: 使用 Azure IoT 中樞裝置佈建服務來跨負載平衡 IoT 中樞佈建裝置 | Microsoft Docs
description: 在 Azure 入口網站中跨負載平衡 IoT 中樞進行裝置佈建服務自動裝置佈建
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 887bda92a1165a3dd17e9105e921a5df9e0c5534
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248159"
---
# <a name="provision-devices-across-load-balanced-iot-hubs"></a>跨負載平衡 IoT 中樞來佈建裝置

本教學課程說明如何使用裝置佈建服務針對多個負載平衡 IoT 中樞佈建裝置。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 使用 Azure 入口網站來將第二個裝置佈建到第二個 IoT 中樞 
> * 在第二個裝置中新增註冊清單項目
> * 將裝置佈建服務的配置原則設定為**平均分佈**
> * 將新的 IoT 中樞連結至裝置佈建服務

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="prerequisites"></a>必要條件

本教學課程是以先前的[將裝置佈建到中樞](tutorial-provision-device-to-hub.md)教學課程為基礎所建置。

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>使用 Azure 入口網站來將第二個裝置佈建到第二個 IoT 中樞

請依照[將裝置佈建到中樞](tutorial-provision-device-to-hub.md)教學課程的步驟，將第二個裝置佈建到其他 IoT 中樞。

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>在第二個裝置中新增註冊清單項目

註冊清單會將它對裝置所使用的證明方法 (用來確認裝置身分識別的方法) 告訴裝置佈建服務。 下一個步驟是新增第二個裝置的註冊清單項目。 

1. 在您裝置佈建服務的頁面中，按一下 [管理註冊]。 [新增註冊清單項目] 頁面會隨即出現。 
2. 按一下頁面頂端的 [新增]。
2. 填寫欄位，然後按一下 [儲存]。

## <a name="set-the-device-provisioning-service-allocation-policy"></a>設定裝置佈建服務的配置原則

配置原則是一項裝置佈建服務設定，可決定要如何將裝置指派到 IoT 中樞。 共有三個支援的配置原則： 

1. **最低延遲**：根據對裝置具有最低延遲的 IoT 中樞，將裝置佈建到該中樞。
2. **權重相等的分佈** (預設)：裝置佈建到每個已連結 IoT 中樞的機率都一樣。 這是預設設定。 如果您只要將裝置佈建到一個 IoT 中樞，可以保留此設定。 
3. **透過註冊清單進行靜態設定**：註冊清單中所需 IoT 中樞的規格，其優先順序高於裝置佈建服務層級的配置原則。

請遵循下列步驟來設定配置原則：

1. 若要設定配置原則，請在 [裝置佈建服務] 頁面中按一下 [管理配置原則]。
2. 將配置原則設為 [平均加權分佈]。
3. 按一下 [檔案] 。

## <a name="link-the-new-iot-hub-to-the-device-provisioning-service"></a>將新的 IoT 中樞連結至裝置佈建服務

連結裝置佈建服務與 IoT 中樞，讓裝置佈建服務能夠將裝置註冊到該中樞。

1. 在 [所有資源] 頁面上，按一下您先前建立的裝置佈建服務。
2. 在 [裝置佈建服務] 頁面中，按一下 [連結的 IoT 中樞]。
3. 按一下 [新增] 。
4. 在 [將連結新增至 IoT 中樞] 頁面中，使用選項按鈕來指定連結的 IoT 中樞是位於目前的訂用帳戶中，還是位於不同的訂用帳戶中。 然後，從 [IoT 中樞] 方塊選擇 IoT 中樞的名稱。
5. 按一下 [檔案] 。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用 Azure 入口網站來將第二個裝置佈建到第二個 IoT 中樞 
> * 在第二個裝置中新增註冊清單項目
> * 將裝置佈建服務的配置原則設定為**平均分佈**
> * 將新的 IoT 中樞連結至裝置佈建服務

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
-->
