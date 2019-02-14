---
title: Azure 的 Ansible 模組和版本對照表
description: Azure 的 Ansible 模組和版本對照表
ms.service: ansible
keywords: ansible, 角色, 對照表, 版本, azure, devops
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 09/22/2018
ms.topic: article
ms.openlocfilehash: 149f37d0231ecc0547e8dc7937d22a9cc38b7df3
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810213"
---
# <a name="ansible-module-and-version-matrix"></a>Ansible 模組和版本對照表

## <a name="ansible-modules-for-azure"></a>Azure 的 Ansible 模組
Ansible 隨附於許多模組，這些模組可以直接在遠端主機上或透過劇本來執行。
本文列出 Azure 的 Ansible 模組，這些模組可以佈建 Azure 雲端資源，例如虛擬機器、網路及容器服務。 您可以從 Ansible 的正式發行版本或從 Microsoft 發佈的下列劇本角色，取得這些模組。

| Azure 的 Ansible 模組                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 角色 | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|-------------------------------------| 
| **計算**                    |           |                          |                          |                            |                                | 
| azure_rm_availabilityset                    | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_availabilityset_facts              | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_deployment                         | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_resource                           | -            | -                           | yes          | 是          | yes                                 | 
| azure_rm_resource_facts                     | -            | -                           | yes          | 是          | yes                                 | 
| azure_rm_virtualmachine_scaleset_facts      | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_virtualmachineimage_facts          | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_resourcegroup                      | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_resourcegroup_facts                | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_virtualmachine                     | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_virtualmachine_facts               | -            | -                           | -            | yes          | yes                                 | 
| azure_rm_virtualmachine_extension           | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_virtualmachine_scaleset            | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_image                              |              | yes                         | 是          | 是          | yes                                 | 
| **網路功能**                    |           |                          |                          |                             |                               | 
| azure_rm_virtualnetwork                     | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_virtualnetwork_facts               | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_subnet                             | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_networkinterface                   | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_networkinterface_facts             | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_publicipaddress                    | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_publicipaddress_facts              | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_dnsrecordset                       | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_dnsrecordset_facts                 | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_dnszone                            | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_dnszone_facts                      | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_loadbalancer                       | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_loadbalancer_facts                 | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_appgateway                         | -            | -                           | -            | yes          | yes                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | yes                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | yes                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | yes                                 |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | yes                                 |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | yes                                 | 
| azure_rm_securitygroup                      | yes          | 是                         | 是          | 是          | yes                                 |
| azure_rm_route                              | -            | -                           | -            | yes          | yes                                 | 
| azure_rm_routetable                         | -            | -                           | -            | yes          | yes                                 | 
| azure_rm_routetable_facts                   | -            | -                           | -            | yes          | yes                                 | 
| **儲存體**                    |           |                          |                          |                             |                               | 
| azure_rm_storageaccount                     | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_storageaccount_facts               | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_storageblob                        | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_managed_disk                       | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_managed_disk_facts                 | yes          | 是                         | 是          | 是          | yes                                 | 
| **容器**                    |           |                          |                          |                            |                                | 
| azure_rm_aks                                | -            | -                           | yes          | 是          | yes                                 | 
| azure_rm_aks_facts                          | -            | -                           | yes          | 是          | yes                                 | 
| azure_rm_acs                                | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_containerinstance                  | -            | yes                         | 是          | 是          | yes                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | -              | -            | yes                                 | 
| azure_rm_containerregistry                  | -            | yes                         | 是          | 是          | yes                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | -            | yes          | yes                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | yes                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | yes                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | yes                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | yes                                 | 
| **Azure Functions**                    |           |                          |                          |                            |                                | 
| azure_rm_functionapp                        | yes          | 是                         | 是          | 是          | yes                                 | 
| azure_rm_functionapp_facts                  | yes          | 是                         | 是          | 是          | yes                                 | 
| **資料庫**                    |           |                          |                          |                             |                               | 
| azure_rm_sqlserver                          | -            | yes                         | 是          | 是          | yes                                 | 
| azure_rm_sqlserver_facts                    | -            | yes                         | 是          | 是          | yes                                 | 
| azure_rm_sqldatabase                        | -            | yes                         | 是          | 是          | yes                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | yes                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | yes                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | yes                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | yes          | yes                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | yes                                 | 
| azure_rm_mysqlserver                        | -            | yes                         | 是          | 是          | yes                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | yes          | yes                                 | 
| azure_rm_mysqldatabase                      | -            | yes                         | 是          | 是          | yes                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | yes          | yes                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | yes                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | yes                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | yes                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | yes                                 | 
| azure_rm_postgresqlserver                   | -            | yes                         | 是          | 是          | yes                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | yes          | yes                                 | 
| azure_rm_postgresqldatabase                 | -            | yes                         | 是          | 是          | yes                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | yes          | yes                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | yes                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | yes                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | yes                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | yes                                 | 
| **金鑰保存庫**                    |           |                          |                          |                             |                               | 
| azure_rm_keyvault                           | -            | yes                         | 是          | 是          | yes                                 |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -              | yes                               |
| azure_rm_keyvaultkey                        | -            | yes                         | 是          | 是          | yes                                 |
| azure_rm_keyvaultsecret                     | -            | yes                         | 是          | 是          | yes                                 |
| **Web Apps**                    |           |                          |                          |                             |                               | 
| azure_rm_appserviceplan                          | -            | -                         | -          | yes          | yes                                 | 
| azure_rm_appserviceplan_facts                    | -            | -                         | -          | yes          | yes                                 | 
| azure_rm_webapp                                  | -            | -                         | -          | yes          | yes                                 | 
| azure_rm_webapp_facts                            | -            | -                         | -          | yes          | yes                                 | 
| **流量管理員**                    |           |                          |                          |                             |                               | 
| azure_rm_trafficmanagerendpoint                  | -            | -                         | -          | yes          | yes                                 | 
| azure_rm_trafficmanagerendpoint_facts            | -            | -                         | -          | yes          | yes                                 | 
| azure_rm_trafficmanagerprofile                   | -            | -                         | -          | yes          | yes                                 | 
| azure_rm_trafficmanagerprofile_facts             | -            | -                         | -          | yes          | yes                                 | 
| **自動調整**                    |           |                          |                          |                             |                               | 
| azure_rm_autoscale                  | -            | -                         | -          | yes          | yes                                 | 
| azure_rm_autoscale_facts            | -            | -                         | -          | yes          | yes                                 | 

## <a name="introduction-to-playbook-role-for-azure"></a>Azure 劇本角色簡介
[azure_preview_module 劇本角色](https://galaxy.ansible.com/Azure/azure_preview_modules/)是最完整的角色，並內含所有最新的 Azure 模組。 相較於官方 Ansible 版本，此角色會更加及時地推出更新和錯誤修正。 如果您將 Ansible 用於 Azure 資源佈建用途，建議您安裝 azure_preview_module 劇本角色。

azure_preview_module 劇本角色每三週發行一次。

## <a name="next-steps"></a>後續步驟
如需劇本角色的相關詳細資訊，請參閱[建立可重複使用的劇本](https://docs.ansible.com/ansible/latest/playbooks_reuse.html)。 
