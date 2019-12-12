---
title: 將 Azure IoT Edge 裝置新增至 Azure IoT Central | Microsoft Docs
description: 以操作員的身分將 Azure IoT Edge 裝置新增至 Azure IoT Central 應用程式
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: e5d60c77e9bdc0733c12bca891eb6c3e33a1fceb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979065"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>教學課程：將 Azure IoT Edge 裝置新增至 Azure IoT Central 應用程式

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本教學課程說明如何在 Azure IoT Central 應用程式中新增和設定 Azure IoT Edge 裝置。 在本教學課程中，我們會從 Azure Marketplace 選擇已啟用 IoT Edge 的 Linux VM。

本教學課程由兩個部分所組成：

* 首先，身為操作員，您要先了解如何進行 IoT Edge 裝置的雲端優先佈建。
* 接著，您將了解如何執行 IoT Edge 裝置的「裝置優先」佈建。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 新增 IoT Edge 裝置
> * 設定 IoT Edge 裝置，以使用共用存取簽章 (SAS) 金鑰來協助佈建
> * 在 IoT Central 中檢視儀表板及模組健康情況
> * 將命令傳送至在 IoT Edge 裝置上執行的模組
> * 對於在 IoT Edge 裝置上執行的模組設定屬性

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您必須要有 Azure IoT 中心應用程式。 按照此快速入門[建立 Azure IoT Central 應用程式](./quick-deploy-iot-central.md)。

## <a name="enable-azure-iot-edge-enrollment-group"></a>啟用 Azure IoT Edge 註冊群組
從 [管理]  頁面中，啟用 Azure IoT Edge 註冊群組的 SAS 金鑰。

![醒目提示裝置連線的管理頁面螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="provision-a-cloud-first-azure-iot-edge-device"></a>佈建雲端優先的 Azure IoT Edge 裝置  
在本節中，您將使用環境感應器範本建立新的 IoT Edge 裝置，並佈建裝置。 選取 [裝置]   > [環境感應器範本]  。 

![醒目提示環境感應器範本的裝置頁面螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

選取 [+ 新增]  ，然後輸入您選擇的裝置識別碼和名稱。 選取 [建立]  。

![建立新裝置對話方塊的螢幕擷取畫面，其中已醒目提示裝置識別碼和建立](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

裝置會進入 [已註冊]  模式。

![醒目提示裝置狀態的環境感應器範本頁面螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-iot-edge-enabled-linux-vm"></a>部署已啟用 IoT Edge 的 Linux VM

> [!NOTE]
> 您可以選擇使用任何電腦或裝置。 作業系統可以是 Linux 或 Windows。

在本教學課程中，我們將使用已啟用 Azure IoT 的 Linux VM (已在 Azure 上建立)。 在 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) 中選取 [立即取得]  。 

![醒目提示立即取得的 Azure Marketplace 螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

選取 [繼續]  。

![[在 Azure 中建立此應用程式] 對話方塊的螢幕擷取畫面，其中已醒目提示 [繼續]](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


您會前往 Azure 入口網站。 選取 [建立]  。

![已醒目顯示 [建立] 的 Azure 入口網站螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

選取 [訂用帳戶]  、建立新的資源群組，然後選取 [(美國) 美國西部 2]  以獲得 VM 可用性。 然後輸入使用者和密碼資訊。 請記住這些資訊，後續步驟中將會用到。 選取 [檢閱 + 建立]  。

![[建立虛擬機器詳細資料] 頁面的螢幕擷取畫面，其中已醒目提示各種選項](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

通過驗證之後，選取 [建立]  。

![[建立虛擬機器] 頁面的螢幕擷取畫面，其中已醒目提示通過驗證和建立](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

建立資源需要幾分鐘的時間。 選取 [前往資源]  。

![[部署完成] 頁面的螢幕擷取畫面，其中已醒目提示 [前往資源]](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-an-iot-edge-device"></a>將 VM 佈建為 IoT Edge 裝置 

在 [支援 + 疑難排解]  底下，選取 [序列主控台]  。

![[支援 + 疑難排解] 選項的螢幕擷取畫面，其中已醒目提示序列主控台](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

您將會看到類似以下的畫面：

![主控台的螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

按下 Enter 鍵，並在出現提示時提供使用者名稱和密碼，然後再次按下 Enter 鍵。 

![主控台的螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

若要以系統管理員 (「根」使用者) 的身分執行命令，請輸入：**sudo su –**

![主控台的螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

檢查 IoT Edge 執行階段版本。 在撰寫本文時，目前的 GA 版本是 1.0.8。

![主控台的螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

安裝 vim 編輯器，或視需要使用 nano。 

![主控台的螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![主控台的螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

編輯 IoT Edge config.yaml 檔案。

![主控台的螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

向下捲動 yaml 檔案的連接字串部分並轉成註解。 

**之前**

![主控台的螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**之後** (按下 Esc 鍵並按下小寫 a，以開始編輯。)

![主控台的螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

將 yaml 檔案的對稱金鑰部分取消註解。 

**之前**

![主控台的螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**之後**

![主控台的螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

移至 IoT Central。 取得 IoT Edge 裝置的範圍識別碼、裝置識別碼和對稱金鑰。
![醒目提示各種裝置連線選項的 IoT Central 螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

移至 Linux 電腦，並將範圍識別碼及註冊識別碼取代為裝置識別碼和對稱金鑰。

按下 Esc，並輸入 **:wq!** 。 按下 Enter 鍵以儲存變更。

重新啟動 IoT Edge 以處理您的變更，然後按下 Enter 鍵。

![主控台的螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

輸入 **iotedge list**。 幾分鐘後，您會看到三個已部署的模組。

![主控台的螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>IoT Central Device Explorer 

在 IoT Central 中，您的裝置會轉為已佈建的狀態。

![醒目提示裝置狀態的 IoT Central 裝置選項螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

[模組]  索引標籤會顯示裝置和模組在 IoT Central 的狀態。 

![IoT Central 模組索引標籤的螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


您會看到雲端屬性列在表單中 (來自您在上一個步驟中建立的裝置範本)。 輸入值，並選取 [儲存]  。 

![「我的 Linux Edge 裝置」表單的螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

以下是以儀表板磚形式呈現的檢視。

![「我的 Linux Edge 裝置」儀表板磚的螢幕擷取畫面](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

## <a name="next-steps"></a>後續步驟

您現在已了解如何在 IoT Central 中管理 IoT Edge 裝置，以下是建議的後續步驟：

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [設定透明閘道](../../iot-edge/how-to-create-transparent-gateway.md)
