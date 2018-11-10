---
title: 掛接 Avere vFXT - Azure
description: 如何使用 Avere vFXT for Azure 掛接用戶端
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 5d55879a5a8487636e2252abd359accf07e60ce6
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669475"
---
# <a name="mount-the-avere-vfxt-cluster"></a>掛接 Avere vFXT 叢集  

請依照下列步驟將用戶端電腦連線到 vFXT 叢集。

1. 決定如何在您的叢集節點之間進行用戶端流量的負載平衡。 如需詳細資訊，請參閱[平衡用戶端負載](#balance-client-load)。 
1. 識別要掛接的 [IP 位址和連接點路徑](#identify-ip-addresses-and-paths-to-mount)。
1. 使用適當的引數發出[掛接命令](#mount-command-arguments)。

## <a name="balance-client-load"></a>平衡用戶端負載

為了在叢集中的所有節點之間平衡用戶端要求，您應將用戶端掛接至面相用戶端的所有 IP 位址。 有幾個簡單的方式可將這項工作自動化。

> [!TIP] 
> 其他負載平衡方法可能適用於大型或複雜的系統；請[建立支援票證](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)以尋求協助。
> 
> 如果您想要使用 DNS 伺服器以進行自動的伺服器端負載平衡，您必須在 Azure 中設定並管理自己的 DNS 伺服器。 在此情況下，您可以根據下列文件為 vFXT 叢集設定循環配置資源 DNS：[Avere 叢集 DNS 組態](avere-vfxt-configure-dns.md)。

### <a name="sample-balanced-client-mounting-script"></a>平衡的用戶端掛接指令碼範例

此程式碼範例會使用用戶端 IP 位址作為隨機產生的元素，將用戶端分配到所有 vFXT 叢集的可用 IP 位址。

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default 
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,nointr,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi   
} 
```

上述功能是 [Avere vFXT 範例](https://github.com/Azure/Avere#tutorials)網站中可用的 Batch 範例之一。

## <a name="create-the-mount-command"></a>建立掛接命令 

> [!NOTE]
> 如果您在建立 Avere vFXT 叢集時尚未建立新的 Blob 容器，在嘗試掛接用戶端之前，請先依照[設定儲存體](avere-vfxt-add-storage.md)中的步驟操作。

在您的用戶端上，``mount`` 命令會將 vFXT 叢集上的虛擬伺服器 (vserver) 對應至本機檔案系統上的路徑。 其格式為 ``mount <vFXT path> <local path> {options}``

掛接命令有三個元素： 

* vFXT 路徑 - (IP 位址與命名空間連接點路徑的組合，說明[如下](#junction-and-ip-address))
* 本機路徑 - 用戶端上的路徑 
* 掛接命令選項 - (列於[掛接命令引數](#mount-command-arguments)中)

### <a name="junction-and-ip"></a>連接點和 IP

vserver 路徑由其 *IP 位址*和*命名空間連接點*的路徑組合而成。 命名空間連接點是在新增儲存體系統時所定義的虛擬路徑。

如果您的叢集是以 Blob 儲存體建立的，則命名空間路徑為 `/msazure`

範例： ``mount 10.0.0.12:/msazure /mnt/vfxt``

如果您在建立叢集之後新增了儲存體，則命名空間連接點路徑會對應至您在建立連接點時設定於 [命名空間路徑] 中的值。 例如，如果您使用 ``/avere/files`` 作為命名空間路徑，則用戶端會將 *IP_address*:/ avere/files 掛接至其本機掛接點。

![在命名空間路徑欄位中選取了的 /avere/files 的 [新增連接點] 對話方塊](media/avere-vfxt-create-junction-example.png)


該 IP 位址是為 vserver 定義的其中一個面向用戶端的 IP 位址。 您可以在 Avere 控制台中的兩個位置找到面向用戶端的 IP 位址範圍：

* **VServers** 資料表 ([儀表板] 索引標籤) - 

  ![Avere 控制台的 [儀表板] 索引標籤；已在圖形下方的資料表中選取了 [VServer] 索引標籤，並將 [IP 位址] 區段圈選起來](media/avere-vfxt-ip-addresses-dashboard.png)

* **面向用戶端的網路**設定頁面 - 

  ![[設定 > VServer > 面向用戶端的網路組態] 頁面；已將特定 vserver 之資料表的 [位址範圍] 區段圈選起來](media/avere-vfxt-ip-addresses-settings.png)

除了路徑以外，掛接每個用戶端時也請加入[掛接命令引數](#mount-command-arguments)，如下所述。

### <a name="mount-command-arguments"></a>掛接命令引數

若要確保用戶端能順暢地掛接，請在掛接命令中傳入下列設定和引數： 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``


| 必要的設定 | |
--- | --- 
``hard`` | 對 vFXT 叢集執行軟掛接可能會產生應用程式失敗和資料遺失的狀況。 
``proto=netid`` | 此選項支援適當處理 NFS 網路錯誤的功能。
``mountproto=netid`` | 此選項支援在掛接作業中適當處理網路錯誤的功能。
``retry=n`` | 設定 ``retry=30`` 可避免暫時性的掛接失敗。 (執行前景掛接時建議使用不同的值)。

| 慣用設定  | |
--- | --- 
``nointr``            | 選項 "nointr" 適用於採用支援此選項的舊版核心 (2008 年 4 月之前) 的用戶端。 請注意，"intr" 是預設選項。


## <a name="next-steps"></a>後續步驟

掛接用戶端之後，您可以使用它們來填入後端資料儲存體 (核心檔案管理工具)。 請參閱下列文件以深入了解其他設定工作：

* [將資料移至叢集核心檔案管理工具](avere-vfxt-data-ingest.md) - 如何使用多個用戶端和執行緒有效率地上傳您的資料
* [自訂叢集調整](avere-vfxt-tuning.md) - 調整叢集設定以符合您的工作負載
* [管理叢集](avere-vfxt-manage-cluster.md) - 如何啟動或停止叢集以及管理節點