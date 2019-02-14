---
title: 在 Azure IoT Central 應用程式中管理裝置 | Microsoft Docs
description: 了解如何以操作員的身分在 Azure IoT Central 應用程式中管理裝置。
author: ellenfosborne
ms.author: elfarber
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 69c7da1a529e46c054d9c824c2aee71fa67bd8ff
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768635"
---
# <a name="manage-devices-in-your-azure-iot-central-application-new-ui-design"></a>在 Azure IoT Central 應用程式中管理裝置 (新的 UI 設計)

本文說明如何以操作員的身分在 Azure IoT Central 應用程式中管理裝置。 身為操作員，您可以：

- 使用 [Device Explorer] 頁面檢視、新增及刪除已連線至您 Azure IoT Central 應用程式的裝置。
- 維護裝置的最新清查。
- 透過變更儲存於裝置屬性中的值，將裝置中繼資料保持為最新的版本。
- 透過從 [設定] 頁面更新特定裝置上的設定來控制裝置的行為。

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="view-your-devices"></a>檢視您的裝置

檢視個別裝置：

1. 在左側導覽功能表上選擇 [Device Explorer]。 您將能在此看見[裝置範本](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)的清單。

1. 在 [範本] 清單中選擇一個裝置範本。

1. 在 [Device Explorer] 頁面的右側窗格中，您會看到從該裝置範本建立的裝置清單。 選擇個別的裝置以查看該裝置的 [裝置詳細資料] 頁面：

    ![[裝置詳細資料] 頁面](./media/howto-manage-devices-experimental/devicelist.png)

## <a name="add-a-device"></a>新增裝置

將裝置新增至 Azure IoT Central 應用程式：

1. 在左側導覽功能表上選擇 [Device Explorer]。

1. 選擇要用來建立裝置的裝置範本。

1. 選擇 [+ 新增]。

1. 選擇 [真實] 或 [模擬]。 真實裝置是您連線至 Azure IoT Central 應用程式的實體裝置。 模擬裝置具有由 Azure IoT Central 為您產生的範例資料。

## <a name="import-devices"></a>匯入裝置

若要將大量裝置連接至您的應用程式，您可以從 CSV 檔案中大量匯入裝置。 CSV 檔案應該具有下列資料行及標頭：

* **IOTC_DeviceID**：裝置識別碼應該是全部小寫。
* **IOTC_DeviceName**：此資料行是選擇性的。

在應用程式中大量註冊裝置：

1. 在左側導覽功能表上選擇 [Device Explorer]。

1. 在左側窗格上，選擇您要用來大量建立裝置的裝置範本。

    > [!NOTE]
    > 如果您還沒有裝置範本，您可以在 [未關聯的裝置] 下方匯入裝置，並在沒有範本的情況下註冊這些裝置。 匯入裝置後，您接著可將其與範本產生關聯。

1. 按一下 [匯入] 。

    ![匯入動作](./media/howto-manage-devices-experimental/BulkImport1.png)

1. 選取具有所要匯入裝置識別碼清單的 CSV 檔案。

1. 在檔案上傳之後，裝置匯入便會開始。 您可以在裝置方格上方追蹤匯入狀態。

1. 匯入完成之後，裝置方格上會顯示成功訊息。

    ![匯入成功](./media/howto-manage-devices-experimental/BulkImport3.png)

如果裝置匯入作業失敗，您會在裝置格線上看到錯誤訊息。 隨即會產生擷取所有錯誤的記錄檔，以供您下載。

**讓裝置與範本產生關聯**

如果您是藉由在 [未關聯的裝置] 下啟動匯入來註冊裝置，則裝置會在沒有與任何裝置範本產生關聯的情況下建立。 裝置必須與範本產生關聯，才能探索有關裝置的資料和其他詳細資料。 請遵循下列步驟讓裝置與範本產生關聯：

1. 在左側導覽功能表上選擇 [Device Explorer]。

