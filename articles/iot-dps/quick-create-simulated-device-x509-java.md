---
title: "使用 Java 將模擬 X.509 裝置佈建到 Azure IoT 中樞 | Microsoft Docs"
description: "Azure 快速入門 - 使用適用於 IoT 中樞裝置佈建服務的 Java 裝置 SDK 來建立及佈建模擬 X.509 裝置"
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 7e4ad361df8a37d4a82c1bc50c6fb134a1ad5159
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>使用適用於 IoT 中樞裝置佈建服務的 Java 裝置 SDK 來建立及佈建模擬 X.509 裝置
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

這些步驟顯示如何在執行 Windows OS 的開發電腦上模擬 X.509 裝置，並使用程式碼範例來連線此模擬裝置與裝置佈建服務和 IoT 中樞。 

繼續之前，請務必完成[使用 Azure 入口網站設定 IoT 中樞裝置佈建服務](./quick-setup-auto-provision.md)中的步驟。


## <a name="prepare-the-environment"></a>準備環境 

1. 請確定您已在電腦上安裝 [Java SE 開發套件 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

1. 下載並安裝 [Maven](https://maven.apache.org/install.html)。

1. 確定 `git` 已安裝在電腦上，並已新增至命令視窗可存取的環境變數。 請參閱[軟體自由保護協會的 Git 用戶端工具](https://git-scm.com/download/)以取得所要安裝的最新版 `git` 工具，其中包括 **Git Bash** (您可用來與本機 Git 存放庫互動的命令列應用程式)。 

1. 開啟命令提示字元。 複製裝置模擬程式碼範例的 GitHub 存放庫：
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. 瀏覽至憑證產生器專案並建置專案。 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

1. 瀏覽至目標資料夾並執行建立的 jar 檔案。

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

1. 根據您的設定，使用下列其中一個方法來建立註冊資訊：

    - **個別註冊**：

        1. 針對_您是否要輸入一般名稱_，輸入 **N**。 從 ---BEGIN CERTIFICATE--- 到 *-----END CERTIFICATE-----* 結尾，將 `Client Cert` 的輸出複製到剪貼簿。

            ![個別憑證產生器](./media/java-quick-create-simulated-device-x509/individual.png)

        1. 在 Windows 電腦上建立名為 _X509individual.pem_ 的檔案，在您選擇的編輯器中開啟該檔案，並將剪貼簿內容複製到這個檔案。 儲存檔案。

        1. 針對_您是否要輸入驗證碼_，輸入 **N**，並保持程式輸出開啟，以供稍後在快速入門中參考。 請注意_用戶端憑證_和_用戶端憑證私密金鑰_的值。
    
    - **註冊群組**：

        1. 針對_您是否要輸入一般名稱_，輸入 **N**。 從 ---BEGIN CERTIFICATE--- 到 *-----END CERTIFICATE-----* 結尾，將 `Root Cert` 的輸出複製到剪貼簿。

            ![群組憑證產生器](./media/java-quick-create-simulated-device-x509/group.png)

        1. 在 Windows 電腦上建立名為 X509group.pem 的檔案，在您選擇的編輯器中開啟該檔案，並將剪貼簿內容複製到這個檔案。 儲存檔案。

        1. 針對_您是否要輸入驗證碼_，輸入 ，並保持程式開啟，以供稍後在快速入門中使用。 請注意_用戶端憑證_、_用戶端憑證私密金鑰_、_簽章者憑證_和_根憑證_的值。

        > [!NOTE]
        > 上述 `Root Cert` 只適用於在主控台輸出中建立的憑證，而不能用來簽署其他用戶端憑證。 如果您需要一組更強固的測試憑證，請參閱[管理 CA 憑證範例](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)。
        >

## <a name="create-a-device-enrollment-entry"></a>建立裝置註冊項目

1. 登入 Azure 入口網站，按一下左側功能表上的 [所有資源] 按鈕，然後開啟您的佈建服務。

1. 根據您的設定，使用下列其中一個方法來輸入註冊資訊：

    - **個別註冊**： 

        1. 在裝置佈建服務摘要刀鋒視窗上，選取 [管理註冊]。 選取 [個別註冊] 索引標籤，然後按一下頂端的 [新增] 按鈕。 

        1. 在 [新增註冊清單項目] 之下，輸入下列資訊：
            - 選取 [X.509] 作為身分識別證明「機制」。
            - 在 .pem 或 .cer 憑證檔案 之下，選取在先前步驟中使用 [檔案總管] widget 建立的憑證檔案 _X509individual.pem_。
            - 您可以選擇性地提供下列資訊：
                - 選取與您的佈建服務連結的 IoT 中樞。
                - 輸入唯一的裝置識別碼。 替您的裝置命名時，務必避免使用敏感性資料。 
                - 使用裝置所需的初始組態更新**初始裝置對應項狀態**。
            - 完成後，按一下 [儲存] 按鈕。 

        ![在入口網站刀鋒視窗中輸入 X.509 裝置註冊資訊](./media/java-quick-create-simulated-device-x509/enter-device-enrollment.png)  

       註冊成功時，您的 X.509 裝置會在 [個別註冊] 索引標籤之下的 [註冊識別碼] 資料行中顯示為 **microsoftriotcore**。 

    - **註冊群組**： 

        1. 在 [裝置佈建服務摘要] 刀鋒視窗中，選取 [憑證]，然後按一下頂端的 [新增] 按鈕。

        1. 在 [新增憑證] 之下，輸入下列資訊：
            - 請輸入唯一的憑證名稱。
            - 選取您先前建立的 _X509group.pem_ 檔案。
            - 完成後，按一下 [儲存] 按鈕。

        ![新增憑證](./media/java-quick-create-simulated-device-x509/add-certificate.png)

        1. 選取新建立的憑證：
            - 按一下 [產生驗證碼]。 複製已產生的程式碼。
            - 輸入_驗證碼_，或以滑鼠右鍵按一下，在您執行中的 _provisioning-x509-cert-generator_ 視窗貼上。  按 **Enter** 鍵。
            - 從 ---BEGIN CERTIFICATE--- 到 *-----END CERTIFICATE-----* 結尾，將 `Verification Cert` 的輸出複製到剪貼簿。
            
                ![驗證產生器](./media/java-quick-create-simulated-device-x509/validation-generator.png)

            - 在 Windows 電腦上建立名為 X509validation.pem 的檔案，在您選擇的編輯器中開啟該檔案，並將剪貼簿內容複製到這個檔案。 儲存檔案。
            - 在 Azure 入口網站中，選取 _X509validation.pem_ 檔案。 按一下 [確認]。

            ![驗證憑證](./media/java-quick-create-simulated-device-x509/validate-certificate.png)

        1. 選取 [管理註冊]。 選取 [註冊群組] 索引標籤，然後按一下頂端的 [新增] 按鈕。
            - 輸入唯一的群組名稱。
            - 選取先前建立的唯一憑證名稱
            - 您可以選擇性地提供下列資訊：
                - 選取與您的佈建服務連結的 IoT 中樞。
                - 使用裝置所需的初始組態更新**初始裝置對應項狀態**。

        ![在入口網站刀鋒視窗中輸入 X.509 群組註冊資訊](./media/java-quick-create-simulated-device-x509/enter-group-enrollment.png)

        成功註冊之後，您的 X.509 裝置群組會顯示在 [註冊群組] 索引標籤的 [群組名稱] 資料行之下。


## <a name="simulate-the-device"></a>模擬裝置

1. 在 [裝置佈建服務摘要] 刀鋒視窗中，選取 [概觀]，並記下您的_識別碼範圍_和_佈建服務全域端點_。

    ![服務資訊](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

1. 開啟命令提示字元。 瀏覽至範例專案資料夾。

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. 根據您的設定，使用下列其中一個方法來輸入註冊資訊：

    - **個別註冊**： 

        1. 編輯 `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`如先前所述，包含您的_識別碼範圍_和_佈建服務全域端點_。 另外如先前所述，也要包含_用戶端憑證_和_用戶端憑證私密金鑰_。

            ```java
            private static final String idScope = "[Your ID scope here]";
            private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
            private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
            private static final String leafPublicPem = "<Your Public PEM Certificate here>";
            private static final String leafPrivateKey = "<Your Private PEM Key here>";
            ```

            - 您可以使用下列格式來包含憑證及金鑰：
            
                ```java
                private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXX\n" +
                    "-----END PRIVATE KEY-----\n";
                ```

    - **註冊群組**： 

        1. 請依照上述的**個別註冊**指示。

        1. 將下列程式碼行新增至 `main` 函式的開頭。
        
            ```java
            String intermediatePem = "<Your Signer Certificate here>";          
            String rootPem = "<Your Root Certificate here>";
                
            signerCertificates.add(intermediatePem);
            signerCertificates.add(rootPem);
            ```
    
            - 您可以使用下列格式來包含您的憑證：
        
                ```java
                String intermediatePem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                String rootPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                ```

1. 建置範例。 瀏覽至目標資料夾並執行建立的 jar 檔案。

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

1. 在入口網站中，瀏覽到連結至您的佈建服務的 IoT 中樞，並開啟 [Device Explorer] 刀鋒視窗。 X.509 模擬裝置成功佈建到 IoT 中樞時，其裝置識別碼會出現在 [Device Explorer] 刀鋒視窗上，且 [狀態] 顯示為 [已啟用]。 請注意，如果您在執行範例裝置應用程式之前開啟了刀鋒視窗，您可能需要按一下頂端的 [重新整理] 按鈕。 

    ![已向 IoT 中樞註冊裝置](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> 如果您在裝置的註冊項目中變更了*初始裝置對應項狀態* (變更自預設值)，它即可從中樞提取所需的對應項狀態並採取適當的動作。 如需詳細資訊，請參閱[了解和使用 Azure IoT 中樞的裝置對應項](../iot-hub/iot-hub-devguide-device-twins.md)。
>


## <a name="clean-up-resources"></a>清除資源

如果您打算繼續使用並探索裝置用戶端範例，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用下列步驟來刪除本快速入門建立的所有資源。

1. 在您的電腦上關閉裝置用戶端範例輸出視窗。
1. 從 Azure 入口網站的左側功能表中，按一下 [所有資源]，然後選取您的裝置佈建服務。 開啟您服務的 [管理註冊] 刀鋒視窗，然後按一下 [個別註冊] 索引標籤。選取您在本快速入門中註冊的裝置之 [註冊識別碼]，然後按一下頂端的 [刪除] 按鈕。 
1. 從 Azure 入口網站的左側功能表中，按一下 [所有資源]，然後選取您的 IoT 中樞。 開啟您中樞的 [IoT 裝置] 刀鋒視窗，選取您在本快速入門中註冊之裝置的 [裝置識別碼]，然後按一下頂端的 [刪除] 按鈕。


## <a name="next-steps"></a>後續步驟

在本快速入門中，您已在 Windows 電腦上建立 X.509 模擬裝置，並使用入口網站上的 Azure IoT 中樞裝置佈建服務，將它佈建到 IoT 中樞。 若要了解如何以程式設計方式註冊您的 X.509 裝置，請繼續閱讀以程式設計方式註冊 X.509 裝置的快速入門。 

> [!div class="nextstepaction"]
> [Azure 快速入門 - 向 Azure IoT 中樞裝置佈建服務註冊 X.509 裝置](quick-enroll-device-x509-java.md)
