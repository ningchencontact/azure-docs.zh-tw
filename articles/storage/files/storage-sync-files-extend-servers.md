---
title: 教學課程：使用 Azure 檔案同步擴充 Windows 檔案伺服器 | Microsoft Docs
description: 了解如何使用 Azure 檔案同步從頭到尾擴充 Windows 檔案伺服器。
services: storage
author: wmgries
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: cc34411cc27870dbd9c707a34ebf34b96c7253dc
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986112"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>教學課程：使用 Azure 檔案同步擴充 Windows 檔案伺服器
在本教學課程中，我們會示範使用 Azure 檔案同步來擴充 Windows Server 儲存體容量的基本步驟。雖然我們會在本教學課程中使用 Windows Server Azure VM，但一般來說，您也可以對內部部署伺服器進行此程序。 如果您已準備好在自己的環境中部署 Azure 檔案同步，請改為使用[部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)一文。

> [!div class="checklist"]
> * 部署儲存體同步服務
> * 準備 Windows Server 以搭配 Azure 檔案同步使用
> * 安裝 Azure 檔案同步代理程式
> * 向儲存體同步服務註冊 Windows Server
> * 建立同步群組和雲端端點
> * 建立伺服器端點

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="sign-in-to-azure"></a>登入 Azure
在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="prepare-your-environment"></a>準備您的環境
在部署 Azure 檔案同步之前，您需要先針對本教學課程進行一些設定。除了建立 Azure 儲存體帳戶和檔案共用外，您還要建立 Windows Server 2016 Datacenter VM，並讓該伺服器準備好進行 Azure 檔案同步。

### <a name="create-a-folder-and-txt-file"></a>建立資料夾和 .txt 檔案

在本機電腦上，建立名為 FilesToSync 的新資料夾，並新增名為 mytestdoc.txt 的文字檔。 在本教學課程稍後的地方，您會將該檔案上傳至檔案共用。

### <a name="create-a-storage-account"></a>建立儲存體帳戶

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>建立檔案共用
接下來，請建立檔案共用。

1. 在 Azure 儲存體帳戶部署完成時，按一下 [前往資源]。
1. 從 [儲存體帳戶] 窗格按一下 [檔案]。

    ![按一下 [檔案]](./media/storage-sync-files-extend-servers/click-files.png)

