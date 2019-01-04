---
title: 使用 Ansible 調整 Azure App Service Web Apps 的規模
description: 深入了解如何使用 Ansible 建立在 Linux 上的 App Service 中具有 Java 8 和 Tomcat 容器執行階段的 Web Apps
ms.service: ansible
keywords: ansible, azure, devops, bash, 劇本, Azure App Service, Web App, 調整規模, Java
author: tomarcher
manager: jeconnoc
ms.author: kyliel
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 740ff6d6a636377f9d58a5231692c87f935ae6d2
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2018
ms.locfileid: "53601860"
---
# <a name="scale-azure-app-service-web-apps-by-using-ansible"></a>使用 Ansible 調整 Azure App Service Web Apps 的規模
[Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/overview) (或簡稱 Web Apps) 可裝載 Web 應用程式、REST API 和行動後端。 您可以使用您慣用的語言進行開發&mdash;.NET、.NET Core、Java、Ruby、Node.js、PHP 或 Python 均可。

Ansible 可讓您將環境中的資源部署和設定自動化。 本文將說明如何使用 Ansible 在 Azure App Service 中調整應用程式的規模。

## <a name="prerequisites"></a>必要條件
- **Azure 訂用帳戶** - 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Azure App Service Web Apps** - 如果您還沒有 Azure 應用程式服務的 Web 應用程式，您可以[使用 Ansible 建立 Azure Web Apps](ansible-create-configure-azure-web-apps.md)。

## <a name="scale-up-an-app-in-app-service"></a>在 App Service 中相應增加應用程式
您可以藉由變更應用程式所屬的 App Service 方案定價層來相應增加。 本節提供會定義下列作業的範例 Ansible 劇本：
- 取得現有 App Service 方案的資訊
- 將 App Service 方案更新為具有三個背景工作角色的 S2

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App serivce plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

將此劇本儲存為 webapp_scaleup.yml。

若要執行劇本，請使用 **ansible-playbook** 命令，如下所示：
```bash
ansible-playbook webapp_scaleup.yml
```

執行劇本後，類似於下列範例的輸出會顯示 App Service 方案已成功更新為具有三個背景工作角色的 S2：
```Output
PLAY [localhost] **************************************************************

TASK [Gathering Facts] ********************************************************
ok: [localhost]

TASK [Get facts of existing App serivce plan] **********************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] ******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] *******************************************
changed: [localhost]

TASK [Get facts] ***************************************************************
ok: [localhost]

TASK [debug] *******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP **********************************************************************
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"] 
> [Ansible on Azure](https://docs.microsoft.com/azure/ansible/)