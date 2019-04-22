---
title: Micro 焦點企業開發人員 4.0 Azure 虛擬機器上設定 Micro 焦點 CICS BankDemo
description: 在 Azure 虛擬機器 (Vm)，以了解如何使用 Micro Focus Enterprise Server 和企業開發人員執行 Micro 焦點 BankDemo 應用程式。
author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: be94cf0367f93f14249239fce5e09c8635a01136
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58892478"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>在 Azure 上微焦點企業開發人員 4.0 設定 Micro 焦點 CICS BankDemo

當您設定 Micro 焦點 Enterprise Server 4.0 和在 Azure 上的企業開發人員 4.0 時，您可以測試 IBM z/OS 工作負載的部署。 本文說明如何設定 CICS BankDemo，企業開發人員所隨附的範例應用程式。

CICs 代表客戶資訊控制系統，許多線上的大型電腦應用程式所用的交易平台。 BankDemo 應用程式適合用於學習企業伺服器與企業開發人員的運作方式，以及如何管理和部署實際的應用程式完整的綠色螢幕終端機。

## <a name="prerequisites"></a>必要條件

- 使用的 VM[企業開發人員](set-up-micro-focus-azure.md)。 請記住，企業開發人員會有完整執行個體的企業伺服器上進行開發和測試目的。 這是用來示範企業伺服器的執行個體。

- [SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express)。 下載並安裝在 Enterprise Developer VM 上。 企業伺服器需要資料庫的 CICS 區域管理，且 BankDemo 應用程式也會使用名為 BANKDEMO 的 SQL Server 資料庫。 這段示範影片，假設您正在使用這兩個資料庫的 「 SQL Server Express。 安裝時，請選取 基本安裝。

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS)。 SSMS 用來管理資料庫和執行 T-SQL 指令碼中。 下載並安裝在 Enterprise Developer VM 上。