1. 按一下 [+ 檔案共用]。

    ![按一下 [新增檔案共用] 按鈕](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. 將新的檔案共用命名為 afsfileshare，在 [配額] 中輸入 "1"，然後按一下 [建立]。 配額上限為 5 TiB，但本教學課程只需要 1 GB。

    ![提供新檔案共用的名稱和配額](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. 選取新的檔案共用，然後在檔案共用位置上，按一下 [上傳]。

    ![上傳檔案](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. 瀏覽至 .txt 檔案建立所在的 FilesToSync 資料夾，選取 mytestdoc.txt，然後按一下 [上傳]。

    ![瀏覽檔案共用](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

此時，您已建立 Azure 儲存體帳戶並在 Azure 中建立了具有一個檔案的檔案共用。 現在，您要使用 Windows Server 2016 Datacenter 來建立 Azure VM，以代表本教學課程中的內部部署伺服器。

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>部署 VM 並連結資料磁碟

1. 接下來，展開入口網站左側的功能表，然後在 Azure 入口網站的左上角選擇 [建立資源]。
1. 在 **Azure Marketplace** 資源清單上方的搜尋方塊中，搜尋並選取**Windows Server 2016 Datacenter**，然後選擇 [建立]。
1. 在 [基本概念] 索引標籤的 [專案詳細資料] 之下，選取您為本教學課程建立的資源群組。

   ![在入口網站刀鋒視窗中輸入 VM 的基本資訊](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. 在 [執行個體詳細資料] 底下提供 VM 名稱，例如 myVM。
1. 保留 [區域]、[可用性選項]、[映像] 及 [大小] 的預設設定。
1. 在 [系統管理員帳戶] 底下提供 VM 的 [使用者名稱] 和 [密碼]。
1. 在 [輸入連接埠規則] 底下，選擇 [允許選取的連接埠]，然後從下拉式清單中選取 [RDP (3389)] 和 [HTTP]。

   在建立 VM 之前，您需要先建立資料磁碟。

1. 按 [下一步: 磁碟]

   ![新增資料磁碟](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

1. 在 [磁碟] 索引標籤的 [磁碟選項] 之下，保留預設值。
1. 在 [資料磁碟] 底下，按一下 [建立並連結新磁碟]。

1. 保留預設值，但將 [大小 (GiB)] 變更為 [1 GB] 以進行本教學課程。

   ![資料磁碟詳細資料](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

1. 按一下 [確定]。
1. 按一下 [檢閱 + 建立]。
1. 按一下頁面底部的 [新增] 。

   您可以按一下 [通知] 圖示以監看**部署進度**。 建立新的 VM 需要幾分鐘的時間才能完成。

1. 在 VM 部署完成後，按一下 [前往資源]。

   ![前往資源](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

   此時，您已建立新的虛擬機器，並連結資料磁碟。 現在，您需要連線至 VM。

### <a name="connect-to-your-vm"></a>連接到您的 VM

1. 在 Azure 入口網站中，於虛擬機器的 [屬性] 頁面上按一下 [連線]。

   ![從入口網站連線至 Azure VM](./media/storage-sync-files-extend-servers/connect-vm.png)

1. 在 [連線至虛擬機器] 頁面中，保留以 [IP 位址] 透過連接埠 3389 進行連線的預設選項，然後按一下 [下載 RDP 檔案]。

   ![下載 RDP 檔案](./media/storage-sync-files-extend-servers/download-rdp.png)

1. 開啟下載的 RDP 檔案，然後在出現提示時按一下 [連線]。
1. 在 [Windows 安全性] 視窗中，選取 [更多選擇]，然後選取 [使用不同的帳戶]。 輸入 localhost\username 作為使用者名稱，並輸入您為虛擬機器建立的密碼，然後按一下 [確定]。

   ![更多選擇](./media/storage-sync-files-extend-servers/local-host2.png)

1. 您可能會在登入過程中收到憑證警告。 按一下 [是] 或 [繼續] 以建立連線。

### <a name="prepare-the-windows-server"></a>準備 Windows Server
針對 [Windows Server 2016 Datacenter] 伺服器，停用 [Internet Explorer 增強式安全性設定]。 此步驟只需要在初始伺服器註冊時執行。 您可以在註冊伺服器後重新啟用它。

在 [Windows Server 2016 Datacenter] VM 中，[伺服器管理員] 會自動開啟。  如果 [伺服器管理員] 未依預設開啟，請在 Explorer 中搜尋。

1. 在 [伺服器管理員] 中，按一下 [本機伺服器]。

   ![位於伺服器管理員 UI 左側的 [本機伺服器]](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. 選取 [屬性] 子窗格上的 [IE 增強式安全性設定] 連結。  

    ![位於伺服器管理員 UI 中的 [IE 增強式安全性設定] 窗格](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. 在 [Internet Explorer 增強式安全性設定] 對話方塊中，針對 [系統管理員] 和 [使用者] 選取 [關閉]。

    ![已選取 [關閉] 的 [Internet Explorer 增強式安全性設定] 快顯視窗](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

   現在，您可以在 VM 中新增資料磁碟。

### <a name="add-the-data-disk"></a>新增資料磁碟

1. 在仍位於 [Windows Server 2016 Datacenter] VM 的情況下，按一下 [檔案和儲存體服務] > [磁碟區] > [磁碟]。

    ![資料磁碟](media/storage-sync-files-extend-servers/your-disk.png)

1. 以滑鼠右鍵按一下名為 [Msft 虛擬磁碟] 的 1 GB 磁碟，然後按一下 [新增磁碟區]。
1. 保留預設值來完成精靈，記下指派的磁碟機代號，然後按一下 [建立]。
1. 按一下 [關閉] 。

   此時，您已將磁碟上線，並建立磁碟區。 您可以在 VM 上開啟 Explorer，並確認新的磁碟機已存在，來確認您已成功地新增資料磁碟。

1. 在 VM 上的 Explorer 中，展開 [此電腦]，然後對新的磁碟機按兩下。 在此範例中，其為 F: 磁碟機。
1. 以滑鼠右鍵按一下並選取 [新增] > [資料夾]。 將資料夾命名為 FilesToSync。
1. 對 [FilesToSync] 資料夾按兩下。
1. 以滑鼠右鍵按一下並選取 [新增] > [文字文件]。 將文字檔命名為 MyTestFile。

    ![新增文字檔](media/storage-sync-files-extend-servers/new-file.png)

1. 關閉 **Explorer** 和**伺服器管理員**。

### <a name="download-the-azurerm-powershell-module"></a>下載 AzureRM PowerShell 模組
接下來，在 **Windows Server 2016 Datacenter** VM 中，於伺服器上安裝 **AzureRM PowerShell 模組**。

1. 在 VM 中，開啟已提升權限的 PowerShell 視窗
1. 執行以下命令：

   ```powershell
   Install-Module -Name AzureRM -AllowClobber
   ```

   > [!NOTE]
   > 如果您的 NuGet 版本比 2.8.5.201 舊，系統會提示您下載並安裝最新版的 NuGet。

   根據預設，PowerShell 資源庫未設為 PowerShellGet 的信任存放庫。 第一次使用 PSGallery 時，您會看到下列提示：

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. 請回答 `Yes` 或 `Yes to All` 以繼續安裝。

`AzureRM` 模組是 Azure PowerShell Cmdlet 的彙總套件模組。 安裝此項目會下載所有可用的 Azure Resource Manager 模組，並使這些模組的 Cmdlet 可供使用。

此時，您已完成教學課程的環境設定，並已準備好開始部署**儲存體同步服務**。

## <a name="deploy-the-service"></a>部署服務 
部署 Azure 檔案同步的第一步，是將**儲存體同步服務**資源放到所選訂用帳戶的資源群組中。 儲存體同步服務會從作為其部署目的地的訂用帳戶和資源群組繼承存取權限。

1. 在 Azure 入口網站中，按一下 [建立資源]，然後搜尋 **Azure 檔案同步**。
1. 在搜尋結果中，選取 [Azure 檔案同步]。
1. 選取 [建立] 來開啟 [部署儲存體同步] 索引標籤。

   ![部署儲存體同步](media/storage-sync-files-extend-servers/afs-info.png)

   在開啟的窗格中，輸入下列資訊：

   | 值 | 說明 |
   | ----- | ----- |
   | **名稱** | 儲存體同步服務的唯一名稱 (每一訂用帳戶)。<br><br>在本教學課程中，我們會使用 afssyncservice02。 |
   | **訂用帳戶** | 您將在本教學課程中使用的訂用帳戶。 |
   | **資源群組** | 您將在本教學課程中使用的資源群組。<br><br>在本教學課程中，我們會使用 afsresgroup101918。 |
   | **位置** | 美國東部 |

1. 完成時，請選取 [建立] 來部署**儲存體同步服務**。
1. 按一下 [通知] 索引標籤 > [前往資源]。

## <a name="install-the-agent"></a>安裝代理程式
Azure 檔案同步代理程式是可下載的套件，可讓 Windows Server 能夠和 Azure 檔案共用進行同步處理。

1. 切換回 **Windows Server 2016 Datacenter** VM，然後開啟 **Internet Explorer**。
1. 前往 [Microsoft 下載中心](https://go.microsoft.com/fwlink/?linkid=858257)。 向下捲動至 [Azure 檔案同步代理程式] 區段，然後按一下 [下載]。

   ![同步代理程式下載](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. 核取 [StorageSyncAgent_V3_WS2016.EXE] 的方塊，然後按 [下一步]。

   ![選取代理程式](media/storage-sync-files-extend-servers/select-agent.png)

1. [允許一次] > [執行] > [開啟] 檔案。
1. 如果尚未這麼做，請關閉 PowerShell 視窗。
1. 接受 [儲存體同步代理程式安裝精靈] 中的預設值。
1. 按一下 [Install] 。
1. 按一下 [完成] 。

您已在 **Windows Server 2016 Datacenter** VM 上部署 Azure Sync 服務並安裝代理程式。 現在，您需要使用**儲存體同步服務**來註冊 VM。

## <a name="register-windows-server"></a>註冊 Windows Server
向儲存體同步服務註冊 Windows Server 會在您的伺服器 (或叢集) 與儲存體同步服務之間建立信任關係。 一部伺服器只能向一個儲存體同步服務註冊，但可以與其他關聯至相同儲存體同步服務的伺服器和 Azure 檔案共用同步。

安裝 **Azure 檔案同步代理程式**之後，伺服器註冊 UI 應該會自動開啟。 如果沒有，您可以從其檔案位置手動開啟它：C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe。

1. 當伺服器註冊 UI 在 VM 中開啟時，請按一下 [確定]。
1. 按一下 [登入] 來開始。
1. 使用 Azure 帳戶認證進行登入，然後按一下 [登入]。
1. 請提供下列資訊：

   ![伺服器註冊 UI 的螢幕擷取畫面](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | 值 | 說明 |
   | **Azure 訂用帳戶** | 包含本教學課程所用儲存體同步服務的訂用帳戶。 |
   | **資源群組** | 包含本教學課程所用儲存體同步服務的資源群組。 在這整個教學課程中，我們使用了 afsresgroup101918。 |
   | **儲存體同步服務** | 針對本教學課程所用儲存體同步服務的名稱。 在這整個教學課程中，我們使用了 afssyncservice02。 |

1. 按一下 [註冊] 來完成伺服器的註冊。
1. 在註冊過程中，系統會提示您額外再登入一次。 進行登入，然後按 [下一步]。
1. 按一下 [確定]。

## <a name="create-a-sync-group"></a>建立同步群組
同步群組定義一組檔案的同步拓撲。 同步群組必須包含一個雲端端點，代表 Azure 檔案共用和一個或多個伺服器端點。 伺服器端點代表已註冊伺服器上的路徑。

1. 若要建立同步群組，請在 [Azure 入口網站](https://portal.azure.com/)中，從您為本教學課程所建立的儲存體同步服務選取 [+ 同步群組]。 在本教學課程中，我們使用了 afssyncservice02 作為範例。

   ![在 Azure 入口網站中建立新的同步群組](media/storage-sync-files-extend-servers/add-sync-group.png)

1. 在開啟的窗格中，輸入下列資訊以建立具有雲端端點的同步群組：

   | 值 | 說明 |
   | ----- | ----- |
   | **同步群組名稱** | 此名稱在儲存體同步服務中必須是唯一的，但可以是任何對您而言合理的名稱。 在本教學課程中，我們會使用 afssyncgroup。|
   | **訂用帳戶** | 本教學課程的儲存體同步服務部署所在的訂用帳戶。 |
   | **儲存體帳戶** |按一下 [選取儲存體帳戶]。 在出現的窗格中，選取具有您為本教學課程所建立 Azure 檔案共用的儲存體帳戶。 我們使用了 afsstoracct101918。 |
   | **Azure 檔案共用** | 您為本教學課程所建立 Azure 檔案共用的名稱。 我們使用了 afsfileshare。 |

1. 按一下頁面底部的 [新增] 。

如果您選取同步群組，就會發現您現在擁有一個**雲端端點**。

## <a name="add-a-server-endpoint"></a>新增伺服器端點
伺服器端點代表已註冊伺服器上的特定位置，例如伺服器磁碟區上的資料夾。

1. 若要新增伺服器端點，請選取新建立的同步群組，然後選取 [新增伺服器端點]。

   ![在 [同步群組] 窗格中新增新的伺服器端點](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. 在 [新增伺服器端點] 窗格中，輸入下列資訊以建立伺服器端點：

   | | |
   | ----- | ----- |
   | 值 | 說明 |
   | **已註冊的伺服器** | 您為本教學課程所建立伺服器的名稱。 在本教學課程中，我們使用了 afsvm101918 |
   | **路徑** | 您為本教學課程所建立磁碟機的 Windows Server 路徑。 在我們的範例中，路徑是 f:\filestosync。 |
   | **雲端階層處理** | 在本教學課程中保持停用。 |
   | **磁碟區可用空間** | 在本教學課程中保持空白。 |

1. 按一下頁面底部的 [新增] 。

您的檔案現在會在 Azure 檔案共用和 Windows Server 之間保持同步。

![已成功同步處理 Azure 儲存體](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解使用 Azure 檔案同步來擴充 Windows Server 儲存體容量的基本步驟。若要深入了解如何規劃 Azure 檔案同步的部署，請遵循下列連結。

> [!div class="nextstepaction"]
> [規劃 Azure 檔案同步部署](./storage-sync-files-planning.md)