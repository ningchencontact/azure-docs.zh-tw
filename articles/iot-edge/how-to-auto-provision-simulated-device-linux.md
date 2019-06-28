---
title: 自動佈建 Linux DPS-Azure IoT Edge 裝置 |Microsoft Docs
description: 在 Linux VM 上使用模擬的 TPM 來測試 Azure IoT Edge 的 Azure 裝置佈建服務
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f75ad287b5f026dea7ba0152b009a60572929148
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329634"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>建立及佈建 Linux 虛擬機器上的虛擬 TPM 的 IoT Edge 裝置

Azure IoT Edge 裝置可以自動佈建[裝置佈建服務](../iot-dps/index.yml)。 如果您不熟悉自動佈建程序，請先檢閱[自動佈建概念](../iot-dps/concepts-auto-provisioning.md)，再繼續作業。 

本文說明如何測試自動佈建在模擬的 IoT Edge 裝置，執行下列步驟： 

* 使用保護硬體的模擬信任平台模組 (TPM)，在 Hyper-V 中建立 Linux 虛擬機器 (VM)。
* 建立 IoT 中樞裝置佈建服務 (DPS) 的執行個體。
* 建立裝置的個別註冊
* 安裝 IoT Edge 執行階段，並將裝置連線至 IoT 中樞

本文中的步驟係供測試之用。

## <a name="prerequisites"></a>必要條件

* [已啟用 Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) 的 Windows 開發機器。 本文使用執行 Ubuntu Server VM 的 Windows 10。 
* 使用中的 IoT 中樞。 

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>使用虛擬 TPM 建立 Linux 虛擬機器

在本節中，您可以建立新的 Linux 虛擬機器在 HYPER-V 上。 好讓您可以使用它來自動佈建運作方式測試 IoT Edge，您可以設定使用模擬的 TPM 的這部虛擬機器。 

### <a name="create-a-virtual-switch"></a>建立虛擬交換器

虛擬交換器可讓您的虛擬機器連線至實體網路。

1. 在 Windows 電腦上開啟 HYPER-V 管理員。 

2. 在 [動作]  功能表中，選取 [虛擬交換器管理員]  。 

3. 選擇 [外部]  虛擬交換器，然後選取 [建立虛擬交換器]  。 

4. 為新的虛擬交換器指定名稱，例如 **EdgeSwitch**。 確定連線類型設定為 [外部網路]  ，然後選取 [確定]  。

5. 快顯視窗會警告您網路連線可能會中斷。 選取 [是]  以繼續進行。 

如果您在建立新的虛擬交換器時看到錯誤，請確定沒有其他交換器正在使用乙太網路配接器，且沒有其他參數使用相同的名稱。 

### <a name="create-virtual-machine"></a>建立虛擬機器

