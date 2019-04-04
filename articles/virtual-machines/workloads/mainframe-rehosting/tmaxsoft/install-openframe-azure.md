---
title: Azure 虛擬機器上安裝 TmaxSoft OpenFrame
description: 重新裝載您 IBM z/OS 主機的工作負載使用 TmaxSoft OpenFrame 環境在 Azure 虛擬機器 (Vm)。
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 6b109f347ee7a917b57acfc56ab4418755295bc5
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896297"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>在 Azure 上安裝 TmaxSoft OpenFrame

了解如何設定 OpenFrame 環境在 Azure 上適用於開發、 示範、 測試或生產環境工作負載。 本教學課程將逐步引導您完成每個步驟。

OpenFrame 包含在 Azure 建立的大型主機模擬環境的多個元件。 比方說，OpenFrame 線上服務來取代的大型主機中介軟體例如 IBM 客戶資訊控制系統 (CICS)，和 OpenFrame 批次中，使用其 TJES 元件，會取代 IBM 大型主機的工作項目子系統 （只要）。

OpenFrame 適用於任何關聯式資料庫，包括 Oracle Database、 Microsoft SQL Server、 IBM Db2 和 MySQL。 這項安裝 OpenFrame 使用 TmaxSoft Tibero 關聯式資料庫。 OpenFrame 和 Tibero 執行 Linux 作業系統上。 本教學課程會安裝 CentOS 7.3，雖然您可以使用其他支援的 Linux 散發套件。OpenFrame 應用程式伺服器和 Tibero 資料庫安裝在一部虛擬機器 (VM) 上。

本教學課程會帶領您逐步 OpenFrame suite 元件的安裝。 部分必須另外安裝。

Main OpenFrame 元件：

- 必要的安裝套件。
- Tibero 資料庫。
- 開放式資料庫連接 (ODBC) 供 OpenFrame 中應用程式中，與 Tibero 資料庫進行通訊。
- 管理整個系統的中介軟體的 OpenFrame 基底。
- OpenFrame 批次，取代了大型主機的批次系統解決方案。
- TACF，控制使用者存取系統和資源服務模組。
- ProSort，批次交易的排序工具。
- OFCOBOL，解譯在大型主機 COBOL 程式的編譯器。
- OFASM，解譯在大型主機組合器程式的編譯器。
- OpenFrame 伺服器類型 C (OSC)，取代了大型主機的中介軟體和 IBM CICS 解決方案。
- Java 企業使用者方案 (JEUS)，已通過認證，Java Enterprise Edition 6 的 web 應用程式伺服器。
- OFGW OpenFrame 閘道元件，可提供 3270 接聽程式。
- OFManager，提供在 web 環境中的 OpenFrame 的作業和管理功能的解決方案。

其他必要 OpenFrame 元件：

- OSI，取代了大型主機中介軟體和 IMS DC 的解決方案。
- TJES，提供在大型主機只要環境的解決方案。
- OFTSAM，解決方案，可讓使用開放系統中的檔案 (V) SAM。
- OFHiDB，取代了大型主機解決方案的 IMS DB。
- OFPLI，解譯在大型主機編譯器的 PL / 我的程式。
- PROTRIEVE，執行大型主機語言 CA Easytrieve 的解決方案。
- OFMiner，解決方案會分析大型主機資產，然後移轉到 Azure。

## <a name="architecture"></a>架構

下圖概述 OpenFrame 7.0 架構安裝的元件在本教學課程：

