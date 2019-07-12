---
title: 在 Azure 上安裝 IBM zD & T 開發/測試環境 |Microsoft Docs
description: 在 Azure 虛擬機器 (VM) 基礎結構上部署 IBM Z 開發和測試環境 (zD & T) 即服務 (IaaS)。
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: ad02ec75dab4cb6971d0467899d80f5f745fd94b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621303"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>在 Azure 上安裝 IBM zD & T 開發/測試環境

若要建立 IBM Z 系統大型主機工作負載的開發/測試環境，您可以 IBM Z 開發和測試環境 (zD & T) 上部署 Azure 虛擬機器 (VM) 基礎結構即服務 (IaaS)。

使用 zD (& T)，您可以利用的成本節約優點 x86 平台，您較不重要的開發和測試環境，然後再推送更新傳回 Z 系統實際執行環境。 如需詳細資訊，請參閱 < [IBM ZD (& T) 的安裝指示](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL)。

Azure 和 Azure Stack 支援下列版本：

- zD & T Personal Edition
- zD & T 平行 Sysplex
- zD & T Enterprise Edition

只在 x86 上執行的所有版本 zD (& T) 的 Linux 系統，而不是 Windows Server。 Enterprise Edition 支援 Ubuntu/Debian 或 Red Hat Enterprise Linux (RHEL) 上。 RHEL 和 Debian VM 映像可供 Azure。

這篇文章會示範如何讓您可以建立和管理的環境使用 zD & T Enterprise Edition web 伺服器，設定 zD & T 在 Azure 上的 Enterprise Edition。 安裝 zD (& T) 不會安裝任何環境。 您必須建立這些個別安裝套件。 例如，應用程式開發人員控制散發套件 (ADCD) 是磁碟區映像的測試環境。 它們包含在媒體散發套件可從 IBM 的 zip 映像。 請參閱如何[設定在 Azure 上 ADCD 環境](demo.md)。

如需詳細資訊，請參閱 < [zD & T 概觀](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html)IBM Knowledge Center 中。

這篇文章會示範如何設定 Z 開發和測試環境 (zD & T) 在 Azure 上的 Enterprise Edition。 然後您可以使用 zD & T Enterprise Edition web 伺服器來建立和管理在 Azure 上的 Z 環境。

## <a name="prerequisites"></a>先決條件

> [!NOTE]
> IBM 讓 zD T Enterprise Edition 安裝在僅限開發/測試環境 —*不*生產環境。

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 您需要為該媒體，也就是僅適用於 IBM 客戶和合作夥伴的存取。 如需詳細資訊，請連絡您的 IBM 代表，或參閱上的連絡資訊[zD & T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment)網站。

- A[授權伺服器](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 這是必要的環境存取權。 您建立它的方式取決於從 IBM 軟體的授權：

     - **硬體為基礎的授權伺服器**需要 USB 硬體裝置，其中包含存取軟體的所有部分所需的合理的語彙基元。 您必須從 IBM 取得此設定。

     - **以軟體為基礎的授權伺服器**會要求您設定集中式伺服器管理授權的金鑰。 此方法較佳，且需要您設定您從 IBM 收到管理伺服器中的索引鍵。

## <a name="create-the-base-image-and-connect"></a>建立基底映像，並連線

1. 在 Azure 入口網站[建立 VM](/azure/virtual-machines/linux/quick-create-portal)您想要的作業系統組態。 本文假設 （具有 4 個 Vcpu 和 16 GB 的記憶體） B4ms VM 執行 Ubuntu 16.04。

2. 建立 VM 之後，開啟輸入連接埠 22 對 SSH、 21 的 FTP 和 web 伺服器的 9443。

3. 取得上顯示的 SSH 認證**概觀**透過 VM 的刀鋒視窗**Connect**  按鈕。 選取  **SSH**索引標籤上，並將 SSH 登入命令複製到剪貼簿。

4. 登入[Bash 殼層](/azure/cloud-shell/quickstart)從您本機電腦和貼上命令。 它會在表單**ssh\<使用者識別碼\>\@\<IP 位址\>** 。 當系統提示您提供認證，請輸入其連接到主目錄。

## <a name="copy-the-installation-file-to-the-server"></a>將安裝檔案複製到伺服器

Web 伺服器的安裝檔案**ZDT\_安裝\_EE\_V12.0.0.1.tgz**。 它隨附於 IBM 提供媒體。 您必須將此檔案上傳至您的 Ubuntu VM。

1. 從命令列中，輸入下列命令，以確保是最新新建立的映像中的所有項目：

    ```
    sudo apt-get update
    ```

2. 建立安裝目錄：

    ```
    mkdir ZDT
    ```

3. 從本機電腦將檔案複製到 VM:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> 此命令會將安裝檔案複製到 ZDT 目錄會根據您的用戶端是執行 Windows 或 Linux 而有所不同的 「 主 」 目錄中。

## <a name="install-the-enterprise-edition"></a>安裝企業版

1. 移至 ZDT 目錄和解壓縮 ZDT\_安裝\_EE\_V12.0.0.1.tgz 檔案使用下列命令：

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. 執行安裝程式：

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. 選取  **1** 安裝 Enterprise Server。

4. 按下**Enter**和仔細閱讀授權合約。 在 授權結束時，輸入**是**繼續進行。

5. 當系統提示您變更為新建立的使用者，密碼**ibmsys1**，使用命令**sudo passwd ibmsys1**輸入新密碼。

6. 若要確認安裝是否成功輸入

    ```
    dpkg -l | grep zdtapp
    ```

7. 請確認輸出會包含字串**zdtapp 12.0.0.0**，這表示，已成功安裝封裝天然氣

### <a name="starting-enterprise-edition"></a>開始的 Enterprise Edition

請記住，web 伺服器啟動時，執行安裝程序期間所建立的 zD (& T) 的使用者識別碼。

1. 若要啟動 web 伺服器，請使用根使用者識別碼來執行下列命令：

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. 複製 URL 輸出指令碼，如下所示：

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. 將 URL 貼到網頁瀏覽器以開啟 zD & T 安裝管理元件。

## <a name="next-steps"></a>後續步驟

[設定註冊應用程式開發人員控制發佈 (ADCD) 在 IBM zD & T v1](./demo.md)
