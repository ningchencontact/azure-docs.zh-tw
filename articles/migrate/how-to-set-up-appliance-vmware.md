---
title: 設定設備的 Azure 移轉伺服器評估/移轉 VMware vm |Microsoft Docs
description: 描述如何設定用於探索、 評估和無代理程式使用 Azure Migrate 伺服器評估/移轉 VMware Vm 移轉的應用裝置。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: fe190381df346278e75a3e6fd9876b80c33bd86b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811722"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>為 VMware Vm 設定設備

本文說明如何設定 Azure Migrate 應用裝置，若您是使用 Azure Migrate Server 評量工具，評定 VMware Vm 或移轉至 Azure 的 VMware Vm，使用 Azure Migrate Server 移轉工具的無代理程式移轉。

VMware VM 設備是輕量型設備使用的 Azure 移轉伺服器評估/移轉執行下列操作：

- 探索內部部署 VMware VM。
- 傳送探索到的 Vm 至 Azure 移轉伺服器評估/移轉中繼資料和效能資料。

[了解更多](migrate-appliance.md)關於 Azure Migrate 應用裝置。


## <a name="appliance-deployment-steps"></a>應用裝置的部署步驟

若要設定設備您：
- 下載.OVA 範本檔案，並將它匯入 vCenter Server。
- 建立設備，並檢查它可以連線至 Azure Migrate Server 評量。 
- 第一次，設定設備，並向 Azure Migrate 專案。

## <a name="download-the-ova-template"></a>下載 OVA 範本

1. 在 **移轉目標** > **伺服器** > **Azure Migrate:Server 評定**，按一下**Discover**。
2. 在 **探索機器** > **您虛擬化的電腦？** ，按一下  **Yes，with VMWare vSphere hypervisor 使用**。
3. 按一下 **下載**下載.OVA 範本檔案。



### <a name="verify-security"></a>驗證安全性

請檢查 OVA 檔案是安全的再將它部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令，以產生 ova 的雜湊：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用方式範例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 如需設備版本 1.0.0.5，產生的雜湊應符合這些設定。 

  **演算法** | **雜湊值**
  --- | ---
  MD5 | ddfdf21c64af02a222ed517ce300c977


## <a name="create-the-appliance-vm"></a>建立設備 VM

匯入已下載的檔案，並建立 VM。

1. 在 vSphere 用戶端主控台中，按一下 [檔案]   > [部署 OVF 範本]  。
2. 在 [部署 OVF 範本精靈] > [來源]  中，指定 OVA 檔案的位置。
3. 在 **名稱**並**位置**，指定 VM 的易記名稱。 選取將裝載 VM 所在清查物件。
5. 在 **主機/叢集**、 指定的主機或叢集上的 VM 會執行。
6. 在 **儲存體**，指定 VM 的儲存體目的地。
7. 在 [磁碟格式]  中，指定磁碟類型和大小。
8. 在 **網路對應**，指定 VM 將連接的網路。 網路需要網際網路連線，以將中繼資料傳送至 Azure Migrate Server 評量。
9. 檢閱並確認設定，然後按一下 [完成]  。


### <a name="verify-appliance-access-to-azure"></a>確認 Azure 應用裝置存取

請確定該應用裝置 VM 可以連線到[Azure Url](migrate-support-matrix-vmware.md#assessment-url-access-requirements)。


## <a name="configure-the-appliance"></a>設定設備

第一次設定設備。

1. 在 vSphere 用戶端主控台中，以滑鼠右鍵按一下 [VM] > [開啟主控台]  。
2. 提供設備的語言、 時區和密碼。
3. 開啟任何可以連線至 VM，並開啟應用裝置的 web 應用程式的 URL 的電腦上的瀏覽器： **https://*設備名稱或 IP 位址*:44368**.

   或者，您也可以按一下應用程式捷徑，以開啟應用程式從應用裝置的桌面。
4. 在 web 應用程式 >**設定必要條件**，執行下列動作：
    - **授權**:接受授權條款，並閱讀第三方資訊。
    - **連線**:應用程式會檢查 VM 有網際網路存取。 如果 VM 使用 proxy:
        - 按一下  **Proxy 設定**，並在表單中指定的 proxy 位址和接聽連接埠， http://ProxyIPAddress 或 http://ProxyFQDN 。
        - 如果 Proxy 需要驗證，請指定認證。
        - 僅支援 HTTP Proxy。
    - **時間同步**:已驗證的時間。 在應用裝置上的時間應該探索才能正常運作的網際網路時間同步。
    - **安裝更新**:Azure Migrate 會檢查確認已安裝最新的應用裝置更新。
    - **安裝 VDDK**:Azure Migrate 會檢查已安裝的 VMWare vSphere 虛擬磁碟開發套件 (VDDK)。
        - Azure Migrates 會將機器複寫至 Azure 的移轉期間使用 VDDK。
        - 下載 VDDK 6.7 從 VMware，並將下載的 zip 內容解壓縮至應用裝置上指定的位置。

## <a name="register-the-appliance-with-azure-migrate"></a>註冊使用 Azure Migrate 的設備

1. 按一下 **登入**。 如果未出現，請確定您已停用快顯封鎖程式，在瀏覽器中。
2. 新的索引標籤中，使用登入您的 Azure 認證。 
    - 使用您的使用者名稱和密碼登入。
    - 不支援使用 PIN 登入。
3. 已成功登入之後，請回到 web 應用程式。
2. 選取建立 Azure Migrate 專案所在的訂用帳戶。 然後選取專案。
3. 指定的名稱。 名稱應該是 14 個字元的英數字元或更少。
4. 按一下 [註冊]  。


## <a name="start-continuous-discovery"></a>啟動持續探索

現在，從應用裝置連線到 vCenter Server，並啟動 VM 探索。 

1. 在 **指定 vCenter Server 詳細資料**，指定的名稱 (FQDN) 或 IP 位址的 vCenter Server。 您可以保留預設連接埠，或指定自訂您的 vCenter Server 所接聽的通訊埠。
2. 在 **使用者名**並**密碼**，指定設備會用來探索 vCenter 伺服器上的 Vm 的唯讀帳戶認證。 請確定該帳戶擁有[探索所需的權限](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions)。
3. 按一下 **驗證連線**藉此確定設備可以連線到 vCenter Server。
4. 建立連線之後，請按一下**儲存並開始探索**。


這會啟動探索。 需要大約 15 分鐘探索到的 Vm 的中繼資料，才會出現在入口網站。 


## <a name="next-steps"></a>後續步驟

檢閱教學課程[VMware 評量](tutorial-assess-vmware.md)並[無代理程式的移轉](tutorial-migrate-vmware.md)。
