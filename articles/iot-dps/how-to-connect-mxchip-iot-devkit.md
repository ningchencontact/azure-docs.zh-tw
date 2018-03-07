---
title: "如何使用 MXChip IoT DevKit 來連線至 Azure IoT 中樞裝置佈建服務 | Microsoft Docs"
description: "如何使用 MXChip IoT DevKit 來連線至 Azure IoT 中樞裝置佈建服務"
services: iot-dps
keywords: 
author: liydu
ms.author: liydu
ms.date: 02/20/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 77c8a6a5e384d27dca2582cc0fedc2bd23c0592e
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-hub-device-provisioning-service"></a>將 MXChip IoT DevKit 連線至 Azure IoT 中樞裝置佈建服務

本文說明如何使用「裝置佈建服務」來設定 DevKit 以讓它自動向「IoT 中樞」註冊。 在本教學課程中，您將了解如何：

* 設定裝置上 DPS 的全域端點
* 使用唯一裝置祕密 (UDS) 來產生 X.509 憑證
* 註冊個別裝置
* 確認裝置已註冊

[MXChip IoT DevKit](https://aka.ms/iot-devkit) 是全方位 Arduino 相容板，具有豐富的週邊設備和感應器。 您可以使用[適用於 Arduino 的 Visual Studio Code 延伸模組](https://aka.ms/arduino)來針對它進行開發。 它隨附一個不斷成長的[專案目錄](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)，可引導您設計運用 Microsoft Azure 服務的「物聯網」(IoT) 解決方案原型。

## <a name="before-you-begin"></a>開始之前

若要完成本教學課程中的步驟，您需要下列項目：

* 使用[使用者入門指南](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)來準備您的 DevKit。
* 使用[韌體升級](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) \(英文\) 教學課程將韌體升級至最新版 (1.3.0 版或更新版)。
* 使用[設定自動佈建](https://docs.microsoft.com/en-us/azure/iot-dps/quick-setup-auto-provision)來建立「IoT 中樞」並與「裝置佈建服務」執行個體連結。

## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>在裝置上設定裝置佈建服務組態

讓 DevKit 連接至您所建立的「裝置佈建服務服務」執行個體：

1. 在 Azure 入口網站中，選取「裝置佈建服務」的 [概觀]，並記下 [全域裝置端點] 和 [識別碼範圍] 值。
  ![DPS 全域端點和識別碼範圍](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. 確定 `git` 已安裝在電腦上，並已新增至命令視窗可存取的環境變數。 請參閱[軟體自由保護組織 (Software Freedom Conservancy) 的 Git 用戶端工具](https://git-scm.com/download/) \(英文\) 來安裝最新版本。

3. 開啟命令提示字元。 複製 DPS 範例程式碼的 GitHub 存放庫：
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. 啟動 VS Code 並將 DevKit 連接至電腦，開啟包含所複製之程式碼的資料夾。

5. 開啟 [DevKitDPS.ino]，使用您剛才記下的值來尋找並取代 `[Global Device Endpoint]` 和 `[ID Scope]`。
  ![DPS 端點](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) 您可以將 **registrationId** 保留空白，應用程式會根據 MAC 位址和韌體版本為您產生一個值。 如果您想要自訂此值，則「註冊識別碼」必須只能使用英數字元、小寫及連字號的組合，且長度上限為 128 個字元。 如需詳細資訊，請參閱[透過 Azure 入口網站管理裝置註冊](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-manage-enrollments)。

6. 在 VS Code 中使用 **Quick Open** (Windows：`Ctrl+P`macOS：`Cmd+P`)，然後輸入 **task device-upload** 來組建程式碼並上傳到 DevKit。

7. 觀察輸出視窗中工作的成功過程。

## <a name="save-unique-device-secret-on-stsafe-security-chip"></a>將唯一裝置祕密儲存在 STSAFE 安全晶片上

您可以根據裝置的[硬體安全模組 (HSM)](https://azure.microsoft.com/en-us/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) 在裝置上設定「裝置佈建服務」。 DevKit 會使用來自 [Trusted Computing Group (TCG)](https://trustedcomputinggroup.org)的[裝置身分識別組成引擎 (Device Identity Composition Engine, DICE)](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf)。 儲存在 DevKit 上 STSAFE 安全晶片中的**唯一裝置祕密 (UDS)**會用來產生裝置唯一 [X.509](https://docs.microsoft.com/en-us/azure/iot-dps/tutorial-set-up-device#select-a-hardware-security-module) 憑證。 此憑證可在稍後用於「裝置佈建服務」中的註冊程序。

一般的**唯一裝置祕密 (UDS)** 是一個長度為 64 個字元的字串。 範例 UDS 如下：

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

每兩個字元會用來作為安全性計算中的十六進位值。 因此上述範例 UDS 會解析成：「`0x19`、`0xe2`、`0x5a`、`0x25`、`0x9d`、`0x0c`、`0x2b`、`0xe0`、`0x3a`、`0x02`、`0xd4`、`0x16`、`0xc0`、`0x5c`、`0x48`、`0xcc`、`0xd0`、`0xcc`、`0x7d`、`0x17`、`0x43`、`0x45`、`0x8a`、`0xae`、`0x1c`、`0xb4`、`0x88`、`0xb0`、`0x74`、`0x99`、`0x3e`、`0xae`」。

將「唯一裝置祕密」儲存在 DevKit 上：

1. 使用 Putty 之類的工具來開啟序列監視器，如需詳細資訊，請參閱[使用設定模式](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) \(英文\)。

2. 在 DevKit 已連接至電腦的情況下，按住 [A] 按鈕，然後按下再放開 [Reset] \(重設\) 按鈕以進入設定模式。 畫面應該會顯示 DevKit 識別碼和 **'Configuration'** \(設定\)。

3. 使用上面的長範例 UDS 字串，然後將一或多個字元變更為其他介於 `0` 和 `f` 之間的值。 這會用來作為您自己的 UDS。

4. 在序列監視器視窗中，輸入 **set_dps_uds [your_own_uds_value]**，然後按 Enter 鍵來儲存它。
  > [!NOTE]
  > 例如，如果您是透過將最後兩個字元變更為 `f` 來設定自己的 UDS，就必須輸入類似 **set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff**的命令。

5. 在不關閉序列監視器視窗的情況下，按下 DevKit 上的 [Reset] \(重設\) 按鈕。

6. 記下 [DevKit MAC Address] \(DevKit MAC 位址\) 和 [DevKit Firmware Version] \(DevKit 韌體版本\) 的值。
  ![韌體版本](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-x509-certificate"></a>產生 X.509 憑證

### <a name="windows"></a>Windows

1. 開啟檔案總管，然後移至包含您所複製之 DSP 範例程式碼的資料夾，當中有一個 [.build] 資料夾，請尋找並複製其中的 **DPS.ino.bin** 和 **DPS.ino.map**。
  ![產生的檔案](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > 如果您已將 Arduino 的 `built.path` 設定變更至其他資料夾。 您必須在所設定的資料夾中尋找這些檔案。

2. 將這兩個檔案貼到與 [.build] 資料夾同層的 [tools] 資料夾中。

3. 執行 **dps_cert_gen.exe**，依照提示輸入您的 **UDS**DevKit 的 **MAC 位址**及**韌體版本**來產生 X.509 憑證。
  ![執行 dps-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. 觀察產生作業的成功過程，**.pem** 憑證會儲存在相同的資料夾中。

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>在裝置佈建服務中建立裝置註冊項目

1. 在 Azure 入口網站中，瀏覽至您的佈建服務。 按一下 [管理註冊]，然後選取 [個別註冊] 索引標籤。![個別註冊](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. 按一下 [新增] 。

3. 在 [機制]中，選擇 [X.509]。
  ![上傳憑證](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

4. 在 [.pem 或 .cer 憑證檔案] 中，上傳您剛取得的 **.pem** 憑證。

5. 將其他項目保留為預設值，然後按一下 [儲存]。

## <a name="start-the-devkit"></a>啟動 DevKit

1. 啟動 VS Code 並開啟序列監視器。

2. 按下 DevKit 上的 [Reset] \(重設\) 按鈕。

您應該會看到 DevKit 開始向「裝置佈建服務」註冊。

![VS Code 輸出](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-the-devkit-is-registered-on-iot-hub"></a>確認 DevKit 已在 IoT 中樞上註冊

在您的裝置開機後，應該會發生下列動作：

1. 裝置會向「裝置佈建服務」傳送註冊要求。
2. 「裝置佈建服務」會回傳註冊挑戰供您的裝置回應。
3. 註冊成功後，裝置佈建服務會對裝置回傳 IoT 中樞 URI、裝置識別碼和加密金鑰。
4. 裝置上的 IoT 中樞用戶端應用程式接著會連線到您的中樞。
5. 成功連線至中樞後，您應該會看到裝置出現在 IoT 中樞的 Device Explorer 中。
  ![裝置已註冊](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-device-id"></a>變更裝置識別碼

在「Azure IoT 中樞」中註冊的預設裝置識別碼是 **AZ3166**。 如果您想要修改它，請依照[這裡的指示](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/)(英文\) 進行操作。

## <a name="problems-and-feedback"></a>問題與意見反應

如果您遇到問題，請參閱[常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)或透過下列管道與我們連絡：

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>後續步驟

現在您已了解如何準備 DevKit 以使用 DICE 向 DPS 安全地註冊裝置，讓它能夠自動向「IoT 中樞」進行註冊而無須任何人為操作。 在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 設定裝置上 DPS 的全域端點
> * 使用唯一裝置祕密 (UDS) 來產生 X.509 憑證
> * 註冊個別裝置
> * 確認裝置已註冊

請前進到其他教學課程來了解如何：

> [!div class="nextstepaction"]
> [建立及佈建模擬裝置](./quick-create-simulated-device.md)

