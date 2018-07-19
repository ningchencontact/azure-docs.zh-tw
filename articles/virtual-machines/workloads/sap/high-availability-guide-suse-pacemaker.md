---
title: 在 Azure 中於 SUSE Linux Enterprise Server 上設定 Pacemaker | Microsoft Docs
description: 在 Azure 中於 SUSE Linux Enterprise Server 上設定 Pacemaker
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: sedusch
ms.openlocfilehash: cac2f91a25907be824e3fd3517736d921c3fde64
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "37923426"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>在 Azure 中於 SUSE Linux Enterprise Server 上設定 Pacemaker

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#memory-preserving-maintenance
[virtual-machines-windows-maintenance]:../../windows/maintenance-and-updates.md#memory-preserving-maintenance

有兩個選項可以在 Azure 中設定 Pacemaker 叢集。 您可以使用隔離代理程式，這個代理程式會透過 Azure API 來重新啟動失敗的節點，或者您可以使用 SBD 裝置。

SBD 裝置需要一個額外的虛擬機器，作為 iSCSI 目標伺服器並且提供 SBD 裝置。 不過，這個 iSCSI 目標伺服器可以與其他 Pacemaker 叢集共用。 使用 SBD 裝置的優點是容錯移轉的時間更快，而如果您是在內部部署環境中使用 SBD 裝置，您操作 Pacemaker 叢集的方式不需要任何改變。 SBD 隔離仍然可以使用 Azure Fence 代理程式，作為 iSCSI 目標伺服器無法使用時的備份隔離機制。

如果您不想要投資一部額外的虛擬機器，您也可以使用 Azure Fence 代理程式。 缺點是如果資源停止容錯或叢集節點無法再彼此進行通訊，則容錯移轉需要 10 到 15 分鐘。

