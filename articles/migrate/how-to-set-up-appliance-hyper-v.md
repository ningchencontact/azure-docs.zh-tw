---
title: 設備的 Azure 移轉伺服器評估/移轉的 HYPER-V Vm 設定 |Microsoft Docs
description: 描述如何設定用於探索、 評估和無代理程式使用 Azure Migrate 伺服器評估/移轉的 HYPER-V Vm 移轉的應用裝置。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: c531fe49ebff6c021547c2d1c2f382bcd6c9caef
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811761"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>設定 HYPER-V 虛擬機器的應用裝置

本文說明如何設定 Azure Migrate 應用裝置，若您是使用 Azure Migrate Server 評量工具，評估 HYPER-V Vm，或將 VMware Vm 移轉到 Azure 中使用 Azure Migrate Server 移轉工具。

HYPER-V VM 設備是輕量型設備使用的 Azure 移轉伺服器評估/移轉執行下列操作：

- 探索內部部署 HYPER-V Vm。
- 傳送探索到的 Vm 至 Azure 移轉伺服器評估/移轉中繼資料和效能資料。

[了解更多](migrate-appliance.md)關於 Azure Migrate 應用裝置。


## <a name="appliance-deployment-steps"></a>應用裝置的部署步驟

若要設定設備您：
- 從 Azure 入口網站來下載壓縮為 HYPER-V VHD。
- 建立設備，並檢查它可以連線至 Azure Migrate Server 評量。 
- 第一次，設定設備，並向 Azure Migrate 專案。

## <a name="download-the-vhd"></a>下載 VHD

下載 zip 壓縮的 VHD 範本設備。

