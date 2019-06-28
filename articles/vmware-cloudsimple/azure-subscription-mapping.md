---
title: 將 Azure 訂用帳戶對應至 Azure CloudSimple VMware 方案上的資源集區
description: 描述如何將 Azure CloudSimple VMware 方案上的資源集區對應至您的 Azure 訂用帳戶
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efda996e03d46a2f97d19558f7c2930b623a639e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332643"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>將對應從私用雲端您 Azure 訂用帳戶的資源集區

Azure 訂用帳戶的對應可讓您將對應從您私用雲端的 vCenter 您 Azure 訂用帳戶的資源集區。 您可以對應只能訂用帳戶已建立 CloudSimple 服務的位置。  建立 VMware 虛擬機器從 Azure 入口網站部署中對應的資源集區的虛擬機器。  在 CloudSimple 入口網站中，您可以檢視和管理您的私用雲端的 Azure 訂用帳戶。

訂用帳戶可以對應至私人雲端的多個 vCenter 資源集區中。  您必須將對應的每個私人雲端的資源集區。  只有對應的資源集區可供從 Azure 入口網站建立 VMware 虛擬機器。

> [!IMPORTANT]
> 對應的資源集區時，也會對應任何子系資源集區。 如果任何子系資源集區已對應，則不能對應父系資源集區。

## <a name="before-you-begin"></a>開始之前

本文假設您已有 CloudSimple 服務和私人雲端在您的訂用帳戶中。  若要建立 CloudSimple 服務時，請參閱[快速入門-建立服務](quickstart-create-cloudsimple-service.md)。  如果您需要建立私人雲端，請參閱[快速入門-設定私用雲端環境](quickstart-create-private-cloud.md)。

您可以將 vCenter 叢集 （根資源集區） 對應至您的訂用帳戶。  如果您想要建立新的資源集區，請參閱[建立資源集區](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html)VMware 文件網站上的文章。

## <a name="default-resource-group"></a>預設資源群組

從 Azure 入口網站中建立新的 CloudSimple 虛擬機器，可讓您選取的資源群組。  建立對應的資源集區中的私用雲端 vCenter 上的虛擬機器將會顯示在 Azure 入口網站上的。  探索到的虛擬機器會放在預設的 Azure 資源群組中。  您可以變更預設資源群組的名稱。

## <a name="map-azure-subscription"></a>將對應的 Azure 訂用帳戶

1. 存取權[CloudSimple 入口網站](access-cloudsimple-portal.md)。

2. 開啟**資源**頁面上，然後選取您想要對應的私人雲端。

3. 選取 **對應的 Azure 訂用帳戶**。

4. 按一下 **編輯 Azure 訂用帳戶對應**。

5. 若要對應可用的資源集區，在左側加以選取並按一下向右箭號。

6. 若要移除對應，在右邊選取它們然後按一下向左箭號。

    ![Azure 訂用帳戶](media/resources-azure-mapping.png)

7. 按一下 [確定]  。

## <a name="change-default-resource-group-name"></a>變更預設的資源群組名稱

1. 存取權[CloudSimple 入口網站](access-cloudsimple-portal.md)。

2. 開啟**資源**頁面上，然後選取您想要對應的私人雲端。

3. 選取 **對應的 Azure 訂用帳戶**。

4. 按一下 **編輯**下 Azure 資源群組名稱。

    ![編輯資源群組名稱](media/resources-edit-resource-group-name.png)

5. 輸入資源群組的新名稱，然後按一下**送出**。

    ![輸入新的資源群組名稱](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>後續步驟

* [使用 Azure 上的 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 深入了解[CloudSimple 虛擬機器](cloudsimple-virtual-machines.md)