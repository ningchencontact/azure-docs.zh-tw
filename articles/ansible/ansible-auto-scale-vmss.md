---
title: 使用 Ansible 自動調整 Azure 中的虛擬機器擴展集
description: 了解如何使用 Ansible 與自動調整來調整 Azure 中的虛擬機器擴展集
ms.service: azure
keywords: ansible, azure, devops, bash, 劇本, 調整, 自動調整, 虛擬機器, 虛擬機器擴展集, vmss
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/10/2018
ms.openlocfilehash: 578ad3207f62e74805be056ca11d3bd9b46513da
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792424"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-azure-using-ansible"></a>使用 Ansible 自動調整 Azure 中的虛擬機器擴展集
Ansible 可讓您將環境中的資源部署和設定自動化。 您可以使用 Ansible 在 Azure 中管理虛擬機器擴展集 (VMSS)，就像管理任何其他 Azure 資源一樣。 

當建立擴展集時，您會定義您想要執行的 VM 執行個體數目。 當您的應用程式需求變更時，您可以自動增加或減少 VM 執行個體數目。 自動調整的能力可讓您在整個應用程式的生命週期中，跟上客戶的需求或對應用程式效能變更做出回應。 在本文中，您將建立自動調整設定，並使其與現有的虛擬機器擴展集產生關聯。 在自動調整規模設定中，您可以設定規則以視需要相應放大或相應縮小。

## <a name="prerequisites"></a>必要條件
- **Azure 訂用帳戶** - 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- 現有的 Azure 虛擬機器擴展集。 - 如果您還沒有，請[使用 Ansible 在 Azure 中建立虛擬機器擴展集](https://docs.microsoft.com/azure/ansible/ansible-create-configure-vmss)。

> [!Note]
> 必須使用 Ansible 2.7，才能執行此教學課程中的下列範例劇本。 

## <a name="auto-scale-based-on-a-schedule"></a>根據排程自動調整   
若要對擴展集啟用自動調整，您必須先定義自動調整設定檔。 此設定檔會定義預設、最小和最大擴展集容量。 這些限制可讓您藉由不繼續建立 VM 執行個體來控制成本，並且在可接受的效能與保留在相應縮小事件中的執行個體數目下限之間取得平衡。 

您可以依據週期性排程或特定日期，對虛擬機器擴展集進行相應縮小和相應放大。 本節提供建立自動調整設定的範例 Ansible 劇本，該設定會在太平洋時區的每個星期一 10:00 將您擴展集中的 VM 執行個體數目增加為三個。 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks: 
    - name: Create auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

請將此劇本儲存為 *vmss-auto-scale.yml*。 若要執行 Ansible 腳本，請使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="auto-scale-based-on-performance-data"></a>根據效能資料自動調整
如果您的應用程式需求增加，您擴展集內 VM 執行個體上的負載也會跟著增加。 如果這樣的負載增加會持續而非只是短暫的需求，您就可以設定自動調整規則來增加擴展集中的 VM 執行個體數目。 建立這些 VM 執行個體並部署應用程式後，擴展集就會開始透過負載平衡器將流量分散給它們。 您可以控制要監視哪些計量 (例如 CPU 或磁碟)、應用程式負載必須符合給定的閾值多久，以及要將多少個 VM 執行個體新增至擴展集。

您可以根據效能標準臨界值、週期性排程或特定日期，對虛擬機器擴展集進行相應縮小和相應放大。 本節所提供的範例 Ansible 劇本，會在太平洋時區的每個星期一 18:00 檢查過去 10 分鐘的工作負載，並根據 CPU 百分比計量將您擴展集中的 VM 執行個體數目相應放大為四個，或相應縮小為一個執行個體。 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get VMSS resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create auto scaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

請將此劇本儲存為 *vmss-auto-scale-metrics.yml*。 若要執行 Ansible 腳本，請使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-information-for-existing-autoscale-settings"></a>取得現有自動調整設定的資訊
您可以透過 *azure_rm_autoscale_facts* 模組與劇本取得任何自動調整設定的詳細資料，如下所示：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve auto scale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

## <a name="disable-the-autoscale-settings"></a>停用自動調整設定
您可以藉由將 `enabled: true` 變更為 `enabled: false`，或刪除劇本的自動調整設定，以停用自動調整設定，如下所示：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"] 
> [虛擬機器擴展集的 Ansible 範例劇本](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)