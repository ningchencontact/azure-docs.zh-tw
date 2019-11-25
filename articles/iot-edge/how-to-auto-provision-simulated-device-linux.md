---
title: Automatically provision Linux devices with DPS - Azure IoT Edge | Microsoft Docs
description: 在 Linux VM 上使用模擬的 TPM 來測試 Azure IoT Edge 的 Azure 裝置佈建服務
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 292ae570d4f2ddd0c09e667860ee2ba01b9fc6b8
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457178"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Create and provision an IoT Edge device with a virtual TPM on a Linux virtual machine

Azure IoT Edge devices can be automatically provisioned using the [Device Provisioning Service](../iot-dps/index.yml). 如果您不熟悉自動佈建程序，請先檢閱[自動佈建概念](../iot-dps/concepts-auto-provisioning.md)，再繼續作業。

This article shows you how to test auto-provisioning on a simulated IoT Edge device with the following steps:

* 使用保護硬體的模擬信任平台模組 (TPM)，在 Hyper-V 中建立 Linux 虛擬機器 (VM)。
* 建立 IoT 中樞裝置佈建服務 (DPS) 的執行個體。
* 建立裝置的個別註冊
* 安裝 IoT Edge 執行階段，並將裝置連線至 IoT 中樞

> [!NOTE]
> TPM 2.0 is required when using TPM attestation with DPS and can only be used to create individual, not group, enrollments.

> [!TIP]
> This article describes how to test DPS provisioning using a TPM simulator, but much of it applies to physical TPM hardware such as the [Infineon OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), an Azure Certified for IoT device.
>
> If you're using a physical device, you can skip ahead to the [Retrieve provisioning information from a physical device](#retrieve-provisioning-information-from-a-physical-device) section in this article.

## <a name="prerequisites"></a>必要條件