![SLES 上的 Pacemaker 概觀](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

## <a name="sbd-fencing"></a>SBD 隔離

如果您想要使用 SBD 裝置進行隔離，請遵循以下步驟。

### <a name="set-up-an-iscsi-target-server"></a>設定 iSCSI 目標伺服器

如果您還沒有 iSCSI 目標虛擬機器，您必須先加以建立。 iSCSI 目標伺服器可以與多個 Pacemaker 叢集共用。

1. 部署新的 SLES 12 SP1 或更新版本的虛擬機器，並且透過 SSH 連線到機器。 此機器不需要是大型機器。 諸如 Standard_E2s_v3 或 Standard_D2s_v3 的虛擬機器大小就已足夠。

1. 更新 SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. 移除套件

   若要避免 targetcli 和 SLES 12 SP3 的已知的問題，請解除安裝下列套件。 您可以忽略與找不到套件有關的錯誤
   
   <pre><code>
   sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>
   
1. 安裝 iSCSI 目標套件

   <pre><code>
   sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. 啟用 iSCSI 目標服務

   <pre><code>   
   sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>在 iSCSI 目標伺服器上建立 iSCSI 裝置

將新的資料磁碟連結至可用於此叢集的 iSCSI 目標虛擬機器。 資料磁碟可以小到 1 GB，且必須放在進階儲存體帳戶或進階受控磁碟上，才能受益於[單一虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)。

在 **iSCSI 目標虛擬機器**上執行下列命令，為新的叢集建立 iSCSI 磁碟。 在下列範例中，**cl1** 用來識別新的叢集，而 **prod-cl1-0** 和 **prod-cl1-1** 是叢集節點的主機名稱。 使用您的叢集節點主機名稱取代它們。

<pre><code>
# List all data disks with the following command
sudo ls -al /dev/disk/azure/scsi1/

# total 0
# drwxr-xr-x 2 root root  80 Mar 26 14:42 .
# drwxr-xr-x 3 root root 160 Mar 26 14:42 ..
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun0 -> ../../../<b>sdc</b>
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun1 -> ../../../sdd

# Then use the disk name to list the disk id
sudo ls -l /dev/disk/by-id/scsi-* | grep sdc

# lrwxrwxrwx 1 root root  9 Mar 26 14:42 /dev/disk/by-id/scsi-14d53465420202020a50923c92babda40974bef49ae8828f0 -> ../../sdc
# lrwxrwxrwx 1 root root  9 Mar 26 14:42 <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0 -> ../../sdc</b>

# Use the data disk that you attached for this cluster to create a new backstore
sudo targetcli backstores/block create <b>cl1</b> <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0</b>

sudo targetcli iscsi/ create iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/luns/ create /backstores/block/<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-0.local:prod-cl1-0</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-1.local:prod-cl1-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

### <a name="set-up-sbd-device"></a>設定 SBD 裝置

從叢集連線到上一個步驟中建立的 iSCSI 裝置。
在您想要建立之新叢集的節點上，執行下列命令。
下列項目會加上下列其中一個前置詞：**[A]** - 適用於所有節點、**[1]** - 僅適用於節點 1 或 **[2]** - 僅適用於節點 2。

1. **[A]** 連線到 iSCSI 裝置

   首先，啟用 iSCSI 和 SBD 服務。

   <pre><code>
   sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** 變更第一個節點上的啟動器名稱

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   變更檔案內容，以符合您在 iSCSI 目標伺服器上建立 iSCSI 裝置時使用的 ACL

   <pre><code>   
   InitiatorName=<b>iqn.2006-04.prod-cl1-0.local:prod-cl1-0</b>
   </code></pre>

1. **[2]** 變更第二個節點上的啟動器名稱

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   變更檔案內容，以符合您在 iSCSI 目標伺服器上建立 iSCSI 裝置時使用的 ACL

   <pre><code>
   InitiatorName=<b>iqn.2006-04.prod-cl1-1.local:prod-cl1-1</b>
   </code></pre>

1. **[A]** 重新啟動 iSCSI 服務

   立即重新啟動 iSCSI 服務以套用變更
   
   <pre><code>
   sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   連線到 iSCSI 裝置。 在下列範例中，iSCSI 目標伺服器的 IP 位址是 10.0.0.17，預設連接埠是 3260。 <b>iqn.2006-04.cl1.local:cl1</b> 是當您執行第一個命令時列出的目標名稱。

   <pre><code>
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>
   
   sudo iscsiadm -m node -T <b>iqn.2006-04.cl1.local:cl1</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   請確定 iSCSI 裝置可以使用，並且記下裝置名稱 (在下列範例中為 /dev/sde)

   <pre><code>
   lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  cl1              4.0   /dev/sde</b>
   </code></pre>

   現在，擷取 iSCSI 裝置的識別碼。

   <pre><code>
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   </code></pre>

   此命令會列出三個裝置識別碼。 我們建議使用以 scsi-3 開頭的識別碼，在上述範例中即為
   
   **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**

1. **[1]** 建立 SBD 裝置

   使用 iSCSI 裝置的裝置識別碼，在第一個叢集節點上建立新的 SBD 裝置。

   <pre><code>
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 10 -4 20 create
   </code></pre>

1. **[A]** 調整 SBD 組態

   開啟 SBD 組態檔

   <pre><code>
   sudo vi /etc/sysconfig/sbd
   </code></pre>

   變更 SBD 裝置的屬性，啟用 Pacemaker 整合，並且變更 SBD 的啟動模式。

   <pre><code>
   [...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   </code></pre>

   建立 softdog 組態檔

   <pre><code>
   echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   立即載入模組

   <pre><code>
   sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>叢集安裝

下列項目會加上下列其中一個前置詞：**[A]** - 適用於所有節點、**[1]** - 僅適用於節點 1 或 **[2]** - 僅適用於節點 2。

1. **[A]** 更新 SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]** 啟用 SSH 存取

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

2. **[2]** 啟用 SSH 存取

   <pre><code>
   sudo ssh-keygen
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** 啟用 SSH 存取

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** 安裝柵欄代理程式
   
   <pre><code>
   sudo zypper install fence-agents
   </code></pre>

1. **[A]** 設定主機名稱解析   

   您可以使用 DNS 伺服器，或修改所有節點上的 /etc/hosts。 這個範例示範如何使用 /etc/hosts 檔案。
   請取代下列命令中的 IP 位址和主機名稱。 使用 /etc/hosts 的好處在於，您的叢集會變成不受 DNS 影響，而 DNS 也可能是單一失敗點。

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   將下列幾行插入至 /etc/hosts。 變更 IP 位址和主機名稱以符合您的環境   
   
   <pre><code>
   # IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** 安裝叢集
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> Press ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> Press ENTER
   # Multicast port [5405] -> Press ENTER
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]** 將節點新增至叢集
   
   <pre><code> 
   sudo ha-cluster-join
   
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.14
   # /root/.ssh/id_rsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]** 將 hacluster 密碼變更為同一個密碼

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]** 設定 corosync 以使用其他傳輸，並新增節點清單。 否則叢集無法運作。
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   將下列粗體內容新增至檔案 (如果檔案中沒有這些值或不同)。 請務必將權杖變更為 30000，以允許記憶體保留維護。 如需詳細資料，請參閱[本 Linux 文章][virtual-machines-linux-maintenance]或[本 Windows 文章][virtual-machines-windows-maintenance]。
   
   <pre><code> 
   [...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      6000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   然後重新啟動 corosync 服務

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[1]** 變更 Pacemaker 預設設定

   <pre><code>
   sudo crm configure rsc_defaults resource-stickiness="1"   
   </code></pre>

## <a name="create-stonith-device"></a>建立 STONITH 裝置

STONITH 裝置會使用服務主體來對 Microsoft Azure 授權。 請遵循下列步驟來建立服務主體。

1. 移至 <https://portal.azure.com>。
1. 開啟 [Azure Active Directory] 刀鋒視窗  
   移至 [屬性]，並記下目錄識別碼。 這是「租用戶識別碼」。
1. 按一下 [應用程式註冊]
1. 按一下 [新增]
1. 輸入名稱、選取應用程式類型 [Web 應用程式/API]、輸入登入 URL (例如 http://localhost))，然後按一下 [建立]
1. 登入 URL 並未使用，而且可以是任何有效的 URL
1. 選取新的應用程式，然後按一下 [設定] 索引標籤中的金鑰
1. 輸入新金鑰的說明、選取 [永不過期]，然後按一下 [儲存]
1. 記下值。 此值會用來做為服務主體的**密碼**
1. 記下應用程式識別碼。 此識別碼會用來做為服務主體的使用者名稱 (以下步驟中的「登入識別碼」)

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** 為柵欄代理程式建立自訂角色

服務主體預設沒有存取您 Azure 資源的權限。 您需要為服務主體提供權限來啟動和停止 (解除配置) 叢集的所有虛擬機器。 如果您尚未建立自訂角色，您可以使用 [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#create-a-custom-role) 或 [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli#create-a-custom-role) 來建立

針對輸入檔使用下列內容。 您必須調整訂用帳戶的內容，也就是使用您訂用帳戶的識別碼取代 c276fc76-9cd4-44c9-99a7-4fd71546436e 和 e91d47c4-76f3-4271-a796-21b4ecfe3624。 如果您只有一個訂用帳戶，請在 AssignableScopes 中移除第二個項目。

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]** 將自訂角色指派給服務主體

將在上一章中建立的自訂角色「Linux 柵欄代理程式角色」指派給服務主體。 不要再使用「擁有者」角色！

1. 移至 https://portal.azure.com。
1. 開啟 [所有資源] 刀鋒視窗
1. 選取第一個叢集節點的虛擬機器
1. 選取 [存取控制 (IAM)]
1. 按一下 [新增]
1. 選取「Linux 柵欄代理程式角色」角色
1. 輸入您先前建立的應用程式名稱
1. Click OK

針對第二個叢集節點重複上述步驟。

### <a name="1-create-the-stonith-devices"></a>**[1]** 建立 STONITH 裝置

當您編輯虛擬機器的權限之後，就可以在叢集中設定 STONITH 裝置。

<pre><code>
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure property stonith-timeout=900

sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

</code></pre>

### <a name="1-create-fence-topology-for-sbd-fencing"></a>**[1]** 為 SBD 隔離建立柵欄拓撲

如果您想要使用 SBD 裝置，我們仍建議使用 Azure Fence 代理程式，在 iSCSI 目標伺服器無法使用時作為備份。

<pre><code>
sudo crm configure fencing_topology \
  stonith-sbd rsc_st_azure

</code></pre>
### **[1]** 啟用 STONITH 裝置的使用

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>


## <a name="next-steps"></a>後續步驟
* [適用於 SAP 的 Azure 虛擬機器規劃和實作][planning-guide]
* [適用於 SAP 的 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* 若要了解如何建立高可用性並為 Azure VM 上的 SAP HANA 規劃災害復原，請參閱 [Azure 虛擬機器 (VM) 上 SAP HANA 的高可用性][sap-hana-ha]
