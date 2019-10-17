---
title: 適用于 Azure 的 Ansible 模組和版本對照表 |Microsoft Docs
description: Azure 的 Ansible 模組和版本對照表
keywords: ansible, 角色, 對照表, 版本, azure, devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 10/14/2019
ms.openlocfilehash: 275dca40ab20c222da2b9115f9a5dc141228c766
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72385473"
---
# <a name="ansible-module-and-version-matrix"></a>Ansible 模組和版本對照表

Ansible 包含一組用來佈建和設定 Azure 資源的模組。 這些資源包括虛擬機器、擴展集、網路服務和容器服務。 本文列出適用于 Azure 的各種 Ansible 模組和其隨附的 Ansible 版本。

## <a name="ansible-modules-for-azure"></a>Azure 的 Ansible 模組

下列模組可以直接在遠端主機上執行，或透過操作手冊來執行。  

這些模組可從 Ansible 官方版本和下列 Microsoft 腳本角色取得。

> [!NOTE]
> 從 Ansible 2.9 開始，我們已將所有 * _facts 模組重新命名為 * _info，以遵循 Ansible 命名慣例。 舊的和重新命名的模組會相互連結，而不會看到取代警告，所有模組都會如以往一樣正常執行。

| Azure 的 Ansible 模組                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2。8 | Ansible 2。9 | Ansible 角色 | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **計算**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_availabilityset_info              | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_deployment                         | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_functionapp                        | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_functionapp_info                  | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_image                              | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_image_info                        | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_resource                           | -            | -                           | 是          | 是          | 是          | 是          | 是          |
| azure_rm_resource_info                     | -            | -                           | 是          | 是          | 是          | 是          | 是          |
| azure_rm_resourcegroup                      | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_resourcegroup_info                | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_virtualmachine                     | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachineextension            | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_virtualmachineimage_info          | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachinescaleset             | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachinescaleset_info       | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | 是          | 是          | 是          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | 是          | 是          | 是          |
| **網路功能**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | 是          | 是          | 是          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | 是          | 是          | 是          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | 是          | 是          | 是          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | 是          | 是          | 是          |
| azure_rm_dnsrecordset                       | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_dnsrecordset_info                 | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_dnszone                            | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_dnszone_info                      | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_loadbalancer                       | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_loadbalancer_info                 | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_networkinterface                   | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_networkinterface_info             | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_publicipaddress                    | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_publicipaddress_info              | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_route                              | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_routetable                         | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_routetable_info                   | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_securitygroup                      | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_subnet                             | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_virtualnetwork                     | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_virtualnetwork_info               | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | 是          | 是          | 是          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | 是          | 是          | 是          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | 是          | 是          |
| **儲存體**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_manageddisk_info                  | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_storageaccount                     | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_storageaccount_info               | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_storageblob                        | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_webapp                             | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_webapp_info                       | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | 是          | 是          | 是          |
| **容器**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | 是          | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_aks                                | -            | -                           | 是          | 是          | 是          | 是          | 是          |
| azure_rm_aks_info                          | -            | -                           | 是          | 是          | 是          | 是          | 是          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_containerinstance                  | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_containerregistry                  | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_containerregistry_info            | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | 是          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | 是          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | 是          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | 是          |
| **資料庫**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mysqldatabase                      | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_mysqlserver                        | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_postgresqldatabase                 | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_postgresqlserver                   | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_sqldatabase                        | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | 是          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | 是          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | 是          | 是          | 是          | 是          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_sqlserver                          | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_sqlserver_info                    | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| **分析**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | 是          | 是          |
| **整合**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | 是          | 是          | 是          |
| **Security**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | 是          | 是          | 是          |
| azure_rm_keyvaultkey                        | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_keyvaultsecret                     | -            | 是                         | 是          | 是          | 是          | 是          | 是          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | 是          | 是          | 是          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | 是          | 是          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | 是          | 是          | 是          |
| **Azure 監視器**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_autoscale_info                    | -            | -                         | -          | 是          | 是          | 是          | 是          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | 是          | 是          | 是          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | 是          | 是          |
| **管理與治理**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | 是        | 是          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | 是        | 是          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | 是        | 是          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | 是        | 是          |
| **物聯網**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | 是        | 是          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | 是        | 是          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | 是        | 是          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | 是        | 是          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | 是        | 是          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | 是        | 是          |

## <a name="introduction-to-playbook-role-for-azure"></a>Azure 劇本角色簡介

[Azure_preview_module 腳本角色](https://galaxy.ansible.com/Azure/azure_preview_modules/)包含所有最新的 azure 模組。 相較於官方 Ansible 版本，此角色會更加及時地推出更新和錯誤修正。 如果您將 Ansible 用於 Azure 資源布建用途，建議您安裝 `azure_preview_module` 腳本角色。

@No__t-0 腳本角色每三周發行一次。

## <a name="next-steps"></a>後續步驟

如需腳本角色的詳細資訊，請參閱[建立可重複使用](https://docs.ansible.com/ansible/latest/playbooks_reuse.html)的腳本。 