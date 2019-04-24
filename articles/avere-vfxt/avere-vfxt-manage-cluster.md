---
title: 管理 Avere vFXT 叢集 - Azure
description: 如何管理 Avere 叢集 - 新增或移除節點、重新啟動、停止或終結 vFXT 叢集
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: be9205fdf7fec0661d7382ed0d1bedf47487b15e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409709"
---
# <a name="manage-the-avere-vfxt-cluster"></a>管理 Avere vFXT 叢集

建立叢集之後，您可能需要新增叢集節點或停止或重新啟動叢集。 此外，您的專案完成時，您需要了解如何停止並永久移除叢集。 

根據叢集管理工作，您可能需要使用 Avere 控制台、vfxt.py 叢集命令列建立指令碼，或 Azure 入口網站執行這個動作。 

下表提供可用於每個工作之工具的概觀。 

|  動作 | Avere 控制台 | vfxt.py  | Azure 入口網站 |
| --- | --- | --- | --- |
| 新增叢集節點 | no | 是 | no |
| 移除叢集節點 | 是 | no | no |
| 停止叢集節點 | 是 (也可以重新啟動服務或重新啟動) | no | 從入口網站關閉節點 VM 會解譯為節點失敗 |
| 啟動已停止的節點 | no | no | 是 |
| 終結單一叢集節點 | no | no | 是 |
| 重新啟動叢集 |  |  |  |
| 安全地關閉或停止叢集 | 是 | 是 | no |
| 終結叢集  | no | 是 | 是，但不保證資料完整性 |

各項工具的詳細指示都包含在下方。

## <a name="about-stopped-instances-in-azure"></a>關於 Azure 中已停止的執行個體

當您關閉或停止任何 Azure VM 時，它會停止產生計算費用，但您仍必須支付其儲存體的費用。 如果您關閉 vFXT 節點或整個 vFXT 叢集，而且您不想要重新啟動它，您應該使用 Azure 入口網站刪除相關的 VM。 

在 Azure 入口網站中，*已停止的*節點 (可以重新啟動) 會在 Azure 入口網站中顯示**已停止**狀態；*已刪除的*節點會顯示**已停止 (已取消配置)** 狀態，而且它不再會產生計算或儲存體的費用。

刪除 VM 之前，請確定所有變更的資料已從快取寫回後端儲存體，方法是，使用 Avere 控制台或 vfxt.py 選項停止或關閉叢集。

## <a name="manage-the-cluster-with-avere-control-panel"></a>使用 Avere 控制台管理叢集 

Avere 控制台可用於下列工作： 

* 停止或重新啟動個別的節點
* 從叢集移除節點
* 停止或重新啟動整個叢集

Avere 控制台會設定資料完整性的優先順序，因此它會嘗試在破壞性的操作之前，將任何已變更的資料寫入到後端儲存體中。 如此可讓它成為比 Avere 入口網站更安全的選項。 

從網頁瀏覽器存取 Avere 控制台。 如果您需要協助，請依照[存取 vFXT 叢集](avere-vfxt-cluster-gui.md)中的指示進行。

### <a name="manage-nodes-with-avere-control-panel"></a>使用 Avere 控制台管理節點

[FXT 節點] 設定頁面提供管理個別節點的控制項。

若要關閉、重新啟動或移除節點，在 [FXT 節點] 頁面上的清單中尋找節點，然後在其 [動作] 資料行中按一下適當的按鈕。

> [!NOTE] 
> 當作用中節點的數目變更時，IP 位址可能會在叢集節點之間移動。

如需詳細資訊，請閱讀 Avere 叢集設定指南中的[叢集 > FXT 節點](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>)。

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>使用 Avere 控制台停止或重新啟動叢集

