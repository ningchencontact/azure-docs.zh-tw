---
title: 依 CloudSimple 將 Azure 訂用帳戶對應至 Azure VMware 解決方案上的資源集區
description: 說明如何透過 CloudSimple 將 Azure VMware 解決方案上的資源集區對應至您的 Azure 訂用帳戶
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1bf721f35500d2ff1344996e7750c5e574f40f31
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816259"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>將資源集區從私人雲端對應至您的 Azure 訂用帳戶

Azure 訂用帳戶對應可讓您將來自私人雲端 vCenter 的資源集區對應到您的 Azure 訂用帳戶。 您只能對應已建立 CloudSimple 服務的訂用帳戶。  從 Azure 入口網站建立 VMware 虛擬機器, 會在對應的資源集區中部署虛擬機器。  在 CloudSimple 入口網站中, 您可以查看和管理私人雲端的 Azure 訂用帳戶。

訂用帳戶可以對應至私人雲端的多個 vCenter 資源集區。  您必須對應每個私人雲端的資源集區。  只有對應的資源集區才可用於從 Azure 入口網站建立 VMware 虛擬機器。

> [!IMPORTANT]
> 對應資源集區也會對應任何子資源集區。 如果已經對應了任何子資源集區, 就無法對應父資源集區。

## <a name="before-you-begin"></a>開始之前

本文假設您的訂用帳戶中有 CloudSimple 服務和私人雲端。  若要建立 CloudSimple 服務, 請參閱[快速入門-建立服務](quickstart-create-cloudsimple-service.md)。  如果您需要建立私人雲端, 請參閱[快速入門-設定私人雲端環境](quickstart-create-private-cloud.md)。

您可以將 vCenter 叢集 (根資源集區) 對應到您的訂用帳戶。  如果您想要建立新的資源集區, 請參閱 VMware 檔網站上[的建立資源集區一](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html)文。

## <a name="default-resource-group"></a>預設資源群組

從 Azure 入口網站建立新的 CloudSimple 虛擬機器, 可讓您選取資源群組。  在對應資源集區的私人雲端 vCenter 上建立的虛擬機器, 將會顯示在 Azure 入口網站上。  探索到的虛擬機器將會放在預設的 Azure 資源群組中。  您可以變更預設資源群組的名稱。

## <a name="map-azure-subscription"></a>對應 Azure 訂用帳戶

1. 存取[CloudSimple 入口網站](access-cloudsimple-portal.md)。

2. 開啟 [**資源**] 頁面, 然後選取您想要對應的私人雲端。

3. 選取 [Azure 訂用帳戶**對應**]。

4. 按一下 [**編輯 Azure 訂**用帳戶對應]。

5. 若要對應可用的資源集區, 請在左側選取它們, 然後按一下向右箭號。

6. 若要移除對應, 請選取右側的對應, 然後按一下向左箭號。

    ![Azure 訂用帳戶](media/resources-azure-mapping.png)

7. 按一下 [確定]。

## <a name="change-default-resource-group-name"></a>變更預設的資源組名

1. 存取[CloudSimple 入口網站](access-cloudsimple-portal.md)。

2. 開啟 [**資源**] 頁面, 然後選取您想要對應的私人雲端。

3. 選取 [Azure 訂用帳戶**對應**]。

4. 按一下 [Azure 資源組名] 底下的 [**編輯**]。

    ![編輯資源組名](media/resources-edit-resource-group-name.png)

5. 輸入資源群組的新名稱, 然後按一下 [**提交**]。

    ![輸入新的資源組名](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>後續步驟

* [使用 Azure 上的 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 深入瞭解[CloudSimple 虛擬機器](cloudsimple-virtual-machines.md)