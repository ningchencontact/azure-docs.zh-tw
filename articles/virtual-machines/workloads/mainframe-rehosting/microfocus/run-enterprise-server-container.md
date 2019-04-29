---
title: Azure 虛擬機器上執行的 Docker 容器中的 Micro Focus Enterprise Server 4.0
description: 將您的 IBM z/OS 大型主機工作負載重新裝載的 Docker 容器中的 Azure 虛擬機器上執行 Micro Focus Enterprise Server。
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61488342"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>在 Azure 上執行的 Docker 容器中的 Micro Focus Enterprise Server 4.0

您可以在 Azure 上的 Docker 容器中執行微焦點 Enterprise Server 4.0。 本教學課程會說明方法。 它會使用 Windows CICS （客戶資訊控制系統） acctdemo 示範適用於 Enterprise Server。

Docker 會新增到應用程式可攜性和隔離。 比方說，您可以匯出的 Docker 映像，從一部 Windows VM 上，執行或可以使用 Docker 的 Windows 伺服器的存放庫中。 新的位置，以相同的組態中執行的 Docker 映像，而不需要安裝 Enterprise Server。 它的映像的一部分。 授權考量仍然適用。

本教學課程會安裝**Windows 2016 Datacenter with Containers**從 Azure Marketplace 的虛擬機器 (VM)。 包含此 VM **Docker 18.09.0**。 下列步驟會示範如何將容器部署、 執行它，並使用 3270 模擬器連線到該。

## <a name="prerequisites"></a>必要條件

開始之前，請參閱這些必要條件：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- Micro Focus 軟體並有效的授權 （或試用版授權）。 如果您是現有的 Micro Focus 客戶，請連絡您的 Micro Focus 代表。 否則，請[要求試用版](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)。

     > [!NOTE]
     > Docker 示範檔案會包含 Enterprise Server 4.0。 本教學課程使用企業與中型市場\_伺服器\_dockerfiles\_4.0\_windows.zip。 從相同的位置存取企業伺服器安裝檔案的存取，或移至*Micro Focus*開始著手。