1. 在左側面板上，選擇 [未關聯的裝置]：

    ![未關聯的裝置](./media/howto-manage-devices-experimental/UnassociatedDevices1.png)

1. 選取要與範本產生關聯的裝置：

1. 按一下 [關聯]：

    ![建立裝置的關聯](./media/howto-manage-devices-experimental/UnassociatedDevices2.png)

1. 從可用範本清單中選擇範本，然後按一下 [產生關聯] 按鈕。

1. 選取的裝置會與您選擇的裝置範本產生關聯。

> [!NOTE]
> 一旦裝置與範本產生關聯後，就無法解除關聯或與其他範本產生關聯。

## <a name="export-devices"></a>匯出裝置

若要將真實裝置連接至 IoT Central，您需要它的連接字串。 您可以大量匯出裝置詳細資料，以取得連接字串和其他屬性。 匯出程序會針對所有選取的裝置，建立包含裝置身分識別、裝置名稱及主要連接字串的 CSV 檔案。

若要從您的應用程式大量匯出裝置：

1. 在左側導覽功能表上選擇 [Device Explorer]。

1. 在左側面板上，選擇您要從中匯出裝置的裝置範本。

1. 選取您想要匯出的裝置，然後按一下 [匯出] 動作。

    ![匯出](./media/howto-manage-devices-experimental/Export1.png)

1. 匯出程序隨即啟動。 您可以在格線頂端追蹤狀態。

1. 匯出完成之後，即會顯示一則成功訊息，以及下載所產生檔案的連結。

1. 按一下 [成功訊息] 將檔案下載到磁碟上的本機資料夾。

    ![匯出成功](./media/howto-manage-devices-experimental/Export2.png)

1. 匯出的 CSV 檔案會包含下列資料行：裝置識別碼、裝置名稱、裝置金鑰及 X509 憑證指紋：

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY   
    * IOTC_X509THUMBPRINT_SECONDARY

## <a name="delete-a-device"></a>刪除裝置

從 Azure IoT Central 應用程式刪除真實或模擬裝置：

1. 在導覽功能表上選擇 [Device Explorer]。

1. 選取所要刪除裝置的裝置範本。

1. 選取所要刪除裝置旁邊的方塊。

1. 選擇 [刪除]。

## <a name="change-a-device-setting"></a>變更裝置設定

設定能控制裝置的行為。 也就是說，它們能讓您為裝置提供輸入。 您可以在 [裝置詳細資料] 頁面上檢視及更新裝置設定。

1. 在導覽功能表上選擇 [Device Explorer]。

1. 針對要變更設定的裝置，選擇裝置的裝置範本。

1. 選擇 [設定]  索引標籤。您可在此查看裝置的所有設定及其目前的值。 針對每個設定，您可以查看裝置是否仍然在同步處理。

1. 將設定修改為您所需的值。 您可以一次修改多個設定，並同時上傳它們。

1. 選擇 [更新]。 值將會傳送至您的裝置。 當裝置確認設定變更之後，設定的狀態就會回到 [已同步]。

## <a name="change-a-property"></a>變更屬性

屬性為與裝置相關聯的裝置中繼資料，例如城市和序號。 您可以在 [裝置詳細資料] 頁面上檢視及更新屬性。

1. 在導覽功能表上選擇 [Device Explorer]。

1. 針對要變更屬性的裝置，選擇裝置的裝置範本。

1. 選擇 [屬性] 索引標籤，您將能在該處看見所有屬性。

1. 將應用程式屬性修改為您所需的值。 您可以一次修改多個屬性，並同時上傳它們。 選擇 [更新]。

> [!NOTE]
> 您無法變更_裝置屬性_的值。 裝置屬性是由裝置所設定，並且在 Azure IoT Central 應用程式內是唯讀的狀態。

## <a name="next-steps"></a>後續步驟

您現在已了解如何在 Azure IoT Central 應用程式中管理裝置，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [如何使用裝置集合](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

<!-- Next how-tos in the sequence -->