* [已啟用 Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) 的 Windows 開發機器。 本文使用執行 Ubuntu Server VM 的 Windows 10。
* 使用中的 IoT 中樞。
* If using a simulated TPM, [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 or later with the ['Desktop development with C++'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) workload enabled.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>使用虛擬 TPM 建立 Linux 虛擬機器

In this section, you create a new Linux virtual machine on Hyper-V. You configured this virtual machine with a simulated TPM so that you can use it for testing how automatic provisioning works with IoT Edge. 

### <a name="create-a-virtual-switch"></a>建立虛擬交換器

虛擬交換器可讓您的虛擬機器連線至實體網路。

1. Open Hyper-V Manager on your Windows machine. 

2. 在 [動作] 功能表中，選取 [虛擬交換器管理員]。 

3. 選擇 [外部] 虛擬交換器，然後選取 [建立虛擬交換器]。 

4. 為新的虛擬交換器指定名稱，例如 **EdgeSwitch**。 確定連線類型設定為 [外部網路]，然後選取 [確定]。

5. 快顯視窗會警告您網路連線可能會中斷。 選取 [是] 以繼續進行。 

如果您在建立新的虛擬交換器時看到錯誤，請確定沒有其他交換器正在使用乙太網路配接器，且沒有其他參數使用相同的名稱。 

### <a name="create-virtual-machine"></a>建立虛擬機器

1. 下載要用於虛擬機器的磁碟映像檔，並將其儲存於本機。 例如 [Ubuntu Server](https://www.ubuntu.com/download/server)。 

2. In Hyper-V Manager again, select **New** > **Virtual Machine** in the **Actions** menu.

3. 使用下列特定組態完成 [新增虛擬機器精靈]：

   1. **指定世代**：選取 [第 2 代]。 Generation 2 virtual machines have nested virtualization enabled, which is required to run IoT Edge on a virtual machine.
   2. **設定網路功能**：將 [連線] 的值設定為您在上一節中建立的虛擬交換器。 
   3. **安裝選項**：選取 [從可開機映像檔安裝作業系統]，並瀏覽至您在本機儲存的磁碟映像檔。

4. Select **Finish** in the wizard to create the virtual machine.

系統可能需要幾分鐘的時間來建立新的 VM。 

### <a name="enable-virtual-tpm"></a>啟用虛擬 TPM

Once your VM is created, open its settings to enable the virtual trusted platform module (TPM) that lets you auto-provision the device.

1. Select the virtual machine, then open its **Settings**.

2. 瀏覽至 [安全性]。 

3. 取消核取 [啟用安全開機]。

4. 核取 [啟用信賴平台模組]。 

5. 按一下 [確定]。  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>啟動虛擬機器，並收集 TPM 資料

In the virtual machine, build a tool that you can use to retrieve the device's **Registration ID** and **Endorsement key**.

1. Start your virtual machine and connect to it.

1. Follow the prompts within the virtual machine to finish the installation process and reboot the machine.

1. Sign in to your VM, then follow the steps in [Set up a Linux development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) to install and build the Azure IoT device SDK for C.

   >[!TIP]
   >In the course of this article, you'll copy to and paste from the virtual machine, which is not easy through the Hyper-V Manager connection application. You may want to connect to the virtual machine through Hyper-V Manager once to retrieve its IP address: `ifconfig`. Then, you can use the IP address to connect through SSH: `ssh <username>@<ipaddress>`.

1. Run the following commands to build the SDK tool that retrieves your device provisioning information from the TPM simulator.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. From a command window, navigate to the `azure-iot-sdk-c` directory and run the TPM simulator. 它會透過連接埠 2321年和 2322 上的通訊端接聽。 Do not close this command window; you will need to keep this simulator running.

   From the `azure-iot-sdk-c` directory, run the following command to start the simulator:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Using Visual Studio, open the solution generated in the `cmake` directory named `azure_iot_sdks.sln`, and build it using the **Build solution** command on the **Build** menu.

1. 在 Visual Studio 的 [方案總管] 窗格中，瀏覽至 **Provision\_Tools** 資料夾。 以滑鼠右鍵按一下 **tpm_device_provision** 專案，然後選取 [設為起始專案]。

1. Run the solution using either of the **Start** commands on the **Debug** menu. The output window displays the TPM simulator's **Registration ID** and the **Endorsement key**, which you should copy for use later when you create an individual enrollment for your device in You can close this window (with Registration ID and Endorsement key), but leave the TPM simulator window running.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Retrieve provisioning information from a physical device

On your device, build a tool that you can use to retrieve the device's provisioning information.

1. Follow the steps in [Set up a Linux development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) to install and build the Azure IoT device SDK for C.

1. Run the following commands to build the SDK tool that retrieves your device provisioning information from the TPM device.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Copy the values for **Registration ID** and **Endorsement key**. 您可以使用這些值為 DPS 中的裝置建立個別的註冊。

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>設定 IoT 中樞裝置佈建服務

在 Azure 中建立 IoT 中樞裝置佈建服務的新執行個體，並將其連結至您的 IoT 中樞。 您可以依照[設定 IoT 中樞 DPS](../iot-dps/quick-setup-auto-provision.md) 中的指示操作。

裝置佈建服務開始執行之後，請從 [概觀] 頁面複製 [識別碼範圍] 的值。 當您設定 IoT Edge 執行階段時會用到此值。 

## <a name="create-a-dps-enrollment"></a>建立 DPS 註冊

從您的虛擬機器擷取佈建資訊，並使用該資訊在裝置佈建服務中建立個別的註冊。 

在 DPS 中建立註冊時，您就有機會宣告**初始裝置對應項狀態**。 在裝置對應項中，您可以根據解決方案中需要的任何計量 (例如區域、環境、位置或裝置類型) 來設定標記，進而將裝置分組。 這些標記會用來建立[自動部署](how-to-deploy-monitor.md)。 

1. In the [Azure portal](https://portal.azure.com), navigate to your instance of IoT Hub Device Provisioning Service. 

2. 在 [設定] 下方，選取 [管理註冊]。 

3. 選取 [新增個別註冊]，然後完成下列步驟以設定註冊：  

   1. 針對 [機制]，選取 [TPM]。 

   2. Provide the **Endorsement key** and **Registration ID** that you copied from your virtual machine.

      > [!TIP]
      > If you're using a physical TPM device, you need to determine the **Endorsement key**, which is unique to each TPM chip and is obtained from the TPM chip manufacturer associated with it. You can derive a unique **Registration ID** for your TPM device by, for example, creating an SHA-256 hash of the endorsement key.

   3. Select **True** to declare that this virtual machine is an IoT Edge device. 

   4. 選擇您的裝置所要連接的連結 **IoT 中樞**。 You can choose multiple hubs, and the device will be assigned to one of them according to the selected allocation policy. 

   5. 視需要提供裝置的識別碼。 您可以使用裝置識別碼，將個別裝置設為模組部署的目標。 If you don't provide a device ID, the registration ID is used.

   6. 視需要將標記值新增至 [初始裝置對應項狀態]。 您可以使用標記將裝置群組設定為模組部署的目標。 例如： 

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

   7. 選取 [儲存]。 

Now that an enrollment exists for this device, the IoT Edge runtime can automatically provision the device during installation. 

## <a name="install-the-iot-edge-runtime"></a>安裝 IoT Edge 執行階段

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 其元件會在容器中執行，並可讓您將其他容器部署到裝置，以便您在邊緣上執行程式碼。 在虛擬機器上安裝 IoT Edge 執行階段。 

開始閱讀您的裝置類型適用的文章之前，請先了解您的 DPS [識別碼範圍] 和裝置的 [註冊識別碼]。 如果您安裝了範例 Ubuntu Server，請使用 **x64** 指示。 請務必將 IoT Edge 執行階段設定為自動佈建，而不是手動佈建。 

[Install the Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md)

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

You can verify that the individual enrollment that you created in Device Provisioning Service was used. Navigate to your Device Provisioning Service instance in the Azure portal. Open the enrollment details for the individual enrollment that you created. Notice that the status of the enrollment is **assigned** and the device ID is listed. 

## <a name="next-steps"></a>後續步驟

佈建新裝置時，裝置佈建服務註冊程序可讓您同時設定裝置識別碼和裝置對應項標記。 您可以使用這些值來鎖定要使用自動裝置管理的個別裝置或裝置群組。 了解如何[使用 Azure 入口網站大規模部署和監視 IoT Edge 模組](how-to-deploy-monitor.md)，或[使用 Azure CLI](how-to-deploy-monitor-cli.md) 執行相同作業。