- 文件[企業伺服器與企業開發人員](https://www.microfocus.com/documentation/enterprise-developer/#")。

## <a name="create-a-vm"></a>建立 VM

1. 保護媒體從企業與中型市場\_伺服器\_dockerfiles\_4.0\_windows.zip 檔案。 安全 ES Docker Prod-XXXXXXXX.mflic 授權檔案 （需要建置 Docker 映像）。

2. 建立 VM。 若要這樣做，請開啟 Azure 入口網站中，選取**建立資源**從左上方，並依篩選*windows server*。 在結果中，選取**Windows Server 2016 Datacenter – with Containers**。

     ![Azure 入口網站的搜尋結果](media/01-portal.png)

3. 若要設定 VM 的屬性，選擇 執行個體詳細資料：

    1. 選擇 VM 大小。 本教學課程中，請考慮使用**標準 DS2\_v2**具有 2 個 Vcpu 和 7 GB 記憶體的 VM。

    2. 選取 **地區**並**資源群組**您想要部署到。

    3. 針對**可用性選項**，使用預設設定。

    4. 針對**Username**，輸入您想要使用的系統管理員帳戶和密碼。

    5. 請確定**連接埠 3389 的 RDP**已開啟。 此連接埠必須對外公開，因此您可以登入 VM。 接受所有預設值，然後按一下**檢閱 + 建立**。

     ![建立虛擬機器 窗格](media/container-02.png)

4. 等待部署完成 （幾分鐘的時間）。 訊息會指出已建立您的 VM。

5. 按一下 **移至資源**以前往**概觀**刀鋒視窗中，為您的 VM。

6. 在右側，按一下**Connect**  按鈕。 **連線至虛擬機器**選項會出現在右邊。

7. 按一下 [**下載 RDP 檔案**] 按鈕，下載 RDP 檔案，可讓您連接至 VM。

8. 檔案下載完成之後，開啟，再輸入的使用者名稱與您為 VM 建立的密碼。

     > [!NOTE]
     > 請勿使用公司認證來登入。 （RDP 用戶端會假設您可能想要使用這些項目。 不這麼做。）

9.  選取 **更多的選擇**，然後選取您的 VM 認證。

到目前為止，VM 是執行中和透過 RDP 連接。 您要登入並準備好進行下一個步驟。

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>建立沙箱目錄，並將 zip 檔案上傳

1.  您可以上傳的示範和授權的檔案在 VM 上建立目錄。 例如， **c:\\沙箱**。

2.  上傳**ent\_伺服器\_dockerfile\_4.0\_windows.zip**而**ES Docker Prod-XXXXXXXX.mflic**檔案至您所建立的目錄。

3.  解壓縮 zip 檔案的內容**ent\_伺服器\_dockerfile\_4.0\_windows**擷取程序所建立的目錄。 此目錄包含讀我檔案 （如.html 和.txt 檔案） 和兩個子目錄**EnterpriseServer**並**範例**。

4.  複製**ES Docker Prod-XXXXXXXX.mflic**到 c:\\沙箱\\ent\_server\_dockerfiles\_4.0\_windows\\EnterpriseServer 和 c:\\沙箱\\ent\_伺服器\_dockerfiles\_4.0\_windows\\範例\\CICS 目錄。

> [!NOTE]
> 請確定您將授權檔案複製到這兩個目錄。 所需要的 Docker 建置步驟，藉此確定映像已經過適當授權。

## <a name="check-docker-version-and-create-base-image"></a>檢查 Docker 版本，並建立基底映像

> [!IMPORTANT]
> 建立適當的 Docker 映像，是兩步驟程序。 首先，建立企業 Server 4.0 的基底映像。 然後建立另一個映像適用於 x64 平台。 雖然您可以建立 x86 （32 位元） 映像，請使用 64 位元映像。

1. 開啟命令提示字元。

2. 檢查已安裝 Docker。 在命令提示字元中，輸入：

    ```
        docker version
    ```

     比方說，版本是 18.09.0，這寫入時。

3. 若要變更目錄，請輸入**cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**。

4. 型別**bld.bat IacceptEULA**開始初始的基底映像的建置程序。 等候幾分鐘的時間來執行此程序。 在結果中，建立兩個映像，請注意，一個用於 x64，一個適用於 x86:

     ![顯示映像的命令 視窗](media/container-04.png)

5. 若要建立用於 CICS 示範最終映像，請切換到 CICS 目錄輸入**cd\\沙箱\\ent\_server\_dockerfiles\_4.0\_windows\\範例\\CICS**。

6. 若要建立映像，請輸入**bld.bat x64**。 等候幾分鐘的時間執行之處理序和訊息，指出 已建立的映像。

7. 型別**docker 映像**以顯示所有安裝在 VM 上的 Docker 映像的清單。 請確定**microfocus/es-acctdemo**是其中一個。

     ![命令視窗顯示 es acctdemo 映像](media/container-05.png)

## <a name="run-the-image"></a>執行映像 

1.  若要啟動 Enterprise Server 4.0 和 acctdemo 應用程式，在命令提示字元中輸入：

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  這類安裝 3270 終端機模擬器[x3270](http://x3270.bgp.nu/)並用它來連接，透過連接埠 9040，正在執行的映像。

3.  取得 acctdemo 容器的 IP 位址，因此 Docker 可做為 DHCP 伺服器來進行容器管理：

    1.  取得執行中容器的識別碼。 型別**Docker ps**命令提示字元中，注意 識別碼 (**22a0fe3159d0**在此範例中)。 您可以儲存起來供下一個步驟。

    2.  若要取得的 IP 位址 acctdemo 容器，使用先前步驟中的容器識別碼，如下所示：

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       例如︰

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. 請注意 acctdemo 映像的 IP 位址。 例如，下列的輸出位址會是 172.19.202.52。

     ![顯示 IP 位址的 [命令] 視窗](media/container-06.png)

5. 使用模擬器映像掛接。 將模擬器設定為使用連接埠 9040 與 acctdemo 映像的位址。 在這裡，它有**172.19.202.52:9040**。 您將會類似。 **登入到 CICS**畫面隨即開啟。

    ![CICS 畫面的登入](media/container-07.png)

6. 輸入登入 CICS 區域**SYSAD** for **USERID**並**SYSAD**如**密碼**。

7. 清除畫面中，使用模擬器的快速鍵。 針對 x3270，選取**快速鍵**功能表選項。

8. 若要啟動 acctdemo 應用程式，請輸入**ACCT**。 應用程式的初始畫面會顯示。

     ![帳戶 [Demo] 畫面](media/container-08.png)

9. 嘗試顯示帳戶類型。 例如，輸入**D**要求並**11111** for**帳戶**。 若要嘗試其他帳戶號碼是 22222、 33333，以此類推。

     ![帳戶 [Demo] 畫面](media/container-09.png)

10. 若要顯示 Enterprise Server 管理主控台，請移至命令提示字元並輸入**啟動 http:172.19.202.52:86**

     ![Enterprise Server 管理主控台](media/container-010.png)

就這麼簡單！ 現在您正在執行，及管理 Docker 容器中的 CICS 應用程式。

## <a name="next-steps"></a>後續步驟

- [在 Azure 上安裝 Micro Focus Enterprise Server 4.0 和企業開發人員 4.0](./set-up-micro-focus-azure.md)
- [大型主機應用程式移轉](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
