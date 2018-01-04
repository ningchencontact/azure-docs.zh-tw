---
title: "建立在 Azure 入口網站中設定 Linux 虛擬機器比例 |Microsoft 文件"
description: "了解如何在 Azure 入口網站，快速建立虛擬機器規模"
keywords: "虛擬機器擴展集"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa6bf6b34d8b93ffa9aceaf7c6112c63d4cb9f1c
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-machine-scale-set-in-the-azure-portal"></a>在 Azure 入口網站中建立 虛擬機器擴展集
虛擬機器擴展集可讓您部署和管理一組相同、自動調整的虛擬機器。 您可以手動調整擴展集中的 VM 數目，或定義規則以根據如 CPU、記憶體需求或網路流量的資源使用量來自動調整。 取得已啟動本文中，您可以建立的虛擬機器擴展集在 Azure 入口網站。 您也可以建立與設定的比例[Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md)或[Azure PowerShell](virtual-machine-scale-sets-create-powershell.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。


## <a name="log-in-to-azure"></a>登入 Azure
登入 Azure 入口網站，網址是 http://portal.azure.com/。


## <a name="create-virtual-machine-scale-set"></a>建立虛擬機器規模集
您可以部署與 Windows Server 映像或 Linux 映像，例如 RHEL、 CentOS、 Ubuntu 或 SLES 設定小數位數。

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。
2. 搜尋*規模集*，選擇**虛擬機器規模集**，然後選取**建立**。
3. 輸入的小數位數組名稱，例如*myScaleSet*。
4. 選取所需的 OS 類型，例如*Windows Server 2016 Datacenter*。
5. 輸入您所需的資源群組的名稱，例如*myResourceGroup*，和位置，例如*美國東部*。
6. 輸入所需的使用者名稱，然後選取偏好的驗證類型。
    - A**密碼**必須至少為 12 個字元，且符合下列的四個複雜性需求以外的三個： 一個小寫字元、 一個大寫字元、 一個數字和一個特殊字元。 如需詳細資訊，請參閱[使用者名稱和密碼需求](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm)。
    - 如果您選取的 Linux 作業系統的磁碟映像，您可以改為選擇  **SSH 公開金鑰**。 僅提供您公開金鑰，例如*~/.ssh/id_rsa.pub*。 您可以使用入口網站至 Azure 雲端 Shell[建立及使用 SSH 金鑰](../virtual-machines/linux/mac-create-ssh-keys.md)。

7. 輸入**公用 IP 位址名稱**，例如*myPublicIP*。
8. 輸入唯一**網域名稱標籤**，例如*myuniquedns*。 此 DNS 標籤構成 FQDN 規模集前方負載平衡器的基底。
9. 若要確認小數位數設定的選項，請選取**建立**。

    ![建立的虛擬機器擴展集在 Azure 入口網站](./media/virtual-machine-scale-sets-create-portal/create-scale-set.png)


## <a name="connect-to-a-vm-in-the-scale-set"></a>連線到擴展集中的 VM
當您建立在入口網站中設定的標尺時，會建立負載平衡器。 網路位址轉譯 (NAT) 規則可用來將標尺集執行個體，例如 RDP 或 SSH 的遠端連線的流量。

若要檢視這些 NAT 規則與您的標尺的連線資訊設定執行個體：

1. 選取資源群組，例如在先前步驟中，建立*myResourceGroup*。
2. 從資源的清單中，選取您**負載平衡器**，例如*myScaleSetLab*。
3. 選擇**輸入 NAT 規則**從視窗左側功能表。

    ![輸入的 NAT 規則可讓您連接到虛擬機器規模集執行個體](./media/virtual-machine-scale-sets-create-portal/inbound-nat-rules.png)

您可以使用下列 NAT 規則來連線到擴展集中的每個 VM。 目的地 IP 位址與 TCP 連接埠值，就會列出每個 VM 執行個體。 例如，如果目的地 IP 位址是*104.42.1.19*和 TCP 連接埠是*50001*，您連接到 VM 執行個體，如下所示：

- Windows 擴展集，連接到 VM 執行個體與 RDP 上`104.42.1.19:50001`
- Linux 擴展集，連線到透過 SSH 的 VM 執行個體上`ssh azureuser@104.42.1.19 -p 50001`

出現提示時，輸入您所指定的認證從上一個步驟建立該規模調整集合時。 小數位數組執行個體是一般的 Vm，您可以互動以正常。 如需有關如何部署和執行應用程式在您的標尺上設定執行個體的詳細資訊，請參閱[部署虛擬機器擴展集上的應用程式](virtual-machine-scale-sets-deploy-app.md)


## <a name="clean-up-resources"></a>清除資源
當不再需要請刪除資源群組，設定標尺，，和所有相關資源。 若要這樣做，請選取 VM 的資源群組，然後按一下 [刪除]。


## <a name="next-steps"></a>後續步驟
取得已啟動本文中，您可以建立在 Azure 入口網站設定的基本比例。 延展性和自動化，依序展開您設定下列使用說明文章的標尺：

- [部署虛擬機器擴展集上的應用程式](virtual-machine-scale-sets-deploy-app.md)
- 自動縮放表單與[Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)、 [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md)，或[Azure 入口網站](virtual-machine-scale-sets-autoscale-portal.md)
- [使用自動作業系統升級的小數位數組 VM 執行個體](virtual-machine-scale-sets-automatic-upgrade.md)