1. 下載要用於虛擬機器的磁碟映像檔，並將其儲存於本機。 例如 [Ubuntu Server](https://www.ubuntu.com/download/server)。 

2. 同樣地，選取 HYPER-V 管理員中**的新** > **虛擬機器**中**動作**功能表。

3. 使用下列特定組態完成 [新增虛擬機器精靈]  ：

   1. **指定世代**：選取 [第 2 代]  。 第 2 代虛擬機器具有巢狀虛擬化已啟用，這是必要的虛擬機器上執行 IoT Edge。
   2. **設定網路功能**：將 [連線]  的值設定為您在上一節中建立的虛擬交換器。 
   3. **安裝選項**：選取 [從可開機映像檔安裝作業系統]  ，然後瀏覽至您儲存在本機的磁碟映像檔。

4. 選取 [**完成**來建立虛擬機器精靈] 中。

系統可能需要幾分鐘的時間來建立新的 VM。 

### <a name="enable-virtual-tpm"></a>啟用虛擬 TPM

您的 VM 建立之後，開啟其設定，以啟用虛擬可信賴平台模組 (TPM)，可讓您 autoprovision 裝置。 

1. 選取虛擬機器，然後開啟它**設定**。

2. 瀏覽至 [安全性]  。 

3. 取消核取 [啟用安全開機]  。

4. 核取 [啟用信賴平台模組]  。 

5. 按一下 [確定]  。  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>啟動虛擬機器，並收集 TPM 資料

在虛擬機器中，建立可用來擷取裝置的 [註冊識別碼]  和 [簽署金鑰]  的 C SDK 工具。 

1. 啟動虛擬機器，並連接到它。

2. 依照提示完成安裝程序，然後重新啟動電腦的虛擬機器內。 

3. 登入您的 VM，然後依照[設定 Linux 開發環境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)安裝，並建置用於 c 的 Azure IoT 裝置 SDK 

   >[!TIP]
   >期間本文中，您將其複製到並貼上從虛擬機器，這並不容易透過 HYPER-V 管理員連接應用程式。 您可能想要連線到透過 HYPER-V 管理員一次來擷取其 IP 位址的虛擬機器： `ifconfig`。 然後，您可以使用的 IP 位址，透過 SSH 連線： `ssh <username>@<ipaddress>`。

4. 執行下列命令，建置用來擷取裝置佈建資訊的 C SDK 工具。 

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```
   >[!TIP]
   >如果您以 TPM 模擬器進行測試，您必須將額外的參數`-Duse_tpm_simulator:BOOL=ON`加以啟用。 完整的命令將會是`cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON .. `。

5. 複製 [註冊識別碼]  和 [簽署金鑰]  的值。 您可以使用這些值為 DPS 中的裝置建立個別的註冊。 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>設定 IoT 中樞裝置佈建服務

在 Azure 中建立 IoT 中樞裝置佈建服務的新執行個體，並將其連結至您的 IoT 中樞。 您可以依照[設定 IoT 中樞 DPS](../iot-dps/quick-setup-auto-provision.md) 中的指示操作。

裝置佈建服務開始執行之後，請從 [概觀] 頁面複製 [識別碼範圍]  的值。 當您設定 IoT Edge 執行階段時會用到此值。 

## <a name="create-a-dps-enrollment"></a>建立 DPS 註冊

從您的虛擬機器擷取佈建資訊，並使用該資訊在裝置佈建服務中建立個別的註冊。 

在 DPS 中建立註冊時，您就有機會宣告**初始裝置對應項狀態**。 在裝置對應項中，您可以根據解決方案中需要的任何計量 (例如區域、環境、位置或裝置類型) 來設定標記，進而將裝置分組。 這些標記會用來建立[自動部署](how-to-deploy-monitor.md)。 


1. 在 [Azure 入口網站](https://portal.azure.com) 中，瀏覽至 IoT 中樞裝置佈建服務的執行個體。 

2. 在 [設定]  下方，選取 [管理註冊]  。 

3. 選取 [新增個別註冊]  ，然後完成下列步驟以設定註冊：  

   1. 針對 [機制]  ，選取 [TPM]  。 
   
   2. 提供**簽署金鑰**並**註冊識別碼**您複製從您的虛擬機器。
   
   3. 選取  **，則為 True**宣告此虛擬機器是 IoT Edge 裝置。 
   
   4. 選擇您的裝置所要連接的連結 **IoT 中樞**。 您可以選擇多個中樞，並將裝置指派給其中一個，根據所選的配置原則。 
   
   5. 視需要提供裝置的識別碼。 您可以使用裝置識別碼，將個別裝置設為模組部署的目標。 如果您未提供裝置識別碼，則會使用註冊識別碼。
   
   6. 視需要將標記值新增至 [初始裝置對應項狀態]  。 您可以使用標記將裝置群組設定為模組部署的目標。 例如: 

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. 選取 [ **儲存**]。 

現在，註冊此裝置，IoT Edge 執行階段可以自動佈建裝置在安裝期間。 

## <a name="install-the-iot-edge-runtime"></a>安裝 IoT Edge 執行階段

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 其元件會在容器中執行，並可讓您將其他容器部署到裝置，以便您在 Edge 上執行程式碼。 在虛擬機器上安裝 IoT Edge 執行階段。 

開始閱讀您的裝置類型適用的文章之前，請先了解您的 DPS [識別碼範圍]  和裝置的 [註冊識別碼]  。 如果您安裝了範例 Ubuntu Server，請使用 **x64** 指示。 請務必將 IoT Edge 執行階段設定為自動佈建，而不是手動佈建。 

* [在 Linux (x64) 上安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-linux.md)
* [在 Linux (ARM32v7/armhf) 上安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-linux-arm.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>為 IoT Edge 指定對 TPM 的存取權

若要讓 IoT Edge 執行階段自動佈建您的裝置，該執行階段必須能夠存取 TPM。 

您可以覆寫 systemd 設定，授予對 IoT Edge 執行階段的 TPM 存取權，讓 **iotedge** 服務有根權限。 如果不想提高服務權限，也可以使用下列步驟，手動提供 TPM 存取權。 

1. 尋找 TPM 硬體模組在您裝置上的檔案路徑，並將其儲存為本機變數。 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. 建立讓 IoT Edge 執行階段能夠存取 tpm0 的新規則。 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. 開啟規則檔案。 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. 將下列存取資訊複製到規則檔案中。 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. 儲存並結束檔案。 

6. 觸發程序 udev 系統以評估新規則。 

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. 確認規則已成功套用。

   ```bash
   ls -l /dev/tpm0
   ```

   成功的輸出顯示如下：

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   如果未發現正確權限套用完成，請嘗試重新啟動電腦，以重新整理 udev。 

8. 開啟 IoT Edge 執行階段複寫檔案。 

   ```bash
   sudo systemctl edit iotedge.service
   ```

9. 新增下列程式碼，以建立 TPM 環境變數。

   ```input
   [Service]
   Environment=IOTEDGE_USE_TPM_DEVICE=ON
   ```

10. 儲存並結束檔案。

11. 確認覆寫成功。

    ```bash
    sudo systemctl cat iotedge.service
    ```

    成功的輸出會顯示 **iotedge** 預設服務變數，然後顯示您在 **override.conf** 中設定的環境變數。 

12. 重新載入設定。

    ```bash
    sudo systemctl daemon-reload
    ```

## <a name="restart-the-iot-edge-runtime"></a>重新啟動 IoT Edge 執行階段

重新啟動 IoT Edge 執行階段，使其可取得您對裝置所做的所有組態變更。 

   ```bash
   sudo systemctl restart iotedge
   ```

確認 IoT Edge 執行階段正在執行。 

   ```bash
   sudo systemctl status iotedge
   ```

如果您看到佈建錯誤，可能是因為組態變更尚未生效。 請嘗試重新啟動 IoT Edge 精靈。 

   ```bash
   sudo systemctl daemon-reload
   ```
   
或者，請嘗試重新啟動虛擬機器，以確認變更是否會在重新啟動後生效。 

## <a name="verify-successful-installation"></a>確認安裝成功

如果執行階段順利啟動，您可以移至您的 IoT 中樞，並看到新裝置已自動佈建。 現在您的裝置已準備好執行 IoT Edge 模組。 

檢查 IoT Edge 精靈的狀態。

```cmd/sh
systemctl status iotedge
```

檢查精靈記錄。

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

列出執行中的模組。

```cmd/sh
iotedge list
```

您可以確認已使用該裝置佈建服務中建立個別註冊。 瀏覽至您的裝置佈建服務執行個體，在 Azure 入口網站中。 開啟您建立個別註冊的註冊詳細資料。 請注意，註冊的狀態是**指派**和裝置識別碼會列。 

## <a name="next-steps"></a>後續步驟

佈建新裝置時，裝置佈建服務註冊程序可讓您同時設定裝置識別碼和裝置對應項標記。 您可以使用這些值來鎖定要使用自動裝置管理的個別裝置或裝置群組。 了解如何[使用 Azure 入口網站大規模部署和監視 IoT Edge 模組](how-to-deploy-monitor.md)，或[使用 Azure CLI](how-to-deploy-monitor-cli.md) 執行相同作業。
