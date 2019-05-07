---
title: Azure 的 VMware 解決方案由 CloudSimple 快速入門-使用 Azure 上的 VMware Vm
description: 了解如何設定及取用 VMware Vm 從 Azure 入口網站使用 Azure CloudSimple VMware 方案
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3940adfaa42de8ac9c3f32a9eadc8f6d643ce3ce
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149468"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>快速入門-使用 Azure 上的 VMware Vm

若要在 Azure 入口網站中建立虛擬機器，用於您訂用帳戶中的系統 CloudSimple 管理員已啟用的虛擬機器範本。 VMware 基礎結構上找到這些 VM 範本。

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>在 Azure 上建立的 CloudSimple VM 需要 VM 範本

建立虛擬機器上您從 vCenter UI 的私人雲端。 若要建立範本，請依照下列中的指示[再製虛擬機器，vSphere Web 用戶端中的範本](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html)。 儲存您的私人雲端的 vCenter VM 範本。

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>在 Azure 入口網站中建立虛擬機器

1. 選取 [所有服務]。

2. 搜尋**CloudSimple 虛擬機器**。

3. 按一下 [新增] 。

    ![建立 CloudSimple 虛擬機器](media/create-cloudsimple-virtual-machine.png)

4. 輸入基本資訊，請按一下**下一步： 大小**。

    ![建立 CloudSimple 虛擬機器基本概念](media/create-cloudsimple-virtual-machine-basic-info.png)

    | 欄位 | 描述 |
    | ------------ | ------------- |
    | 訂用帳戶 | 私用雲端相關聯 azure 訂用帳戶。  |
    | 資源群組 | 要將指派給 VM 的資源群組。 您可以選取現有的群組，或建立新的。 |
    | 名稱 | 若要識別 VM 的名稱。  |
    | 位置 | 在其中裝載此 VM 的 azure 區域。  |
    | 私用雲端 | 您想要用來建立虛擬機器的 CloudSimple 私用雲端。 |
    | 資源集區 | 對應 VM 的資源集區。 選取此選項，從可用的資源集區。 |
    | vSphere 範本 | VM 的 vSphere 範本。  |
    | 使用者名稱 | VM 系統管理員的使用者名稱 （適用於 Windows 的範本）|
    | 密碼 |  VM 系統管理員 （適用於 Windows 的範本） 的密碼。 |
    | 確認密碼 | 確認密碼 |

5. 選取的核心和記憶體容量，VM，然後按一下 數字**下一步： 設定**。 如果您想要公開完整的 CPU 虛擬化客體作業系統，請選取此核取方塊。 需要硬體虛擬化的應用程式可以在虛擬機器，而不需要二進位轉譯或 paravirtualization 上執行。 如需詳細資訊，請參閱 VMware 文件<a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">公開 （expose） VMware 硬體輔助虛擬化</a>。

    ![建立 CloudSimple 虛擬機器大小](media/create-cloudsimple-virtual-machine-size.png)

6. 下表中所述，設定網路介面和磁碟，然後按一下**檢閱 + 建立**。

    ![建立 CloudSimple 虛擬機器組態](media/create-cloudsimple-virtual-machine-configurations.png)

    網路介面、 按一下**新增網路介面**並設定下列設定。
    
    | 控制 | 描述 |
    | ------------ | ------------- |
    | 名稱 | 輸入要識別介面的名稱。  |
    | 網路 | 在您的私用雲端 vSphere 設定分散式的連接埠群組的清單中選取。  |
    | 配接器 | 從已設定 VM 的可用類型清單中選取 vSphere 配接器。 如需詳細資訊，請參閱 VMware 眭妎踱恅<a href="https://kb.vmware.com/s/article/1001805" target="_blank">選擇您的虛擬機器的網路介面卡</a>。 |
    | 在開機時開啟電源 | 選擇是否要部署 VM 開機時啟用 NIC 硬體。 預設值是**啟用**。 |

    對於磁碟，按一下**新增磁碟**並設定下列設定。

    | Item | 描述 | 
    | ------------ | ------------- | 
    | 名稱 | 輸入名稱以識別磁碟。  | 
    | 大小 | 選取其中一個可用的大小。  | 
    | SCSI 控制器 | 選取磁碟的 SCSI 控制器。  |
    | Mode | 決定磁碟與快照集之間的參與方式。 選擇其中一個選項： <br> 的獨立持續性：寫入磁碟的所有資料都會永久都寫入。<br> 的獨立非持續性：當您關閉電源或重設虛擬機器時，寫入磁碟的變更都會被捨棄。  獨立的非持續性模式可讓您永遠重新啟動 VM，在相同的狀態。 如需詳細資訊，請參閱 < <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware 文件</a>。

7. 驗證完成後，檢閱設定，然後按一下**建立**。 若要進行任何變更，請按一下頂端的索引標籤，或按一下。

    ![建立 CloudSimple 虛擬機器-檢閱](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>後續步驟

* [檢視 CloudSimple 虛擬機器的清單](https://docs.azure.cloudsimple.com/azure-manage-vm/)
* [從 Azure 中管理 CloudSimple 虛擬機器](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)