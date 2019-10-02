---
title: Azure VMware Solution by CloudSimple-使用 Azure 資料箱進行遷移
description: 使用 Azure 資料箱將大量資料移轉至 Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5f19b98fbbbad2f43227bfa2f73eab47bf7b1699
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817458"
---
# <a name="migrating-data-to-azure-vmware-solution-by-cloudsimple-using-azure-data-box"></a>使用 Azure 資料箱將資料移轉至 Azure VMware Solution by CloudSimple

Microsoft Azure 資料箱雲端解決方案可讓您以快速、實惠和可靠的方式，將數 TB 的內部部署資料傳送至 Azure。 透過向您寄送專屬的資料箱存放裝置，來加速安全的資料傳輸。 每個存放裝置的最大可用儲存容量為 80 TB，並透過區域性貨運公司運輸至您的資料中心。 該裝置具有堅固的外殼，可在運輸過程中保護裝置與資料。

使用 Azure 資料箱，您可以將大部分的 VMware 資料移轉至私人雲端。  內部部署 vSphere 環境中的資料會使用 NFS 通訊協定複製到資料箱。  大量資料移轉牽涉到將虛擬機器、設定和相關聯資料的時間點複本複製到資料箱，並將其傳送至 Azure。

在本文中，您將瞭解：

* 設定 Azure 資料箱。
* 使用 NFS 將資料從內部部署 VMware 環境複製到資料箱。
* 準備傳回 Azure 資料箱。
* 準備 blob 資料，以供 CloudSimple 複製到 Azure VMware 解決方案。
* 將資料從 Azure 複製到您的私人雲端。

## <a name="scenarios"></a>案例

Azure 資料箱應用於大量資料移轉的下列案例中。

* 透過 CloudSimple 將大量資料從內部部署遷移至 Azure VMware 解決方案，以作為基準並透過網路同步差異。
* 遷移大量電源關閉的虛擬機器（冷虛擬機器）。
* 遷移虛擬機器資料，以設定開發和測試環境。
* 遷移大量的虛擬機器範本、Iso、虛擬機器磁片。

## <a name="before-you-begin"></a>開始之前

* 從您的 Azure 入口網站檢查必要條件和[順序資料箱](../databox/data-box-deploy-ordered.md)。  在訂單程式期間，您必須選取可允許 BLOB 儲存體的儲存體帳戶。  當您收到資料箱之後，請將它連線到您的內部部署網路，並使用 vSphere 管理網路可連線的 IP 位址來[設定資料箱](../databox/data-box-deploy-set-up.md)。

* 在 Azure 的相同區域中建立虛擬網路和儲存體帳戶，並在其中布建您的 Azure VMware solution by CloudSimple。

* 使用[使用 ExpressRoute 將 azure 虛擬網路連線至 CloudSimple](virtual-network-connection.md)一文中所述的步驟，建立從私人雲端到儲存體帳戶建立的[azure 虛擬網路](cloudsimple-azure-network-connection.md)連線

## <a name="set-up-azure-data-box-for-nfs"></a>設定 NFS 的 Azure 資料箱

