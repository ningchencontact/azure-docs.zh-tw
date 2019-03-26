---
title: Azure IoT Central 中的裝置連線能力 | Microsoft Docs
description: 本文介紹 Azure IoT Central 裝置連線能力的重要相關概念
author: dominicbetts
ms.author: dobett
ms.date: 02/28/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 28aef1c1b5091db75b4a1fb6786ee712b42a1ea2
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418766"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Azure IoT Central 中的裝置連線能力

本文介紹 Microsoft Azure IoT Central 裝置連線能力的重要相關概念。

使用 azure IoT Central [Azure IoT 中樞裝置佈建服務 」 (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)管理所有的裝置註冊和連線。

使用 DPS 可讓：

- IoT 中心，以支援大規模的連線裝置和上架。
- 您可以產生裝置認證和設定離線的裝置，而不需註冊的裝置透過 IoT 中心 UI。
- 若要使用連接的裝置被共用存取簽章 (SAS)。
- 連線使用業界標準 X.509 憑證的裝置。
- 您要使用您自己的裝置識別碼，在 IoT Central 中註冊裝置。 使用您自己的裝置識別碼，可簡化與現有的後端辦公室系統整合。
- 單一、 一致的方式將裝置連接到 IoT 中心。

本做法文章會說明下列四個使用案例：

1. [快速連線使用單一裝置的 SAS](#connect-a-single-device)
1. [使用 SAS 大規模的裝置連線](#connect-devices-at-scale-using-sas)
1. [連接的裝置使用 X.509 憑證的大量](#connect-devices-using-x509-certificates)這是建議用於生產環境的方法。
1. [將裝置連線但先不註冊](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>將單一裝置連線

當您利用 IoT Central 試用或測試的裝置，則這個方法會很有用。

若要使用 SAS 的 IoT Central 連接單一裝置，請遵循下列步驟：

1. 若要加入實際的裝置，瀏覽至**Device Explorer**，選擇裝置範本，然後選取 **+ 新增 > 實際**:
    - 輸入您自己 （小寫）**裝置識別碼**或使用建議的識別碼。
    - 請輸入**裝置名稱**或使用建議的名稱。

      ![新增裝置](media/concepts-connectivity/add-device.png)

1. 若要取得裝置連接資訊，請選取**Connect** [裝置] 頁面上。 您需要**領域識別碼**，**裝置識別碼**，並**主索引鍵**值：
    - 每個 IoT Central 應用程式都有唯一[領域識別碼](../iot-dps/concepts-device.md#id-scope)所產生 DPS。
    - [裝置識別碼](../iot-dps/concepts-device.md#device-id)是唯一的裝置識別碼。 裝置識別碼會儲存在[身分識別登錄](../iot-hub/iot-hub-devguide-identity-registry.md)。
    - **主索引鍵**是 SAS 權杖，為裝置產生的 IoT 中心。

      ![連線詳細資料](media/concepts-connectivity/device-connect.png)

若要啟用您的裝置連線，並將資料傳送至 IoT 中，您的 IoT Central 應用程式中使用裝置程式碼中的連接資訊。 如需連線裝置的詳細資訊，請參閱 <<c0> [ 後續步驟](#next-steps)。

## <a name="connect-devices-at-scale-using-sas"></a>使用 SAS 大規模的裝置連線

若要將裝置連接到 IoT Central，大規模使用 SAS，您需要註冊，然後再將裝置：

### <a name="register-devices-in-bulk"></a>大量註冊裝置

若要註冊大量的裝置與 IoT Central 應用程式，使用 CSV 檔案[匯入裝置識別碼和裝置名稱](howto-manage-devices.md#import-devices)。

若要擷取匯入的裝置的連線資訊[從您的 IoT Central 應用程式匯出 CSV 檔案](howto-manage-devices.md#export-devices)。

> [!NOTE]
> 若要了解如何連接裝置，以及在不含第一個它們在註冊 IoT Central，請參閱而不需要第一個註冊的裝置的連線。

### <a name="set-up-your-devices"></a>設定您的裝置

若要啟用您的裝置連線，並將資料傳送至 IoT 中，您的 IoT Central 應用程式，從匯出檔案，在您的裝置程式碼中使用的連接資訊。 如需連線裝置的詳細資訊，請參閱 <<c0> [ 後續步驟](#next-steps)。

## <a name="connect-devices-using-x509-certificates"></a>使用 X.509 憑證的裝置連線

在生產環境中，使用 X.509 憑證是 IoT 中心的建議的裝置驗證機制。 若要深入了解，請參閱[使用 X.509 CA 憑證進行裝置驗證](../iot-hub/iot-hub-x509ca-overview.md)。

下列步驟說明如何將裝置連接至使用 X.509 憑證的 IoT Central:

1. 在您的 IoT Central 應用程式_新增並驗證中繼或根 X.509 憑證_您用來產生裝置憑證：

    - 瀏覽至**管理 > 裝置連線 > 憑證 (X.509)** 並新增 X.509 根或中繼憑證，您用來產生分葉裝置憑證。

      ![連線設定](media/concepts-connectivity/connection-settings.png)

      如果您有安全性缺口，或您主要的憑證設定為過期，請使用次要憑證可減少停機時間。 您可以繼續使用次要憑證，而您更新主要憑證的裝置佈建。

    - 驗證憑證擁有權，可確保憑證上的傳者具有憑證的私密金鑰。 若要確認憑證：
        - 選取 [] 按鈕旁**驗證碼**產生程式碼。
        - 使用您在上一個步驟中產生的驗證程式碼中建立 X.509 驗證憑證。 將憑證儲存為.cer 檔案。
        - 上傳已簽署的驗證憑證，然後選取**確認**。

          ![連線設定](media/concepts-connectivity/verify-cert.png)

1. 使用 CSV 檔案_匯入和註冊裝置_IoT Central 應用程式中。

1. _設定您的裝置。_ 使用上傳的根憑證來產生分葉憑證。 使用**裝置識別碼**中分葉憑證的 CNAME 值。 裝置識別碼應該是全部小寫。 然後撰寫您的裝置佈建服務的資訊。 當裝置第一處於開啟狀態時，它會從 DPS 擷取其 IoT Central 應用程式的連接資訊。

### <a name="further-reference"></a>進一步的參考

- 適用於 [RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases) 的範例實作。

- [C 範例裝置用戶端](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)。

### <a name="for-testing-purposes-only"></a>僅供測試用途

僅供測試，您可以使用這些公用程式產生的 CA 憑證和裝置憑證。

- 如果您使用 DevKit 裝置，這[命令列工具](https://aka.ms/iotcentral-docs-dicetool)會產生您可以新增至您的 IoT Central 應用程式，以驗證憑證的 CA 憑證。

- 使用此[命令列工具](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )來：
  - 建立的憑證鏈結。 在 GitHub 文章中，請遵循步驟 2。
  - 將憑證儲存為.cer 檔案上傳至您的 IoT Central 應用程式中。
  - 您可以使用 IoT Central 應用程式的驗證程式碼來產生驗證憑證。 在 GitHub 文章中，請遵循步驟 3。
  - 建立分葉憑證，為您為參數，此工具來使用您的裝置識別碼的裝置。 在 GitHub 文章中，請依照步驟 4。

## <a name="connect-without-registering-devices"></a>連接，而不註冊裝置

IoT 中心可讓主要案例就是 oem 大量製造裝置可以連線到 IoT Central 應用程式，且不需先註冊。 製造商必須產生適當的認證，並設定在 factory 中的裝置。 當裝置啟動第一次時，它會自動連接至 IoT Central 應用程式。 IoT 中心操作員必須核准的裝置，才可以 stat 傳送的資料。

下圖概述此流程：

![連線設定](media/concepts-connectivity/device-connection-flow.png)

下列步驟說明此程序的更多詳細資料。 這些步驟稍有不同，取決於您要使用 SAS 或 X.509 憑證進行裝置驗證：

1. 設定您的連線設定：

    - **X.509 憑證：**[新增並驗證根/中繼憑證](#connect-devices-using-x509-certificates)並用它來產生下一個步驟中的裝置憑證。
    - **SAS：** 將複製的主索引鍵。 這個金鑰是 IoT Central 應用程式的群組 SAS 索引鍵。 使用金鑰來產生下一個步驟中的裝置的 SAS 金鑰。
    ![連線設定 SAS](media/concepts-connectivity/connection-settings-sas.png)

1. 產生您的裝置認證
    - **X.509 憑證中：** 產生您使用的根或中繼憑證，您新增至您的 IoT Central 應用程式的裝置之分葉憑證。 請務必使用小寫**裝置識別碼**為分葉憑證的 CNAME。 針對測試用途，使用這[命令列工具](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )來產生裝置憑證。
    - **SAS：** 使用此[命令列工具](https://www.npmjs.com/package/dps-keygen)來產生裝置的 SAS 金鑰。 使用群組**主索引鍵**從上一個步驟。 裝置識別碼必須是小寫。

      若要安裝[金鑰產生器公用程式](https://github.com/Azure/dps-keygen)，執行下列命令：

      ```cmd/sh
      npm i -g dps-keygen
      ```

      若要從群組的 SAS 金鑰產生裝置金鑰，請執行下列命令：

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. 若要設定您的裝置，快閃每個裝置**領域識別碼**，**裝置識別碼**，和**X.509 裝置憑證**或是**SAS 金鑰**。

1. 然後開啟裝置，讓它連接到您的 IoT Central 應用程式。 當您切換裝置上時，它會先連接至 DPS 擷取其 IoT Central 註冊資訊。

1. 連接的裝置一開始顯示成**關聯的裝置**上**Device Explorer**頁面。 裝置佈建狀態為 [已註冊]。 **關聯**裝置，以適當的裝置範本及核准的裝置連線到您的 IoT Central 應用程式。 裝置可以從 IoT 中樞擷取連接字串，並開始傳送資料。 裝置佈建現已完成，現已佈建狀態**已佈建**。

## <a name="provisioning-status"></a>佈建狀態

當實際的裝置連接到您的 IoT Central 應用程式，其佈建狀態變更，如下所示：

1. 裝置佈建狀態是第一個**Registered**。 此狀態表示裝置會在 IoT Central 中建立具有裝置識別碼。 在註冊裝置時：
    - 在新增新的真實裝置**Device Explorer**頁面。
    - 使用加入一組裝置**匯入**上**Device Explorer**頁面。
    - 未以手動方式在註冊裝置**Device Explorer**頁面上，但使用有效的認證連接，並會顯示為**無關聯**上的裝置**Device Explorer**頁面。

1. 裝置佈建狀態會變為**已佈建**連線到您的 IoT Central 應用程式，以有效認證的裝置完成佈建步驟。 在此步驟中，裝置會從 IoT 中樞來擷取連接字串。 裝置現在可以連線到 IoT 中樞，並開始傳送資料。

1. 操作員可以封鎖裝置。 當裝置遭到封鎖時，它無法將資料傳送至您的 IoT Central 應用程式。 已封鎖裝置的佈建狀態為**封鎖**。 它可以繼續傳送資料之前，操作員必須重設裝置。 運算子會解除封鎖的裝置佈建狀態回到先前的值，當**Registered**或是**已佈建**。

## <a name="get-a-connection-string"></a>取得連接字串

下列步驟說明如何取得連接字串，以及在裝置：

1. 選取  **Connect**上**Device Explorer**頁面以取得連線詳細資料：**範圍 ID**，**裝置識別碼**，以及**裝置主索引鍵**:

    ![連線詳細資料](media/concepts-connectivity/device-connect.png)

1. 使用`dps-keygen`命令列公用程式產生連接字串：若要安裝[金鑰產生器公用程式](https://github.com/Azure/dps-keygen)，執行下列命令：

    ```cmd/sh
    npm i -g dps-keygen
    ```

    若要產生的連接字串，請執行下列命令：

    ```cmd/sh
    dps-keygen -di:<device_id> -dk:<device_key> -si:<scope_id>
    ```

## <a name="sdk-support"></a>SDK 支援

Azure 裝置 Sdk 供應項目最簡單的方式為您實作裝置程式碼。 可用的裝置 SDK 如下：

- [Azure IoT SDK for C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK for Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK for Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK for Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK for .NET](https://github.com/azure/azure-iot-sdk-csharp)

每個裝置在連線時，都會使用可識別裝置的唯一連接字串。 裝置只能連接到 IoT 中樞註冊的所在。 當您在 Azure IoT Central 應用程式中建立實際的裝置時，應用程式產生的資訊，您必須建構連接字串使用`dps-keygen`。

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK 的功能和 IoT 中樞連線能力

所有裝置在與 IoT 中樞通訊時，都會使用下列 IoT 中樞連線能力選項：

- [裝置到雲端傳訊](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [裝置對應項](../iot-hub/iot-hub-devguide-device-twins.md)

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

### <a name="protocols"></a>通訊協定

裝置 SDK 支援使用下列網路通訊協定來連線至 IoT 中樞：

- MQTT
- AMQP
- HTTPS

如需這些差異通訊協定和其選擇方針的資訊，請參閱[選擇通訊協定](../iot-hub/iot-hub-devguide-protocols.md)。

如果裝置無法使用任何所支援的通訊協定，則可以使用 Azure IoT Edge 來轉換通訊協定。 IoT Edge 支援其他 intelligence-on-the-edge 案例，可從 Azure IoT Central 應用程式將處理卸載至邊緣。

## <a name="security"></a>安全性

在裝置與 Azure IoT Central 之間交換的資料會都加密。 IoT 中樞會對每個要從裝置連線至任何裝置面向 IoT 中樞端點的要求進行驗證。 為了避免透過網路交換認證，裝置會使用已簽署的權杖來進行驗證。 如需詳細資訊，請參閱[控制 IoT 中樞的存取權](../iot-hub/iot-hub-devguide-security.md)。

## <a name="next-steps"></a>後續步驟

既然您已了解在 Azure IoT Central 的裝置連線能力，以下是建議的後續步驟：

- [準備及連線 DevKit 裝置](howto-connect-devkit.md)
- [準備及連線 Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [將一般 Node.js 用戶端連線至 Azure IoT Central 應用程式](howto-connect-nodejs.md)
- [C SDK:佈建的裝置用戶端 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