1. 在 **移轉目標** > **伺服器** > **Azure Migrate:Server 評定**，按一下**Discover**。
2. 在 **探索機器** > **您虛擬化的電腦？** ，按一下**Yes，with HYPER-V 使用**。
3. 按一下 **下載**下載 VHD 檔案。

    ![下載 VM](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>驗證安全性

檢查壓縮的檔案的安全，再將它部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令來產生雜湊的 vhd
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用方式範例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  如需設備版本 1.19.05.10，產生的雜湊應符合這些設定。

  **演算法** | **雜湊值**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79


  
## <a name="create-the-appliance-vm"></a>建立設備 VM

匯入已下載的檔案，並建立 VM。

1. 壓縮 VHD 將檔案解壓縮至資料夾將裝載的應用裝置 VM 的 HYPER-V 主機上。 會擷取三個資料夾。
2. 開啟 [HYPER-V 管理員]。 在 **動作**，按一下**匯入虛擬機器**。

    ![將 VHD 部署](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. 在 匯入虛擬機器精靈 >**在您開始之前**，按一下**下一步**。
3. 在 **找出資料夾**，指定包含擷取的 VHD 的資料夾。 然後按 [下一步]  。
1. 在 [**選取虛擬機器**，按一下**下一步]** 。
2. 在 **選擇 匯入型別**，按一下**複製虛擬機器 （建立新的唯一識別碼）** 。 然後按 [下一步]  。
3. 在  **選擇目的地**，保留預設設定。 按一下 [下一步]  。
4. 在 **儲存體資料夾**，保留預設設定。 按一下 [下一步]  。
5. 在 **選擇網路**，指定 VM 將會使用虛擬交換器。 此參數需要網際網路連線，將資料傳送至 Azure。
6. 在 **摘要**，檢閱設定。 然後按一下 [ **完成**]。
7. 在 HYPER-V 管理員 >**虛擬機器**，啟動 VM。


### <a name="verify-appliance-access-to-azure"></a>確認 Azure 應用裝置存取

請確定該應用裝置 VM 可以連線到[Azure Url](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)。

## <a name="configure-the-appliance"></a>設定設備

第一次設定設備。

1. 在 HYPER-V 管理員 >**虛擬機器**，以滑鼠右鍵按一下 VM > **Connect**。
2. 提供設備的語言、 時區和密碼。
3. 開啟任何可以連線至 VM，並開啟應用裝置的 web 應用程式的 URL 的電腦上的瀏覽器： **https://*設備名稱或 IP 位址*:44368**.

   或者，您也可以按一下應用程式捷徑，以開啟應用程式從應用裝置的桌面。
1. 在 web 應用程式 >**設定必要條件**，執行下列動作：
    - **授權**:接受授權條款，並閱讀第三方資訊。
    - **連線**:應用程式會檢查 VM 有網際網路存取。 如果 VM 使用 proxy:
        - 按一下  **Proxy 設定**，並在表單中指定的 proxy 位址和接聽連接埠， http://ProxyIPAddress 或 http://ProxyFQDN 。
        - 如果 Proxy 需要驗證，請指定認證。
        - 僅支援 HTTP Proxy。
    - **時間同步**:已驗證的時間。 在應用裝置上的時間應該與虛擬機器探索才能正常運作的網際網路時間同步。
    - **安裝更新**:Azure Migrate Server 評估檢查應用裝置已安裝最新更新。

### <a name="register-the-appliance-with-azure-migrate"></a>註冊使用 Azure Migrate 的設備

1. 按一下 **登入**。 如果未出現，請確定您已停用快顯封鎖程式，在瀏覽器中。
2. 新的索引標籤中，使用登入您的 Azure 認證。 
    - 使用您的使用者名稱和密碼登入。
    - 使用 PIN 登入不支援。
3. 已成功登入之後，請回到 web 應用程式。
4. 選取建立 Azure Migrate 專案所在的訂用帳戶。 然後選取專案。
5. 指定的名稱。 名稱應該是 14 個字元的英數字元或更少。
6. 按一下 [註冊]  。


### <a name="delegate-credentials-for-smb-vhds"></a>SMB Vhd 的委派認證

如果您在 Smb 來執行 Vhd，您必須啟用從應用裝置的 HYPER-V 主機的認證委派。 若要從應用裝置執行這項操作：

1. 在應用裝置 VM 上執行此命令。 HyperVHost1/HyperVHost2 是範例主機名稱。

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. 或者，這樣在本機群組原則編輯器的應用裝置上：
    - 在 **本機電腦原則** > **電腦組態**，按一下 **系統管理範本** > **系統** > **認證委派**。
    - 按兩下**允許委派新認證**，然後選取**已啟用**。
    - 在 **選項**，按一下**顯示**，並新增您想要使用探索到清單中，每一部 HYPER-V 主機**wsman /** 做為前置詞。
    - 在 **認證委派**，按兩下**允許委派新認證，使用僅限 NTLM 伺服器驗證**。 同樣地，新增您想要使用探索到清單中，每一部 HYPER-V 主機**wsman /** 做為前置詞。

## <a name="start-continuous-discovery"></a>啟動持續探索

從應用裝置連線到 HYPER-V 主機或叢集，並啟動 VM 探索。

1. 在 **使用者名**並**密碼**，指定設備將會用來探索 Vm 的帳戶認證。 指定的認證，易記名稱，然後按一下**儲存詳細資料**。
2. 按一下 **新增主機**，並指定 HYPER-V 主機/叢集詳細資料。
3. 按一下 [驗證]  。 通過驗證之後，會顯示可探索每個主機/叢集上的 Vm 數目。
    - 如果驗證失敗之主控件，檢閱錯誤停留在圖示**狀態**資料行。 修正問題，並重新驗證。
    - 若要移除主機或叢集，請選取 >**刪除**。
    - 您無法從叢集移除特定主機。 您只能移除整個叢集。
    - 即使叢集中的特定主機問題，您可以加入叢集。
4. 通過驗證之後，按一下**儲存並開始探索**開始探索程序。

這會啟動探索。 需要大約 15 分鐘探索到的 Vm 的中繼資料，才會出現在 Azure 入口網站。 

## <a name="verify-vms-in-the-portal"></a>在入口網站中確認 VM

探索完成之後，您可以確認 Vm 出現在入口網站。

1. 開啟 Azure Migrate 儀表板。
2. 在  **Azure Migrate-伺服器** > **Azure Migrate:Server 評定**頁面上，按一下圖示，顯示的計數**探索伺服器**。 


## <a name="next-steps"></a>後續步驟

試試看[HYPER-V 評估](tutorial-assess-hyper-v.md)使用 Azure Migrate Server 評量。