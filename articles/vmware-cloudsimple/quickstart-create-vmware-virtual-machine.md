---
title: Azure VMware Solution by CloudSimple 快速入門 - 在 Azure 上取用 VMware VM
description: 在本快速入門中，您將了解如何使用 Azure VMware Solution by CloudSimple 從 Azure 入口網站設定和取用 VMware VM
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b430efbc931d72de4b095a7eac4c1e7ca496b1b9
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393503"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>快速入門：在 Azure 上取用 VMware VM

若要在 Azure 入口網站中建立虛擬機器，您可以使用私用雲端 vCenter 提供的虛擬機器範本。 VCenter 管理員可以在私人雲端上建立其他範本。

## <a name="create-a-vm-template"></a>建立 VM 範本

首先，使用 vCenter UI 在您的私人雲端上建立虛擬機器。 若要建立範本，請依照 VMware 文章[將虛擬機器複製到 vSphere Web 用戶端中的範本](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html)中的指示操作。 將 VM 範本儲存在您的私人雲端 vCenter 上。

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>在 Azure 入口網站中建立虛擬機器

1. 選取 [所有服務]  。

2. 搜尋 **CloudSimple 虛擬機器**。

3. 選取 [新增]  。

    ![選取 [新增]](media/create-cloudsimple-virtual-machine.png)

4. 輸入下列關於虛擬機器的資訊，然後選取 **[下一步：大小]** 。

    ![建立 CloudSimple 虛擬機器 - 基本](media/create-cloudsimple-virtual-machine-basic-info.png)

    | 欄位 | 說明 |
    | ------------ | ------------- |
    | **訂用帳戶** | 與您的私人雲端相關聯的 Azure 訂用帳戶。  |
    | **資源群組** | VM 所將指派到的資源群組。 您可以選取現有的群組或建立新的群組。 |
    | **名稱** | 用來識別 VM 的名稱。  |
    | **位置** | 裝載 VM 的 Azure 區域。  |
    | **私人雲端** | 您要在其中建立 VM 的 CloudSimple 私人雲端。 |
    | **ResourcePool** | VM 的對應資源集區。 請從可用的資源集區中選取。 |
    | **vSphere 範本** | VM 的 vSphere 範本。  |
    | **使用者名稱** | VM 管理員的使用者名稱 (適用於 Windows 範本)。|
    | **密碼** |  VM 管理員的密碼 (適用於 Windows 範本)。 |
    | **確認密碼** | 在先前的欄位中提供的密碼。 |

5. 選取 VM 的核心數目和記憶體容量。 如果您想要對客體作業系統公開完整的 CPU 虛擬化，請選取 [對客體 OS 公開]  。 需要硬體虛擬化的應用程式可以直接在虛擬機器上執行，而不需進行二進位轉譯或半虛擬化。 如需詳細資訊，請參閱 VMware 文章<a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">公開 VMware 硬體輔助虛擬化</a>。 完成時，選取 **[下一步：組態]** 。

    ![建立 CloudSimple 虛擬機器 - 大小](media/create-cloudsimple-virtual-machine-size.png)

6. 依照下表中的說明設定網路介面和磁碟，然後選取 [檢閱 + 建立]  。

    ![建立 CloudSimple 虛擬機器 - 組態](media/create-cloudsimple-virtual-machine-configurations.png)

    針對網路介面選取 [新增網路介面]  ，然後設定下列設定：
    
    | 設定 | 說明 |
    | ------------ | ------------- |
    | **名稱** | 輸入識別介面的名稱。  |
    | **網路** | 在您的私人雲端 vSphere 中，從已設定的分散式連接埠群組清單中選取。  |
    | **配接器** | 從為 VM 設定的可用類型清單中選取 vSphere 配接器。 如需詳細資訊，請參閱 VMware 文章<a href="https://kb.vmware.com/s/article/1001805" target="_blank">選擇虛擬機器的網路介面卡</a>。 |
    | **在開機時開啟電源** | 選擇是否要在 VM 開機時啟用 NIC 硬體。 預設值為 [啟用]  。 |

    針對磁碟選取 [新增磁碟]  ，然後設定下列設定：

    | 設定 | 說明 |
    | ------------ | ------------- |
    | **名稱** | 輸入識別磁碟的名稱。  |
    | **大小** | 選取其中一個可用的大小。  |
    | **SCSI 控制器** | 選取磁碟的 SCSI 控制器。  |
    | **模式** | 模式會指定磁碟參與快照集的方式。 請選擇其中一個選項： <br> **獨立而持續**：寫入磁碟的所有變更都會永久寫入。<br> **獨立而非持續**：當您將虛擬機器關閉電源或重設時，寫入磁碟的變更就會被捨棄。 獨立而非持續的模式可讓您一律以相同的狀態重新啟動 VM。 如需詳細資訊，請參閱 <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware 文件</a>。

7. 驗證完成後，請檢閱設定並選取 [建立]  。 若要進行變更，請選取頂端的索引標籤，或選取**上一步：組態**。

    ![建立 CloudSimple 虛擬機器 - 檢閱 + 建立](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>後續步驟

* [檢視 CloudSimple 虛擬機器的清單](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [從 Azure 管理 CloudSimple 虛擬機器](https://docs.azure.cloudsimple.com/azure-manage-vm/)
