---
title: 資料科學虛擬機器集區 - Azure | Microsoft Docs
description: 將資料科學虛擬機器集區部署為小組的共用資源
keywords: 深度學習, 資料科學工具, 資料科學虛擬機器, 地理空間分析, 小組資料科學流程
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: gokuma
ms.openlocfilehash: 648b0818f07aca8763fa4af01380076ae307b0a2
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408947"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>建立共用的資料科學虛擬機器集區

本文探討如何建立共用的資料科學虛擬機器 (DSVM) 集區，以供小組使用。 使用共用集區的好處是資源使用率較佳，可促進共用和協同作業，以及更有效地管理 DSVM 資源。 

您可以使用許多方法和技術來建立 DSVM 集區。 本文將焦點放在互動式 VM 的集區。 替代的受控計算基礎結構是 Azure Machine Learning Compute。 如需詳細資訊，請參閱[設定計算目標](../service/how-to-set-up-training-targets.md#amlcompute)。

## <a name="interactive-vm-pool"></a>互動式虛擬機器集區

整個 AI/資料科學小組所共用的互動式 VM 集區可讓使用者登入 DSVM 的可用執行個體，而不必讓每一組使用者擁有專用的執行個體。 這種設定方式可提升可用性，並更加有效地使用資源。 

您用來建立互動式 VM 集區的技術是 [Azure 虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/)。 您可以使用擴展集來建立和管理一組負載平衡且可自動調整的相同 VM。 

使用者會登入主要集區的 IP 或 DNS 位址。 擴展集會自動將工作階段路由傳送至擴展集內的可用 DSVM。 因為使用者希望不論登入哪個虛擬機器都能有類似的環境，所以擴展集內虛擬機器的所有執行個體都會掛接共用網路磁碟機，例如 Azure 檔案服務共用或 NFS 共用。 使用者的共用工作區通常會保存在已掛接在每個執行個體上的共用檔案存放區。 

您可以在 [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json) 上找到使用 Ubuntu DSVM 執行個體建立擴展集的 Azure Resource Manager 範本範例。 Azure Resource Manager 範本的[參數檔](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json)範例位於相同位置。 

在 Azure CLI 中指定參數檔的值，即可透過 Azure Resource Manager 範本建立擴展集。 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
上述命令假設您有：
* 參數檔案的複本，內含針對您的擴展集執行個體指定的值。
* 虛擬機器執行個體的數目。
* Azure 檔案服務共用的指標。
* 每個 VM 上將掛接的儲存體帳戶的認證。 

在命令中，參數檔會在本機參考。 您也可以透過內嵌方式傳遞參數，或在指令碼中提示這些參數。  

上述範本可在前端擴展集到後端 Ubuntu DSVM 集區之間實現 SSH 和 Jupyterhub 連接埠。 身為使用者，您只需要以正常方式登入 SSH 上或 JupyterHub 上的虛擬機器。 因為虛擬機器執行個體可透過動態方式相應增加或相應減少，所以所有狀態都必須儲存在掛接的 Azure 檔案服務共用中。 您可以使用相同的方法來建立 Windows DSVM 集區。 

在 GitHub 的 Azure DataScienceVM 存放庫中也可取得[可掛接 Azure 檔案服務共用的指令碼](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh)。 此指令碼會在參數檔案中指定的掛接點掛接 Azure 檔案服務共用。 此指令碼也會在初始使用者的主目錄中建立所掛接磁碟機的軟式連結。 Azure 檔案服務共用中的特定使用者 Notebook 目錄會軟式連結至 `$HOME/notebooks/remote` 目錄，以便使用者存取、執行及儲存其 Jupyter Notebook。 在虛擬機器上建立其他使用者，以將每個使用者的 Jupyter 工作區指向 Azure 檔案服務共用時，也可以使用相同的慣例。 

虛擬機器擴展集支援自動調整。 您可以設定何時建立其他執行個體及何時相應減少執行個體的規則。 例如，您可以相應減少為零個執行個體，以在完全不使用 VM 時節省雲端硬體使用成本。 虛擬機器擴展集的文件頁面會提供[自動調整](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)的詳細步驟。

## <a name="next-steps"></a>後續步驟

* [設定通用身分識別](dsvm-common-identity.md)
* [安全地儲存用來存取雲端資源的認證](dsvm-secure-access-keys.md)















