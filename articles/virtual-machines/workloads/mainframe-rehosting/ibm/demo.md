---
title: 設定註冊應用程式開發人員控制發佈 (ADCD) 在 IBM zD & T v1 |Microsoft Docs
description: Azure 虛擬機器 (Vm) 上執行的 IBM Z 開發和測試環境 (zD & T) 環境。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: d527b08f3610531bef8e98a11998942411651d27
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621356"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>設定註冊應用程式開發人員控制發佈 (ADCD) 在 IBM zD & T v1

您可以執行的 IBM Z 開發和測試環境 (zD & T) 環境在 Azure 虛擬機器 (Vm)。 此環境中模擬 IBM Z 系列的架構。 它可以裝載各種 Z 系列的作業系統或安裝 （也稱為 Z 執行個體或封裝），可透過自訂的組合稱為 IBM 應用程式開發人員控制散發 (ADCDs)。

這篇文章會示範如何設定 ADCD zD & T 在 Azure 上的環境中執行個體。 ADCDs 建立開發和測試環境中 zD & t 執行的完整 Z 系列作業系統實作

ZD (& T)，例如 ADCDs 是僅適用於 IBM 客戶和合作夥伴，專門用於開發與測試之用。 也就是不是要用於生產環境。 許多 IBM 安裝套件可供下載，透過[Passport 優點](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html)或是[IBM PartnerWorld](https://www.ibm.com/partnerworld/public)。

## <a name="prerequisites"></a>先決條件

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- [ZD & T 環境][ibm-install-z]先前在 Azure 上設定。 本文假設您使用的稍早建立的相同 Ubuntu 16.04 VM 映像。

- 透過 IBM PartnerWorld 或 Passport 利用 ADCD 媒體的存取。

- A[授權伺服器](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 如此才能執行 IBM zD & t。 您建立它的方式取決於從 IBM 軟體的授權：

  - **硬體為基礎的授權伺服器**需要 USB 硬體裝置，其中包含存取軟體的所有部分所需的合理的語彙基元。 您必須從 IBM 取得此設定。

  - **以軟體為基礎的授權伺服器**會要求您設定集中式伺服器管理授權的金鑰。 此方法較佳，且需要您設定您從 IBM 收到管理伺服器中的索引鍵。

## <a name="download-the-installation-packages-from-passport-advantage"></a>從 Passport 利用下載安裝套件

需要 ADCD 媒體的存取權。 下列步驟假設您是 IBM 客戶，並且可以使用 Passport 的優點。 可以使用 IBM 合作夥伴[IBM PartnerWorld](https://www.ibm.com/partnerworld/public)。

> [!NOTE]
> 本文假設 Windows 電腦，用來存取 Azure 入口網站，以及下載 IBM 媒體。 如果您使用 Mac 或 Ubuntu 桌面、 命令和取得 IBM 媒體的程序可能稍有不同。

1. 登入[Passport 利用](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer)。

2. 選取 **下載軟體**並**媒體存取**。

3. 選取 **程式供應項目和合約編號**，然後按一下**繼續**。

4. 輸入組件描述或組件編號，然後按一下**Finder**。

5. （選擇性） 按字母順序清單來顯示，並依名稱檢視產品。

6. 選取 **所有的作業系統**中**作業系統欄位**，並**所有語言**中**語言欄位**。 然後，按一下**移**。

7. 按一下 **選取 個別檔案**來展開清單並顯示下載個別的媒體。

8. 請確認您想要下載，請選取套件**下載**，，然後將檔案下載至您想要的目錄。

## <a name="upload-the-adcd-packages"></a>上傳 ADCD 套件

已封裝之後，您必須上傳至您在 Azure 上的 VM。

1. 在 Azure 入口網站中，起始**ssh**與您所建立的 Ubuntu VM 的工作階段。 移至您的 VM，選取**概觀**刀鋒視窗中，然後選取**Connect**。

2. 選取  **SSH**索引標籤，然後將複製的 ssh 命令到剪貼簿。

3. 登入您的 VM 使用您的認證並[SSH 用戶端](/azure/virtual-machines/linux/use-remote-desktop)的選擇。 此示範使用 Linux 延伸模組適用於 Windows 10，將 Windows 命令提示字元中的 bash 殼層。 PuTTY 也一樣好。

4. 當登入，建立要上傳 IBM 封裝的目錄。 請注意 Linux 會區分大小寫。 比方說，這段示範影片假設套件上傳至：

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. 使用 SSH 用戶端，例如將檔案上傳[WinSCP](https://winscp.net/eng/index.php)。 SCP 是 SSH 的一部分，因為它會使用連接埠 22，這是 SSH 的使用。 如果您的本機電腦不是 Windows，您可以輸入[scp 命令](http://man7.org/linux/man-pages/man1/scp.1.html)SSH 工作階段中。

6. 起始上傳到 Azure VM 建立的目錄，這會成為 zD & t 的映像儲存體

    > [!NOTE]
    > 請確定**ADCDTOOLS。XML**包含在上傳**home/MyUserID/ZDT/adcd/nov2017**目錄。 稍後您將會用到此資訊。

7. 等候檔案上傳，這可能需要一些時間，視您連接至 Azure。

8. 上傳完成時，瀏覽至 磁碟區的目錄，並解壓縮的所有**gz**磁碟區：

    ```
        gunzip \*.gz
    ```
    
![檔案總管 中顯示解壓縮 gz 磁碟區](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>設定映像儲存體

下一個步驟是設定 zD (& T) 若要使用已上傳的套件。 映像儲存體的程序內 zD (& T) 可讓您掛接和使用映像。 它可以使用 SSH 或 FTP。

1. 開始**zDTServer**。 若要這樣做，您必須是根層級。 在順序中，輸入下列兩個命令：
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. 請注意 URL 輸出命令和使用此 URL 來存取 web 伺服器。 它看起來類似：
     > https://(your VM name or IP address):9443/ZDTMC/index.html
     >
     > 請記住，您的 web 存取會使用連接埠 9443。 使用此登入 web 伺服器。 ZD (& T) 的使用者識別碼都**zdtadmin**且密碼為**密碼**。

    ![IBM zD & T Enterprise Edition 歡迎使用 畫面](media/02-welcome.png)

3. 在 **快速入門**頁面的 **設定**，選取**映像儲存體**。

     ![IBM zD & T Enterprise Edition 快速入門 畫面](media/03-quickstart.png)

4. 在 **設定映像儲存體**頁面上，選取**SSH 檔案傳輸通訊協定**。

5. 針對**主機名稱**，型別**Localhost**並輸入您上傳的映像的目錄路徑。 比方說，/home/MyUserID/ZDT/adcd/nov2017/volumes。

6. 請輸入**使用者識別碼**並**密碼**vm。 請勿使用 ZD T 使用者識別碼和密碼。

7. 測試連線，並確定您具有存取權，然後選取**儲存**儲存設定。

## <a name="configure-the-target-environments"></a>設定目標環境

下一個步驟是設定 zD & T 目標環境。 此模擬的裝載的環境是執行您的映像的位置。

1. 在 **快速入門**頁面的 **設定**，選取**目標環境**。

2. 在 **設定目標環境**頁面上，選取**Add Target**。

3. 選取  **Linux**。 IBM 則支援兩種類型的環境，Linux 和 Cloud(OpenStack)，但在 Linux 上執行這段示範影片。

4. 在上**加入目標環境**頁面上，如**主機名稱**，輸入**localhost**。 保持**SSH 連接埠**設為**22**。

5. 在 **目標環境標籤**方塊中，輸入標籤，例如**MyCICS。**

     ![新增目標環境 畫面](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>設定 ADCD 和部署

完成先前的組態步驟之後，您必須設定 zD (& T) 若要使用的套件和目標環境。 同樣地，您可以使用 zD (& T)，可讓您掛接和使用映像的映像儲存體程序。 它可以使用 SSH 或 FTP。

1. 在 **快速入門**頁面的 **設定**，選取**ADCD**。 一組指示出現時，它會告訴您完成後，才可掛接 ADCD 封裝所需的步驟。 本節將說明為什麼我們名為目標目錄稍早執行過的方式。

2. 假設所有映像上傳至正確的目錄中，按一下**影像從 ADCD** （如下列螢幕擷取畫面中的步驟 7 所示） 的右下角顯示的連結。

     ![IBM zD & T 企業版設定 ADCD 畫面](media/05-adcd.png)

## <a name="create-the-image"></a>建立映像

上一個組態步驟完成時，**建立映像使用 ADCD 元件**頁面隨即出現。

1. 要顯示該磁碟區中的不同封裝中選取磁碟區 (在此情況下的年 11 月 2017)。

2. 針對此示範中，選取**客戶資訊控制系統 (CICS)-5.3**。

3. 在 **映像名稱**方塊中，輸入映像的名稱，例如**MyCICS 映像**。

4. 選取 **建立映像**右下方的按鈕。

     ![IBM zD & T Enterprise Edition-建立映像使用 ADCD 元件 畫面](media/06-adcd.png)

5. 在出現的視窗，告知您的映像已成功部署中，選擇**部署映像**。

6. 在 **映像部署至目標環境**頁面上，選取您在前一頁建立的映像 (**MyCICS 映像**) 和稍早建立的目標環境 (**MyCICS**).

7. 在下一個畫面上，提供 VM （亦即，非 ztadmin 認證） 的認證。

8. 在 屬性 窗格中，輸入 數目**中央處理器 (CPs)** ，數量**系統記憶體 (GB)** ，而**部署目錄**執行映像。 因為這是一個示範，讓它保持小型。

9. 請確定已選取方塊**問題 IPL 命令，以 z/OS 之後自動部署**。

     ![屬性 畫面](media/07-properties.png)

10. 選取 **完整**。

11. 選取 **部署映像**從**映像部署至目標環境**頁面。

您的映像現在可以部署並已準備好可掛接 3270 終端機模擬器。

> [!NOTE]
> 如果您收到錯誤，指出您沒有足夠的磁碟空間，請注意，區域需要 151 Gb。

恭喜您！ 您現在已在 Azure 上執行的 IBM 大型主機環境。

## <a name="learn-more"></a>深入了解

- [大型主機移轉： 迷思與事實](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [在 Azure 上的 IBM DB2 pureScale](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [疑難排解](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [揭開大型主機移轉至 Azure 的神秘面紗](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