使用 [Tutorial 一文中的**連接到您的裝置**一節中所述的步驟，連接到您的 Azure 資料箱本機 web UI：纜線並連接到您的 Azure 資料箱 @ no__t-0。  設定資料箱以允許存取 NFS 用戶端。

1. 在本機 Web UI 中，移至 [連線並複製] 頁面。 在 [NFS 設定] 下方，按一下 [NFS 用戶端存取]。 

    ![設定 NFS 用戶端存取 1](media/nfs-client-access.png)

2. 輸入 VMware ESXi 主機的 IP 位址，然後按一下 [**新增**]。 您可以重複此步驟，以設定 vSphere 叢集中所有主機的存取權。 按一下 [確定]。

    ![設定 NFS 用戶端存取 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **一律針對您想複製的檔案，在共用之下建立一個資料夾，然後將檔案複製到該資料夾**。 在區塊 Blob 和分頁 Blob 底下建立的資料夾，代表資料以 Blob 形式上傳至的容器。 您無法將檔案直接複製到儲存體帳戶中的 [root] 資料夾。

在區塊 Blob 與分頁 Blob 共用底下，第一層實體是容器，而第二層實體是 Blob。 在 Azure 檔案共用底下，第一層實體是共用，第二層實體是檔案。

下表顯示您資料箱上的共用 UNC 路徑，以及用於上傳資料的 Azure 儲存體路徑 URL。 最終的 Azure 儲存體路徑 URL 可以衍生自 UNC 共用路徑。
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure 區塊 Blob | <li>共用的 UNC 路徑：`//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 分頁 Blob  | <li>共用的 UNC 路徑：`//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 檔案儲存體       |<li>共用的 UNC 路徑：`//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> 使用 Azure 區塊 Blob 來複製 VMware 資料。

## <a name="mount-nfs-share-as-a-datastore-on-on-premises-vcenter-cluster-and-copy-data"></a>將 NFS 共用掛接為內部部署 vCenter 叢集上的資料存放區並複製資料

您的 Azure 資料箱中的 NFS 共用必須掛接為內部部署 vCenter 叢集或 VMware ESXi 主機上的資料存放區，才能複製資料。  掛接之後，您可以將資料複製到 NFS 資料存放區。

1. 登入您的內部部署 vCenter 伺服器。

2. 以滑鼠右鍵按一下**資料中心**，選取 [儲存體] [新增] 按一下 [**新資料**存放**區** **]** ，然後按一下

   ![加入新的資料存放區](media/databox-migration-add-datastore.png)

3. 在 [新增資料存放區] 的步驟1中，選取 [ **NFS**類型]。

   ![加入新的資料存放區類型](media/databox-migration-add-datastore-type.png)

4. 在步驟2中，選取 [ **nfs 3**為 Nfs 版本]，然後按 **[下一步]** 。

   ![加入新的資料存放區-NFS 版本](media/databox-migration-add-datastore-nfs-version.png)

5. 在步驟3中，指定資料存放區的名稱、路徑和伺服器。  您可以使用伺服器資料箱的 IP 位址。  資料夾路徑的格式會是 `/<StorageAccountName_BlockBlob>/<ContainerName>/`。

   ![加入新的資料存放區-NFS 設定](media/databox-migration-add-datastore-nfs-configuration.png)

6. 在步驟4中，選取您想要裝載資料存放區的 ESXi 主機，然後按 **[下一步]** 。  在叢集中，選取 [所有主機] 以確保虛擬機器的遷移。

   ![加入新的資料存放區-選取主機](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 在步驟5中，檢查 [摘要]，然後按一下 **[完成]**

## <a name="copy-data-to-data-box-nfs-datastore"></a>將資料複製到資料箱 NFS 資料存放區

虛擬機器可以遷移或複製到新的資料存放區。  任何需要遷移的未使用虛擬機器，都可以使用 storage vMotion 遷移到資料箱 NFS 資料**存放區**。  Active 虛擬機器可以**複製**到資料箱 NFS 資料存放區。

* 識別可**移動**的虛擬機器清單。
* 識別必須**複製**的虛擬機器清單。

### <a name="move-virtual-machine-to-data-box-datastore"></a>將虛擬機器移至資料箱資料存放區

1. 以滑鼠右鍵按一下您想要移至資料箱資料存放區的虛擬機器，然後選取 [**遷移**]。

    ![遷移虛擬機器](media/databox-migration-vm-migrate.png)

2. 選取 [只針對遷移類型**變更儲存體**]，然後按 **[下一步]** 。

    ![僅遷移虛擬機器-儲存體](media/databox-migration-vm-migrate-change-storage.png)

3. 選取資料箱資料存放區作為目的地，然後按 **[下一步]** 。

    ![遷移虛擬機器-選取資料存放區](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. 檢查資訊，然後按一下 **[完成]** 。

5. 針對其他虛擬機器，重複步驟1到4。

> [!TIP]
> 您可以選取處於相同電源狀態的多部虛擬機器（開啟電源或關閉電源），並大量進行遷移。

虛擬機器的儲存體會從 Azure 資料箱遷移至 NFS 資料存放區。  遷移所有虛擬機器之後，您可以關閉已啟動的虛擬機器，以準備將資料移轉至 Azure VMware 解決方案。

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-data-box-datastore"></a>將虛擬機器或虛擬機器範本複製到資料箱資料存放區

1. 以滑鼠右鍵按一下您想要複製的虛擬機器或虛擬機器範本。  選取 [**複製**]、[**複製到虛擬機器**]

    ![虛擬機器複製](media/databox-migration-vm-clone.png)

2. 選取複製的虛擬機器或虛擬機器範本的名稱。

3. 選取您要放置複製物件的資料夾，然後按 **[下一步]** 。

4. 選取您要複製的物件所在的叢集或資源集區，然後按 **[下一步]** 。

5. 選取 [Azure 資料箱 NFS 資料存放區] 做為儲存位置，然後按 **[下一步]** 。

    ![虛擬機器複製-選取資料存放區](media/databox-migration-vm-clone-select-datastore.png)

6. 如果您想要自訂複製物件的任何選項，請選取 [自訂] 選項，然後按 **[下一步]** 。

7. 檢查設定，然後按一下 **[完成]** 。

8. 針對其他虛擬機器或虛擬機器範本，重複步驟1到7。

虛擬機器將會從 Azure 資料箱複製並儲存在 NFS 資料存放區上。  複製虛擬機器之後，請確定複製的虛擬機器已關閉電源，以準備將資料移轉至 Azure VMware 解決方案。

### <a name="copy-iso-files-to-data-box-datastore"></a>將 ISO 檔案複製到資料箱資料存放區

1. 從您的內部部署 vCenter web UI，流覽至 [**儲存體**]。  選取 [資料箱 NFS 資料存放區]，然後按一下 [檔案 **]。**  建立用來儲存 ISO 檔案的**新資料夾**。

    ![複製 ISO-建立新資料夾](media/databox-migration-create-folder.png)

2. 為將儲存 ISO 檔案的資料夾提供名稱。

3. 按兩下新建立的資料夾以存取內容。

4. 按一下 **[上傳**檔案]，然後選取您想要上傳的 iso。
    
    ![複製 ISO-上傳檔案](media/databox-migration-upload-iso.png)

> [!TIP]
> 如果您的內部部署資料存放區已經有 ISO 檔案，您可以選取檔案並**複製到**資料箱 NFS 資料存放區。


## <a name="prepare-azure-data-box-for-return"></a>準備要傳回的 Azure 資料箱

一旦所有虛擬機器資料、虛擬機器範本資料和任何 ISO 檔案都複製到資料箱 NFS 資料存放區，資料存放區就可以與您的 vCenter 中斷連線。  所有虛擬機器和虛擬機器範本都必須從清查中移除，然後才能中斷資料存放區的連線。

### <a name="remove-objects-from-inventory"></a>從清查移除物件

1. 從您的內部部署 vCenter web UI，流覽至 [**儲存體**]。  選取 [資料箱 NFS 資料存放區]，然後按一下 [ **vm**]。

2. 確定所有虛擬機器都已關閉電源。

    ![從清查中移除虛擬機器-關閉電源](media/databox-migration-select-databox-vm.png)

3. 選取 [所有虛擬機器]，按一下滑鼠右鍵，然後選取 [**從清查移除**]。

    ![從清查移除虛擬機器](media/databox-migration-remove-vm-from-inventory.png)

4. 從頂端按鈕選取 [**資料夾中的 VM 範本**]，然後重複步驟3。 

### <a name="remove-azure-data-box-nfs-datastore-from-vcenter"></a>從 vCenter 移除 Azure 資料箱 NFS 資料存放區

在準備傳回之前，資料箱 NFS 資料存放區必須與 VMware ESXi 主機中斷連線。

1. 從您的內部部署 vCenter web UI，流覽至 [**儲存體**]。

2. 以滑鼠右鍵按一下資料箱 NFS 資料存放區，然後選取 [**卸載資料**存放區]。

    ![卸載資料箱資料存放區](media/databox-migration-unmount-datastore.png)

3. 選取裝載資料存放區的所有 ESXi 主機，然後按一下 **[確定]** 。

    ![卸載資料箱資料存放區-選取主機](media/databox-migration-unmount-datastore-select-hosts.png)

4. 請檢查並接受任何警告，然後按一下 **[確定]** 。

### <a name="prepare-data-box-for-return-and-return-the-data-box"></a>準備傳回的資料箱，並傳回資料箱

遵循傳回 Azure 資料箱一文中所述的步驟[，並確認資料上傳至 Azure](../databox/data-box-deploy-picked-up.md)以傳回資料箱。  檢查將資料複製到 Azure 儲存體帳戶的狀態，一旦狀態顯示為 [已完成]，您就可以驗證 Azure 儲存體帳戶中的資料。

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution-by-cloudsimple"></a>將資料從 Azure 儲存體複製到 Azure VMware Solution by CloudSimple

當您的資料箱的訂單狀態顯示為 [已完成] 時，您的 Azure 儲存體帳戶上就會提供複製到您 Azure 資料箱的資料。  資料現在可以透過 CloudSimple 複製到您的 Azure VMware 解決方案。  儲存體帳戶中的資料必須使用 NFS 通訊協定複製到私人雲端的 vSAN 資料存放區。  首先，使用**AzCopy**，將 blob 存放區資料複製到 Azure 中 Linux 虛擬機器上的受控磁片。  透過 NFS 通訊協定讓受控磁片可供使用，並將 NFS 共用掛接為私人雲端上的資料存放區，並複製資料。  這個方法可讓您更快速地將資料複製到您的私人雲端。 

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-export-as-nfs-share"></a>使用 Linux 虛擬機器和受控磁片將資料複製到私人雲端，並匯出為 NFS 共用

1. 在您的儲存體帳戶建立所在的相同區域中，于 Azure 中建立[Linux 虛擬機器](../virtual-machines/linux/quick-create-portal.md)，並將 azure 虛擬網路連線到您的私人雲端。

2. 建立受控磁片，其大於 blob 資料量，並[將其附加至您的 Linux 虛擬機器](../virtual-machines/linux/attach-disk-portal.md)。  如果 blob 資料量大於可用的最大受控磁片，則必須以多個步驟或使用多個受控磁片來複製資料。

3. 連接到 Linux 虛擬機器並掛接受控磁片。

4. [在您的 Linux 虛擬機器上安裝 AzCopy](../storage/common/storage-use-azcopy-v10.md)。

5. 使用 AzCopy，將資料從您的 Azure blob 儲存體下載至受控磁片。  命令語法： `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`。  以您的 Azure 儲存體帳戶名稱取代 `<storage-account-name>`，並使用容器 `<container-name>`，其中包含使用 Azure 資料箱複製的資料。

6. 在您的 linux 虛擬機器上安裝 NFS 伺服器。

    1. 在 Ubuntu/Debian 發佈上： `sudo apt install nfs-kernel-server`。
    2. 在 Enterprise Linux 散發套件上： `sudo yum install nfs-utils`。

7. 變更受控磁片上的資料夾許可權，其中 Azure blob 存放區的資料已複製到該處。  針對您想要匯出為 NFS 共用的所有資料夾，變更其許可權。

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. 藉由編輯 `/etc/exports` 檔案，將用戶端 IP 位址的許可權指派給存取 NFS 共用。

    ```bash
    sudo vi /etc/exports
    ```
    
    針對私人雲端的每個 ESXi 主機 IP，在檔案中輸入下列幾行。  如果您要建立多個資料夾的共用，請新增所有資料夾。

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. 使用命令 `sudo exportfs -a` 匯出 NFS 共用。

10. 使用命令 `sudo systemctl restart nfs-kernel-server` 重新開機 NFS 核心伺服器。


### <a name="mount-linux-virtual-machine-nfs-share-as-a-datastore-on-private-cloud-vcenter-cluster-and-copy-data"></a>將 Linux 虛擬機器 NFS 共用掛接為私人雲端 vCenter 叢集上的資料存放區並複製資料

您必須將來自 Linux 虛擬機器的 NFS 共用掛接為私人雲端 vCenter 叢集上的資料存放區，才能複製資料。  掛接之後，您可以將資料從 NFS 資料存放區複製到私人雲端 vSAN 資料存放區。

1. 登入您的私用雲端 vCenter server。

2. 以滑鼠右鍵按一下**資料中心**，選取 [儲存體] [新增] 按一下 [**新資料**存放**區** **]** ，然後按一下

   ![加入新的資料存放區](media/databox-migration-add-datastore.png)

3. 在 [新增資料存放區] 的步驟1中，選取 [ **NFS**類型]。

   ![加入新的資料存放區類型](media/databox-migration-add-datastore-type.png)

4. 在步驟2中，選取 [ **nfs 3**為 Nfs 版本]，然後按 **[下一步]** 。

   ![加入新的資料存放區-NFS 版本](media/databox-migration-add-datastore-nfs-version.png)

5. 在步驟3中，指定資料存放區的名稱、路徑和伺服器。  您可以針對伺服器使用 Linux 虛擬機器的 IP 位址。  資料夾路徑的格式會是 `/<folder>/<subfolder>/`。

   ![加入新的資料存放區-NFS 設定](media/databox-migration-add-datastore-nfs-configuration.png)

6. 在步驟4中，選取您想要裝載資料存放區的 ESXi 主機，然後按 **[下一步]** 。  在叢集中，選取 [所有主機] 以確保虛擬機器的遷移。

   ![加入新的資料存放區-選取主機](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 在步驟5中，檢查 [摘要]，然後按一下 **[完成]**

### <a name="add-virtual-machines-and-virtual-machine-templates-from-nfs-datastore-to-the-inventory"></a>從 NFS 資料存放區將虛擬機器和虛擬機器範本新增至清查

1. 從您的私用雲端 vCenter web UI，流覽至 [**儲存體**]。  選取 [Linux] [虛擬機器 NFS 資料存放區]，然後按一下 [檔案 **]。**

    ![從 NFS 資料存放區選取檔案](media/databox-migration-datastore-select-files.png)

2. 選取包含虛擬機器或虛擬機器範本的資料夾。  從 [詳細資料] 窗格中，選取虛擬機器的 `.vmx` 檔案或虛擬機器範本的 @no__t 1 檔案。

3. 按一下 [**註冊 VM** ]，在您的私用雲端 vCenter 上註冊虛擬機器。

    ![註冊虛擬機器](media/databox-migration-datastore-register-vm.png)

4. 選取您要在其中註冊虛擬機器的資料中心、資料夾和叢集/資源集區。

4. 針對所有虛擬機器和虛擬機器範本重複步驟3和4。

5. 流覽至包含 ISO 檔案的資料夾。  選取 [ISO 檔案]，並**複製到**您 vSAN 資料存放區上的資料夾。

虛擬機器和虛擬機器範本現在可在您的私用雲端 vCenter 上取得。  這些虛擬機器必須先從 NFS 資料存放區移至 vSAN 資料存放區，才能開啟它們。  您可以執行虛擬機器的儲存體 vMotion，並選取 vSAN 資料存放區作為虛擬機器的目標。

虛擬機器範本必須從您的 Linux 虛擬機器 NFS 資料存放區複製到 vSAN 資料存放區。

### <a name="clean-up-of-your-linux-virtual-machine"></a>清除 Linux 虛擬機器

將所有資料複製到您的私人雲端之後，您就可以從私人雲端移除 NFS 資料存放區。

1. 請確定所有虛擬機器和範本都已移動並複製到您的 vSAN 資料存放區。

2. 從清查移除 NFS 資料存放區中的所有虛擬機器範本。

3. 從您的私用雲端 vCenter 卸載 Linux 虛擬機器資料存放區。

4. 從 Azure 刪除虛擬機器和受控磁片。

5. 如果您不想要使用儲存體帳戶上的 Azure 資料箱來保留傳輸的資料，請刪除 Azure 儲存體帳戶。  
    


## <a name="next-steps"></a>後續步驟

* 深入瞭解[Azure 資料箱](../databox/data-box-overview.md)
* 深入瞭解[將工作負載遷移至私用雲端](migrate-workloads.md)的不同選項