- [Visual Studio 2017](https://azure.microsoft.com/downloads/)最新的 service pack 或[Visual Studio Community](https://visualstudio.microsoft.com/vs/community/)，您可以免費下載。

- Rumba 桌面或另一個 3270 模擬器。

## <a name="configure-the-windows-environment"></a>設定 Windows 環境

在 VM 上安裝企業開發人員 4.0 之後，您必須設定它隨附的 Enterprise Server 的執行個體。 若要這樣做，您需要安裝一些額外的 Windows 功能，如下所示。

1. 您可以使用 RDP 登入您所建立的企業伺服器 4.0 VM。

2. 按一下 **搜尋**旁的圖示**開始**按鈕並輸入**Windows 功能**。 伺服器管理員新增角色及功能精靈 隨即開啟。

3. 選取 **網頁伺服器 (IIS) 角色**，然後核取下列：

    - Web 管理工具
    - IIS 6 管理相容性 （選取所有可用的功能）
    - IIS 管理主控台
    - IIS 管理指令碼及工具
    - IIS 管理服務

4. 選取  **World Wide Web 服務**，並檢查下列項目：

     應用程式開發功能：
    - .NET 擴充性
    - ASP.NET
    - 一般 HTTP 功能：加入所有可用的功能
    - 健全狀況和診斷：加入所有可用的功能
    - 安全性：
        - 基本驗證
        - Windows 驗證

5. 選取  **Windows Process Activation Service**及其所有子系。

6. 針對**功能**，檢查**Microsoft.NET framework 3.5.1**，並檢查下列項目：

    - Windows Communication Foundation HTTP 啟動
    - Windows Communication Foundation 非 HTTP 啟動

7. 針對**功能**，檢查**Microsoft.NET framework 4.6**，並檢查下列項目：

   - 具名的管道啟動
   - TCP 啟動
   - TCP 連接埠共用

     ![新增角色及功能精靈:角色服務](media/01-demo-roles.png)

8. 當您選取所有選項時，按一下**下一步**安裝。

9. 之後的 Windows 功能中，移至**控制台中\>系統及安全性\>系統管理工具**，然後選取**服務**。 向下捲動，並確定下列服務正在執行，並將設定為**自動**:

    - **NetTcpPortSharing**
    - **Net.Pipe Listener Adapter**
    - **Net.tcp 接聽程式配接器**

10. 若要設定 IIS 和 WAS 的支援，請從功能表中找到**Micro 焦點企業開發人員命令提示字元 （64 位元）** 並執行為**管理員**。

11. 型別**wassetup – i**然後按**Enter**。

12. 指令碼執行之後，您可以關閉視窗。

## <a name="configure-the-local-system-account-for-sql-server"></a>設定 SQL Server 的本機系統帳戶

某些企業伺服器處理程序必須要能夠登入 SQL Server，並建立資料庫和其他物件。 這些程序會使用本機系統帳戶，因此您必須將該帳戶提供給系統管理員授權單位。

1. 啟動**SSMS**然後按一下**Connect**連接到本機 SQLEXPRESS 伺服器使用 Windows 驗證。 它應該會出現在**伺服器名稱**清單。

2. 在左側，展開**安全性**資料夾，然後選取**登入**。

3. 選取  **NT AUTHORITY\\系統**，然後選取**屬性**。

4. 選取 **伺服器角色**並查看**sysadmin**。

     ![SSMS 物件總管 視窗中：登入屬性](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>建立 BankDemo 資料庫及其所有物件

1. 開啟**Windows 檔案總管**並瀏覽至**c:\\使用者\\公用\\文件\\Micro Focus\\企業開發人員\\樣本\\大型主機\\CICS\\DotNet\\BankDemo\\SQL**。

2. 複製的內容**BankDemoCreateAll.SQL**檔案到您的剪貼簿。

3. 開啟**SSMS**。 在右側，按一下**伺服器**，然後選取**新的查詢**。

4. 貼上剪貼簿的內容**新的查詢** 方塊中。

5. 按一下 執行 SQL **Execute**從**命令**上述查詢的索引標籤。

未出現任何錯誤，應該執行查詢。 完成時，您會有 BankDemo 應用程式的範例資料庫。

![SQLQuery1.sql output](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>確認已建立的資料庫資料表和物件

1. 以滑鼠右鍵按一下**BANKDEMO**資料庫，然後選取**重新整理**。

2. 依序展開**資料庫**，然後選取**資料表**。 您應該會看到類似下列的內容。

     ![在 [物件總管] 中展開 BANKDEMO 資料表](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>建立企業開發人員應用程式

1. 開啟 Visual Studio 並登入。

2. 底下**檔案** 功能表選項中，選取**開啟專案/方案**，瀏覽至**c:\\使用者\\公用\\文件\\Micro焦點\\企業開發人員\\樣本\\大型主機\\CICS\\DotNet\\BankDemo**，然後選取**sln**檔案。

3. 需要一些時間來檢查的物件。 COBOL 程式會顯示在 [方案總管] 中，CopyBooks (CPY) 以及 JCL CBL 副檔名。

4. 以滑鼠右鍵按一下**BankDemo2**專案，然後選取**設定為啟始專案**。

    > [!NOTE]
    > BankDemo 專案會使用 HCOSS （主應用程式相容性選項適用於 SQL Server），不會用於這段示範影片。

5. 在 **方案總管**，以滑鼠右鍵按一下**BankDemo2**專案，然後選取**建置**。

    > [!NOTE]
    > 建置方案層級會導致錯誤，HCOSS 尚未設定。

6. 建置專案時，檢查**輸出**視窗。 它看起來應該像下列映像。

     ![輸出視窗會顯示建置成功](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>部署至區域資料庫 BankDemo 應用程式

1. 以系統管理員身分開啟企業開發人員命令提示字元 （64 位元）。

2. 瀏覽至 **%公用\\文件\\Micro Focus\\企業開發人員\\範例\\大型主機\\CICS\\DotNet\\BankDemo**。

3. 在命令提示字元中，執行**bankdemodbdeploy**和包含的參數，例如部署資料庫：

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> 請務必使用正斜線 （/） 不是反斜線 (\\)。 此指令碼會執行一段時間。

![系統管理：企業開發人員命令提示字元 視窗](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>在企業系統管理員 for.NET 建立 BankDemo 區域

1. 開啟**Enterprise Server for.NET 管理**UI。

2. 若要啟動 [MMC] 嵌入式管理單元中，從 Windows**開始**功能表上，選擇**Micro 焦點企業開發人員\>組態\>Enterprise Server for.NET 管理**。(適用於 Windows Server 中，選擇**Micro 焦點企業開發人員\>Enterprise Server for.NET 管理**)。

3. 依序展開**地區**中左的窗格，然後以滑鼠右鍵按一下容器**CICS**。

4. 選取 **定義區域**來建立新的 CICS 區域，稱為**BANKDEMO**、 (local) 的資料庫中託管。

5. 提供資料庫伺服器執行個體，請按一下**下一步**，然後輸入 區域名稱**BANKDEMO**。

     ![定義區域對話方塊](media/07-demo-cics.png)

6. 若要選取跨區域資料庫的區域定義檔，找出**地區\_bankdemo\_db.config**中**c:\\使用者\\公用\\文件\\Micro Focus\\企業開發人員\\範例\\大型主機\\CICS\\DotNet\\BankDemo**。

     ![定義區域的區域名稱：BANKDEMO](media/08-demo-cics.png)

7. 按一下 [完成] 。

## <a name="create-xa-resource-definitions"></a>建立 XA 資源定義

1. 在左窗格中**Enterprise Server for.NET 管理**UI 中，展開**系統**，然後**XA 資源定義**。 此設定會定義區域與企業伺服器及應用程式資料庫的交互操作。

2. 以滑鼠右鍵按一下**XA 資源定義**，然後選取**新增的伺服器執行個體**。

3. 在下拉式清單方塊中，選取**資料庫的服務執行個體**。 它會在本機電腦 SQLEXPRESS。

4. 選取下的執行個體**XA 資源定義 (machinename\\sqlexpress)** 容器，然後按一下**新增**。

5. 選取**資料庫 XA 資源定義**，然後輸入**BANKDEMO** for**名稱**並**區域**。

     ![新的資料庫 XA 資源定義畫面](media/09-demo-xa.png)

6. 按一下省略符號 (**...**) 以顯示 [連接字串] 精靈。 針對**伺服器名稱**，型別 **(local)\\SQLEXPRESS**。 針對**登入**，選取**Windows 驗證**。 資料庫名稱 中輸入**BANKDEMO**

     ![編輯連接字串的畫面](media/10-demo-string.png)

7. 測試連線能力。

## <a name="start-the-bankdemo-region"></a>啟動 BANKDEMO 區域

> [!NOTE]
> 第一個步驟是很重要：您必須設定要使用您剛才建立的 XA 資源定義的區域。

1. 瀏覽至**BANDEMO CICS 區域**下方**地區容器**，然後選取**編輯區域啟動檔案**從**動作**窗格。 捲動至 SQL 內容，然後輸入**bankdemo** for **XA 資源名稱**，或使用省略符號來選取它。

2. 按一下 **儲存**圖示以儲存您的變更。

3. 以滑鼠右鍵按一下**BANKDEMO CICS 區域**中**主控台**窗格，然後選取**啟動/停止區域**。

4. 在底部**啟動/停止區域** 方塊中，會出現在中間窗格中，選取**開始**。 幾秒鐘後，會啟動區域。

     ![SQL 啟動/停止方塊](/media/11-demo-sql.png)

     ![CICS 區域 BANKDEMO-開始使用 畫面](media/12-demo-cics.png)

## <a name="create-a-listener"></a>建立接聽程式

您需要針對 TN3270 的工作階段存取 BankDemo 應用程式建立的接聽程式。

1. 在左窗格中，依序展開**組態編輯器**，然後選取**接聽程式**。

2. 按一下 **開啟的檔案**圖示，然後選取**seelistener.exe.config**檔案。 將編輯這個檔案，並每次企業伺服器啟動時載入。

3. 請注意兩個區域，先前定義的 （ESDEMO 和 JCLDEMO）。

4. 若要建立新的區域 BANKDEMO，以滑鼠右鍵按一下**區域**，然後選取**加入區域**。

5. 選取  **BANKDEMO 區域**。

6. 以滑鼠右鍵按一下新增 TN3270 頻道**BANKDEMO 區域**，然後選取**新增頻道**。

7. 針對**名稱**，輸入**TN3270**。 針對**連接埠**，輸入**9024**。 （請注意 ESDEMO 應用程式會使用連接埠 9230，因此您必須使用不同的連接埠）。

8. 若要儲存檔案，按一下**儲存**圖示，或選擇**檔案** \> **儲存**。

9. 若要啟動接聽程式，請按一下**啟動的接聽程式**圖示，或選擇**選項** \> **啟動接聽程式**。

     ![接聽程式組態編輯器視窗](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>設定 Rumba 存取 BankDemo 應用程式

您需要執行最後一件事是設定 3270 工作階段使用 Rumba，3270 模擬器。 此步驟可讓您存取 BankDemo 應用程式透過您剛建立的接聽程式。

1. 從 Windows**啟動**功能表上，啟動 Rumba Desktop。

2. 底下**連線** 功能表項目中，選取**TN3270**。

3. 按一下 **插入**並輸入**127.0.0.1**的 IP 位址和**9024**使用者定義的連接埠。

4. 在對話方塊的底部，按一下**Connect**。 黑色的 CICS 畫面隨即出現。

5. 型別**銀行**顯示初始 3270 畫面 BankDemo 應用程式。

6. 使用者識別碼的輸入**B0001**和密碼，輸入任何內容。 第一個畫面 BANK20 隨即開啟。

![大型主機顯示歡迎使用 畫面](media/14-demo.png)
![大型主機顯示-Rumba-子系統示範畫面](media/15-demo.png)

恭喜！ 您現在已執行的 CICS 應用程式在 Azure 中使用 Micro Focus Enterprise Server。

## <a name="next-steps"></a>後續步驟

- [在 Azure 上執行 Docker 容器中的企業伺服器](run-enterprise-server-container.md)
- [大型主機移轉-入口網站](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [虛擬機器](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [疑難排解](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [揭開大型主機移轉至 Azure 的神秘面紗](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
