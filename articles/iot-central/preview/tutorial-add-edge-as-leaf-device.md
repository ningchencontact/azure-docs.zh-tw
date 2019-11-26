---
title: 將 Azure IoT Edge 裝置新增至 Azure IoT Central | Microsoft Docs
description: 以操作員的身分將 Azure IoT Edge 新增至 Azure IoT Central
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: ae80a624ed1f85a1f59fea79b152a4bc31067ad1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892634"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application-preview-features"></a>教學課程：將 Azure IoT Edge 裝置新增至 Azure IoT Central 應用程式 (預覽功能)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本教學課程說明如何在 Microsoft Azure IoT Central 應用程式中新增和設定 *Azure IoT Edge 裝置*。 在本教學課程中，我們會從 Azure Marketplace 選擇已啟用 Azure IoT Edge 的 Linux VM。

本教學課程由兩個部分所組成：

* 首先，身為操作員，您要先了解如何進行 Azure IoT Edge 裝置的雲端優先佈建。
* 接著，您將學習如何執行 Azure IoT Edge 裝置的裝置優先佈建。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 新增 Azure IoT Edge 裝置
> * 設定 Azure IoT Edge 裝置使用 SAS 金鑰協助佈建
> * 在 IoT Central 中檢視儀表板、模組健康情況
> * 將命令傳送至在 Azure IoT Edge 裝置上執行的模組
> * 對於在 Azure IoT Edge 裝置上執行的模組設定屬性

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您必須要有 Azure IoT 中心應用程式。 按照此快速入門[建立 Azure IoT Central 應用程式](./quick-deploy-iot-central.md) (英文)。

## <a name="enable-azure-iot-edge-enrollment-group"></a>啟用 Azure IoT Edge 註冊群組
從 [管理] 頁面啟用 Azure IoT Edge 註冊群組的 SAS 金鑰。

![裝置範本 - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="cloud-first-azure-iot-edge-device-provisioning"></a>雲端優先的 Azure IoT Edge 裝置佈建   
在本節中，您將使用**環境感應器範本**建立新的 Azure IoT Edge 裝置，並佈建裝置。 按一下左側導覽列上的 [裝置]，然後按一下 [環境感應器範本]。 

![裝置範本 - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

按一下 [+ 新增]  ，然後輸入適合您的裝置識別碼和名稱。 

![裝置範本 - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

裝置會進入 [已註冊]  模式。

![裝置範本 - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-azure-iot-edge-enabled-linux-vm"></a>部署已啟用 Azure IoT Edge 的 Linux VM

>注意：您可以選擇使用任何電腦或裝置。 作業系統：Linux 或 Windows)

在本教學課程中，我們選擇已啟用 Azure IoT 的 Linux VM (可在 Azure 上建立)。 您將會進入 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview)，請按一下 [立即取得]  按鈕。 

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

按一下 [繼續] 

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


您將會進入 Azure 入口網站。 請按一下 [建立]  按鈕

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

選取 [訂用帳戶] 建立新的資源群組，最好選取 [美國西部 2] 以獲得 VM 可用性，然後輸入使用者和密碼。 請記住使用者，後續的步驟會需要密碼。 按一下 [檢閱 + 建立] 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

驗證之後，按一下 [建立] 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

建立資源需要幾分鐘的時間。 按一下 [前往資源] 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-azure-iot-edge-device"></a>將 VM 佈建為 Azure IoT Edge 裝置 

在左側導覽中的 [支援 + 疑難排解] 底下，按一下 [序列主控台]

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

您會看到如下的畫面

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

按下 Enter 鍵，並在出現提示時提供使用者名稱和密碼，然後按下 Enter 鍵。 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

若要以系統管理員/根使用者身分執行命令，請執行命令：**sudo su –**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

檢查 Azure IoT Edge 執行階段版本。 目前的 GA 版本為 1.0.8

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

安裝 vim editor 或使用 nano (依您的個人偏好)。 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

編輯 Azure IoT Edge yaml 檔案

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

向下捲動 yaml 檔案的連接字串部分並轉成註解。 

**之前**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**之後** (按下 Esc 鍵並按下小寫 a，以開始編輯)

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

將 yaml 檔案的對稱金鑰部分取消註解。 

**之前**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**之後**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

移至 IoT Central，並取得 Azure IoT Edge 裝置![裝置連線](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)的範圍識別碼、裝置識別碼和對稱金鑰

移至 [Linux] 方塊，並將範圍識別碼、註冊識別碼取代為裝置識別碼和對稱金鑰

按下 **Esc** 並輸入 **:wq!** 並按下 **Enter** 鍵以儲存變更

重新啟動 Azure IoT Edge 處理您的變更，然後按下 **Enter** 鍵

![裝置連線](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

輸入：**iotedge list**，這會需要幾分鐘的時間，然後您會看到三個部署完畢的模組

![裝置連線](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>IoT Central Device Explorer 

您的裝置會在 IoT Central 轉為已佈建狀態

![裝置連線](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

[模組] 索引標籤會顯示裝置和模組在 IoT Central 的狀態 

![裝置連線](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


雲端屬性會顯示在表單中 (來自您在上一個步驟中建立的裝置範本)。 輸入值，並按一下 [儲存]  。 

![裝置連線](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

儀表板圖格

![裝置連線](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

在本教學課程中，您已了解如何：

* 新增 Azure IoT Edge 裝置
* 設定 Azure IoT Edge 裝置使用 SAS 金鑰協助佈建
* 在 IoT Central 中檢視儀表板、模組健康情況
* 將命令傳送至在 Azure IoT Edge 裝置上執行的模組
* 對於在 Azure IoT Edge 裝置上執行的模組設定屬性

## <a name="next-steps"></a>後續步驟

您現在已了解如何在 IoT Central 中管理 Azure IoT Edge 裝置，以下是建議的後續步驟：

<!-- Next how-tos in the sequence -->

關於設定透明閘道的方式，請按照此教學課程操作

> [!div class="nextstepaction"]
> [設定透明閘道](../../iot-edge/how-to-create-transparent-gateway.md)