[系統維護] 設定頁面提供重新啟動叢集服務、重新啟動叢集，或安全地關閉叢集的命令。 如需詳細資訊，請閱讀[管理 > 系統維護](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (在 Avere 叢集設定指南中)。

當叢集正在關閉時，它會先將訊息張貼至 [儀表板] 索引標籤。 幾分鐘之後，Avere 控制台工作階段將會停止回應，這表示叢集已經關閉。

## <a name="manage-the-cluster-with-vfxtpy"></a>使用 vfxt.py 管理叢集

vfxt.py 是用來建立和管理叢集的命令列工具。 

vfxt.py 已預先安裝在叢集控制器 VM 上。 如果您想要將其安裝在另一個系統上，請參閱 <https://github.com/Azure/AvereSDK> 上的文件。

vfxt.py 指令碼可以用於以下叢集管理工作：

* 將新節點加入至叢集
* 停止或啟動叢集  
* 終結叢集

如同 Avere 控制台，vfxt.py 作業會嘗試確認已變更的資料永久儲存在後端儲存體，然後再關閉或終結叢集或節點。 如此可讓它成為比 Avere 入口網站更安全的選項。

完整的 vfxt.py 使用指南可在 GitHub 上取得：[使用 vfxt.py 的雲端叢集管理](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>使用 vfxt.py 新增叢集節點

新增叢集節點的範例命令指令碼包含在叢集控制器上。 在控制器上找出 ``./add-nodes``，並在編輯器中開啟它，以使用您的叢集資訊加以自訂。 

叢集必須在執行中，才能使用此命令。 

提供下列值： 

* 叢集的資源群組名稱，如果網路和儲存體資源與叢集不同，則也是它們的名稱
* 叢集位置
* 叢集網路和子網路 
* 叢集節點存取的角色 (您可以使用內建角色[Avere 運算子](../role-based-access-control/built-in-roles.md#avere-operator))
* 叢集管理 IP 位址和系統管理密碼 
* 要新增的節點數目 (1、2 或 3)
* 節點執行個體類型和快取大小值 

如果您未使用原型，則必須建構諸如以下的命令，包括上述的所有資訊。 

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \ 
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

如需詳細資訊，請閱讀 vfxt.py 使用指南中的[將節點新增至叢集](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster)。

### <a name="stop-a-cluster-with-vfxtpy"></a>使用 vfxt.py 停止叢集

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>使用 vfxt.py 啟動已停止的叢集

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```    

由於叢集已停止，因此您必須傳遞執行個體識別碼以指定叢集節點。 若要深入了解，請閱讀 vfxt.py 使用指南中的[指定要修改的叢集](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify)。

### <a name="destroy-a-cluster-with-vfxtpy"></a>使用 vfxt.py 終結叢集

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

如果您不想要從叢集快取寫入已變更的資料，可以使用選項 ``--quick-destroy``。

如需其他資訊，請閱讀 [vfxt.py 使用指南](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>)。  

## <a name="manage-cluster-vms-from-the-azure-portal"></a>從 Azure 入口網站管理叢集 VM 

Azure 入口網站可用來個別終結叢集 VM，但如果沒有先完全關閉叢集，則不保證資料完整性。 

Azure 入口網站可以用於以下叢集管理工作： 

* 啟動已停止的 vFXT 節點
* 停止個別的 vFXT 節點 (叢集會將其解譯節點失敗)
* *如果*您不需要確保叢集快取中已變更的資料寫入至核心篩選，則終結 vFXT 叢集
* 安全地關閉 vFXT 節點和其他叢集資源之後，請將其永久移除

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>從 Azure 入口網站重新啟動 vFXT 執行個體

如果您需要重新啟動已停止的節點，您必須使用 Azure 入口網站。 選取左側功能表中的 [虛擬機器]，然後按一下清單中的 VM 名稱，以開啟其概觀頁面。

按一下概觀頁面頂端的 [啟動] 按鈕，重新啟用 VM。

![Azure 入口網站畫面，其中顯示啟動已停止之 VM 的選項](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>刪除叢集節點

如果您想要刪除 vFXT 叢集中的一個節點，但保留叢集的其餘極點，應該先使用 Avere 控制台，[從叢集移除節點](#manage-nodes-with-avere-control-panel)。

> [!CAUTION]
> 如果您沒有先從 vFXT 叢集移除節點就加以刪除，可能會遺失資料。

若要永久終結當作 vFXT 節點使用的一個或多個執行個體，請使用 Azure 入口網站。
選取左側功能表中的 [虛擬機器]，然後按一下清單中的 VM 名稱，以開啟其概觀頁面。

按一下概觀頁面頂端的 [刪除] 按鈕，永久終結 VM。

您可以在安全地關閉叢集節點之後，使用此方法永久移除它們。 

### <a name="destroy-the-cluster-from-the-azure-portal"></a>從 Azure 入口網站終結叢集

> [!NOTE] 
> 如果您想要將快取中任何其餘的用戶端變更寫入後端儲存體，請使用 vfxt.py `--destroy` 選項或使用 Avere 控制台，完全關閉叢集，然後再移除 Azure 入口網站中的節點執行個體。

您可以刪除 Azure 入口網站中的節點執行個體，藉此永久終結它們。 您也可以如上述一次刪除一個，或者您可以使用 [虛擬機器] 頁面找出所有叢集 VM，選取其核取方塊，然後按一下 [刪除] 按鈕，將它們一次全部移除。

![入口網站中的 VM 清單，依「叢集」一詞篩選，核取並醒目提示其中三個](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>從 Azure 入口網站刪除額外的叢集資源

如果您專為 vFXT 叢集建立額外的資源，您可能想要在卸除叢集時加以移除。 無法終結需的資料或與其他專案共用任何項目所包含的項目。

除了刪除叢集節點之外，請考慮移除下列元件： 

* 叢集控制器 VM
* 與叢集節點相關聯的資料磁碟
* 與叢集元件相關聯的網路介面和公用 IP
* 虛擬網路
* 儲存體帳戶 (**只有**在不包含任何重要資料時)
* 可用性設定組 

![Azure 入口網站「所有資源」清單，其中顯示針對測試叢集建立的資源](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>從 Azure 入口網站刪除叢集的資源群組

如果您專為放入叢集而建立資源群組，可以終結資源群組來終結叢集的所有相關資源。 

> [!Caution] 
> 只有在您確定群組中沒有任何值時，才能終結資源群組。 例如，請確定您已經從資源群組內的任何儲存體容器中，移動所需的任何資料。  

若要刪除資源群組，請按一下入口網站左側功能表中的 [資源群組]，然後篩選資源群組清單，以找出您針對 vFXT 叢集建立的資源群組。 選取該資源群組，然後按一下面板右側的三個點。 選擇 [刪除資源群組]。 入口網站會要求您確認刪除，這是無法復原的。  

![顯示「刪除資源群組」動作的資源群組](media/avere-vfxt-delete-resource-group.png)
