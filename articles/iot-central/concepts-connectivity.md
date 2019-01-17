---
title: Azure IoT Central 中的裝置連線能力 | Microsoft Docs
description: 本文介紹 Azure IoT Central 裝置連線能力的重要相關概念
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 3671f6a3e3832a384e968fbf38128aff6bfb2252
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247668"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Azure IoT Central 中的裝置連線能力

本文介紹 Microsoft Azure IoT Central 裝置連線能力的重要相關概念。

Azure IoT Central 使用 [Azure IoT 中樞裝置佈建服務 (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)，讓 IoT Central 可支援大規模的裝置上線與連線。

-   客戶現在可以產生裝置認證並離線設定裝置，而不需要先在 IoT Central 中註冊裝置
-   IoT Central 支援透過業界建議的 X509 憑證式連線進行裝置連線，同時繼續支援並改善共用存取簽章 (SAS) 連線
-   IoT Central 客戶現在可以自備裝置識別碼在 IoT Central 中註冊裝置，因此可與現有的後台系統進行簡單的整合
-   以一致的方式將裝置連線到 IoT Central 

>[!NOTE]
>IoT Central 使用 Azure IoT 裝置佈建服務 (DPS) 作為所有裝置註冊和連線的基礎，您可以[深入了解 DPS](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)。

根據您的使用案例，遵循指示將裝置連線到 IoT Central
1. [快速地將單一裝置連線 (使用共用存取簽章)](#connect-a-single-device)
1. [使用共用存取簽章 (SAS) 大規模地將裝置連線](#connect-devices-at-scale-using-shared-access-signatures)
1. [使用 X509 憑證大規模地將裝置連線](#connect-devices-using-x509-certificates)，**建議用於生產工作負載**
1. [將裝置連線但先不註冊](#connect-without-first-registering-devices) 


>[!NOTE]
>以下是可供裝置連線及佈建的全球端點：**global.azure-devices-provisioning.net**。

## <a name="connect-a-single-device"></a>將單一裝置連線
使用 SAS 將單一裝置連線到 IoT Central 很簡單，只需要幾個步驟 
1. 從 Device Explorer 中新增 [真實裝置]，按一下 [+ 新增] > [實際] 以新增實際裝置。
    * 輸入裝置識別碼**<span style="color:Red"> (應為小寫)</span>**，或使用建議的裝置識別碼。
    * 輸入裝置名稱，或使用建議的名稱   
    ![新增裝置](media/concepts-connectivity/add-device.png)
1. 取得所新增裝置的連線詳細資料 (例如**範圍識別碼、裝置識別碼和主要金鑰**)，方法是按一下裝置頁面上的 [連線]。
    * [範圍識別碼](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)視每個 IoT Central 應用程式而定，並由 DPS 產生，可用來確保應用程式中的裝置識別碼為唯一。
    * **裝置識別碼**是每個應用程式唯一的裝置識別碼，裝置必須在註冊呼叫過程中傳送裝置識別碼。   
    * [主要金鑰] 是 IoT Central 為這個特定裝置所產生的 SAS 權杖。 
    ![連線詳細資料](media/concepts-connectivity/device-connect.PNG)
1. 在您的裝置程式碼中使用這些連線詳細資料 (**裝置身分識別、裝置名稱和裝置主要金鑰**) 來佈建裝置並加以連線，並立即開始看到資料流通。 如果您正在使用 MxChip 裝置，請遵循[這裡的逐步指示](howto-connect-devkit.md#add-a-real-device)，從＜準備 DevKit 裝置＞一節開始。   

    以下是其他可能會用到的語言參考。

    *   **C 語言：** 如果您所使用的是 C 語言，請遵循[這份 C 語言裝置用戶端範例](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)來將範例裝置連線。 在範例中使用下列設定。   

         ```
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         ## Enter the Device Id and Symmetric keys 
         prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
        ```

    *   **Node.js：** 如果您想要使用 Node.js，請[使用這裡的逐步指示](tutorial-add-device.md#prepare-the-client-code)，從＜準備用戶端程式碼＞一節開始。



## <a name="connect-devices-at-scale-using-shared-access-signatures"></a>使用共用存取簽章大規模地將裝置連線

若要使用 SAS 大規模地將裝置連線到 IoT Central，需要執行兩個步驟 
1. 透過 CSV 檔案將裝置匯入 IoT Central 來**註冊裝置**，然後使用用來將裝置連線的裝置連線詳細資料來匯出裝置
1. **裝置設定**：使用連線詳細資料 (**範圍識別碼、裝置識別碼和主要金鑰**) 對裝置進行程式設計，讓它可呼叫佈建服務，在啟動時取得其連線資訊/IoT Central 應用程式指派。

>[!NOTE]
>還有一個進階選項可讓您將裝置連線，而不需要先在 IoT Central 中註冊裝置，您可以[在這裡深入了解](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)。

**註冊裝置**

若要將大量裝置連線至您的應用程式，Azure IoT Central 有提供透過 CSV 檔案大量匯入裝置的功能。 

CSV 檔案需求：CSV 檔案應該具有下列資料行 (及標頭)
1.  IOTC_DeviceID **<span style="color:Red">(應為小寫)</span>**
1.  IOTC_DeviceName (選擇性)



匯入裝置以在您的應用程式中註冊
1.  在左側導覽功能表上選取 [總管]。
1.  在左側窗格上，選擇您要用來大量建立裝置的裝置範本。 
1.  按一下 [匯入]，然後選取具有所要匯入裝置識別碼清單的 CSV 檔案。
CSV 檔案應該具有下列資料行 (及標頭)
    *   IOTC_DeviceID **<span style="color:Red">(應為小寫)</span>**
    *   IOTC_DeviceName (選擇性)
1.  匯入完成之後，裝置方格上會顯示成功訊息。

匯出裝置以取得連線詳細資料，匯出會建立包含裝置識別碼、裝置名稱和裝置金鑰的 CSV 檔案。 使用這些詳細資料將裝置連線到 IoT Central。
若要從您的應用程式大量匯出裝置：
1.  在左側導覽功能表上選取 [總管]。
1.  選取您想要匯出的裝置，然後按一下 [匯出] 動作。
1.  匯出完成之後，隨即顯示成功訊息，以及下載所產生檔案的連結。
1.  按一下成功訊息將檔案下載到磁碟上的本機資料夾。
1.  匯出的 CSV 檔案將會擁有下列資料行資訊：**裝置識別碼、裝置名稱、裝置的主要/次要金鑰及主要/次要憑證指紋**
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY


**裝置設定**

在您的裝置程式碼中使用這些連線詳細資料 (**裝置身分識別 (IOTC_DEVICEID)、裝置主要金鑰 (IOTC_SASKEY_PRIMARY) 和範圍識別碼**) 來佈建裝置並加以連線。 如果您尚未這麼做，請從 IoT Central 應用程式的 [管理] > [裝置連線] > [範圍識別碼] 取得**範圍識別碼**。
如果您正在使用 **MxChip** 裝置進行連線，請遵循[這裡的逐步指示](howto-connect-devkit.md#add-a-real-device)，從＜準備 DevKit 裝置＞一節開始。   

以下是其他可能會用到的語言參考。

   *   **C 語言：** 如果您所使用的是 C 語言，請遵循[這份 C 語言裝置用戶端範例](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)來將範例裝置連線。 在範例中使用下列設定。   
         ```
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         ## Enter the Device Id and Symmetric keys 
         prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
        ```
    * **Node.js：** 如果您想要使用 Node.js，請[使用這裡的逐步指示](tutorial-add-device.md#prepare-the-client-code)，從＜準備用戶端程式碼＞一節開始。


## <a name="connect-devices-using-x509-certificates"></a>使用 X509 憑證來將裝置連線

使用 X.509 憑證作為證明機制是調整**生產環境**並簡化裝置佈建的絕佳方式。 X.509 憑證通常會排列在信任鏈結中，其中每個憑證是由下一個較高憑證的私密金鑰簽署，依此類推，於自我簽署的根憑證終止。 這樣會從受信任根憑證授權單位 (CA) 產生的根憑證，透過每個中繼 CA 將委派的信任鏈結建立至裝置上安裝之終端實體「分葉」憑證。 若要深入了解，請參閱[使用 X.509 CA 憑證進行裝置驗證](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview)。 

若要使用 X509 憑證將裝置連線到 IoT Central，需要執行三個主要步驟 
1. 透過新增/驗證用來產生裝置憑證的 X509 根/中繼憑證，在 IoT Central 應用程式中**進行連線設定**。  為 X509 憑證進行連線設定有兩個步驟。  

    *   **新增 X509 根或中繼憑證**，以用來產生分葉裝置憑證。 移至 [管理] > [裝置連線] > [憑證]。 
    
        ![連線設定](media/concepts-connectivity/connection-settings.PNG)
    *   **憑證驗證：** 驗證憑證擁有權可確保憑證的上傳者擁有憑證的私密金鑰。 驗證憑證
        *  產生驗證碼：按一下 [驗證碼] 欄位旁的按鈕即可產生驗證碼。 
        *  以此驗證碼建立 X.509 驗證憑證，並將憑證儲存為 .cer 檔案。 
        *  上傳已簽署的驗證憑證，然後按一下 [驗證]。

        ![連線設定](media/concepts-connectivity/verify-cert.png)
    *   **次要憑證：** 在 IoT 解決方案的生命週期內，您必須輪替憑證。 輪替憑證的兩大主因是安全性缺口和憑證到期。 當您更新主要憑證時，使用次要憑證可使嘗試佈建的裝置減少停機時間。

    **僅供測試** 
    
    以下是一些可用來產生 CA 憑證和裝置憑證的公用程式命令列工具。

    * 如果您正在使用 MxChip，這是用來產生 CA 憑證的[命令列工具](https://aka.ms/iotcentral-docs-dicetool)。將它新增至您的 IoT Central 應用程式，然後驗證憑證。 

    *   使用此[命令列工具](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )來
        * 建立憑證鏈結 (遵循 GitHub 文件中的步驟 2 進行)。 
         將憑證儲存為 .cer 檔案並上傳至 IoT Central (主要)。   
        * 從 IoT Central 應用程式取得驗證碼、產生憑證 (遵循 GitHub 文件中的步驟 3 進行)，然後上傳進行驗證。 
        * 使用您的裝置識別碼作為傳遞給工具的參數 (遵循步驟 4 進行)，來建立分葉憑證。 儲存憑證並用於您的裝置。     

1. 透過 CSV 檔案將裝置匯入 IoT Central 來**註冊裝置**。

1. **裝置設定**：使用上傳的根憑證來產生分葉憑證。 確定您使用**裝置識別碼**作為分葉憑證中的 CNAME，而且其為**小寫**。 這是用來產生分葉/裝置憑證**僅供測試**的[命令列工具](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )。

    使用佈建服務資訊對裝置進行程式設計，讓它可在啟動時取得其連線詳細資料和 IoT Central 應用程式指派。    

    **其他參考資料** 
    *   適用於 [RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases) 的範例實作。  

    *   [C 範例裝置用戶端](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)。

>[!NOTE]
>為裝置產生分葉憑證時，使用**裝置識別碼**作為 CNAME。

>[!NOTE]
>**裝置識別碼**應為小寫 
 
## <a name="connect-without-first-registering-devices"></a>將裝置來連線但先不註冊
IoT Central 的其中一個主要使用案例是讓 OEM 大量製造裝置、產生認證並在原廠進行設定，而不需要先在 IoT Central 中進行註冊。 啟動裝置並連線到 IoT Central 之後，操作員會核准裝置以連線到 IoT Central 應用程式。

以下是使用這項功能來將裝置連線的流程

![連線設定](media/concepts-connectivity/device-connection-flow.PNG)


根據您選擇的裝置驗證機制 (X509/SAS)，遵循下列步驟進行

1. **連線設定** 
    * **X509 憑證：**[新增並驗證根/中繼憑證](#connect-devices-using-x509-certificates)，然後在下一個步驟中使用它來產生裝置憑證。
    * **SAS：** 複製主要金鑰 (此金鑰是此 IoT Central 應用程式的群組 SAS 金鑰)，然後在下一個步驟中使用它來產生裝置 SAS 金鑰。 
![連線設定 SAS](media/concepts-connectivity/connection-settings-sas.png)

1. **產生裝置認證** 
    *   **憑證 X509：** 使用您已新增至此應用程式的根/中繼憑證，為您的裝置產生分葉憑證。 確定您使用**裝置識別碼**作為分葉憑證中的 CNAME **<span style="color:Red">(應為小寫)</span>**。 這是用來產生分葉/裝置憑證以供測試的[命令列工具](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )。
    *   **SAS**：裝置 SAS 金鑰可以使用此[命令列工具](https://www.npmjs.com/package/dps-keygen)產生。 使用上一個步驟中的主要 SAS 金鑰 (群組 SAS 金鑰)。 確定**裝置識別碼<span style="color:Red"> 為小寫</span>**。

        使用下列指示來產生裝置 SAS 金鑰           

        ```
        npm i -g dps-keygen
        ```
    
        **使用量**
                        
        ```
        dps-keygen <Primary_Key(GroupSAS)> <device_id>
        ```

1. **裝置設定** 
    
     使用**範圍識別碼、裝置識別碼、裝置憑證/SAS 金鑰**更新裝置，然後啟動裝置以連線到 IoT Central 應用程式。

1. **將裝置連線到 IoT Central：** 啟動裝置之後，連線到 DPS/IoT Central 進行註冊。

1. **將裝置關聯到範本：** 連線的裝置將會顯示在 **Device Explorer** 中的 [UnAssociated Devices] \(未關聯的裝置\) 下。 裝置佈建狀態為 [已註冊]。 將裝置**關聯**到適當的裝置範本，並核准裝置以連線到 IoT Central 應用程式。 裝置會取得 IoT Central 應用程式的連線詳細資料，然後連線並開始傳送資料。 裝置佈建現已完成，而且「佈建狀態」會變成 [已佈建]。

## <a name="device-provisioning-status"></a>裝置佈建狀態
當真實裝置連線到 Azure IoT Central 時，需要執行一連串步驟 
1. **已註冊**：裝置會先**註冊**，這表示裝置是在 IoT Central 中建立，並具有裝置的裝置識別碼。
在下列情況下，裝置為 [已註冊]  
    *   在 **Explorer** 上新增的真實裝置
    *   在 **Explorer** 上使用 [匯入] 新增的一組裝置
    *   尚未註冊但使用有效的認證連線，並顯示在 [未關聯] 裝置下的裝置。 

    在上述所有情況下，「佈建狀態」為 [已註冊]

1. **已佈建**：下一個步驟是當裝置使用有效的認證連線時，IoT Central 會完成佈建步驟 (方法是在 IoT 中樞內建立裝置)。 然後會將連接字串傳回裝置，以連線並開始傳送資料。 裝置「佈建狀態」現在會從 [已註冊] 變成 [已佈建]。

1.  **已封鎖**：操作員可以封鎖裝置，一旦裝置遭到封鎖，就無法將資料傳送至 IoT Central，而且必須加以重設。 封鎖的裝置「佈建狀態」為 [已封鎖]。 操作員也可以將裝置解除封鎖。 裝置解除封鎖之後，「佈建狀態」會返回之前的「佈建狀態」([已註冊] 或 [已佈建])。 

## <a name="getting-device-connection-string"></a>取得裝置連接字串
您可以使用下列步驟，將許多中樞裝置連接字串擷取到 Azure IoT 中樞 
1. 從裝置頁面取得連線詳細資料，例如**範圍識別碼、裝置識別碼、裝置主要金鑰** (移至裝置頁面 > 按一下 [連線]) 

    ![連線詳細資料](media/concepts-connectivity/device-connect.PNG)

1. 使用以下命令列工具來取得裝置連接字串。
    使用以下指示來取得裝置連接字串  

    ```cmd/sh
    npm i -g dps-keygen
    ```
    **使用量**

    為了建立連接字串，請在 bin/ 資料夾底下找出二進位檔
    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```
    深入了解[這裡的 dps-keygen 工具](https://www.npmjs.com/package/dps-keygen)。

## <a name="sdk-support"></a>SDK 支援

Azure 裝置 SDK 能讓您以最簡單的方式，在連線至 Azure IoT Central 應用程式的裝置上實作程式碼。 可用的裝置 SDK 如下：

- [Azure IoT SDK for C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK for Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK for Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK for Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK for .NET](https://github.com/azure/azure-iot-sdk-csharp)

每個裝置在連線時，都會使用可識別裝置的唯一連接字串。 裝置只能連線至其註冊所在的 IoT 中樞。 當您在 Azure IoT Central 應用程式中建立真實裝置時，應用程式會產生連接字串供您使用。

## <a name="sdk-features-and-iot-hub-connectivity"></a>SDK 的功能和 IoT 中樞連線能力

所有裝置在與 IoT 中樞通訊時，都會使用下列 IoT 中樞連線能力選項：

- [裝置到雲端傳訊](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [裝置對應項](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

下表摘要說明 Azure IoT Central 裝置功能與 IoT 中樞功能的對應方式：

| Azure IoT 中心 | Azure IoT 中樞 |
| ----------- | ------- |
| 測量：遙測 | 裝置到雲端傳訊 |
| 裝置屬性 | 裝置對應項的報告屬性 |
| 設定 | 裝置對應項所需和所報告的屬性 |

若要深入了解如何使用裝置 SDK，請參閱下列其中一篇文章，以取得程式碼範例：

- [將一般 Node.js 用戶端連線至 Azure IoT Central 應用程式](howto-connect-nodejs.md)
- [將 Raspberry Pi 裝置連線到 Azure IoT Central 應用程式](howto-connect-raspberry-pi-python.md)
- [將 DevDiv kit 裝置連線到 Azure IoT Central 應用程式](howto-connect-devkit.md)


## <a name="protocols"></a>通訊協定

裝置 SDK 支援使用下列網路通訊協定來連線至 IoT 中樞：

- MQTT
- AMQP
- HTTPS

如需這些差異通訊協定和其選擇方針的資訊，請參閱[選擇通訊協定](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols)。

如果裝置無法使用任何所支援的通訊協定，則可以使用 Azure IoT Edge 來轉換通訊協定。 IoT Edge 支援其他 intelligence-on-the-edge 案例，可從 Azure IoT Central 應用程式將處理卸載至邊緣。

## <a name="security"></a>安全性

在裝置與 Azure IoT Central 之間交換的資料會都加密。 IoT 中樞會對每個要從裝置連線至任何裝置面向 IoT 中樞端點的要求進行驗證。 為了避免透過網路交換認證，裝置會使用已簽署的權杖來進行驗證。 如需詳細資訊，請參閱[控制 IoT 中樞的存取權](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security)。

> [!NOTE]
> 目前，連線至 Azure IoT Central 的裝置必須使用 SAS 權杖。 連線至 Azure IoT Central 的裝置不支援 X.509 憑證。

## <a name="next-steps"></a>後續步驟

現在，您已了解 Azure IoT Central 中的裝置連線能力，以下是建議的後續步驟：

- [準備及連線 DevKit 裝置](howto-connect-devkit.md)
- [準備及連線 Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [將一般 Node.js 用戶端連線至 Azure IoT Central 應用程式](howto-connect-nodejs.md)