![OpenFrame 元件](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Azure 的系統需求

下表列出 Azure 上安裝的需求。
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>需求</th><th>描述</th></tr>
</thead>
<tbody>
<tr><td>在 Azure 上支援的 Linux 散發套件
</td>
<td>
Linux x86 （32 位元、 64 位元） 2.6<br/>
Red Hat 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>硬體
</td>
<td>核心：2 （最低要求）<br/>
記憶體：4 GB （最小值）<br/>
交換空間：1GB （最低要求）<br/>
硬碟：100 GB （最小值）<br/>
</td>
</tr>
<tr><td>選用的 Windows 使用者的軟體
</td>
<td>PuTTY:在本指南來設定 VM 的功能<br/>
WinSCP:您可以使用的熱門的 SFTP 用戶端和 FTP 用戶端<br/>
For Windows eclipse:支援的 TmaxSoft 開發平台<br/>
（Microsoft Visual Studio 不支援這一次）
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>必要條件

規劃消費組合所需的軟體，並完成所有手動的程序的幾天。

開始之前，執行下列作業：

- 取得 TmaxSoft OpenFrame 安裝媒體。 如果您是現有的 TmaxSoft 客戶，連絡您 TmaxSoft 代表授權的複本。 否則，要求從試用版[TmaxSoft](http://www.tmaxsoft.com/contact/)。

- 透過傳送電子郵件給要求 OpenFrame 文件<support@tmaxsoft.com>。

- 如果沒有的話，請取得 Azure 訂用帳戶。 您也可以建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)開始之前。

- 選用。 設定站對站 VPN 通道或要允許使用者在組織中的 Azure VM 會限制存取的 jumpbox。 此步驟並非必要，但最佳的作法是。

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>設定 OpenFrame 和 Tibero Azure 上的 VM

您可以設定 OpenFrame 環境中使用各種不同的部署模式，但下列程序示範如何部署 OpenFrame 應用程式伺服器和 Tibero 資料庫在一部 VM 上。 在大型環境中，以及相當的工作負載，最佳做法就是部署在自己的 VM，以提升效能上的個別資料庫。

**若要建立 VM**

1. 移至 Azure 入口網站，網址<http://portal.azure.com>並登入您的帳戶。

2. 按一下 [虛擬機器] 。

    ![在 Azure 入口網站中的 [資源] 清單](media/vm-01.png)

3. 按一下 [新增] 。

    ![在 Azure 入口網站中新增選項](media/vm-02.png)

4. 右邊**作業系統**，按一下**詳細**。

     ![在 Azure 入口網站中的更多選項](media/vm-03.png)

5. 按一下  **CentOS 型 7.3**完全遵循本逐步解說也可以選擇另一個支援的 Linux 散發套件。

     ![在 Azure 入口網站中的作業系統選項](media/vm-04.png)

6. 在 **基本概念**設定中，輸入**名稱**，**使用者名稱**，**驗證類型**，**訂用帳戶**（隨用隨付是付款的 AWS 樣式），並**資源群組**(使用現有的帳戶或建立 TmaxSoft 群組)。

7. 完成時 (包括的公開/私密金鑰組**驗證類型**)，按一下**送出**。

> [!NOTE]
> 如果使用的 SSH 公開金鑰**驗證類型**，請參閱產生公開/私密金鑰組下, 一節的步驟，然後繼續以下步驟。

### <a name="generate-a-publicprivate-key-pair"></a>產生公開/私密金鑰組

如果您使用的 Windows 作業系統，您會需要 PuTTYgen 來產生公開/私密金鑰組。

公開金鑰可以自由地共用，但私密金鑰應該保持完全祕密，且永遠不會共用與另一個合作對象。 之後產生的金鑰，您必須貼**SSH 公用金鑰**在組態中，作用中，將它上傳至 Linux VM。 它會儲存在授權\_中的索引鍵\~/.ssh 目錄的使用者帳戶的主目錄。 在 Linux VM 就能辨識及驗證連接，一旦您提供相關聯**SSH 私密金鑰**SSH 用戶端 （在我們的案例，PuTTY） 中。

當讓新的個人存取的 VM: 

- 每個新的個別產生自己公開/私密金鑰使用 PuTTYgen。
- 個人分別儲存他們自己的私用金鑰，並將公開金鑰資訊傳送至 VM 的系統管理員。
- 系統管理員將公開金鑰的內容貼\~/.ssh/authorized\_金鑰檔案。
- 新的人員會透過 PuTTY 連線。

**若要產生公開/私密金鑰組**

1.  下載從 PuTTYgen<https://www.putty.org/>並將它使用所有預設設定安裝。

2.  若要開啟 PuTTYgen，找出 PuTTY 安裝目錄在 c:\\Program Files\\PuTTY。

    ![PuTTY 介面](media/puttygen-01.png)

3.  按一下 [產生]。

    ![PuTTY 金鑰產生器對話方塊](media/puttygen-02.png)

4.  產生之後, 儲存公開金鑰和私密金鑰。 貼上的內容中的公開金鑰**SSH 公開金鑰**一節**建立虛擬機器\>基本概念**窗格 （步驟 6 和 7 上一節中所示）。

    ![PuTTY 金鑰產生器對話方塊](media/puttygen-03.png)

### <a name="configure-vm-features"></a>設定 VM 的功能

1. 在 Azure 入口網站中**選擇大小**刀鋒視窗中，選擇您想要的 Linux 機器的硬體設定。 *最小*安裝 Tibero 和 OpenFrame 需求是 2 個 Cpu 和 4 GB RAM，在此範例安裝中所示：

    ![建立虛擬機器-基本概念](media/create-vm-01.png)

2. 按一下  **3 個設定**並使用預設設定來設定選用功能。
3. 檢閱您的付款詳細資料。

    ![建立虛擬機器-購買](media/create-vm-02.png)

4. 提交您的選擇。 Azure 會開始部署 VM。 此程序通常需要幾分鐘的時間。

5. 部署 VM 時，會顯示其儀表板，其中顯示未在設定期間選取的所有設定。 請記下的**公用 IP 位址**。

    ![在 Azure 儀表板上的 tmax](media/create-vm-03.png)

6. 開啟 PuTTY。

7. 針對**主機名稱**、 輸入您的使用者名稱和公用 IP 位址，您複製。 例如，**使用者名稱\@publicip**。

    ![PuTTY 組態 對話方塊](media/putty-01.png)

8. 在 **分類**方塊中，按一下**連線\>SSH \> Auth**。提供的路徑您**私密金鑰**檔案。

    ![PuTTY 組態 對話方塊](media/putty-02.png)

9. 按一下 **開啟**啟動 PuTTY 視窗中。 如果成功，您會連線到新的 CentOS VM 在 Azure 上執行。

10. 若要以根使用者身分登入，請輸入**sudo bash**。

    ![在命令視窗中的根使用者登入](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>設定環境和封裝

現在，在建立 VM，而且您已登入，您必須執行一些設定步驟，並安裝所需的預先安裝套件。

1. 將名稱對應**ofdemo**藉由使用 vi 編輯主機檔案的本機 IP 位址 (`vi /etc/hosts`)。 假設我們的 IP 是 192.168.96.148 ofdemo，這是在變更之前：

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     這是在變更後：

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. 建立群組和使用者：

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. 變更使用者 oframe7 的密碼：

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. 更新核心參數 /etc/sysctl.conf 中：

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. 重新整理核心參數，而不重新開機需要：

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. 取得必要的套件：請確定伺服器連線到網際網路，下載下列套件，然後再安裝它們：

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - ncurses

          > [!NOTE]
          > 安裝 ncurses 套件之後，建立下列的符號連結：
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - strace
     - ltrace
     - gdb

7. 如果 Java RPM 安裝中，執行下列作業：

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>安裝 Tibero 資料庫

Tibero 在 Azure 上提供 OpenFrame 環境中的數個關鍵功能：

- Tibero 做 OpenFrame 內部資料存放區中，不同的系統函式。
- VSAM 檔案，包括 KSDS、 RRDS 和 ESDS，在內部使用 Tibero 資料庫來儲存資料。
- TACF 資料存放庫會儲存在 Tibero。
- OpenFrame 目錄資訊會儲存在 Tibero。
- Tibero 資料庫可以用來取代 IBM Db2，來儲存應用程式資料。

**若要安裝 Tibero**

1. 確認存在 Tibero 二進位安裝程式檔案，並檢閱的版本號碼。
2. 將 Tibero 軟體複製到 Tibero 使用者帳戶 (oframe)。 例如︰

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. 開啟.bash\_vi 中的設定檔 (`vi .bash_profile`) 並在其中貼上下列：

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. 若要執行的 bash 設定檔，在命令提示字元中輸入：

    ```
    source .bash_profile
    ```

5. 產生提示檔案 （如 Tibero 組態檔），然後在 vi 中開啟它。 例如︰

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. 修改\$TB\_HOME/client/config/tbdsn.tbr 並改為放 127.0.0.1 oflocalhost 所示：

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. 建立資料庫。 出現下列輸出：

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. 若要回收 Tibero，請先關閉的狀況下使用`tbdown`命令。 例如︰

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. 現在開機 Tibero 使用`tbboot`。 例如︰

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. 若要建立資料表空間，存取使用 SYS 的資料庫使用者 (sys/tmax)，然後建立必要的資料表空間的預設磁碟區和 TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. 現在，請輸入下列 SQL 命令：

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. 開機 Tibero，並確認 Tibero 處理序正在執行：

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

輸出：

![Tibero 輸出](media/tibero-01.png)

## <a name="install-odbc"></a>安裝 ODBC

OpenFrame 中的應用程式與使用開放原始碼 unixODBC 專案所提供的 ODBC API Tibero 資料庫通訊。

若要安裝 ODBC:

1. 確認 unixODBC 2.3.4.tar.gz installer 檔案是否存在，或使用`wget unixODBC-2.3.4.tar.gz`命令。 例如︰

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. 將解壓縮二進位檔。 例如︰

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. 瀏覽至 unixODBC 2.3.4 目錄，並使用檢查電腦的資訊來產生 Makefile。 例如︰

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     根據預設，unixODBC 安裝在 /usr /local 因此`--prefix`傳遞要變更位置的值。 同樣地，組態檔會安裝在 /etc 根據預設，因此`--sysconfdir`傳遞所需位置的值。

4. 執行 Makefile: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. 編譯之後，將可執行檔複製程式目錄中。 例如︰

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. 若要編輯的 bash 設定檔使用 vi (`vi ~/.bash_profile`)，並新增下列：

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. 適用於 ODBC。 據以編輯下列檔案。 例如︰

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. 建立符號連結，並驗證 Tibero 資料庫連線：

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

會顯示下列輸出：

![顯示的 ODBC 輸出連接到 SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>安裝 OpenFrame 基底

基底的應用程式伺服器安裝之前 OpenFrame 使用來管理 Azure，包括處理伺服器的程序的交易之系統的個別服務。

**若要安裝 OpenFrame 基底**

1. 確定 Tibero 安裝成功，然後確認下列 OpenFrame\_Base7\_0\_Linux\_x86\_64.bin 安裝程式檔和 base.properties 組態檔會出現。

2. 更新下列 Tibero 專屬資訊的 bash 設定檔：

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. 執行的 bash 設定檔：`[oframe7@ofdemo ~]$ . .bash_profile`
4. 確定 Tibero 處理序正在執行。 例如︰

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![基本](media/base-01.png)

     > [!IMPORTANT]
     > 請確定您先啟動 Tibero 才安裝。

5. 產生在授權[technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do)和 PUT OpenFrame 基底、 批次、 TACF OSC 授權的適當資料夾中：

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. 下載 OpenFrame 基底的二進位檔和 base.properties 檔案：

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. 執行安裝程式使用 base.properties 檔案。 例如︰

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    完成時，安裝完整的訊息是 diplayed。

8. 確認 OpenFrame 基底目錄結構使用`ls -ltr`命令。 例如︰

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. 啟動 OpenFrame 基底：

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![tmboot 命令輸出](media/base-02.png)

10. 確認處理序狀態已準備好用於 si tmadmin 命令。 RDY 所示**狀態**處理程序的每個資料行：

     ![tmadmin 命令輸出](media/base-03.png)

11. 關閉 OpenFrame 基底：

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>安裝 OpenFrame 批次

OpenFrame 批次模擬大型主機環境，批次的數個元件所組成，用來在 Azure 上執行的批次作業。

**若要安裝批次**

1. 確定基底安裝成功，然後確認 OpenFrame\_Batch7\_0\_Fix2\_MVS\_Linux\_x86\_64.bin 安裝程式檔案和batch.properties 組態檔會出現：

2. 在命令提示字元中，輸入`vi batch.properties`編輯使用 vi batch.properties 檔案。

3. 請修改參數，如下所示：

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. 若要執行批次安裝程式，在命令提示字元中輸入：

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. 安裝完成時，輸入以啟動安裝的 OpenFrame 套件`tmboot`在命令提示字元。

    ![tmboot 輸出](media/tmboot-01.png)

6. 型別`tmadmin`在命令提示字元中，檢查 OpenFrame 程序。

    ![Tmax 管理畫面](media/tmadmin-01.png)

7. 執行下列命令：

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. 使用`tmdown`啟動並關閉 批次的命令：

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>安裝 TACF

控制使用者存取系統與透過 RACF 安全性資源 OpenFrame 服務模組 TACF 管理員。

**若要安裝 TACF**

1. 確認 OpenFrame\_Tacf7\_0\_Fix2\_Linux\_x86\_64.bin 安裝程式檔和 tacf.properties 組態檔會出現。
2. 確定批次安裝成功，然後開啟 tacf.properties 檔案使用 vi (`vi tacf.properties`)。
3. 修改 TACF 參數：

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. 完成之後 TACF 安裝程式，適用於 TACF 環境變數。 在命令提示字元中，輸入：

     ```
     source \~/.bash\_profile
     ```

5. 執行 TACF 安裝程式。 在命令提示字元中，輸入：

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     輸出看起來像這樣：

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. 在命令提示字元中，輸入`tmboot`重新啟動 OpenFrame。 輸出看起來像這樣：

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. 確認處理序狀態已準備好使用`tmadmin`在`si`命令。 例如︰

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     在 **狀態**資料行，RDY 出現：

    ![在 [狀態] 欄中的 RDY](media/tmboot-02.png)

8. 執行下列命令：

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. 關閉伺服器使用`tmdown`命令。 輸出看起來像這樣：

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>安裝 ProSort

ProSort 是批次交易中用於排序資料的公用程式。

**若要安裝 ProSort**

1. 請確定批次安裝成功，然後確認**prosort bin prosort\_2sp3 linux64-2123 opt.tar.gz**安裝程式檔案。

2. 執行安裝程式使用的內容檔案。 在命令提示字元中，輸入：

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. 將 prosort 目錄移到主要位置。 在命令提示字元中，輸入：

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. 建立授權子目錄，然後將複製的授權檔。 例如︰

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. 在 vi 開啟 bash.profile (`vi .bash_profile`) 並將它更新，如下所示：

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. 若要執行的 bash 設定檔，在命令提示字元中，輸入： ` . .bash_profile`

7. 建立設定檔。 例如︰

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. 建立符號連結。 例如︰

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. 藉由執行驗證 ProSort 安裝`prosort -h`命令。 例如︰

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>安裝 OFCOBOL

OFCOBOL 是 OpenFrame 編譯器解譯的大型主機 COBOL 程式。 

**若要安裝 OFCOBOL**

1. 確定批次/線上安裝成功，然後確認 OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin 安裝程式檔案。

2. 若要執行 OFCOBOL 安裝程式，在命令提示字元中，輸入：

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. 閱讀授權合約，然後按 Enter 鍵以繼續。

4. 接受授權合約。 安裝完成時，顯示下列內容：

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. 開啟 bash 設定檔，在 vi (`vi .bash_profile`)，並確認，使用 OFCOBOL 變數更新。
6. 執行的 bash 設定檔。 在命令提示字元中，輸入：

     ```
      source ~/.bash_profile
     ```

7. 將 OFCOBOL 授權複製到安裝資料夾中。 例如︰
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. 移至 OpenFrame tjclrun.conf 組態檔，並在 vi 中開啟它。 例如︰
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   以下是在變更之前 SYSLIB 區段：
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   以下是在變更後的 SYSLIB 區段：
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. 檢閱 OpenFrame\_COBOL\_InstallLog.log vi 檔案，並確認沒有任何錯誤。 例如︰
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. 使用`ofcob --version `命令，並檢閱要驗證安裝的版本號碼。 例如︰

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. 重新啟動使用 OpenFrame`tmdown/tmboot`命令。

## <a name="install-ofasm"></a>安裝 OFASM

OFASM 是 OpenFrame 編譯器解譯大型主機的組合器程式。

**若要安裝 OFASM**

1. 確定批次/線上安裝成功，然後確認 OpenFrame\_ASM3\_0\_Linux\_x86\_64.bin 安裝程式檔案。

2. 執行安裝程式。 例如︰

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. 閱讀授權合約，然後按 Enter 鍵以繼續。
4. 接受授權合約。
5. 請確認 bash 設定檔會更新與 OFASM 變數。 例如︰

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. 開啟 vi OpenFrame tjclrun.conf 組態檔，然後編輯它，如下所示：

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     以下是 [SYSLIB] 區段*之前*變更：

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     以下是 [SYSLIB] 區段*之後*變更：

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. 開啟 OpenFrame\_ASM\_InstallLog.log vi 檔案，並確認沒有任何錯誤。 例如︰

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. 重新開機 OpenFrame，藉由發出下列命令之一：

     ```
     tmdown / tmboot
     ```

     – 或 –

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>安裝 OSC

OSC 是 OpenFrame 環境類似於 IBM CICS 支援高速的 OLTP 交易和其他管理功能。

**若要安裝 OSC**

1. 確定基底安裝成功，然後確認 OpenFrame\_OSC7\_0\_Fix2\_Linux\_x86\_64.bin 安裝程式檔案和 osc.properties 組態檔存在。
2. 編輯 osc.properties 檔案中的下列參數：
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. 執行安裝程式使用的內容檔案，如所示：

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     完成時，會顯示 「 安裝完成 」 訊息。

4. 請確認 bash 設定檔會更新與 OSC 變數。
5. 檢閱 OpenFrame\_OSC7\_0\_Fix2\_InstallLog.log 檔案。 您應該會看到類似下面的畫面：

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. 您可以使用 vi 開啟 ofsys.seq 組態檔。 例如︰

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. 在 \#基底和\#批次區段、 編輯參數，如下所示。

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. 將授權檔案複製。 例如︰

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. 若要啟動及關閉 OSC，請輸入初始化 CICS 區域共用記憶體`osctdlinit OSCOIVP1`在命令提示字元。

10. 執行`oscboot`OSC 開機。 輸出看起來像這樣：

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. 若要確認處理序狀態已就緒，請使用`tmadmin`si 命令。 所有處理序應該會顯示在 RDY**狀態**資料行。

    ![顯示 RDY 的程序](media/tmadmin-02.png)

12. OSC 使用關閉`oscdown`命令。

## <a name="install-jeus"></a>安裝 JEUS

JEUS （Java 企業使用者解決方案） 提供 OpenFrame web 應用程式伺服器的展示層。

安裝 JEUS 之前, 安裝 Apache Ant 套件，可提供的程式庫和命令列安裝 JEUS 所需的工具。

**若要安裝 Apache Ant**

1. 下載 Ant 二進位使用`wget`命令。 例如︰

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. 使用`tar`公用程式來擷取二進位檔案，並將它移至適當的位置。 例如︰

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. 為了提高效率，建立符號連結：

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. 開啟 bash 設定檔，在 vi (`vi .bash_profile`) 和更新含有下列變數：

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  適用於修改過的環境變數。 例如︰

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**若要安裝 JEUS**

1. 展開 安裝程式使用`tar`公用程式。 例如︰

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. 建立**jeus**資料夾 (`mkdir jeus7`) 並解壓縮二進位檔。
3. 若要變更**安裝程式**目錄 （或您自己的環境使用 JEUS 參數）。 例如︰

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. 執行`ant clean-all`然後再執行組建。 輸出看起來像這樣：

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  請網域-組態-template.properties 檔案的備份。 例如︰

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. 開啟 vi 網域-組態-template.properties 檔案：

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. 變更`jeus.password=jeusadmin nodename=Tmaxsoft`至 `jeus.password=tmax1234 nodename=ofdemo`

8. 執行`ant install`命令，以建置 JEUS。
9.  更新.bash\_與 JEUS 變數所示的設定檔：

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. 執行的 bash 設定檔。 例如︰

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *選擇性*。 建立簡單的關機和開機 JEUS 元件的別名：

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. 若要確認安裝，請啟動網域系統管理員伺服器，如所示：

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. 確認所使用之語法的 web 登入：

     ```
     http://<IP>:<port>/webadmin/login
     ```

     比方說，<http://192.168.92.133:9736/webadmin/login.>登入畫面出現：
    
     ![JEUS WebAdmin 登入畫面](media/jeus-01.png)

     > [!NOTE]
     > 如果您遇到任何問題與連接埠安全性時，開啟連接埠 9736 或停用防火牆 (`systemctl stop firewall`)。

14. 若要變更為 server1 的主機名稱，請按一下**鎖定，並編輯**，然後按一下**server1**。 在 [伺服器] 視窗中，將變更主機名稱，如下所示：

    1.  變更**Nodename**要**ofdemo**。
    2.  按一下 **確定**視窗的右側。
    3.  按一下 **套用變更**在左下角的視窗和描述，輸入*主機名稱變更*。

    ![JEUS WebAdmin 畫面](media/jeus-02.png)

15. 請確認設定成功在 [確認] 畫面中。

    ![jeus_domain Server screen](media/jeus-03.png)

16. 啟動受管理的伺服器處理序"server1"使用下列命令：

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>安裝 OFGW

OFGW 是 OpenFrame 閘道支援 3270 終端機模擬器和 OSI base 之間的通訊和管理終端機模擬器和 OSI 之間的工作階段。

**若要安裝 OFGW**

1. 請確定 JEUS 已安裝成功，然後確認 OFGW7\_0\_1\_Generic.bin 安裝程式檔案。
2. 執行安裝程式。 例如︰

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. 使用下列位置之相對應的提示：
     -   JEUS 主目錄
     -   JEUS 網域名稱
     -   JEUS 伺服器名稱
     -   Tibero 驅動程式
     -   Tmax 節點識別碼 ofdemo

4. 接受其餘的預設值，然後按 Enter 以結束安裝程式。

5. 確認如預期般運作 OFGW 的 URL:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     出現下列畫面：

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>安裝 OFManager

OFManager 提供操作和管理功能 OpenFrame web 環境中。

**若要安裝 OFManager**

1. 確認 OFManager7\_Generic.bin 安裝程式檔案。
2. 執行安裝程式。 例如︰

     ```
     OFManager7_Generic.bin
     ```

3.  按下 Enter 以繼續，然後接受授權合約。
4.  選擇安裝資料夾。
5.  接受預設值。
6.  您可以選擇 Tibero 做為資料庫。
7.  按下 Enter 以結束安裝程式。
8.  確認如預期般運作 OFManager 的 URL:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

[開始] 畫面會出現：

![Tmax OpenFrame 管理員登入畫面](media/ofmanager-01.png)

這會完成 OpenFrame 元件的安裝。

## <a name="next-steps"></a>後續步驟

如果您考慮的大型主機移轉，我們不斷擴充的合作夥伴生態系統可幫助您。 如需選擇合作夥伴解決方案的詳細指引，請參閱 [Platform Modernization Alliance](https://www.platformmodernization.org/pages/mainframe.aspx)。

-   [開始使用 Azure](https://docs.microsoft.com/azure/)
-   [主機 Integration Server (HIS) 文件](https://docs.microsoft.com/host-integration-server/)
-   [Azure 虛擬資料中心以及轉移指南](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
